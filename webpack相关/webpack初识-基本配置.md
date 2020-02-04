# webpack基本配置
## 准备步骤
- 初始化
```
//新建文件夹，文件夹内执行命令
npm init
//局部下载webpack
npm install webpack  --save-dev
```
- 建立相关的文件夹，如src，img，本例中：dist存放打包后文件，src下分为script和style分别存放js文件和css文件；
- 创建配置文件
  - 根目录下创建webpack.config.js

```
//使用commonjs引入
module.exports ={
  entry: './src/script/main.js',
	output: {
    //webpack3要求绝对路径
		path: __dirname + '/dist/js',
		filename: 'bundle.js'
	}
}
```
- 修改package.json里的

```
"scripts": {
  "test": "echo \"Error: no test specified\" && exit 1",
  "webpack": "webpack --config webpack.config.js --progress --display-modules --colors --display-reasons"
}
```
- 运行webpack，成功打包

- webpack.config.js的作用
  - 可以不命名为webpack.config.js，但是webpack执行时是找不到配置文件的，所以如果命名为webpack.dev.config.js，那么要有一个配置`webpack --config webpack.dev.config.js`，执行npm run webpack才会成功。

## entry和output配置
### entry配置
- entry是指需要打包的文件；
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

### output的配置
- output是指打包生成的文件
- entry中输入多个chunk时，为确保文件名唯一避免相互覆盖使用占位符命名filename；
- 三种占位符
  - [name]是chunk的name；
  - [hash]是本次打包的hash值，hash值相同；
  - [chunkhash]是每个chunk的hash值，不同文件同次打包不相同，保证文件的唯一性，只有改变文件中的内容时hash才变化，未做改变的文件hash值不变；
  - [ext]是源文件的后缀名占位符

## plugin插件
### 处理html的插件
#### html-webpack-plugin
- 使用html-webpack-plugin插件来解决，webpack.config.js的出口文件名用占位符自动生成的js打包文件名（[chunkhash]）每一次都不一样的问题；

```
var htmlWebpackPlugin = require('html-webpack-plugin')
//modules.exprots中的添加插件配置
plugins: [
    //html中引入script，如果是hash或者chunkhash生成的js，则src每次都要修改，避免修改的方法就是使用webpack的插件
    new htmlWebpackPlugin()
]
```
- 此时可以将html中引入的js文件自动进行更改，而不用每次生成都要修改生成的hash值；

```
<script type="text/javascript" src="main-031075bb272d3409c40c.js"></script>
<script type="text/javascript" src="m-031075bb272d3409c40c.js"></script>
```
- 但是此时在dist中生成的打包后的html和原本的html文件没有任何的关联，为了将其进行关联，可在插件对象中传入参数：

```
new htmlWebpackPlugin({
  //此时会默认找到根目录下的index.html文件
  template: 'index.html'
})
```
- 为了使得js和html文件不打包到一起，可以在配置的output中进行修改：

```
output: {
      path: __dirname + '/dist',
      filename: 'js/[name]-[hash].js'
  },
```
- 在配置文件webpack.config.js的插件对象中传入的参数，可以使用ejs在index.html中获取到

```
new htmlWebpackPlugin({
      //添加
      title: 'webpack is good'
  })

//index.html中使用ejs
<head>
    <meta charset="UTF-8">
    <title>
        <%= htmlWebpackPlugin.options.title %>
    </title>
</head>
```
- 在htmlWebpackPlugin对象中添加minify配置，是对html的代码进行压缩的配置；
```
//对对象上代码进行压缩
minify: {
    //参考https://www.npmjs.com/package/html-webpack-plugin
    //删除注释
    removeComments: true,
    //删除空格
    collapseWhitespace: true
}
```
- 如果需要将相同的的html进行不同的打包，则可以创建多个htmlWebpackPlugin对象

```
plugins: [
        //html中引入script，如果是hash或者chunkhash生成的js，则src每次都要修改，避免修改的方法就是使用webpack的插件
        new htmlWebpackPlugin({
            //关联原始html模板文件
            template: 'index.html',
            //指定唯一的html名称[chunhash]
            filename: 'a.html',
            //设置js脚本在head还是body里
            inject: 'head',
            title: 'this is a',
            //对象上代码进行压缩
            minify: {
                //参考https://www.npmjs.com/package/html-webpack-plugin
                //删除注释
                removeComments: true,
                //删除空格
                collapseWhitespace: true
            }
        }),
        new htmlWebpackPlugin({
            //关联原始html模板文件
            template: 'index.html',
            //指定唯一的html名称[chunhash]
            filename: 'b.html',
            //设置js脚本在head还是body里
            inject: 'head',
            title: 'this is b'
        }),
        new htmlWebpackPlugin({
            //关联原始html模板文件
            template: 'index.html',
            //指定唯一的html名称[chunhash]
            filename: 'c.html',
            //设置打包成的模块，是插入在页面的head还是body里
            inject: 'body',
            title: 'this is c'
        })
    ]
```
- 倘若有多个js，而且不同的js被不同的html需要，name使用`chunks:['main','a']`，表示引入的js是哪些；
- 也可以使用excludeChunks，来指定排除哪些js不用；
- 此时可以引入不同的js文件，但是是通过script标签中的src属性获得的，这样获得的缺点是要发送多次请求。为了减少向服务器发送的请求，可以使用内嵌式：

```
//修改webpack.config.js里options中的
inject: false

//在index.html的head中添加
<script type="text/javascript">
    <%= 
    compilation.assets[htmlWebpackPlugin.files.chunks.main.entry.substr(htmlWebpackPlugin.files.publicPath.length)].source() %>
</script>
//讲解：htmlWebpackPlugin.files.chunks可以得到所有依赖的js文件，其中入口js文件就htmlWebpackPlugin.files.chunks.main.entry取到，但是此时的js带着‘http://’，所以，使用htmlWebpackPlugin.files.publicPath.length来截取后面非上线的文件路径，便可得到要插入html中的js代码。而compilation.assets是webpack的一个语法，不了解。

//index.html中的body里面添加，就是指除了入口文件main.js之外，其他文件仍使用script标签来引入，即使用外链式引入。
 <% for(var k in htmlWebpackPlugin.files.chunks){ %>
    <% if(k !== 'main'){ %>
        <script type="text/javascript" src="<%= htmlWebpackPlugin.files.chunks[k].entry %>"></script>
    <% } %>
  <% } %>
```

## loader加载器
- 学习网址：https://webpack.js.org/concepts/loaders/；
- Webpack 本身只能处理原生的js模块，但是loader转换器可以将各种类型的资源转换成js模块。这样，任何资源都可以成为Webpack可以处理的模块。
### 使用loader的三种方式
- 在文件中直接引入loader文件，es6的语法

```
//es6的语法
import Styles from 'style-loader!css-loader?modules!./styles.css';
//commonjs语法
require('style-loader!css-loader?modules!./styles.css')
```
- 在命令行界面使用的方式CLI

```
//直接执行
webpack --module-bind jade-loader --module-bind 'css=style-loader!css-loader'
```
- 在配置文件中使用loader

```
module: {
    rules: [
      {
        <!-- 首先对资源一个正则匹配 -->
        test: /\.css$/,
        <!-- 匹配成功后会使用多个loader对其进行处理 -->
        use: [
          { loader: 'style-loader' },
          {
            loader: 'css-loader',
            options: {
              modules: true
            }
          }
        ]
      }
    ]
  }
```
### babel
- Babel通过语法转换器支持最新版本的JavaScript。这些插件允许你立刻使用新语法，无需等待浏览器支持。
- 在src文件夹下创建component文件夹，component里面创建layer组件，组件里有js、css、html文件；
- 在src文件夹中创建入口js文件,app.js;

```
//app.js中引入html
import layer from './component/layer/layer.js'
const App = function() {
    console.log(layer)
}
new App()

//layer.js中渲染模板
import tpl from './layer.html'
function layer() {
    return {
        name: 'layer',
        tpl: tpl
    }
}
export default layer;
```
- 安装babel转化es6的代码`npm install --save-dev babel-loader babel-core`(webpack3版本)
- 使用babel加载器将es6的语法进行转化，转化时要指定参数，方式有三种：
  - 使用配置指定

```
//在配置webpack.config.js中添加
module: {
        rules: [{
            test: /\.js$/,
            loader: "babel-loader",
            //加快打包速度的配置
            //排除范围
            exclude: __dirname + '/node_modules/',
            //babel-loader的处理范围
            include: '/src/',
            //webpack3中使用options代替query
            options: {
                'presets': ['env']
            }
        }]
    }
```
  - 在package.json中添加

```
"babel":{
  "presets":["env"];
}
```
  - 根目录下创建.babelrc文件

```
{
  "presets": ["env"]
} 
```
#### 使用node的path方法
- 注意，**使用path.resolve时，拼接的文件名称字符串内没有斜杠**
```
//引入node的path方法
var path = require('path');

//使用
exclude: path.resolve(__dirname, 'node_modules'),
include: path.resolve(__dirname, 'src'),
```

### 处理css文件的loader
#### css-loader和style-loader
- 安装`npm i style-loader css-loader --save-dev`
- 在src下面创建css文件夹，创建common.css文件，写入内容；
- 在配置中写入css的loader配置

```
{
    test: /\.css$/,
    use: [
        { loader: 'style-loader' },
        {
            loader: 'css-loader',
            options: {
                modules: true
            }
        }
    ]
}
```
#### postcss-loader
- 安装：`npm i postcss-loader --save-d`
- postCSS是一种处理css的工具，比如可以支持变量和混入（mixin），增加浏览器相关的声明前缀，或是把使用将来的 CSS 规范的样式规则转译（transpile）成当前的 CSS 规范支持的格式。
- postcss-loader可以补全css代码的兼容性前缀;
- 在web对postcss进行配置，**需下载`npm i postcss-import autoprefixer --save-d`**

```
{
    test: /\.css$/,
    use: [
        'style-loader',
        {
            loader: 'css-loader',
            //importLoaders: 1的作用：处理 .css文件中使用@import引用进来的文件，指css-loader后指定数量的loader处理import引用进来的资源;
            options: { modules: true, importLoaders: 1 }
        },
        {
            loader: 'postcss-loader',
            options: {
                plugins: (loader) => [
                    require('postcss-import')({ root: loader.resourcePath }),
                    require('autoprefixer')(), //CSS浏览器兼容
                ]
            }
        }
    ]
},
```
#### less-loader
- 安装`npm i less-loader less --save-dev`
- 直接在对css配置的loader中加入less文件的规则

```
{
    test: /\.less$/,
    use: ['style-loader',
        {
            loader: 'css-loader',
            options: { modules: true, importLoaders: 1 }
        },
        {
            loader: 'postcss-loader',
            options: {
                plugins: (loader) => [
                    require('postcss-import')({ root: loader.resourcePath }),
                    require('autoprefixer')(), //CSS浏览器兼容
                ]
            }
        },
        {
            loader: "less-loader" // compiles Less to CSS 
        }
    ]
}
```
- 如果是sass文件和less文件同理；

## webpack处理模板文件
- layer.html是模板文件；webpack处理模板文件的方法有两种：
    - 将模板文件当成一个字符串进行处理；
    - 将模板文件当成已经编译好的模板的处理函数；

### webpack处理html文件
- 如果模板是html文件，那么需要安装html-loader（前面已安装）；
    - 在app.js中引入layer.js文件，在layer.js中引入layer.html模板，webpack会将模板直接打包插入到index.html中

```
//app.js中
//引入css
import './css/common.css'
//引入入口js文件
import Layer from './component/layer/layer.js'

const App = function() {
    // const NUM = 1
    // alert(NUM)
    // console.log(layer)
    var dom = document.getElementById('app');
    var layer = new Layer();
    dom.innerHTML = layer.tpl;
}
new App();

//layer.js中
import './layer.less'
//引入模板
import tpl from './layer.html'

function layer() {
    return {
        name: 'layer',
        tpl: tpl,
    }
}
export default layer

//webpack.config.js中加入
{
    test: /\.html$/,
    use: {
        loader: 'html-loader'
    }
},

```
### webpack处理ejs或tpl文件
- 如果模板是ejs或者tpl文件，那么需要安装ejs-loader `npm install ejs-loader --save-dev`；
- layer.js载入ejs模板时，返回的是一个function，这时的import tpl from './layer.tpl';中的tpl代表的不再是字符串，表示的是一个已经编译过的函数;
- 操作，（注意将layer.js中引入的html文件更改成tpl文件）

```
//layer.tpl中
<div class="layer">
    <div>this is
        <%= name %> layer</div>
    <% for (var i=0;i<arr.length; i++) {%>
        <%= arr[i] %>
            <% } %>
</div>

//app.js中插入的模板不再是一个字符串，而是一个函数，函数需要传参数
dom.innerHTML = layer.tpl({
    name: 'join',
    arr: ['apple', 'xiaomi', 'vivo', 'oppo']
});
```

## webpack处理图片或者其他文件
### 图片文件的处理
#### 在css中使用相对路径的图片生成背景
```
//common.css修改
html,
body {
    padding: 0;
    margin: 0;
    //使用相对路径
    background-image: url("../assets/人生1.jpg");
}

//webpack.config.js中添加
{
    test: /\.(png|jpg|gif|svg)$/i,
    use: {
        loader: "file-loader"
    }
}
```
- 打包，可看到运行后的图片显示在页面上

#### 在html中引入相对路径图片
- 在body中插入img标签

```
//index.html文件
<body>
    <!-- <h1>
        这是html模板
    </h1> -->
    //插入图片
    <img src="./src/assets/人生1.jpg" />
    <div id="app"></div>
</body>
```
- 编译打包后，图片可以自动找到，绝对定位也会是一样；
#### 在模板中引入图片
- 绝对路径引入图片，打包后可以直接找到；
- 相对路径的话，打包后图片加载失败；如果想要使用相对路径引入图片，那么需要在src中使用require来引入图片；

```
//相对路径错误引用
<img src='../../assets/人生1.jpg'/>

//相对路径正确引用
<img src="${require('../../assets/人生1.jpg')}"/>
```
#### 改变图片打包后的输出地址
```
{
    test: /\.(png|jpg|gif|svg)$/i,
    use: {
        loader: "file-loader",
        //插入参数
        options: {
            name: 'assets/[name]-[hash:5].[ext]'
        }
    }
}
```

### 处理其他文件
- 安装`npm i url-loader --save-d`
- url-loader和file-loader相似，但是url-loader可以指定limit参数。
- url-loader可以指定一个limit参数，当图片大于这个参数，则webpack会将图片直接丢给file-loader处理，当其小于指定的limit参数时，url-loader就会直接对其进行处理，转为base64编码；

```
{
    test: /\.(png|jpg|gif|svg)$/i,
    use: {
        loader: "url-loader",
        options: {
            limit: 30000,
            name: 'assets/[name]-[hash:5].[ext]'
        }
    }
}
```
- 使用url-loader会有一种代码的冗余，无论在哪里引用，都会使得引用处多一串代码；

#### 压缩文件
- 安装`npm install image-webpack-loader --save-dev`
- 配置修改，将limit设置的小一点可看出效果

```
{
    test: /\.(png|jpg|gif|svg)$/i,
    use: [{
            loader: "url-loader",
            options: {
                limit: 100,
                name: 'assets/[name]-[hash:5].[ext]'
            }
        },
        {
            loader: 'image-webpack-loader'
        },
    ]
}
```
- 原本打包后25.5KB的图片变成18.6KB了；
- image-webpack-loader有很多的参数，可以针对不一样的图片类型进行不同的图片优化；
- 官网：`https://www.npmjs.com/package/image-webpack-loader`


