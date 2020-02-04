# webpack
- [webpack](#webpack)
    - [构建工具](#%E6%9E%84%E5%BB%BA%E5%B7%A5%E5%85%B7)
        - [构建工具的功能](#%E6%9E%84%E5%BB%BA%E5%B7%A5%E5%85%B7%E7%9A%84%E5%8A%9F%E8%83%BD)
        - [常用的构建工具](#%E5%B8%B8%E7%94%A8%E7%9A%84%E6%9E%84%E5%BB%BA%E5%B7%A5%E5%85%B7)
    - [webpack介绍](#webpack%E4%BB%8B%E7%BB%8D)
    - [webpack常用配置](#webpack%E5%B8%B8%E7%94%A8%E9%85%8D%E7%BD%AE)
        - [出入口 Entry&Output](#%E5%87%BA%E5%85%A5%E5%8F%A3-entryoutput)
        - [模块 Module](#%E6%A8%A1%E5%9D%97-module)
        - [模块加载（转换）器 Loader](#%E6%A8%A1%E5%9D%97%E5%8A%A0%E8%BD%BD%EF%BC%88%E8%BD%AC%E6%8D%A2%EF%BC%89%E5%99%A8-loader)
        - [插件 Plugin](#%E6%8F%92%E4%BB%B6-plugin)
    - [webpack-dev-server](#webpack-dev-server)
        - [devServer配置项](#devserver%E9%85%8D%E7%BD%AE%E9%A1%B9)
    - [webpack插件](#webpack%E6%8F%92%E4%BB%B6)
        - [extract-text-webpack-plugin](#extract-text-webpack-plugin)
    - [开发/生产环境打包](#%E5%BC%80%E5%8F%91%E7%94%9F%E4%BA%A7%E7%8E%AF%E5%A2%83%E6%89%93%E5%8C%85)

## 构建工具
### 构建工具的功能
- 代码转换：TypeScript 编译成 JavaScript、SCSS 编译成 CSS 等。
- 文件优化：压缩 JavaScript、CSS、HTML 代码，压缩合并图片等。
- 模块合并：在采用模块化的项目里会有很多个模块和文件，需要构建功能把模块分类合并成一个文件。
- 代码分割：提取多个页面的公共代码、提取首屏不需要执行部分的代码让其异步加载。
- 自动刷新：监听本地源代码的变化，自动重新构建、刷新浏览器。
- 代码校验：在代码被提交到仓库前需要校验代码是否符合规范，以及单元测试是否通过。
### 常用的构建工具
- grunt
- gulp
- fis3(百度)
- webpack
## webpack介绍
- WebPack可以看做是模块打包机，它做的事情是：
	- 分析你的项目结构，找到JavaScript模块；
	- 其它的一些浏览器不能直接运行的拓展语言（Scss，TypeScript等）；
	- 并将其转换和打包为合适的格式供浏览器使用；
- WebPack和Grunt以及Gulp相比有什么特性
	- Webpack和另外两个并没有太多的可比性；
	- Gulp/Grunt是一种能够优化前端的开发流程的工具，而WebPack是一种模块化的解决方案；
	- Webpack的优点使得Webpack在很多场景下可以替代Gulp/Grunt类的工具；
	- Grunt和Gulp的工作方式是：在一个配置文件中，指明对某些文件进行类似编译，组合，压缩等任务的具体步骤，工具之后可以自动替你完成这些任务。
	- Webpack的工作方式是：把你的项目当做一个整体，通过一个给定的主文件（如：index.js），Webpack将从这个文件开始找到你的项目的所有依赖文件，使用loaders处理它们，最后打包为一个（或多个）浏览器可识别的JavaScript文件。

## webpack常用配置
### 出入口 Entry&Output
- entry：入口
	- 关联的很多其他需要打包的文件
- output：出口
	- path：文件打出的路径，这个path是nodejs内置的方法；
### 模块 Module
- module：模块，在 Webpack眼里一切皆模块，默认只识别js文件, 如果是其它类型文件利用对应的loader转换为js模块。
	- rules：做很多的规定，比如：加载css、将es6编译成es5;
	- "-loader"其实是可以省略不写的，多个loader之间用“!”连接起来

	```
	module: {
	   //加载器配置
	   loaders: [
	       //.css 文件使用 style-loader 和 css-loader 来处理
	       { test: /\.css$/, loader: 'style-loader!css-loader' },
	       //.js 文件使用 jsx-loader 来编译处理
	       { test: /\.js$/, loader: 'jsx-loader?harmony' },
	       //.scss 文件使用 style-loader、css-loader 和 sass-loader 来编译处理
	       { test: /\.scss$/, loader: 'style!css!sass?sourceMap'},
	       //图片文件使用 url-loader 来处理，小于8kb的直接转为base64
	       { test: /\.(png|jpg)$/, loader: 'url-loader?limit=8192'}
	   ]
	}
	```
	- webpack本身只能加载js模块，如果需要加载其他类型的文件（模块），就需要使用对应的loader进行转换/加载；
	- 如果我们想要在js文件中通过require引入模块，比如css或image，那么就需要在这里配置加载器，这一点对于React来说相当方便，因为可以在组件中使用模块化CSS。而一般的项目中可以不用到这个加载器。
	- module 的作用是添加loaders, 那loaders有什么作用呢？
### 模块加载（转换）器 Loader
- loader：模块加载器，将非js模块包装成webpack能理解的js模块；
- loader用于转换应用程序的资源文件。他们是运行在nodejs下的函数，使用参数来获取一个资源的来源，并且返回一个新的来源（资源的位置）。
- 文件loader:
	- url-loader 像 file loader 一样工作，但如果文件小于限制，可以返回 data URL
	- file-loader 将文件发送到输出文件夹，并返回（相对）URL
- JSON的loader
	- json-loader 加载 JSON 文件（默认包含）
- ES5-6的loader
	- babel-loader 加载 ES2015+ 代码，然后使用 Babel 转译为 ES5
- css的loader
	- style-loader 将模块的导出作为样式添加到 DOM 中
	- css-loader 解析 CSS 文件后，使用 import 加载，并且返回 CSS 代码
	- less-loader 加载和转译 LESS 文件
	- sass-loader 加载和转译 SASS/SCSS 文件
	- stylus-loader 加载和转译 Stylus 文件
- 代码规范loader
	- eslint-loader PreLoader，使用 ESLint 清理代码
- vue的loader
	- vue-loader 加载和转译 Vue 组件

- resolve
	- alias可以用于定义别名，用过seajs等模块工具的都知道alias的作用，比如我们在这里定义了ui这个别名，那么在模块中想引用ui目录下的文件，就可以直接这样写：`require('ui/dialog.js');`不用加上前面的更长的文件路径。
```
resolve: {
	//查找module的话从这里开始查找
    root: 'E:/github/flux-example/src', //绝对路径
    //自动扩展文件后缀名，意味着我们require模块可以省略不写后缀名
    extensions: ['', '.js', '.json', '.scss'],
    //模块别名定义，方便后续直接引用别名，无须多写长长的地址
    alias: {
        AppStore : 'js/stores/AppStores.js',//后续直接 require('AppStore') 即可
        ActionType : 'js/actions/ActionType.js',
        AppAction : 'js/actions/AppAction.js'
    }
}
```

### 插件 Plugin
- plugin：插件，在webpack构建流程中的特定时机插入具有特定功能的代码；

```
//使用了CommonsChunkPlugin用于生成公用代码，不只可以生成一个，还能根据不同页面的文件关系，自由生成多个，例如：
var CommonsChunkPlugin = require("webpack/lib/optimize/CommonsChunkPlugin");
module.exports = {
    entry: {
        p1: "./page1",
        p2: "./page2",
        p3: "./page3",
        ap1: "./admin/page1",
        ap2: "./admin/page2"
    },
    output: {
        filename: "[name].js"
    },
    plugins: [
        new CommonsChunkPlugin("admin-commons.js", ["ap1", "ap2"]),
        new CommonsChunkPlugin("commons.js", ["p1", "p2", "admin-commons.js"])
    ]
};
// 在不同页面用<script>标签引入如下js:
// page1.html: commons.js, p1.js
// page2.html: commons.js, p2.js
// page3.html: p3.js
// admin-page1.html: commons.js, admin-commons.js, ap1.js
// admin-page2.html: commons.js, admin-commons.js, ap2.js
```
- 例：
```
module.exports = {
    devtool: "source-map",    //生成sourcemap,便于开发调试
    entry: getEntry(),         //获取项目入口js文件
    output: {
        path: path.join(__dirname, "dist/js/"), //文件输出目录
        publicPath: "dist/js/",        //用于配置文件发布路径，如CDN或本地服务器
        filename: "[name].js",        //根据入口文件输出的对应多个文件名
    },
    module: {
        //各种加载器，即让各种文件格式可用require引用
        loaders: [
            // { test: /\.css$/, loader: "style-loader!css-loader"},
            // { test: /\.less$/, loader: "style-loader!csss-loader!less-loader"}
        ]
    },
    resolve: {
        //配置别名，在项目中可缩减引用路径
        alias: {
            jquery: srcDir + "/js/lib/jquery.min.js",
            core: srcDir + "/js/core",
            ui: srcDir + "/js/ui"
        }
    },
    plugins: [
        //提供全局的变量，在模块中使用无需用require引入
        new webpack.ProvidePlugin({
            jQuery: "jquery",
            $: "jquery",
            // nie: "nie"
        }),
        //将公共代码抽离出来合并为一个文件
        new CommonsChunkPlugin('common.js'),
        //js文件的压缩
        new uglifyJsPlugin({
            compress: {
                warnings: false
            }
        })
    ]
};
```


## webpack-dev-server
- webpack-dev-server是一个轻量级的服务器，修改文件源码后，自动刷新页面将修改同步到页面 上；
- webpack-dev-server的他爹和他爹的朋友
	- webpack-dev-middleware：作为一个 webpack 中间件，它会开启 watch mode 监听文件变更，并自动地在内存中快速地重新打包、提供新的 bundle，自动编译（watch mode）+速度快（全部走内存）。
	- webpack-hot-middleware：
		- webpack 可以通过配置 webpack.HotModuleReplacementPlugin 插件来开启全局的 HMR 能力；
		- 开启后 bundle 文件会变大一些，因为它加入了一个小型的 HMR 运行时（runtime），当你的应用在运行的时候，webpack 监听到文件变更并重新打包模块时，HMR 会判断这些模块是否接受 update，若允许，则发信号通知应用进行热替换。
- webpack-dev-server是一个小型的Node.js Express服务器，它使用webpack-dev-middleware来服务于webpack的包，除此自外，它还有一个通过Sock.js来连接到服务器的微型运行时
- webpack-dev-server是一个独立的NPM包,你可以通过npm install webpack-dev-server来安装它。
- webpack-dev-server配置
```
var WebpackDevServer = require("webpack-dev-server");
var webpack = require("webpack");

var compiler = webpack({});
var server = new WebpackDevServer(compiler, {

  contentBase: "/path/to/directory",

  hot: true,

  historyApiFallback: false,

  compress: true,

  proxy: {
    "**": "http://localhost:9090"
  },

  setup: function(app) {
 
  },

 
  staticOptions: {
  },
  quiet: false,
  noInfo: false,
  lazy: true,
  filename: "bundle.js",
  watchOptions: {
    aggregateTimeout: 300,
    poll: 1000
  },
  publicPath: "/assets/",
  headers: { "X-Custom-Header": "yes" },
  stats: { colors: true }
});
server.listen(8080, "localhost", function() {});
```

### devServer配置项
- contentBase
	- 即 SERVERROOT，如 “path.join(__dirname, "src/html")”，后续访问 http://localhost:3333/index.html 时，SERVER 会从 src/html 下去查找 index.html 文件。
	- 它可以是单个或多个地址的形式：（若不填写该项，默认为项目根目录。）
```
//单个
contentBase: path.join(__dirname, "public")
//多个：
contentBase: [path.join(__dirname, "public"), path.join(__dirname, "assets")]
```
- port
	- 即监听端口，默认为8080。
- compress
	- 传入一个 boolean 值，通知 SERVER 是否启用 gzip。
- hot
	- 传入一个 boolean 值，通知 SERVER 是否启用 HMR。
- https
	- 可以传入 true 来支持 https 访问，也支持传入自定义的证书：
```
https: true
//也可以传入一个对象，来支持自定义证书
https: {
  key: fs.readFileSync("/path/to/server.key"),
  cert: fs.readFileSync("/path/to/server.crt"),
  ca: fs.readFileSync("/path/to/ca.pem"),
}
```
- proxy
	- 代理配置，适用场景是，除了 webpack-dev-server 的 SERVER（SERVER A） 之外，还有另一个在运行的 SERVER（SERVER B），而我们希望能通过 SERVER A 的相对路径来访问到 SERVER B 上的东西。
```
devServer: {
	contentBase: path.join(__dirname, "src/html"),
    port: 3333,
    hot: true,
    proxy: {
        "/api": "http://localhost:5050"
    }
}
//运行 webpack-dev-server 后，若访问 http://localhost:3333/api/user，则相当于访问 http://localhost:5050/api/user。
```
- publicPath
	- 如同 webpack-dev-middleware 的 publicPath 一样，表示从内存中的哪个路径去存放和检索静态文件；
	- 不过官方文档有一处错误需要堪正 —— 当没有配置 devServer.publicPath 时，默认的 devServer.publicPath 并非根目录，而是 output.publicPath；
	- 这也是为何咱们的例子里压根没写 devServer.publicPath，但还能正常请求到 https://localhost:3333/assets/bundle.js。

- setup
	- webpack-dev-server 的服务应用层使用了 express，故可以通过 express app 的能力来模拟数据回包，devServer.setup 方法就是干这事的：
```
devServer: {
   contentBase: path.join(__dirname, "src/html"),
   port: 3333,
   hot: true,
   setup(app){  //模拟数据
       app.get('/getJSON', function(req, res) {
           res.json({ name: 'vajoy' });
       });
   }
}
```

## webpack插件

### extract-text-webpack-plugin
- 作用：该插件的主要是为了抽离css样式,防止将样式打包在js中引起页面样式加载错乱的现象；
- 安装：`npm install extract-text-webpack-plugin --save-dev`
- 插件参数：
	- use：指需要什么样的loader去编译文件,这里由于源文件是.css所以选择css-loader
	- fallback：编译后用什么loader来提取css文件
	- publicfile：用来覆盖项目路径,生成该css文件的文件路径
- 使用：
```
const ExtractTextPlugin = require("extract-text-webpack-plugin");

module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ExtractTextPlugin.extract({
          fallback: "style-loader", // 编译后用什么loader来提取css文件
          use: "css-loader" // 指需要什么样的loader去编译文件,这里由于源文件是.css所以选择css-loader
        })
      }
    ]
  },
  plugins: [
    new ExtractTextPlugin("styles.css"),
  ]
}
```


## 开发/生产环境打包
- webpack.base.conf.js
- 开启source Map ，帮助调试