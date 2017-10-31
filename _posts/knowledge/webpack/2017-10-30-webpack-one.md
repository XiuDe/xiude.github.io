---
layout: original
title: "webpack版本总结"
date: 2017-10-30 21:22:49 +0800 
categories: 前端框架研究
tag: webpack
---
* content
{:toc}
webpack将代码打包成适合浏览器运行的格式。webpack相较于[gulp](http://www.gulpjs.com.cn/docs/getting-started/)打包vue或react更常用，但是webpack的版本更新有很多组件会报错，这里总结以下webpack3.8.1和webpack-dev-server2.9.3的相关问题。以及简单的webpack流程安装总结，详细部分以前文章总结过。

- [英文官网](http://webpack.github.io/docs/)
- [Webpack-handlebook](http://zhaoda.net/webpack-handbook/)
- [Gitbook](http://fakefish.github.io/react-webpack-cookbook/index.html)
- 关于博客收集
    + [详解前端模块化工具-Webpack](https://segmentfault.com/a/1190000003970448)
    + [阮一峰webpack](https://github.com/ruanyf/webpack-demos)
    + [Webpack 入门指迷](https://segmentfault.com/a/1190000002551952)
    + [webpack 入门指南一小时](http://blog.csdn.net/yczz/article/details/49250623)
    + [webpack-howto](https://github.com/petehunt/webpack-howto#8-optimizing-common-code)
    + [Webpack 一探究竟](https://mp.weixin.qq.com/s?__biz=MjM5MTA1MjAxMQ==&mid=2651220238&idx=1&sn=ebdba528f199e10f6b273c3a6fd04650&scene=0&key=b28b03434249256b7847bfdf62609ae5dc60fe6644c4c53f8f8e392425069ed2d9fa147c8c66f9d719456241c3a687ba&ascene=14&uin=MjYzMjMwMzE4MA%3D%3D&devicetype=android-21&version=26031031&nettype=WIFI&pass_ticket=3wAH%2BijJnBbvbS8rfC%2FF5Sv78DZDco29Ejqjt96dzxTLB72USiOBN69mnq9DFTFy)

<!-- more -->

### 1. webpack安装、打包，部署简单总结
#### 1. webpack安装
- 全局安装`npm install webpack -g`
- 按需求构建项目目录

#### 2.webpack打包
- `webpack --config` + webpack项目配置名
- 如`webpack --config webpack.develop.config.js`
- 打包成功后直接访问即可

#### 3.简化webpack简化命令
- 在该项目下`npm init -y`生成package.json文件在`"script"`中书写

```
// webpack开发阶段配置文件
"develop":"webpack --config webpack.develop.config.js",

// webpack发布阶段开发文件
"public":"webpack --config webpack.publish.config.js" 
```

- 然后运行`npm run develop`

#### 4.修改代码后浏览器自动刷新
> webpack-dev-server是官网辅助开发工具模块，封装了webpack和express模块

- 全局安装下安装`npm install webpack-dev-server -save-dev -g`
- 当前项目目录下执行`npm install webpack-dev-server -save-dev`
- 修改package.json的"develop"为`"webpack-dev-server --inline --config webpack.develop.config.js  --devtool eval --progress --colors --hot --content-base src"`
    + `--content-base src`指向src，以src为根目录开启一个服务器
    + `inline`用来支持dev-server自动刷新的配置
- 修改webpack.develop.config.js文件devServer

```
module.exports = {
    entry:path.resolve(__dirname,'src/js/app.js'),
    output:{
        path: path.resolve(__dirname,'dist'),
        filename: 'bundle.js',
    },
    devServer:{
        contentBase: "./dist",
        historyApiFallback:true,
        inline:true,
        hot:true,
    }
}
```

> 注意版本之间的兼容，这里是webpack3.8.1和webpack-dev-server2.9.3，参见[博客](http://www.cnblogs.com/caideyipi/articles/7080010.html)。

#### 5.webpack构建的项目目录
![](/styles/images/webpack/webpack01.png)


### 2. webpack加载器loaders
> webpack3.8.1和babel-loader7.1.2

#### 1.loaders加载器
> 将react的jsx、ES6或SCSS等转换成原生js和css

- webpack3.8.1下babel-loader7.1.2下调试运行
- webpack2和3部分兼容webpack1语法

#### 2.babel-loader安装
- 当前项目下`npm install babel-loader --save-dev`
- `npm install babel-core babel-preset-es2015 babel-preset-react --save-dev`
    + `babel-preset-es2015`转换es6为原生js
    + `babel-preset-react`转换jsx为原生js

#### 3.相关文件配置
- webpack.develop.config.js

```
/**
 * webpack 开发阶段配置文件
 * */
var path = require('path');
var webpack = require("webpack");

module.exports = {
    entry:path.resolve(__dirname,'src/js/app.js'),
    output:{
        path: path.resolve(__dirname,'dist'),
        filename: 'bundle.js',
    },
    devServer:{
        contentBase: "./dist", // 当前文件夹下的index文件
        historyApiFallback:true,
        inline:true,
        hot:true,
    },
    module:{
        rules:[ //配置loader，rules或是loaders，这里使用loaders也不会报错
            {
            test:/\.jsx?$/,
                loader:"babel-loader",
                options: {
                    // npm install babel-core babel-preset-es2015 babel-preset-react --save-dev
                    // 安装的加载器部分对应
                    presets: ['es2015', 'react'] 

                }
        }
        ]
    },
    plugins: [
        new webpack.HotModuleReplacementPlugin()
        // hot这个属性已经没有用了，使用热模块的话我们需要用到一个叫webpack.HotModuleReplacementPlugin的插件。
        // 需要加载 webpack进来
    ]
}


```

- package.js文件

```
{
  "name": "webpack_example",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "develop": "webpack-dev-server --inline --config webpack.develop.config.js  --devtool eval --progress --colors",
    "publish": "webpack --config webpack.publish.config.js"
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "babel-core": "^6.26.0",
    "babel-loader": "^7.1.2",
    "babel-preset-es2015": "^6.24.1",
    "babel-preset-react": "^6.24.1",
    "webpack": "^3.8.1",
    "webpack-dev-server": "^2.9.3"
  },
  "dependencies": {
    "react": "^16.0.0",
    "react-dom": "^16.0.0"
  }
}

```

- 入口文件app.js

```
import React,{Component} from 'react'
import ReactDom from 'react-dom'

ReactDom.render(
    <div>react中的jsx通过loader打包编译</div>,
     document.getElementById('app')
)
```

#### 4.参考文档
- [webpack3版本配置一](http://blog.csdn.net/qq_20334295/article/details/74166356?locationNum=1&fps=1)
- [webpack3版本配置二](http://blog.csdn.net/qq_20334295/article/details/74933034)
- [webpack3配置webpack-dev-server](http://www.cnblogs.com/caideyipi/articles/7080010.html)

### 3. webpack加载器loaders应用
#### 1.加载css和scss
- 当前目录下安装`install css-loader style-loader --save-dev`
- 需要提前安装node-sass否则会报错`npm install node-sass --save-dev`

> webpack.develop.config.js

```
// rules下
{
    test:/\.scss$/,  
    use:['style-loader','css-loader','sass-loader'] //webpack 2+写法
}
```

#### 2.url加载器-处理图片
- 该项目下安装`npm install url-loader -save-dev`。
- webpack3.8.1不报错，如果报错`npm install file-loader --save-dev`。

```
{
    test:/\.(png|jpg|jpeg|gif)$/,
    use:'url-loader?limt-25000' // 小于3kb内联在网页中，大于3kb请求
}
```

#### 3.url加载器-处理字体


```
{
    test:/\.(eot|woff|ttf|woff2|svg)$/,
    use:'url-loader?limt-25000' // 小于3kb内联在网页中，大于3kb请求
}
```

### 3. webpack发布
#### 1.将完成的项目构建
- 运行package.json的publish`npm run 名`。
    + `npm run publish`。

#### 2.分离第三方包
- `npm run publish`生成的bundle.js文件过大，不好维护，分离第三方包
- 1.修改publish的配置文件将入口文件修改

```
entry:{
    app:path.resolve(__dirname,'src/js/app.js'),
    vendors:['react','react-dom'] // 存储第三方包名
}
```

- 2.添加插件

```
plugins: [
    // 帮助进行热重载的
    new webpack.HotModuleReplacementPlugin(),
    // 分离第三方包的插件 name后接entry的分离名，filename接分离后的js名
     new webpack.optimize.CommonsChunkPlugin({name:'vendors',filename:'vendors.js'})
]
```

- 3.在index.js中引入vendors.js，如果想要压缩代码，在package.json的publish中加入`-p`，如`"publish": "webpack --config webpack.publish.config.js -p"`，不加代码就不压缩。
- 4.重新构建

### 4. webpack的常用插件
> 加载器是项目构建之前做预处理操作，插件是项目构建之后做后处理工作(压缩，合并，混淆等)

- 内置插件
- 第三方插件

#### 1.删除文件夹的插件
- `npm install clean-webpack-plugin -save-dev`。
- 在webpack配置文件下引入插件`var CleanPlugin = require('clean-webpack-plugin')`，然后plugins下`new CleanPlugin(['dist'])`，dist为删除的目录文件。

#### 2.自动生成html页面插件
- `npm install html-webpack-plugin -save-dev`。
- webpack发布阶段的配置文件`var HtmlWebpackPlugin = require("html-webpack-plugin");`。

```
new HtmlWebpackPlugin({
    template:'./src/template.html',
    htmlWebpackPlugin:{
        "file":{
        //    "css":["app.css"],
            "js":["vendors.js","bundle.js"]
        }
        },
        minify:{ // 压缩
            removeComments:true, // 删除评论
            collapseWhitespace:true, // 压缩空格
            removeAttributeQuotes:true //
        }
})
```
- 运行`npm run publish`。

#### 3.压缩代码优化插件 - 内置插件
- 在webpack配置文件plugins下直接配置

```
 new webpack.optimize.UglifyJsPlugin({
    compress:{
        warings:false
    }
})
```
- 运行`npm run publish`查看效果。