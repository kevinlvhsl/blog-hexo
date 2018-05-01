---
title: webpack.md
date: 2018-02-25 23:21:50
tags:
---
![webpack](/assets/webpack.png)
webpack是一个项目打包工具
### 安装   npm install webpack （这里展示webpack1.13.2版本） ### 使用 直接打包单个js文件    `webpack index.js  index.bundle.js`  表示打包index.js  打包后的文件叫做 index.boundle.js 也可以在命令后面直接带参数 如： --watch 等待   --module-bind 'css=style-loader!css-loader' 
<!-- more -->
打包后的文件 以数字（1、2、3...）的方式命名， 并且用webpack自己的__webpack_require__方式引入加载文件```/***/ },
/* 3 */
/***/ function(module, exports, __webpack_require__) {
   
exports = module.exports = __webpack_require__(4)(false);
// imports


// module
exports.push([module.id, "body {\n  padding: 0;\n  background-color: red;\n}", ""]);

// exports
/***/ },
/* 4 */
/***/ function(module, exports) {
    ....
```

### 参数  webpack参数 可以配合npm 脚本功能来配置在package.json 中的scripts下面    将参数都一一配置到命令中，就不需要每次执行webpack命令时带上``` --watch     文件修改后自动打包--progress     打包的时候 输出打包的进度--display-modules  输出打包的文件 和使用的loader（加载到的所有文件都列出来）--display-reasons  输出打包这个模块时 为什么要打包这个模块的原因 （是因为某个文件加载引入了当前模块）--module-bind 'css=style-loader!css-loader'   表示加载到的css文件用css-loader先处理，后用style-loader来处理
--config    指定具体的配置文件   如： webpack  --config  webpack.dev.config.js    将本次打包的配置文件指定为后面的dev.config.js常在不同环境时使用
  --context    指定上下文  就是当前文件运行的目录__dirname， 可以指定到其他目录下去。比如config文件不在更目录下， 可以将context 指定到根目录去，但一般不这么做
  --entry        入口文件  一般都在config文件中配置
  --module-bind   指定处理的loader  --output-path
  --output-file
  --output-chunk-file
  --output-named-chunk-file
  --output-source-map-file
  --output-public-path
  --output-jsonp-function
  --output-pathinfo
  --output-library
  --output-library-target
  --records-input-path
  --records-output-path
  --records-path
  --define
  --target
  --cache                                                                                           [default: true]
  --watch, -w
  --watch which closes when stdin ends
  --watch-aggregate-timeout
  --watch-poll
  --hot
  --debug
  --devtool
  --progress
  --resolve-alias
  --resolve-loader-alias
  --optimize-max-chunks
  --optimize-min-chunk-size
  --optimize-minimize
  --optimize-occurence-order
  --optimize-dedupe
  --prefetch
  --provide
  --labeled-modules
  --plugin
  --bail
  --profile
  -d                                    shortcut for --debug --devtool sourcemap --output-pathinfo
  -p                                    shortcut for --optimize-minimize
  --json, -j
  --colors, -c                    打包时命令行输出的字是彩色的
  --sort-modules-by
  --sort-chunks-by
  --sort-assets-by
  --hide-modules
  --display-exclude
  --display-modules         模块绑定，打印模块之间的引入关系
  --display-chunks
  --display-error-details
  --display-origins
  --display-cached
  --display-cached-assets
  --display-reasons, --verbose, -v
```
### webpack配置
+ entry 
入口可以有三种配置方式
  1、 字符串  `entry: './src.main.js'`
  2、数组   `entry: ['./src/manage.js', './src.app.js'] ` 可以指定多个打包入口
  3、对象方式  在多页面应用中常用。 比如一个项目有pc端入口， 和 移动端入口  或管理后台入口 ```
    entry: {        page1: './src.main.js',
        page2: ['./src/manage.js', './src.app.js']
    }
```
+ output            设置输出文件相关参数    
  + filename        输出文件的名字 可以取几个占位符变量来修改文件的hash和名称  、      
    -- hash   打包的hash
    --chunkhash        文件的hash  文件修改了，hash就会变，没修改则不变。 作用类似文件版本号
    --name            当前入口的名称， entry中指定的入口文件的名字 

  + path        
    引用打包后文件的路径，'./dist' 相对路径        
  + publicPath： 设置文件上线后引用的前缀路径   默认是可以不用配置
    pablickPath： 'https://cdn.js/' 表示打包以后，在index.html中引入的文件是用这个地址下去引用文件 


+ plugins  插件  是一个数组     webpack插件功能， 非常有用，最常用的  合并公共文件、压缩、混淆、HTML```var htmlWebpackPlugin = require('html-webpack-plubin');
entry: {
    main: './src.main.js',
    a: ['./src.a.js'],
    b: ['./src.b.js'],
    c: ['./src.c.js'],
},
plugins: [
  new webpack.optimize.CommonsChunkPlugin({
    ...
  }),
  * htmlWebpackPlugin 可以用来给多页应用指定多个入口 *
  new htmlWebpackPlugin({
      template: 'index.html',        // 指定模板html文件
      filename: 'index.html',        // 指定输出后文件的名称（这里可以使用hash name 等占位符）
      inject: 'head',                // 指定文件输出在index.html的位置  head(head标签中)、body(body标签中)  和false  false表示不输出
      minify: {
          removeComment: true,       // 压缩， 并删除注释
          collapseWhitespace: true,       // 压缩， 并删除空格
          // 。。。 其他配置参考：  https://www.npmjs.com/package/html-webpack-plugin
      },
      chunks: ['main'],         // 当前页面引入的chunk 在多页面应时非常有用
      title: '自定义参数',             // 自定义参数可以在模板文件中使用 <%= htmlWebpackPlugin.options.title %> 来取值
      date: new Date(),             // 自定义参数可以在模板文件中使用 <%= htmlWebpackPlugin.options.date %> 来取值
  }),
  // 多页面应用，多口案例
  new htmlWebpackPlugin({
    template: 'index.html',
    filename: 'a.html',            // 这里是多页应用，生成b入口页面
    title: 'this is a.html',
    chunks: ['main', 'a'],          // 这里只会加载指定的chunk文件，不会加载a.js
  }),
  new htmlWebpackPlugin({
    template: 'index.html',
    filename: 'b.html',            // 这里是多页应用，生成b入口页面
    title: 'this is b.html',
    chunks: ['main', 'b'],          // 这里只会加载指定的chunk文件，不会加载a.js
  }),
  new htmlWebpackPlugin({
    template: 'index.html',
    filename: 'c.html',            // 这里是多页应用，生成c入口页面
    title: 'this is c.html',
    chunks: ['main', 'c'],     
    excludeChunks: ['a', 'b'],        // 排除哪些chunk 文件（因为如果chunk很多的话一个个包含会麻烦，直接排除更方便）
  }),
]


// index.html
<head>
    <title><%= htmlWebpackPlugin.options.title %></title>  <!-- 这里就会拿到插件中传过来的自定义参数 -->
</head>
<body>
    <%= htmlWebpackPlugin.files %>  <!-- 可以拿到所有files的内容  如 htmlWebpackPlugin.files.main.entry -->
    <script src="<%= htmlWebpackPlugin.files.main.entry %>" ></script>  这样可自己定义main.js插入在文件的哪个位置
</body>
``` 

&&& 官方还提供一个案例，直接将打包后的文件，输出到index.html中 以inline的方式插入到HTML中

``` 
doctype html
  html  
    head    
      meta(http-equiv="Content-type" content="text/html; charset=utf-8")    
      title #{htmlWebpackPlugin.options.title}  
    body    
      each cssFile in htmlWebpackPlugin.files.css      
        style !{compilation.assets[cssFile.substr(htmlWebpackPlugin.files.publicPath.length)].source()}    
      each jsFile in htmlWebpackPlugin.files.js      
        script(type="text/javascript") !{compilation.assets[jsFile.substr(htmlWebpackPlugin.files.publicPath.length)].source()}
```

compilation 就是能拿到打包以后的文件的索引， 在HTML中插入inline的chunk，并且在body中inject ：false， 手动插入需要引入的chunk


### loaders机制  [loaders](https://webpack.js.org/loaders/)
  对应的模块类型用对应的loader来处理， webpack默认是只认识js文件Using

+ LoadersThere are three ways to use loaders in your application:  三种使用方式
  1、Configuration (recommended): Specify them in your webpack.config.js file.  配置文件中配置（推荐方式）
  2、module.rulesInline: Specify them explicitly in each import statement.  在require的时候指定
  3、loader import Styles from 'style-loader!css-loader?modules!./styles.css';CLI: Specify them within a shell command.    命令行中做参数传 > `webpack --module-bind jade-loader --module-bind 'css=style-loader!css-loader' `

css文件、sass、less、stylus 文件 需要通过css相关的loader来处理 如：    
    style-loader：将css代码转成一个style标签插入到html页面
    css-loader： 识别css模块，以css语法来解析模块内容
    sass-loader： 以sass语法先解析sass模块内容
+ LoaderFeatures特性
  1.Loaders can be chained. They are applied in a pipeline to the resource. A chain of loaders are executed in reverse order. The first loader in a chain of loaders returns a value to the next. At the end loader, webpack expects JavaScript to be returned.  可以串式链接
  2.Loaders can be synchronous or asynchronous.    可以是同步和异步
  3.Loaders run in Node.js and can do everything that’s possible there.  可以在nodejs中使用
  4.Loaders accept query parameters. This can be used to pass configuration to the loader. 可以传query参数 配置
  5.Loaders can also be configured with an options object.    可以传不同的配置项
  6.Normal modules can export a loader in addition to the normal main via package.json with the loader field.  还可以获取webpack配置
  7.Plugins can give loaders more features.  插件可以给loader更多的特性
  8.Loaders can emit additional arbitrary files.    可以生产额外的文件在配置文件中使用

这里主要讲一下babel-loader的使用和常见的loader使用
  + 安装: babel
  `npm install babel-loadre babel-core --save-dev`

  + 配置使用
  ``` 
  module.exports = {​
    loaders: {​}​
  }​
  webpack2/3 中 用rules
  module: {
    rules: [
      { test: /\.js$/, exclude: /node_modules/, loader: "babel-loader" }  
      { test: /\.css$/, use: 'css-loader' },
    ]}​
  ```

  loaders 有几个参数
test 正则表达式或string来指定要处理的文件或路径匹配
exclude 排除不要处理的目录  如./node_modulesinclude  包含要处理的目录
loader  指定loader名称loaders  
loaders为数组的时候用

postcss 是后处理工具，
  css-loader 的参数importLoaders=1 表示经过前面的loader数为1个 意思是import进来的文件也先经过postcss处理再import进来










