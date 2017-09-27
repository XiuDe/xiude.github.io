---
layout: original
title: "webpack 3.x补充"
date: 2017-09-27 23:08:49 +0800 
categories: 前端框架研究
tag: webpack 3.X
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
