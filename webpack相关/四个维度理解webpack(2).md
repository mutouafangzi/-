# 四个维度理解webpack(二)
[TOC]
## 搭建开发环境
- 通过webpack启动本地服务的方法
	- webpack watch mode命令行起个参数
	- 使用webpack-dev-server插件
	- express+webpack-dev-middleware 
### webpack watch mode
- 命令行执行`webpack -watch`
- cleanwebpackplugin每次运行清除指定目录
	- 安装`npm i clean-webpack-plugin --save-dev`
	- 配置

```
var CleanWebpackPlugin = require('clean-webpack-plugin');
//指定清除dist目录
new CleanWebpackPlugin(['dist'])
```
### webpack-dev-server
- 一个官方的开发服务器
- 特点
	- live reloading浏览器的自动刷新；
	- 不能直接打包文件，搭建服务器
	- 路径重定向：本地路径切换线上路径；
	- 支持https
	- 浏览器中显示编译错误
	- 接口代理
	- 模块热更新：不刷新浏览器的情况下，更新代码
- 安装 `npm i webpack-dev-server --save-dev`
- 版本问题
	- 如果版本是这样的：webpack-dev-server 3.1.0，webpack 3.10.0，那么执行完结果会报错。
	- 需要卸载的话参考下面命令：

	```
	// webpack
	npm uninstall webpack --save-dev
	// webpack-dev-server
	npm uninstall  webpack-dev-server --save-dev
	```
	- 推荐版本：webpack@3.10.0 & webpack-dev-server@2.9.7

#### devServer配置
- 常见配置项
	- inline：为false时会在页面出现打包进度条；
	- contentBase提供路径：本地服务器在哪个目录搭建页面，一般我们在当前目录即可；
	- port端口，默认是8080
	- historyApiFallback指定规则：当我们搭建spa应用时非常有用，它使用的是HTML5 History Api，任意的跳转或404响应可以指向 index.html 页面；
	- https
	- open：true，则自动打开浏览器。
	- proxy远程接口代理
	- hot模块热更新
	- openpage指定初始页面位置
	- lazy访问资源时才会对资源进行打包
	- overlay遮罩显示编译错误提示

- 配置示例

```
//webpack.config.js中添加配置
devServer: {
	port: 9001,
    contentBase: path.join(__dirname, "dist")
},
//package.config.js中添加配置
"scripts": {
    "server": "webpack-dev-server --open",
    ...
},
```
- 通过`npm run server`在线运行项目
- 运行后发现打开的页面http://localhost:9001/图片都找不到，此时需要再次修改一个配置，在output中添加publicPath项；

```
output: {
	path: path.resolve(__dirname, 'dist'),
    publicPath: '/',
    filename: 'js/[name]-[hash:5].bundle.js',
    //chunkFilename: '[name].chunk.js'
},
```

#### Proxy
- 当您有一个单独的API后端开发服务器，并且想要在同一个域上发送API请求时，则Proxy代理这些 url 。
- 代理远程接口请求；
- 集成的包：http-proxy-middleware
- 使用的参数options
	- target
	- changeOrigin
	- headers
	- logLevel
	- pathRewrite

```
devServer: {
	port: 9001,
    //设置代理
    proxy: {
        '/api': {
            target: 'https://weibo.com',
            changeOrigin: true,
            //在打包信息里看到代理信息
            logLevel: 'debug',
            pathRewrite: {
                '^/comment': '/aj/v6/comment'
            },
            //加入头部信息，比如cookie等
            headers: {
                'Cookie': 'YF-Ugrow-G0=8751d9166f7676afdce9885c6d31cd61; login_sid_t=42867222a30d8c08fd6783561ef57fdf; cross_origin_proto=SSL; YF-V5-G0=35ff6d315d1a536c0891f71721feb16e'
            }
        }
    },
    contentBase: path.join(__dirname, "dist"),
    //inline: false,
    historyApiFallback: true,
},
```



### Module Hot Reloading模块热更新
- 热更新优势：
	- 保持应用的数据状态；
	- 节省调试时间；
	- 样式调试更快；
- 使用方式
	- devServer.hot
	- webpack.HotModuleReplacementPlugin
	- webpack.NamedModulesPlugin
- API
	- module.hot
	- module.hot.accept
	- module.hot.deline
- 配置
```
//devServer中添加
//热更新
hot: true,
hotOnly: true,

//plugin中添加
//模块热更新
new webpack.HotModuleReplacementPlugin(),
new webpack.NamedModulesPlugin()
```

### Source Map调试
- 部署前端之前，开发者通常会对代码进行打包压缩，这样可以减少代码大小，从而有效提高访问速度。然而，压缩代码的报错信息是很难Debug的，因为它的行号和列号已经失真。这时就需要Source Map来还原真实的出错位置了。
- 开启source map方法
	- Devtool参数：七个值
		- eval(Development)
		- eval-source-map(Development)
		- cheap-eval-source-map(Development)
		- cheap-module-eval-source-map(Development)
		- source-map(production)
		- hidden-source-map(production)
		- nosource-source-map(production)
	- 使用插件：
		- webpack.SourceMapDevToolPlugin
		- webpack.EvalSourceMapDevToolPlugin

#### JS Source
```
//在module.exports的json对象中插入
devtool: 'cheap-module-eval-source-map',
```

#### CSS Source
- 在css中使用source map，需要开启相关的loader
	- css-loader.option.sourcemap
	- less-loader.option.sourcemap
	- sass-loader.option.sourcemap

### EsLint检查代码格式
- 安装`npm i eslint eslint-loader eslint-plugin-html eslint-friendly-formatter --save-dev`
- 配置ESLint
	- webpack config
	- .eslintrc.*
	- package.json中的eslintConfig
	- 规则
		- 参照：Javascript Standard Style( https://standardjs.com/)
		- eslint-config-standard 
		- eslint-plugin-promise 
		- eslint-plugin-standard 
		- eslint-plugin-import 
		- eslint-plugin-node
		- 安装`npm i eslint eslint-loader eslint-plugin-html eslint-friendly-formatter --save-dev`以及`npm i eslint-config-standard eslint-plugin-promise eslint-plugin-standard eslint-plugin-node eslint-plugin-import --save-dev`
	- eslint-loader配置
		- options.failOnWaring
		- options.failOnError
		- options.formatter
		- options.outputReport
	- devServer.overlay: 借助这个属性显示错误，这个配置属性用来在编译出错的时候，在浏览器页面上显示错误，默认是false，可设置为true。

```
//webpack.config.js中插入
{
   test: /\.js$/,
   include: [path.resolve(__dirname, './src/')],
   exclude: ['/node_modules/', path.resolve(__dirname, './src/libs/')],
   use: [{
           'loader': 'babel-loader',
           'options': {
               presets: ['env']
           }
       },
       //插入
       {
           loader: 'eslint-loader',
           options: {
               formatter: require('eslint-friendly-formatter')
           }
       },
   ],
}
```
- 检验规则，在根目录下创建.eslintrc.js文件.

```
module.exports = {
    //eslint找到这个标识后，不会再去父文件夹中找eslint的配置文件；
    root: true,
    // 继承eslint-config-standard里面提供的lint规则；
    extends: 'standard',
    // 必须指定解析器，否则错误难消
    //使用babel-eslint来作为eslint的解析器
    //parser: "babel-eslint",

	// 设置解析器选项
    // parserOptions: {      
    // 表明自己的代码是ECMAScript模块
    //   sourceType: 'module'    
    // },
    plugins: [
        // 使用的插件eslint-plugin-html. 写配置文件的时候，可以省略eslint-plugin-
        'html'
    ],
    //定义预定义的全局变量,比如browser: true，这样你在代码中可以放心使用宿主环境给你提供的全局变量。
    env: {
        browser: true,
        node: true,
        es6: true
    },
    rules: {
        // 添加各类校验规则
        //启用额外的规则或者覆盖基础配置中的规则的默认选项
        //设定空格缩进为4的规则
        indent: ['error', 4],
        //结尾不要有新一行的规则
        "eol-last": ['error', 'never'],

        "no-unused-vars": [1, {
            // 允许声明未使用变量
            "vars": "local",
            // 参数不检查
            "args": "none"
        }],
        // 关闭语句强制分号结尾
        //"semi": [0],
        //空行最多不能超过100行
        //"no-multiple-empty-lines": [0, { "max": 100 }],
        //关闭禁止混用tab和空格
        //"no-mixed-spaces-and-tabs": [0],
    }
}
```

#### eslint配置参数
- 在.eslintrc.js中的rules 就是配置规则的

```
rules: {
    "规则名": [规则值, 规则配置]
}
```
- 规则值

```
"off"或者0    //关闭规则关闭
"warn"或者1    //在打开的规则作为警告（不影响退出代码）
"error"或者2    //把规则作为一个错误（退出代码触发时为1）
```
- 本例中用到的规则
```
//设定空格缩进为4的规则
"indent": ['error', 4],
//结尾不要有新一行的规则
"eol-last": ['error', 'never'],

"no-unused-vars": [1, {
    // 允许声明未使用变量
    "vars": "local",
    // 参数不检查
    "args": "none"
}],
// 关闭语句强制分号结尾
//"semi": [0],
//空行最多不能超过100行
//"no-multiple-empty-lines": [0, { "max": 100 }],
//关闭禁止混用tab和空格
//"no-mixed-spaces-and-tabs": [0],
//
"space-before-function-paren": [1, "never"],
// or
// "space-before-function-paren": [0, {
//anonymous 针对匿名函数表达式 (比如 function () {})。
//     "anonymous": "always",
//named 针对命名的函数表达式 (比如 function foo () {})。
//     "named": "always",
//asyncArrow 针对异步的箭头函数表达式 (比如 async () => {})。
//     "asyncArrow": "always"
// }],
```

## 开发环境和生产环境
### 开发环境
- 开发环境中需要的功能：
	- 模块热更新
	- sourceMap
	- 接口代理
	- 代码规范检查
- 生产环境
	- 提取公共代码
	- 压缩混淆
	- 文件压缩或是Base64编码
	- 去除无用代码
- 共同点
	- 同样的入口
	- 同样的代码处理（loader处理）
	- 同样的解析配置
- 怎么做？
	- 使用webpack-merge工具，拼接相关配置
		- webpack.dev.conf.js开发环境配置
		- webpack.prod.conf.js生产环境配置
		- webpack.common.conf.js共同的配置
### 开发环境生产环境分离
- 根目录下创建build文件夹；
- build文件夹下
	- webpack.common.conf.jswebpack.common.js属于公共模块
	- webpack.dev.js开发环境
	- webpack.prod.js生产环境
- package.json添加生成生产环境资源命令和开发环境启动服务命令

```
"server": "webpack-dev-server --env development --open --config build/webpack.common.conf.js",
"build": "webpack --env production --config build/webpack.common.conf.js",
```

#### 注意问题
- 本项目使用的webpack版本是3.10.0版本，webpack-dev-server使用的是@2.6.7；
- 如果在打包运行过程中出现错误，报的错误是node_modules文件夹内的文件的错误，那么一般都是因为版本问题；比如

```
Error: undefined:10:5: missing '}'
    at error (E:\LF_project\webpack-demo\exercise-webpack\webpackDemo20180226\node_modules\css\lib\parse\index.js:62:15)
```
- 本项目使用版本推荐

```
"dependencies": {
    "babel-core": "^6.26.0",
    "babel-loader": "^7.1.3",
    "babel-preset-env": "^1.6.1",
    "lodash": "^4.17.5",
    "lodash-es": "^4.17.5",
    "webpack-dev-server": "^3.0.0"
},
"devDependencies": {
    "autoprefixer": "^8.0.0",
    "awesome-typescript-loader": "^3.5.0",
    "babel-core": "^6.26.0",
    "babel-loader": "^7.1.4",
    "babel-plugin-transform-runtime": "^6.23.0",
    "babel-polyfill": "^6.26.0",
    "babel-preset-env": "^1.6.1",
    "babel-runtime": "^6.26.0",
    "clean-webpack-plugin": "^0.1.19",
    "connect-history-api-fallback": "^1.5.0",
    "css": "^2.2.1",
    "css-loader": "^0.28.7",
    "cssnano": "^3.10.0",
    "eslint": "^4.18.2",
    "eslint-config-standard": "^11.0.0",
    "eslint-friendly-formatter": "^3.0.0",
    "eslint-loader": "^2.0.0",
    "eslint-plugin-html": "^4.0.2",
    "eslint-plugin-import": "^2.9.0",
    "eslint-plugin-node": "^6.0.1",
    "eslint-plugin-promise": "^3.7.0",
    "eslint-plugin-standard": "^3.0.1",
    "express": "^4.16.3",
    "extract-text-webpack-plugin": "^2.1.2",
    "file-loader": "^1.1.6",
    "glob-all": "^3.1.0",
    "html-loader": "^0.5.5",
    "html-webpack-inline-chunk-plugin": "^1.1.1",
    "html-webpack-plugin": "^3.0.6",
    "http-proxy-middleware": "^0.18.0",
    "img-loader": "^2.0.1",
    "jquery": "^3.3.1",
    "less": "^3.0.1",
    "less-loader": "^4.0.6",
    "node-sass": "^4.7.2",
    "opn": "^5.3.0",
    "postcss-cssnext": "^3.1.0",
    "postcss-loader": "^2.1.1",
    "postcss-sprites": "^4.2.1",
    "purify-css": "^1.2.5",
    "purifycss-webpack": "^0.7.0",
    "sass-loader": "^6.0.7",
    "style-loader": "^0.19.1",
    "transform-runtime": "0.0.0",
    "ts-loader": "^4.0.0",
    "typescript": "^2.7.2",
    "url-loader": "^1.0.1",
    "webpack": "^3.10.0",
    "webpack-dev-middleware": "^1.12.0",
    "webpack-dev-server": "^2.9.7",
    "webpack-hot-middleware": "^2.21.2",
    "webpack-merge": "^4.1.2",
    "webpack-visualizer-plugin": "^0.1.11"
},
```


#### 使用middleware搭建开发环境
- 安装依赖
	- Express or Koa第三方服务
	- webpack-dev-middleware
	- webpack-hot-middleware
	- http-proxy-middleware做代理
	- connect-history-api-fallback
	- opn启动后自动打开浏览器
- build文件夹下创建server.js文件

```
const webpack = require("webpack")
const opn = require('opn')

const express = require('express')
const app = express()
const port = 3000

const webpackDevMiddleware = require("webpack-dev-middleware")
const webpackHotMiddleware = require('webpack-hot-middleware')
const proxyMiddleware = require('http-proxy-middleware')
const historyApiFallback = require('connect-history-api-fallback')


const config = require('./webpack.common.conf.js')('development')
const compiler = webpack(config)

const proxyTable = require('./proxy')

for (let context in proxyTable) {
    app.use(proxyMiddleware(context, proxyTable[context]))
}

app.use(historyApiFallback(require("./historyfallback.js")))

app.use(webpackDevMiddleware(compiler, {
    publicPath: config.output.publicPath
}))
app.use(webpackHotMiddleware(compiler))


app.listen(port, function() {
    console.log('成功监听接口' + port)
    opn('http:localhost:' + port)
})
```
- build文件夹下创建proxy.js文件
- build文件夹下创建historyfallback.js文件


## webpack打包结果分析
- 官方工具
	- Offical Analyse Tool
	- webpack-bundle-analyzer
### Offical Analyse Tool
- 命令：
	- Mac下：`webpack --profile --json >stats.json`
	- window下：`webpack --profile --json | Out-file'stats.json' -Encoding OEM`
- 官方网址：http://webpack.github.io/analyse/

### webpack-bundle-analyzer
- 插件方式
	- BundleAnalyzerPlugin
- 命令方式
	- webpack-bundle-analyzer stats.json
	- 安装：`npm i webpack-bundle-analyzer --save-dev`
	- 在webpack.config.js中插入，

```
//引入插件
var BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin

//创建对象
new BundleAnalyzerPlugin(),
```


## 打包速度优化
- 影响打包速度的原因
	- 业务复杂，文件较多
	- 依赖比较多
	- 页面比较多
- 优化办法一
	- 分开vendor和app
	- 使用插件DllPlugin和DllReferencePlugin

```
//生产环境的配置中设定
new webpack.DllReferencePlugin({
	//使用DllPlugin生成的第三方压缩的json文件
	manifest:require('../src/...')
})
```
- 优化办法二
	- UglifyJsPlugin中传入parallel
	- 利用cache
- 优化办法三
	- HappyPack：可以使loader并行处理；
	- 线程池：HappyPack.ThreadPool；
- 优化办法四
	- 开启babel-loader的缓存options.cacheDirectory;
	- 规定打包范围：include和exclude
- 优化其他方法
	- 较少resolve
	- Devtool：去除sourcemap
	- cache-loader缓存所有的loader的记录
	- 升级node和webpack

## 长缓存优化
- 什么是长缓存优化
- 为什么需要长缓存
- 场景一：改变app代码，vendor变化
	- 解决
		- 提取vendor
		- hash->chunkhash
		- 提取webpack runtime

```
//将runtime部分的代码提取到一个单独的文件中，代码如下:
module.exports = {
     entry: {
         app: './src/index.js',
         vender: [
             'lodash'
         ]
     },
     plugins: [
         new webpack.optimize.CommonsChunkPlugin({
             name: 'vender',
             minChunks: Infinity
         }),
         //再次使用了CommonsChunkPlugin，从vender中提取出了名为manifest的运行时代码。
         new webpack.optimize.CommonsChunkPlugin({
             name: 'manifest',
             chunks: ['vender']
         })
     ],
     output: {
         filename: '[name].[chunkhash].js',
         path: path.resolve(__dirname, 'dist')
    }
};
```
- 场景二：引入新模块，模块顺序变化，vendor hash变化
	- 解决
		- 使用NamedChunksPlugin插件
		- 使用NamedModulesPlugin插件
- 场景三：动态引入模块时，vendor hash变化
	- 解决
		- 定义动态模块的chunkname
- 总结
	- 独立打包vendor
	- 抽出mainfest（webpack runtime）
	- 使用NamedChunksPlugin插件
	- 使用NamedModulesPlugin插件
	- 动态模块给定模块名称

## 多页面应用
- 多页面应用特点
	- 多入口entry
	- 多页面html
	- 每个页面不同的chunk
	- 每个页面不同的参数
### 多配置实现多页面应用
- 使用方式
	- webpack3.1.0以上版本支持
	- 使用parallel-webpack工具
- 多配置实现的优点
	- 可以使用parallel-webpack来提高打包速度；
	- 配置更加独立、灵活
- 多配置的缺点
	- 不能多页面之间共享代码
### 单配置实现多页面应用
- 优点
	- 可以共享各个entry之间的公用代码
- 缺点
	- 打包速度比较慢
	- 输出的内容比较复杂

```
const generatePage = function({
	title = '',
	entry = '',
	template = './src/index.html',
	name = '',
	chunks = []
}={}){
	return {
		entry,
		plugins:[
			new HtmlWebpackPlugin({
				chunks,
				temeplate,
				filename:name +'.html'
			})
		]
	}
}

const pages=[
	generatePage({
		title:'paga A'
		entry:{
			a:'./src/pages/a'
		},
		name:'a',
		chunks:['react','a']
	}),
	generatePage({
		title:'paga B'
		entry:{
			b:'./src/pages/b'
		},
		name:'b',
		chunks:['react','b']
	}),
	generatePage({
		title:'paga C'
		entry:{
			c:'./src/pages/c'
		},
		name:'c',
		chunks:['react','c']
	})
]

module.exports = pages.map(page => merge(baseConfig, page))
```