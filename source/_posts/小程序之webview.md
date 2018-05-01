---
title: 小程序之webview
date: 2018-01-24 18:12:27
tags: [小程序, 前端]
---

#### 小程序
![小程序](/assets/miniprogram.png)

#### 小程序的新功能webview

最近公司开发的小程序又接到新的需求，要跳转第三方支付, 但是在小程序里面打不开第三方应用，只能跳转第三方支付的公众号了。
但是第三方的公众号，和我们不是同一个主体，也不方便让支付方便绑多个小程序。
只能用到小程序的新功能 [web-view](https://mp.weixin.qq.com/debug/wxadoc/dev/component/web-view.html)。 听起来好像native开一个webview一样的,很高级的样子。
然而到了用的时候，才知道不是那么回事，只能硬着头皮上了。
<!-- more -->
#### 上一波简单的代码

```
<template>
<view>
    <web-view src="{{url}}"></web-view>
</view>
</template>
<script>
import wepy from 'wepy'

export default class extends wepy.page {
    config = {
        navigationBarTitleText: '跳转第三方支付页面...'
    }
    data = {
        url: ''
    }

    onLoad(options) {
        if (options && options.url) {
            this.url = decodeURIComponent(options.url)
            this.$apply()
        } else {
            wx.showToast({
                title: '支付URL参数出错',
                icon: 'none'
            })
            setTimeout(() => {
                wx.navigateBack()
            }, 2000)
        }
    }
    onUnload() {
        console.log('web view unload')
    }
}
</script>
```

#### 需要注意的点
1. webview所需要访问的页面，域名必须配置在微信[小程序管理后台](https://mp.weixin.qq.com)-设置-开发设置-业务域名  列表中配置，配置时需要下载一个txt的文件，放置在被域名的根路径下（域名需ICP备案）
2. web-view会自动撑满整个页面， 所以页面中要放其他东西也会被挡住， 所以需要是一个新的页面来放这个web-view
3. url也是可以动态来加载的，整个页面还是可以判断生命周期函数的， 但是webview没有生命周期
4. 因为web-view里面装的是一个网页，而网页和小程序不同，也不通，没办法跳转。 但是微信jssdk对这个做了兼容。在wxsdk升级到1.3后能够通过 `wx.miniProgram.navigateTo``wx.miniProgram.navigateBack`,`wx.miniProgram.switchTab`,`wx.miniProgram.reLaunch`,`wx.miniProgram.redirectTo`,`wx.miniProgram.postMessage 向小程序发送消息`,`wx.miniProgram.getEnv` 这些方法与小程序交互。
5. 万一webview中加载的页面没有继承wx jssdk，就只能通过监听前一个页面的onShow方法，来处理页面的关闭回调了。

![业务域名](/assets/webview-host.jpg)
![业务域名配置](/assets/webview-condition.png)

#### 我这个页面的需求
商品页面，发起支付 -> 然后选择支付方式 下单传递参数 启动轮询监听订单状态 -> 跳到新页面通过url打开第三方支付页面 -> 完成支付后台通知本地应用改变订单状态 -> 前一个页面未关闭，监听到状态后，作出响应跳转到其他页面 -> 完成一次第三方支付。