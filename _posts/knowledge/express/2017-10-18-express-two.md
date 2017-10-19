---
layout: original
title: "express框架接口"
date: 2017-10-18 9:19:49 +0800 
categories: node研究
tag: [node, express]
---
* content
{:toc}


<!-- more -->

## node+express后台接口开发
- [json接口](http://www.expressjs.com.cn/4x/api.html#res.json)
- [jsonp接口](http://www.expressjs.com.cn/4x/api.html#res.jsonp)
- restful接口

### express的json形式接口

```
/*
    后台接口开发
*/
const express = require('express');
const db = require('./db.js');
const app = express();

// 指定api路径 allBooks (json接口)
// 直接访问 localhost:3000/allBooks
 app.get('/allBooks',(req,res)=>{
     let sql = 'select * from book';
    db.base(sql,null,(result)=>{
         res.json(result);
     });
 });

app.listen(3000,()=>{
    console.log('running...');
});
```

### express的jsonp形式接口

```
/*
    后台接口开发
*/
const express = require('express');
const db = require('./db.js');
const app = express();

// 修改json回调函数传递参数的key
// express默认的访问形式是 localhost:3000/allBooks?callback=foo
// 指定后访问形式为 localhost:3000/allBooks?cb=foo
// jsonp形式将数据作为参数传递给了回调函数
// 如果请求方式不对，不会返回错误，返回的是json格式
app.set('jsonp callback name', 'cb');

// 指定api路径 allBooks （jsonp接口）
app.get('/allBooks',(req,res)=>{
    let sql = 'select * from book';
    db.base(sql,null,(result)=>{
        res.jsonp(result);
    });
});

app.listen(3000,()=>{
    console.log('running...');
});
```

### express的restful形式接口

```
/*
    restful api  是从URL的格式来表述的，
                 不像json和jsonp是从响应的数据格式来说的。
    restful api 一个URL对应一个资源，每个资源与请求方式是有关系的
                请求方式 + url 表示特定的资源
                参数直接利用/往后加不用问号的形式
    restful请求方式结合json和jsonp形式使用
                             
    get     http://localhost:3000/books     获取所有图书信息

    get     http://localhost:3000/books/book  添加单本图书信息
    post    http://localhost:3000/books/book  提交表单信息

    get     http://localhost:3000/books/book/1  编辑图书信息传参数
    put     http://localhost:3000/books/book    提交表单信息

    delete  http://localhost:3000/books/book/2

    传统的URL风格
    http://localhost:3000/
    http://localhost:3000/toAddBook
    http://localhost:3000/addBook
    http://localhost:3000/toEditBook?id=1
    http://localhost:3000/editBook
    http://localhost:3000/deleteBook?id=2
*/
const express = require('express');
const db = require('./db.js');
const app = express();

// http://localhost:3000/books  
// app.get('/books',(req,res)=>{  
//     let sql = 'select * from book';
//     db.base(sql,null,(result)=>{
//         res.json(result);
//     });
// });

// http://localhost:3000/books/book/1
app.get('/books/book/:id',(req,res)=>{
    let id = req.params.id;
    let sql = 'select * from book where id=?';
    let data = [id];
    db.base(sql,data,(result)=>{
        res.json(result[0]);
    });
});

app.listen(3000,()=>{
    console.log('running...');
});
```
