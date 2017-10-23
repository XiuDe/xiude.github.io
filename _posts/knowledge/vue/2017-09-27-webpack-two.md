---
layout: original
title: "webpack 3.x 语法补充"
date: 2017-10-09 23:08:49 +0800 
categories: 前端框架研究
tag: [webpack 3.x, webpack -p]
---
* content
{:toc}

[webpack中文文档](https://doc.webpack-china.org/)<br>
[webpack github page](http://webpack.github.io/docs/)<br>


补充webpack 3.x的webpack.config.js的相应配置。
生成package.json的命令`npm init -y`。


<!-- more -->
#### 1. 关于output根路径的变更
```
output:{
    path:path.resolve('__dirname',dist),
    filename:'build.js'
}
```

#### 2. 关于modules中loader书写问题的变更
```
module:{
    rules:[{
        test:/\.css$/,
        use:['style-loader'!'css-loader']
        }]
}

注意： use后loader的顺序。

```

#### 3. 关于将vue2.0项目打包成完整静态页面
```
在package.json中的"scripts"中添加"dist"
然后执行npm run dist生成打包后的静态页面
"scripts": {
    "dev": "webpack-dev-server --inline --hot --open --port 5008",
    "dist": "webpack -p"
  }

  其中"dist"是webpack.config.js的module.exports中的output输出的指定文件夹名
    path : __dirname+'/dist'

  module.exports={
  entry:'./src/main.js',  //指定打包的入口文件
  output:{
    path : __dirname+'/dist',  // 注意：webpack1.14.0 要求这个路径是一个绝对路径
    filename:'build.js'
  }
```