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