# webpack练习
## 安装webpack和基本环境搭建
- 新建一个工作的文件夹
- 打开命令行，cd进入该文件夹
```
//初始化一下npm
> E:\work\Webpack>npm init
```
- 然后按照提示输入这个项目的一些信息，不想填也可以一直按回车。
```
//安装Webpack
> E:\work\Webpack>npm install webpack --save-dev
```
- 这样一个webpack的基本框架就出来了，下面试一下它的打包功能

## webpack的打包功能
- 在该工作文件夹下新建一个hello.js，随便输入一点函数
```
function hello(str){
    alert(str);
}
```
- 然后在命令行中将这个hello.js打包为hello.bundle.js
  - 注意，因为我没有在环境变量PATH中配置webpack，终端在PATH里找不到webpack这个二进制文件;
  - 所以可以在package.json中配置scripts，如果配置好scripts，npm会帮助你在node_modules/.bin目录下找对应的文件；
  
  ```
  //这句命令的意思是将hello.js打包，并且打包后的文件名称为hello.bundle.js
  "start": "webpack hello.js hello.bundle.js"
  //运行命令
  npm run start或者npm start
  ```
- 然后同样的，在该项目下新建一个world.js，随便输入一点函数

```
function world(){
    return{
        //返回一个空     
    }
}
```
- 接下来，在hello.js中将这个函数引用进来

```
require('./world.js');
//要注意文件的引用路径，不然打包的时候会报错。
```

- 在命令行中打包hello.js（和上面一样的命令）


## 使用webpack打包css文件
### 单独打包css文件
- 我们在同样的层级下新建一个style.css

```
html,
body {
    padding: 0px;
    margin: 0px
}
```
- 然后在hello.js中引入style.css

```
require('./style.css');
```
- 命令行中打包hello.js `npm run start`
- 会报错，错误提示

```
//告诉你需要一个合适的loader来处理css文件，webpack天生是不支持css文件的，如果要支持css需要依赖loader
You may need an appropriate loader to handle this file type.
```
- 下载处理css文件的loader

```
npm install css-loader style-loader --save-dev
```
  - css-loader作用是让webpack可以解析执行css文件
  - style-loader是将css-losder处理完的文件生成一个style标签，将其插入到html中header中去；

- 回车安装loader，安装完毕之后，在hello.js中，要指定css文件的loader

```
//将之前写的require('./style.css');改成下面的语句

require('css-loader!./style.css');

//这句话的意思是在使用这个css文件之前必须经过loader的处理
```

- css文件打包成功

### 在html中打包css文件
- 新建一个html文件，这个html文件中随便写点东西，并且要把打包后的文件hello.bundle.js文件引入进来。如下：

```
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8">
        <title>测试webpack的Index测试</title>
        <script src="hello.bundle.js" type="text/javascript" charset="utf-8"></script>
    </head>
    <body>
        hello!!!
    </body>
</html>
```
- 需要在hello.js里面将刚刚写的函数执行：

```
//commonJS
require('./world.js')
require('css-loader!./style.css')

function hello(str) {
    alert(str)
}

hello('hello world')
//这是要添加的
```

- 执行之后发现css样式未起作用。这是因为在require的时候，除了指定css-loader，还需要指定style-loader，修改hello.js代码如下：

```
//commonJS
require('./world.js')
//新添加了style-loader!
require('style-loader!css-loader!./style.css')

function hello(str) {
    alert(str)
}

hello('hello world')
```
- 使用命令行再次运行下，打开html文件，就会发现css样式已经被执行了。 

- 以后每次写css，不想要这样重复的去reqiure；在webpack中可以全局的指定命令，将hello.js中的require(‘style-loader!css-loader!./style.css’); 修改为之前的require(‘./style.css’); 
- 然后在package.json中重新配置scripts

```
"start": "webpack hello.js hello.bundle.js --module-bind 'css=style-loader!css-loader'"
```
- 打开html文件，css样式存在，说明指令已经生效。
#### 更新文件后打包
- 创建js和css两个文件夹，将css文件和js文件分别放入两个文件夹下；
- 更新hello.js
```
- require('style-loader!css-loader!./style.css')
+ require('../css/style.css')
```
- 更新index.js
```
- <script type="text/javascript" src="hello.bundle.js"></script>
+ <script type="text/javascript" src="./js/hello.build.js"></script>
```
- 执行`webpack js/hello.js js/hello.build.js --module-bind css=style-loader!css-loader`命令
- 打开html文件，css样式存在，说明指令已经生效。




