# 四个维度理解webpack(一)
[TOC]

- webpack@3.10.0
- 下载`npm i webpack@3.10.0 --save-dev`
## 模块化开发
### JS模块化
- 命名空间
- commonJS
	- 一个文件为一个模块；
	- 通过module.exports暴露模块接口；
	- 通过require引入模块；
- AMD/CMD/UMD
	- AMD异步模块定义；
		- 使用define定义模块；
		- 使用require加载模块；
		- 例如：requireJS；
		- 依赖前置，提前执行；
	- CDM同步模块定义
		- 一个文件一个模块；
		- 使用define定义模块；
		- 使用require加载模块；
		- 例如：seaJS；
		- 尽可能懒执行；
	- UMD通用模块定义
		- 判断是否支持CommonJS
		- 判断是否支持AMD
		- 如果都没有，使用全局变量
- ES6 module
	- 一个文件一个模块；
	- 使用export或者export default(定义什么接收什么)暴露模块；
	- 引入模块使用`import...from...`；
- webpack支持：AMD、ES6（推荐的）、CommonJS

### CSS模块化
- css设计模式
	- OOCSS面向对象的css
		- 在不同地方使用的css类
	- SMACSS可扩展css
	- Atomic CSS原子化css
	- MCSS多层级的css
	- AMCSS属性css
	- BEM：block、element、modifier
- CSS Modules

## webpack了解
### 环境准备
- 命令行工具
- 安装必须：node、webpack@3.10.0
### 版本
#### 功能进化
- 1版本：
	- 编译、打包；
	- HMR（模块化更新）
	- 代码分割
	- 文件处理
- 2版本
	- tree shaking
	- ES module
	- 动态Import()
	- 新的文档
- 3版本
	- Scope Hoisting(作用域提升)，运行速度提升
	- Magic Comments(配合动态的import使用)
#### 版本迁移
![Alt text](./1519724715378.png)

## webpack概念
### entry
- 代码的入口
- 打包的入口
- 单个或者多个entry
	- 多页面应用程序
	- 单页面：业务代码和逻辑代码
- entry有几种使用方法：
  - 字符串

```
entry:__dirname+'/src/script/main.js'
//对应
output:{
  path:__dirname+'/dist/js',
  filename:'bundle.js'
}
```
  - 数组

```
entry:[
  __dirname+'/src/script/main.js',
  __dirname+'/src/script/a.js'
]
//对应
output:{
  path:__dirname+'/dist/js',
  filename:'bundle.js'
}
```
  - 对象

```
entry:{
	main:__dirname+'/src/script/main.js',
	a:__dirname+'/src/script/a.js'
}
//对应
output:{
  path:__dirname+'/dist/js',
  filename:'[name]-[hash].js'
}
//or 
output:{path:__dirname+'/dist/js',filename:'[name]-[chunkhash].js'}
```

### Output
- 特点
	- 打包生成的文件（bunble）
	- 一个或多个
	- 自定义规则
- output是指打包生成的文件
- entry中输入多个chunk时，为确保文件名唯一避免相互覆盖使用占位符命名filename；
- 生成的名字有三种占位符
  - [name]是chunk的name；
  - [hash]是本次打包的hash值，hash值相同；
  - [chunkhash]是每个chunk的hash值，不同文件同次打包不相同，保证文件的唯一性，只有改变文件中的内容时hash才变化，未做改变的文件hash值不变；
  - [ext]是源文件的后缀名占位符

### Loaders
- 特点
	- 处理文件
	- 转化为模块
- 常用的loader
	- 编译相关：babel-loader、ts-loader
	- 样式相关：style-loader、css-loader、less-loader、postcss-loader
	- 文件相关：file-loader、url-loader

### Plugins
- 特点
	- 参与打包整个过程
	- 打包优化和压缩
	- 配置编译时的变量
	- 极其灵活
- 常用的Plugins
	- 优化相关：CommonsChunkPlugin、UglifyjsWebpackPlugin等
	- 功能相关：ExtractTextWebpackPlugin、HtmlWebpackPlugin、HotModuleReplacementPlugin、CopyWebpackPlugin等

### 名词
- chunk代码块
- bundle打包后的代码
- module模块

## webpack使用
- webpack命令
	- webpack -h 查询命令
	- webpack -v 查询版本
- webpack配置
	- 指定webpack配置文件：`webpack --config webpack.config.js`；
- 第三方脚手架

### 打包JS
- 创建基础配置文件`webpack.config.js`
- 指定配置文件名称`webpack --config webpack.config.js`
- 配置文件

```
module.exports = {
    entry: {
        app: './app.js'
    },
    output: {
        filename: '[name].[hash:5].js'
    }
}
```
### 编译ES6/7
- 安装`npm i --save-dev babel-loader babel-core`
- babel-loader
- babel-presets
	- 指定babel按照什么规范打包语法；
	- 一个规范的总结；
	- 设定preset的targets参数，指定编译后的需要匹配的目标：

```
use: {
	loader: 'babel-loader',
    options: {
	   presets: [
             ['env', {
	             'targets': {
                     'browsers': ['> 1%', 'last 2 versions']
                 }
             }]
         ]
     }
},
```
#### babel插件
- babel polyfill、 babel runtime transform等，处理兼容的模块内的一些方法
##### babel polyfill
- 全局垫片：一旦引入，全局可调用；
- 为开发应用准备；
- 安装`npm i babel-polyfill --save`
- 引入：`import 'babel-polyfill'`

##### babel runtime transform
- 局部垫片
- 为开发框架准备
	- 不会污染全局
- 安装`npm i babel-plugin-transform-runtime --save-dev`,`npm i babel-runtime --save`
- 配置在：.babelrc文件中

### Typesript
- 安装：`npm i typescript ts-loader --save-dev`，`npm i typescript awesome-typescript-loader --save-dev`
- 配置：tsconfig.json或者webpack.config.js
- 配置常用选项
	- compilerOptions
	- include
	- exclude

### 提取共用代码
- CommonsChunkPlugin的配置
	- `name和names`：chunk的名称，如果这个chunk已经在entry中定义，该chunk会被直接提取；如果没有定义，则生成一个空的chunk来提取其他所有chunk的公共代码。
	- `filename`：可以指定提取出的公共代码的文件名称，可以使用output配置项中文件名的占位符。未定义时使用name作为文件名。
	- `chunks`：可以指定要提取公共模块的源chunks，指定的chunk必须是公共chunk的子模块，如果没有指定则使用所有entry中定义的入口chunk。
	- `minChunks`：在一个模块被提取到公共chunk之前，它必须被最少minChunks个chunk所包含。（通俗的说就是一个模块至少要被minChunks个模块所引用，才能被提取到公共模块。）
		- 该数字必须不小于2或者不大于chunks的个数。默认值等于chunks的个数。
		- 如果指定了Infinity，则创建一个公共chunk，但是不包含任何模块，内部是一些webpack生成的runtime代码和chunk自身包含的模块（如果chunk存在的话）。
- 优点
	- 减少代码冗余
	- 提高加载速度
- 安装`npm i `
- 配置
	- options.name or options.names
	- options.filename
	- options.minChunks
	- options.chunks
	- options.children
	- options.deepChildren
	- options.async

```
{
	plugins:[
		new webpack.optimize.CommonsChunkPlugin(option)
	]
}
```
- 场景
	- 单页应用
	- 单页 + 第三方依赖
	- 单页 + 第三方依赖 + webpack生成代码
#### 用webpack的CommonsChunkPlugin提取公共代码的3种方式
- 方式一：传入字符串参数
```
// 默认会把所有入口节点的公共代码提取出来,生成一个common.js
new webpack.optimize.CommonsChunkPlugin('common.js')
```
- 方式二：有选择的提取公共代码

```
//提供公共代码，默认会把所有入口节点的公共代码提取出来,生成一个common.js
// 只提取main节点和index节点
new webpack.optimize.CommonsChunkPlugin('common.js',['main','index']), 
```

- 方式三：有选择性的提取（对象方式传参）
```
new webpack.optimize.CommonsChunkPlugin({
    name:'common', // 注意不要.js后缀
    chunks:['main','user','index']
}),
```

### 代码分割和懒加载
- 通过写代码的规则来规定某些要代码分割；
#### 代码分割
- webpack内置方法
	- require.ensure参数（四个）
		- []:dependencies：引入模块，不执行
		- callback：执行引入模块
		- errorCallback：可省略
		- chunkName：接受的参数
	- require.include参数
- ES2015的loader
	- system.import() -> import()
- 场景
	- 分离业务代码 和 第三方依赖
	- 分离业务代码 和 业务公共代码和第三方依赖
	- 分离首次加载 和 访问后加载的代码
#### 懒加载

## 处理CSS文件
### 引入css
#### style-loader
- style-loader：创建style标签，在页面中；
	- style-loader/url：将css文件在html中变成link标签引入；
	- style-loader/useable：设置css可用不可用；

```
//webpack.config.js
{
	test: /\.css$/,
    use: [{
        loader: 'style-loader/useable'
    }, {
        loader: 'css-loader'
    }]
},
//app.js
import base from './css/base.css'
import common from './css/common.css'
var flag = false;
setInterval(function() {
    if (flag) {
        base.unuse();
    } else {
        base.use()
    }
    flag = !flag
}, 500)
```
- style-loader的options
	- insertAt：插入的位置；
	- insertInto：插入到dom；
	- singleton：是否只使用一个style标签；
	- transform：转化，浏览器环境下，插入到页面前面；

```
{
	test: /\.css$/,
    use: [{
        loader: 'style-loader',
        options: {
            insertInto: '#app',
            singleton: true,
            transform: './css.transform.js'
        }
    }, {
        loader: 'css-loader'
    }]
},
```
#### css-loader
- css-loader：是如何import一个css模块；
- css-loader的options
	- alias：解析的别名
	- importLoader：@import
	- minimize：是否压缩
	- modules：启用css-modules
	- localIdentName：打包后的class名称；
	- getLocalIdent：保证打包后的class名称和原class名称一致；
- 使用`localIdentName:'[path][name]_[local]--[hash:base64:5]'`来设置打包后的css文件中class的名称
	- path：被打包文件的路径；
	- name：被打包文件的名称；
	- local：被打包文件的class名；

```
{
	loader: 'css-loader',
    options: {
        minimize: true,
        modules: true,
        localIdentName: '[path][name]_[local]_[hash:base64:5]'
    }
}
```
### css modules
- 安装`npm i postcss postcss-loader autoprefixer cssnano postcss-cssnext --save-dev`
- css-nano：优化压缩css文件，css-loader底层就是用这个；
- Autoprefixer：自动添加css的前缀以匹配任何文件；
	- 配置

```
{
	loader: 'postcss-loader',
    options: {
        ident: 'postcss',
        plugins: [
            require('autoprefixer')(),
            require('postcss-cssnext')()
        ]
    }
},
```
- Broswerslist指定匹配哪些浏览器
	- 所有插件都公用：package.json

	```
	"browserslist":[
		">=1%",
		"last 2 versions"
	]
	```
	- 添加文件：.browserslistrc
- postcss-import将引入的css插入到当前的css文件中；
- postcss-url配合postcss-import使用；
	
### 配置less/sass
- 安装`npm i less-loader sass-loader less node-sass --save-dev`


### 提取css代码
- 方法一：extract-loader
- 方法二：使用ExtractTextWebpackPlugin（推荐）
	- 安装`npm i extract-text-webpack-plugin --save-dev`
	- 配置

```
var ExtractTextWebpackPlugin = require('extract-text-webpack-plugin')
//配置插件
plugins: [
	 new ExtractTextWebpackPlugin({
        filename: '[name].min.css',
        allChunks: false
    }),
    new PurifyCSS({
        paths: glob.sync([
            path.join(__dirname, './*.html'),
            path.join(__dirname, "./src/*.js")
        ])

    }),
    //Tree Shaking不使用的代码不打包
    new webpack.optimize.UglifyJsPlugin()
]
//配置loader
{
	test: /\.less$/,
    use: ExtractTextWebpackPlugin.extract({
        fallback: {
            loader: 'style-loader',
            options: {
                singleton: true,
                transform: './css.transform.js'
            }
        },
        use: [{
            loader: 'css-loader',
            options: {
                // 压缩
                minimize: true,
                modules: true,
                localIdentName: '[local]',
            }
        }, {
            loader: 'less-loader'
        }]
    })
}
```
- 生成app.min.css文件后是不会自动加载进入index.html中的，需要我们手动加载进去。

```
<link rel="stylesheet" href="./dist/app.min.css">
```

## Tree Shaking
- 不使用的代码不打包进入；
- 包括：JS Tree Shaking和CSS Tree Shaking
- 使用场景
	- 常规优化
	- 引入第三方库的某一个功能
### JS Tree Shaking
- 使用插件帮助：Webpack.optimize.uglifyJS

```
var webpack = require('webpack')
new Webpack.optimize.UglifyJsPlugin()
```
### CSS tree shaking
- 使用purifycss-webpack
	- 安装 `npm i purifycss-webpack glob-all purify-css --save-dev`
	- 配置
		- path:glob.sync([])
- 使用glob-all处理多路径

```
var PurifyCSS = require("purifycss-webpack");
new PurifyCSS({
	 paths: glob.sync([
        path.join(__dirname, './*.html'),
        path.join(__dirname, "./src/*.js")
    ])
}),
```

## 文件处理
- 处理文件内容
	- 图片文件
	- 字体文件
	- 第三方JS库

### 图片处理
- css中引入的图片的处理
	- 自动合成雪碧图
	- 压缩图片
	- Base64编码
- 使用：file-loader，url-loader，img-loader，postcss-sprites
- 安装`npm i file-loader url-loader img-loader postcss-sprites --save-dev`
- 图片打包的配置

```
//
{
	test: /\.(jpg|img|png|gif|svg|jpeg)$/,
    use: [{
         loader: 'file-loader',
         options: {
             publicPath: '',
             //outputPath: '',
             //将打包生成的文件关联原文件夹
             useRelativePath: true
         }

		//如果想根据图片大小来进行打包，可以这样设置
		loader: 'url-loader',
        options: {
        //当图片大小小于限制时会自动转成 base64 码引用
            limit: 200
        }
    }]
}

//注意，输出的路径要将dist/注掉，不然打包出来的文件路径会多出来一层dist/
 output: {
	path: path.resolve(__dirname, 'dist'),
    //publicPath: 'dist/',
    filename: '[name].bundle.js',
    //chunkFilename: '[name].chunk.js'
},
```
- 图片合成雪碧图的配置

```
//在postcss-loader的配置中引入postcss-sprites，进行设置
{
	//处理css文件
    loader: 'postcss-loader',
    options: {
        ident: 'postcss',
        plugins: [
            //打包生成雪碧图的配置
            require('postcss-sprites')({
                //雪碧图打包后的地址
				spritePath: 'dist/assets/imgs/sprites',
                //适应高分辨率视口
                retina: true
            }),
            //require('autoprefixer')(),
            require('postcss-cssnext')()
        ]
    }
}

//修改雪碧图的名字
{        
	loader: 'url-loader',
    options: {
        name: '[name]-min.[ext]',
        limit: 200,
        //publicPath: '',
        //outputPath: '',
        //将打包生成的文件关联原文件夹
        useRelativePath: true
    }
},
```
### 字体文件
- 字体文件后缀名
	- eot,woff2,ttf,svg

```
{
	test:/\.(eot|woff2?|ttf|svg)$/,
	use:[
		{
			loader:'url-loader',
			options:{
				name: '[name]-min.[ext]',
                limit: 200,
                //将打包生成的文件关联原文件夹
                useRelativePath: true
			}
		}
	]
}
```
- fa-loader

### 第三方JS库
- 方法一：使用webpack.providePlugin插件
	- 使用npm下载第三方插件后的配置

```
//通过npm下载的第三方插件后，使用配置信息进行配置
new webpack.ProvidePlugin({
	$:'jquery'
})
```
	- 使用本地第三方js文件的配置：

```
//将第三方js文件放入进入本地的文件夹，使用script标签进行引入。
//插入resolve
resolve:{
	alias: {
		//$代表精确匹配
		jquery$:path.resolve(__dirname, 'src/libs/jquery.min.js')
		//src/libs/jquery.min.js里面放着jquery
}
```
- 方法二：使用imports-loader
	- 安装 `npm i imports-loader`

```
{
	test:path.resolve(_dirname,'src/app.js');
	use:[
		{
			loader:'imports-loader',
			options:{
				$: 'jquery'
			}
		}
	]
}
```
- 方法三：window对象

## 处理HTML文件
- 自动生成HTML
- 场景优化
### 生成HTML
- htmlwebpackplugin参数
	- template
	- filename
	- minify
	- chunks
	- inject
- 安装`npm i html-webpack-plugin --save-dev`
- 配置：

```
var htmlWebpackPlugin = require('html-webpack-plugin')

//处理html在plugin中
new htmlWebpackPlugin({
    filename: 'index.html',
    template: './index.html',
    //inject: false,
    chunks: ['app'],
    //压缩html文件
    minify: {
        //消除空格
        collapseWhitespace: true
    }
})
```
### html中引入的图片
- 使用html-loader，安装`npm i html-loader --save-dev`
- html-loader的options
	- attrs: [img:src]；

```
{
	test: /\.html$/,
    use: [{
        loader: 'html-loader',
        options: {
            attrs: ['img:src', 'img:data-src']
        }
    }]
}

//url-loader中配置修改
{     
   //引入图片的处理
    loader: 'url-loader',
    options: {
        name: '[name]-min.[ext]',
        limit: 20,
        //publicPath: '',
        outputPath: 'assets/imgs/',
        //将打包生成的文件关联原文件夹,就是原文件夹的位置和包裹关系和打包后的一样
        //useRelativePath: true
    }
}
```
### 配合优化
- 提前载入webpack加载代码：
	- inline-manifest-webpack-plugin在和html-loader配合时有bug（不推荐）；
	- html-webpack-inline-chunk-plugin（推荐）
- 安装`npm i html-webpack-inline-chunk-plugin --save-dev`，`npm i babel-core babel-loader babel-preset-env --save-dev`
- 配置：
```
//loader中：
{
	test: /\.js$/,
    use: {
        'loader': 'babel-loader',
        'options': {
	        presets: ['env']
        }
    },
    exclude: '/node_modules/'
}

//plugin中
//提取公共代码的插件
new webpack.optimize.CommonsChunkPlugin({
    //公共模块打包后的名称
    name: 'manifest',
    //指定被引入几次的代码会被打包
    minChunks: 2,
    chunks: ['pageA', 'pageB']
}),
new HtmlInlinkChunkPlugin({
    inlineChunks: ['manifest']
}),

//修改htmlWebpackPlugin配置
//处理html
new htmlWebpackPlugin({
    //取消这个
    //chunks: ['app'],
}),
```

