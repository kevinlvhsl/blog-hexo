---
title: CocosCreator之手动加载远程图片减少黑屏时间
date: 2018-08-07 09:42:41
tags: [优化, 微信小游戏, 黑屏]
---

![cocos_creator](/assets/cocos_creator.jpg)

小游戏，只所以为小，因为它的体积确实小，下载起来快，随用随走。

> 在微信小游戏中， 游戏包体大小是有限制的，首包是4M，分包的话，在cocoscrator中，也只能分包js文件，所以，图片文件还是只能通过远程加载。但是，creator构建出来的图片资源，会一次性被小游戏加载回来，这样，就会造成小游戏在加载图片资源时，会有黑屏，资源过大时，黑屏时间就更长。所以，这时候使用手动动态加载大图，可以有效的减少初始化游戏时的网络压力。

### 下面通过以下几个方法，来优化我们的游戏


### 1、手动加载超大图
>将游戏中使用到的大图（超过50kb的），采用低分辨率小图作为初始图，然后在节点onLoad以后，采用cc.loader.load() 方法,来动态加载原图。 这样既不会造成初始没有图，也可以展示更清晰的原图。
<!-- more -->
```js
/**
 * 从远程加载图片
 * @param {图片路径} imgPath
 * @param {当前节点} targetNode
 */
export const loadRemoteImage = function (imgPath, targetNode, cb) {
    if (!imgPath) return
    let type = imgPath.slice(imgPath.lastIndexOf('.') + 1) || 'png'
    cc.loader.load({url: imgPath, type: type}, function (error, texture) {
        targetNode.getComponent(cc.Sprite).spriteFrame = new cc.SpriteFrame(texture)
        cb && cb()
    })
}

// 并且修改一下libs下的wx-downloader.js
function downloadRemoteFile(item, callback) {
        // Download from remote server  判断传进来的item 是否是要自定义下载
        var relatUrl = item.url;

        // filter protocol url (E.g: https:// or http:// or ftp://)
        if (REGEX.test(relatUrl)) {
          callback(null, null);
          return
        }
        var remoteUrl = '';
        if (relatUrl.indexOf('http') === 0) {
          remoteUrl = relatUrl;
        } else {
          remoteUrl = wxDownloader.REMOTE_SERVER_ROOT + '/' + relatUrl;
        }

        item.url = remoteUrl;

        wx.downloadFile({
          url: remoteUrl,
          success: function (res) {
            // 这里面不变
          },
          fail: function (res) {
            // Continue to try download with downloader, most probably will also fail
            callback(null, null);
          }
        })
      };

// js代码中使用  第一个参数是要下载的图片地址， 第二个参数值使用图片的Node节点
loadRemoteImage(Config.REMOTE_HOST + '/res/gzh.jpg', this.bg)
```


### 2、动画图片使用手动加载
这两种方法有个共同点，就是这些资源存放在一个固定的目录下， 叫： `resources` 目录的名字不能改，只能是这个名
> 1、游戏中如果大量使用帧动画的话，图片资源肯定小不了，这样在场景初始化时，也可能造成黑屏。所有我们就采用动态加载动画图片的方法。

```js

/**
 * 加载动画的序列帧
 * @param {object} param0
 * cc.loader.loadResDir 加载指定的目录，目录下的文件会以一个资源数组asstes返回
 */
export const loadAnimFrames = function ({ dir = 'watch', callback} ) {
    let frames = []
    let clip = null
    cc.loader.loadResDir(dir, function (err, assets) {
        if (assets && assets.length) {
            assets.forEach(ass => {
                if (ass.name) {
                    frames.push(ass)
                }
            })
            clip = cc.AnimationClip.createWithSpriteFrames(frames, 5)   // 创建动画clip， 默认帧率为5， 越大越快
            clip.name = dir
            clip.wrapMode = cc.WrapMode.Loop    // 设置为永久循环
            callback && callback(clip)

        }
    })
}

// 使用
    let anim = this.node.getComponent(cc.Animation)
    Util.loadAnimFrames({
        dir: 'eat',
        callback: (clip) => {
            // 将加载回来的clip设置到当前节点
            anim.addClip(clip)
            anim.play('eat')
        }
    })
```

> 2、另一种情况就是，图片在游戏场景中本身就是根据数据动态来渲染的，比如：等级不同渲染不同的图标
```js
    // loadRes  加载资源，第一个参数：资源路径(resources下)， 参数2：回调
    let iconMap = {'4': 'icon-level-1.png', '3': 'icon-level-2.png',
    '2': 'icon-level-3.png','1': 'icon-level-4.png'}
    cc.loader.loadRes(iconMap[prize.level], function (error, texture) {
        levelIcon.getComponent('cc.Sprite').spriteFrame = new cc.SpriteFrame(texture)
    })
```




> 光做以上步骤还不行， 因为有些游戏中图片还是很多，每一个图片都这样去设置低分辨率小图也不是办法，还有关键的一步。

### 3、手动加载所有图片资源包 wx.donwloadFile
> 这里需要做些准备,
1、在构建打包以后，要将build目录下，wechatgame/res/raw-assets 目录打包成zip压缩包，并且上传到远程oss服务器上，
2、再将这个raw-assets目录删除， 这样小游戏启动时加载图片资源就加载不到。
3、再将删除了资源文件的小游戏源码，通过微信开发者工具上传到微信服务器上。
> 这时候，小游戏控制台可能会报一些错误 donwloadfile:ok 这样的错误。 不过这个不用管，它是小游戏自动去下载raw-assets中资源找不到(因为我们把目录删除了)
4、然后我们需要在小游戏的主入口场景中加上以下代码，就可以把资源文件手动下载到我们的小游戏文件目录下（这个时候，还需要展示一个加载资源的loading图片覆盖在主场景之上，这样就友好的展示了资源加载过程。）
```js

/**
 * 加载远程资源
 * wx.env.USER_DATA_PATH： 这个是小游戏在手机上的临时目录
 **/
    loadRemoteAssets () {
        const self = this
        const fs = wx.getFileSystemManager()  // 获取微信小游戏sdk中的 文件系统
        // 然后
        const downloadTask = wx.downloadFile({
            url: Config.REMOTE_HOST + '/res/raw-assets.zip',  // 我们上传到服务器的资源文件压缩包地址
            header: {
                'content-type': 'application/json'
            },
            filePath: '',
            success: function (res){    // 资源下载成功以后，我们将文件解压到小游戏的运行目录
                console.log('资源下载成功', res)
                let zip_res = res.tempFilePath
                fs.unzip({
                    zipFilePath: zip_res,
                    targetPath: wx.env.USER_DATA_PATH + '/res/',
                    success: function (result) {
                        console.log('解压缩成功---', result)
                        wx.setStorageSync('downloaded', true)
                        self.MainScene.init()       // 解压成功以后再让主场景初始化数据
                        setTimeout(() => {
                            self.hideLoading()
                        }, 700)
                    }
                })
            },
            fail: function(err){
                console.error('资源下载失败', err)
            },
            complete: function (res) {
                console.log('资源下载 complete')

            }
        })
        if (downloadTask) {     // 资源下载的时候，在界面上展示下载的进度，让用户能感知游戏进程
            downloadTask.onProgressUpdate(function(res){
                self.loadFileProgress.progress = res.progress / 100
                self.proLabel.string = '资源加载: ' + res.progress + '%'
            })
        }
    },
```

