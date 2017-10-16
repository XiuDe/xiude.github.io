---
layout: original
title: "node实现动态网站"
date: 2017-10-15 21:50:49 +0800 
categories: node研究
tag: node
---
* content
{:toc}

node实现动态网站及后台模板引擎的使用。
- 接触到的node内容
    + Node.js不需要依赖第三方应用软件（Apache），可以基于api自己实现
    + 实现静态资源服务器
    + 路由处理(多页面多分支)
    + 动态网站
    + 模板引擎(art-template)
    + get和post参数传递和处理

<!-- more -->


## 动态网站
- 创建nodejs服务器实现动态网站
- 项目目录
    + view文件夹下index.tpl和result.tpl名字随便取
    + node的服务器文件deal.js
    + 数据文件scores.json文件

```
// index.tpl 查询页面
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>查询成绩</title>
</head>
<body>
    <form action="http://localhost:3000/score" method="post">
        请输入考号：<input type="text" name="code">
        <input type="submit" value="查询">
    </form>
</body>
</html>

// result.tpl 查询结果页面
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>成绩结果</title>
</head>
<body>
    <div>
        <ul>
            <li>语文：$$chinese$$</li>
            <li>数学：$$math$$</li>
            <li>外语：$$english$$</li>
            <li>综合：$$summary$$</li>
        </ul>
    </div>
</body>
</html>


```

- scores.json页面

```
{
    "no123" : {
        "chinese" : "100",
        "math" : "140",
        "english" : "149",
        "summary" : "289"
    },
    "no124" : {
        "chinese" : "120",
        "math" : "120",
        "english" : "119",
        "summary" : "239"
    },
    "no125" : {
        "chinese" : "130",
        "math" : "110",
        "english" : "139",
        "summary" : "269"
    }
}
```

- deal.js页面

```
/*
    动态网站开发
    成绩查询功能
*/

const http = require('http');
const path = require('path');
const fs = require('fs');
const querystring = require('querystring');
const scoreData = require('./scores.json');

http.createServer((req,res)=>{
    // 路由（请求路径+请求方式）
    // 查询成绩的入口地址 /query
    if(req.url.startsWith('/query') && req.method == 'GET'){
        fs.readFile(path.join(__dirname,'view','index.tpl'),'utf8',(err,content)=>{
            if(err){
                res.writeHead(500,{
                    'Content-Type':'text/plain; charset=utf8'
                });
                res.end('服务器错误，请与管理员联系');
            }
            res.end(content);
        });
    }else if(req.url.startsWith('/score') && req.method == 'POST'){
        // 获取成绩的结果 /score
        let pdata = '';
        req.on('data',(chunk)=>{
            pdata += chunk;
        });
        req.on('end',()=>{
            let obj = querystring.parse(pdata);
            let result = scoreData[obj.code];
            fs.readFile(path.join(__dirname,'view','result.tpl'),'utf8',(err,content)=>{
                if(err){
                    res.writeHead(500,{
                        'Content-Type':'text/plain; charset=utf8'
                    });
                    res.end('服务器错误，请与管理员联系');
                }
                // 返回内容之前要进行数据渲染
                content = content.replace('$$chinese$$',result.chinese);
                content = content.replace('$$math$$',result.math);
                content = content.replace('$$english$$',result.english);
                content = content.replace('$$summary$$',result.summary);

                res.end(content);
            });
        });
    }
    

}).listen(3000,()=>{
    console.log('running....');
});
```

- 根据不同学号动态返回不同数据

## art-template模板的使用
- 解决占位付不统一问题
- 下载安装参见[art-template官方文档](http://aui.github.io/art-template/zh-cn/)
- `node init -y`生成package.json后，修改package.json文件mian的入口后语法测试

```
// let html = template(__dirname + '/mytpl.art', {
//     user: {
//         name: 'lisi'
//     }
// });
// console.log(html);<h2>lisi</h2>
// ----------------------------------

// 方式一
// let tpl = '<ul>{{each list as value}}<li>{{value}}</li>{{/each}}</ul>';
// let render = template.compile(tpl);
// let ret = render({
//     list : ['apple','orange','banana']
// });
// console.log(ret);
// -----------------------------------

// 方式二
// let tpl = '<ul>{{each list as value}}<li>{{value}}</li>{{/each}}</ul>';
// let tpl = '<ul>{{each list}}<li>{{$index}}-------------{{$value}}</li>{{/each}}</ul>';
// let ret = template.render(tpl,{
//     list : ['apple','orange','banana','pineapple']
// });
// console.log(ret);

/*实例*/
// 1. 入口文件
let template = require('art-template');
let html = template(__dirname + '/score.art', {
    chinese : '120',
    math : '130',
    english : '146',
    summary : '268'
});
console.log(html);
// 2. score.art模板文件
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>成绩结果</title>
</head>
<body>
    <div>
        <ul>
            <li>语文：{{chinese}}</li>
            <li>数学：{{math}}</li>
            <li>外语：{{english}}</li>
            <li>综合：{{summary}}</li>
        </ul>
    </div>
</body>
</html>
```

## 利用模板引擎改写实例
- 项目目录mydemo文件夹下view文件夹，index.js入口文件、scores.json文件以及package.json包。view文件夹下存放index.art和result.art以及全部成绩list.art模板。
- 关于入口文件，多一个页面多一个路由也就多一个分支。
- 编写好 `node .`查看效果

```
/*view文件夹下*/
// index.art
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>查询成绩</title>
</head>
<body>
    <form action="http://localhost:3000/score" method="post">
        请输入考号：<input type="text" name="code">
        <input type="submit" value="查询">
    </form>
    <div><a href="http://localhost:3000/all">全部成绩</a></div>
</body>
</html>

// result.art
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>成绩结果</title>
</head>
<body>
    <div>
        <ul>
            <li>语文：{{chinese}}</li>
            <li>数学：{{math}}</li>
            <li>外语：{{english}}</li>
            <li>综合：{{summary}}</li>
        </ul>
    </div>
</body>
</html>

// list.art
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>所有信息</title>
</head>
<body>
    <div>
        {{each list}}
        <ul>
            <li>语文：{{$value.chinese}}</li>
            <li>数学：{{$value.math}}</li>
            <li>外语：{{$value.english}}</li>
            <li>综合：{{$value.summary}}</li>
        </ul>
        {{/each}}
    </div>
</body>
</html>

```

> scores.json

```
{
    "no123" : {
        "chinese" : "100",
        "math" : "140",
        "english" : "149",
        "summary" : "289"
    },
    "no124" : {
        "chinese" : "120",
        "math" : "120",
        "english" : "119",
        "summary" : "239"
    },
    "no125" : {
        "chinese" : "130",
        "math" : "110",
        "english" : "139",
        "summary" : "269"
    }
}
```

> index.js入口文件

```
const http = require('http');
const path = require('path');
const fs = require('fs');
const querystring = require('querystring');
const scoreData = require('./scores.json');
const template = require('art-template');

http.createServer((req,res)=>{
    // 路由（请求路径+请求方式）
    // 查询成绩的入口地址 /query
    if(req.url.startsWith('/query') && req.method == 'GET'){
        let content = template(path.join(__dirname,'view','index.art'),{});
        res.end(content);
    }else if(req.url.startsWith('/score') && req.method == 'POST'){
        // 获取成绩的结果 /score
        let pdata = '';
        req.on('data',(chunk)=>{
            pdata += chunk;
        });
        req.on('end',()=>{
            let obj = querystring.parse(pdata);
            let result = scoreData[obj.code];
            let content = template(path.join(__dirname,'view','result.art'),result);
            res.end(content);
        });
    }else if(req.url.startsWith('/all') && req.method == 'GET'){
        let arr = [];
        for(let key in scoreData){
            arr.push(scoreData[key]);
        }
        // 全部成绩
        let content = template(path.join(__dirname,'view','list.art'),{
            list : arr
        });
        res.end(content);
    }
}).listen(3000,()=>{
    console.log('running....');
});
```
