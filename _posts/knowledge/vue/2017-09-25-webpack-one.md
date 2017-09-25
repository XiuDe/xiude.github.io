---
layout: original
title: "webpack总结"
date: 2017-09-25 18:42:49 +0800 
categories: 前端框架研究
tag: webpack
---
* content
{:toc}


总结webpack的安装以及webpack相应的命令。



<!-- more -->
#### 1. webpack安装
- webpack是一个资源的打包工具，分为1.0和2.0版本，可以将 .js,  .css , image等静态资源当做一个模块来进行打包，那么每一种模块都是有一个对应的 loader来实现
- [webpack 1.0版本官网](https://webpack.github.io/docs/usage.html)
- [webpack 2.0版本官网](https://webpack.js.org/)
- 版本webpack 1.14.0

##### 1.1 webpack和nodejs的关系
- webpack是node的第三方资源包，使用webpack首先安装node环境。
- [nodejs官网](https://nodejs.org/en/)
    + LTS版本是长期稳定版本。
    + current是当前最新版本。
    + 安装完成后检测版本`node -v`和`npm -v`出现对应版本则安装完成。
- 修改npm下载资源包的来源
    + 使用`npm install` 安装第三方包是去国外网站上下载，有可能会被墙而安装失败，所以我们要将下载源切换到国内淘宝上因此需要利用 `npm install nrm -g`安装一个全局的`nrm`。
    + 安装好nrm以后，在cmd命令面板中输入： `nrm use taobao` 将下载源切换到淘宝，可以使用 `nrm ls` 查看当前使用的下载源，使用`nrm ls`显示`*`的位置为我们用的下载源的地址。
    + 也可安装淘宝提供的类似于npm的工具cnpm来替代npm安装node包,安装包命令和npm一样，安装cnpm命令： `npm install cnpm -g`。

##### 1.2 安装webpack步骤
  
- 第一种安装方式： 
    + 在cmd命令行面板中 执行： `npm install webpack@1.14.0 -g` 将webpack1.14.0版本安装为全局就能够在cmd命令行面板中使用webpack指令。
-第二种安装方式： 
    + 在cmd命令行面板中 执行： `cnpm install webpack@1.14.0 -g` 将webpack1.14.0版本安装为全局就能够在cmd命令行面板中使用webpack指令。 

#### 2. webpack打包
```
需求：index.html实现简单的计算器
      calc.js实现实现add加的功能
      main.js实现获取Dom元素，并且调用calc.js中的add方法
      1. 在index.html中导入main.js <script src="main.js"></script>
      2. main.js中获取calc.js的add方法 var add = require("./calc.js");
      3. calc.js将add方法作为参数传递 module.exports = add;

      以上是最初的实现，这样会报错，解决方案：
      1. sublime下安装一个插件terminal，安装这个插件是为了方便webpck打包，
         在要打包的文件夹下用命令提示符也行，不过比较麻烦。
      2. 在sublime需要打包的文件夹下右键Open Terminal Here执行命令webpack main.js build.js
      3. 这条命令是将main.js，以及main.js中require的文件都打包成build.js文件
      4. 打包完成后在index.html中导入的main.js改成我们打包好的build.js，完成打包。
      5. 打包过的所有js文件修改后，要重新打包。
```
