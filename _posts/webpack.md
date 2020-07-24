# webpack 学习
---
## 01 webpack安装
```
npm install --save-dev webpack-cli
```
## 02 初始化webpack
在工作目录本地安装webpack，接着安装webpack-cli(此工具用于在命令行运行webpack)
```
npm init -y
npm install webpack webpack-cli --save-dev
```

## 03 webpack初体验
运行指令：
1. 开发环境：webpack 源文件 -o 目标文件 --mode=development
2. 生产环境： wenpack 源文件 -o 目标文件 --mode=production

生产环境生成的built.js是压缩后的，在学习过程中使用开发环境

## 04 打包样式资源
webpack只能打包JS/JSON文件，不能处理其他的资源，但是我们通过loader引入其他类型的文件。
例如，我们需要引入CSS文件：
首先下载引入CSS所需要的loarder
```
webpack init

npm i webpack webpack-cli -D
npm i css-loader style-loader -D
```
然后在webpack.config.js中进行配置
```javascript
/*
    webpack.config.js 是webpack的配置文件
        作用：指示webpack做什么事情

        所有构建工具都是基于nodejs这个平台，模块化默认采用commonJS
*/

//resolve 用来拼接绝对路径的方法
const { resolve } = require('path')

module.exports = {
    //webpack配置

    //入口起点
    entry: './src/index.js',
    //输出
    output: {
        //文件名
        filename: 'built.js',
        //输出路径
        //_dirname代表当前文件的目录绝对路径
        path: resolve(__dirname, 'build')
    },
    
    //loader的配置
    module:{
        rules: [
            //详细loader配置
            {
                //匹配哪些文件
                test: /\.css$/,
                use: [
                    //执行顺序，从右到左
                    //创建style标签，将JS中的项目资源插入进去，添加到head中生效
                    'style-loader', 
                    //将CSS文件变成commonJS的模块加载到JS，里面内容是样式字符串
                    'css-loader'
                 ]
            },

            {
                test: /\.less$/,
                use: [
                    'style-loader', 
                    'css-loader',
                    'less-loader'
                ]
            }
        ]
    },

    //plugins的配置
    plugins: [
        //详细plugins的配置
    ],

    //模式
    mode: 'development',
        //mode: 'production'

}
```
配置完成之后在工作目录下执行 `webpack` 就会把配置中的资源打包到目标文件里。

## 05 打包html资源

webpack.config配置文件
```javascript
/*
loader: 1.下载  2.使用
plugins：1. 下载  2.引用  3.使用
*/


const { resolve } = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
    entry: './src/index.js',

    output: { 
        filename: 'built.js',
        path: resolve(__dirname,'build')
     },

     module: {
         rules: [
             //loader配置
         ]
     },

     plugins: [
        //plugin的配置
        //npm html-webpack-plugin -D 下载

        //功能：默认会创建一个空的html，自动引入打包输出的所有资源
        //需要有结构的html文件
        new HtmlWebpackPlugin({
            //复制'./src/index.htm'文件，并自动引入打包输出的所有资源
            template: './src/index.html'
        })
     ],

     mode: 'development'
}
```
