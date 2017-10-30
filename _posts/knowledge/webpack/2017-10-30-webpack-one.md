---
layout: original
title: "webpack版本总结"
date: 2017-10-30 21:22:49 +0800 
categories: 前端框架研究
tag: webpack
---
* content
{:toc}

webpack相较于[gulp](http://www.gulpjs.com.cn/docs/getting-started/)打包vue或react更常用，但是webpack的版本更新有很多组件会报错，这里总结以下webpack3.8.1和webpack-dev-server2.9.3的相关问题。以及简单的webpack流程安装总结，详细部分以前文章总结过。

- [英文官网](http://webpack.github.io/docs/)
- [Webpack-handlebook](http://zhaoda.net/webpack-handbook/)
- [Gitbook](http://fakefish.github.io/react-webpack-cookbook/index.html)
- 关于博客的手机
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