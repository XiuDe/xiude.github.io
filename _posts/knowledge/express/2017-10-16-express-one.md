---
layout: original
title: "express框架"
date: 2017-10-17 9:19:49 +0800 
categories: node研究
tag: [node, express]
---
* content
{:toc}

- [express官网](http://www.expressjs.com.cn/)
- [express github](https://github.com/expressjs/express/)
- express安装
- [express路由](http://www.expressjs.com.cn/guide/routing.html)
- [express中间件](http://www.expressjs.com.cn/guide/using-middleware.html)
- [express模板引擎](http://www.expressjs.com.cn/guide/using-template-engines.html)

<!-- more -->

## express
### express安装
- 首先在项目下生成package.json文件
- 在当前文件下执行`cnpm install express --save`
- 安装完成入口文件测试[Hello World](http://www.expressjs.com.cn/starter/hello-world.html)
- 当前文件下`node .`执行
- express是基于node的所以Hello world可以写为：

```
 const app = require('express')();

 app.get('/',(req,res)=>{// get和node的http.createServer在这实现一样
     res.send('ok');     // 在这里使用express框架
 }).listen(3000,()=>{
     console.log('running...');
 });

```

### express托管静态文件
- 项目下的图片之类的各种文件都可以用url访问
- [官方文档](http://www.expressjs.com.cn/starter/static-files.html)
- 相比于之前的[文章](https://xiude.github.io/2017/10/15/nodejs/)自己设置的托管更完善

```
/*
    托管静态文件

    可以指定虚拟目录
    可以指定多个目录作为静态资源目录
*/
const express = require('express');
const app = express();
// 实现静态资源服务
// let server = app.use(express.static('public'));//public文件夹下
                                                  //的所有资源，
                                                  //都能通过url访问
                                                  //localhost:3000/hello.html

// use方法的第一个参数可以指定一个虚拟路径，url直接访问，访问不到
// url中需要加上虚拟路径 localhost:3000/
// let server = app.use('/abc',express.static('public'));//localhost:3000/abc/hello.html
// app.use('/nihao',express.static('hello'));
// server.listen(3000,()=>{
//     console.log('running...');
// });
// -----------------------------------简化代码
app.use('/abc',express.static('public'));
app.use('/nihao',express.static('hello'));// 指定多个目录不受影响
app.listen(3000,()=>{
    console.log('running...');
});
```

### express路由
- [express路由](http://www.expressjs.com.cn/guide/routing.html)
- app.use()
- app.all()
- 基本路由处理
- 使用字符串模式的路由路径
- 使用正则表达式的路由路径
- app.router()
- express.Router类把路由封装到独立的模块中

> 代码示例

```
/*
    路由（根据请求路径和请求方式进行路径分发处理）
    http的常用请求方式：
    post   添加
    get    查询
    put    更新
    delete 删除

    restful api 
    (满足特定URL的地址，符合上面的请求，不存在文件后缀)
*/

const express = require('express');
const app = express();

// 直接使用use分发可以处理所有的路由请求
/*
  与路径无关：localhost:3000
              localhost:3000/abc/hi.html
              都能访问到
*/
// app.use((req,res)=>{
//     res.send('ok');
// });


// 基本的路由处理
// app.get('/',(req,res)=>{
//     res.send('get data');
// });

// app.post('/',(req,res)=>{
//     res.send('post data');
// });

// app.put('/',(req,res)=>{
//     res.send('put data');
// });

// app.delete('/',(req,res)=>{
//     res.send('delete data');
// });


// all方法绑定的路由与请求方式无关
/*
  该路径下localhost:3000/abc的所有请求都能访问到
*/
// app.all('/abc',(req,res)=>{
//     res.end('test router');
// });


// ----------------------------
// route方法可以指定特定的请求方式
/*
   只有get和post才能访问到
*/
// app.route('/hello')
//    .get((req,res)=>{
//        res.send('get data');
//    }).post((req,res)=>{
//        res.send('post data');
//    });
// app.listen(3000,()=>{
//    console.log('running...');
// });

```

> express.Router示例

```
// myrouter.js文件
const express = require('express');
const router = express.Router();

router.get('/hi',(req,res)=>{
    res.send('hi router');
});

router.get('/hello',(req,res)=>{
    res.send('hello router');
});

router.post('/abc',(req,res)=>{
    res.send('abc router');
});

module.exports = router;


// 入口文件
const express = require('express');
const app = express();
/*
  express.Router类把路由封装到独立的模块中
  express.Router类 + app.use实现路由模块化
*/
const router = require('./myrouter.js');

app.use('/admin',router);

app.listen(3000,()=>{
    console.log('running...');
});

```

## express中间件
- [express中间件](http://www.expressjs.com.cn/guide/using-middleware.html)

### express中间件:应用级中间件
- [应用级中间件](http://www.expressjs.com.cn/guide/using-middleware.html#middleware.application)

```
/*
    中间件：就是处理过程中的一个环节
    （本质上就是一个函数，这个函数可以随时访问req和res对象）
    next串联中间件，req和res贯穿其中
*/

const express = require('express');
const app = express();
let total = 0;
/*
  以下四个中间件之间用next传递请求
*/
// 如果路径去掉，只要有访问就能检测到，无论哪个路径
app.use((req,res,next)=>{
    console.log('有人访问');
    // next方法的作用就是把请求传递到下一个中间件，
    // 没有next后面的不执行
    next()
});

app.use('/user',(req,res,next)=>{
    // 记录访问时间
    console.log(Date.now());
    // next方法的作用就是把请求传递到下一个中间件
    next()
});

app.use('/user',(req,res,next)=>{
    // 记录访问日志
    console.log('访问了/user');
    next()
});

app.use('/user',(req,res)=>{// 只有访问/user下才能计数
    total++;
    console.log(total);
    res.send('result');
});

app.listen(3000,()=>{
    console.log('running...');
});


```

### express中间件:路由级中间件
- [路由级中间件](http://www.expressjs.com.cn/guide/using-middleware.html#middleware.router)

```
/*
    中间件的挂载方式和执行流程
    use方法
    路由方式:get post put delete
*/
const express = require('express');
const app = express();

// app.get('/abc',(req,res,next)=>{
//     console.log(1);
//     // 跳转到下一个路由
//     next();// 没有next请求页面时不会得到2
              // 页面会一直转圈
// },(req,res)=>{// 中间件可以继续绑定   
//     console.log(2);
//     res.send('abc');
// });


// app.get('/abc',(req,res,next)=>{
//     console.log(1);
//     // next参数设置为route是跳转到下一个路由
       // 运行页面后 服务器打印13，不会打印2
       // 页面会显示hello
//     next('route');
// },(req,res)=>{
//     console.log(2);
//     res.send('abc');
// });

// app.get('/abc',(req,res)=>{
//     console.log(3);
//     res.send('hello');
// });
// --------------------------------
var cb0 = function (req, res, next) {
  console.log('CB0');
  next();
}

var cb1 = function (req, res, next) {
  console.log('CB1');
  next();
}

var cb2 = function (req, res) {
  res.send('Hello from C!');
}

app.get('/example', [cb0, cb1, cb2]);

app.listen(3000,()=>{
    console.log('running...');// 后台打印 CB0 CB1
                              // 页面显示 Hello from C!
});
```

### express中间件:内置中间件
- [内置中间件](http://www.expressjs.com.cn/guide/using-middleware.html#middleware.built-in)

### express中间件:错误处理中间件
- [错误处理中间件](http://www.expressjs.com.cn/guide/using-middleware.html#middleware.error-handling)

### express中间件:第三方中间件
- [第三方中间件](http://www.expressjs.com.cn/guide/using-middleware.html#middleware.third-party)
- 第三方中间件body-parser，提供了表单处理的方式，使用前安装
- body-parser还支持json形式的数据

```
/*
    第三方中间件body-parser:处理post请求
        express提供了自带的get请求

    第三方中间件处理表单形式的数据测试
*/
const express = require('express');
const app = express();
const bodyParser = require('body-parser')
// 挂载内置中间件
// 启用了内置的中间件
app.use(express.static('public')); // 访问的时候访问：
                                   // http://localhost:3000/login
                                   // 如果访问http://localhost:3000/public/login访问不到数据

// 挂载参数处理中间件（参数处理是post提交），默认解析post提供的表单数据
// 启动了中间件的功能
app.use(bodyParser.urlencoded({ extended: false }));


// 处理post提交参数
app.post('/login',(req,res)=>{
    let data = req.body;
    // console.log(data);// 表单传过来的数据对象
    if(data.username == 'admin' && data.password == '123'){
        res.send('success');
    }else{
        res.send('failure');
    }
});

// 处理get提交参数，express自带的
app.get('/login',(req,res)=>{
    let data = req.query;
    console.log(data);
    res.send('get data');
});



app.listen(3000,()=>{
    console.log('running...');
});
```

- body-parser：利用jquery的ajax请求json形式的数据总结

```
// 当前目录下public文件夹下的login.html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <script type="text/javascript" src="jquery.js"></script>
    <script type="text/javascript">
    $(function(){
        $('#btn').click(function(){
            var obj = { // post传参先获取对象，然后利用JSON.stringify(obj)
                        // 将数据转为json格式
                        // 后台的数据处理要改为json格式
                username : $('#username').val(),
                password : $('#password').val()
            }
            $.ajax({
                type : 'post',// get post put delete
                url : 'http://localhost:3000/login',
                contentType : 'application/json', // 设置传递方式为json格式
                dataType : 'text',// 
                // get传参的方式$('form:eq(0)')得到单个表单，serialize(),得到所有数据
                // data : $('form:eq(0)').serialize(),
                data : JSON.stringify(obj),
                success : function(data){
                    console.log(data);
                }
            });
        });
    }); 
    </script>
</head>
<body>
    <form action="http://localhost:3000/login" method="get">
        用户名：<input type="text" name="username" id="username"><br>
        密  码：<input type="password" name="password" id="password"><br>
        <input type="button" id="btn" value="提交">
    </form>
</body>
</html>


// 当前目录下的node处理文件
/*
    第三方中间件:处理post请求
    express提供了自带的get请求
*/
const express = require('express');
const app = express();
const bodyParser = require('body-parser')
// 挂载内置中间件
// 启用了内置的中间件，处理该目录下public文件夹下的内容
app.use(express.static('public'));

// 挂载参数处理中间件（参数处理是post提交），默认解析post提供的表单数据
// 启动了中间件的功能
app.use(bodyParser.urlencoded({ extended: false }));

// 处理json格式的参数
app.use(bodyParser.json());


// 处理post提交参数，public文件夹下login页面的请求
app.post('/login',(req,res)=>{
    let data = req.body;
    // console.log(data);// 表单传过来的数据对象
    if(data.username == 'admin' && data.password == '123'){
        res.send('success');
    }else{
        res.send('failure');
    }
});

// 处理get提交参数，express自带的
app.get('/login',(req,res)=>{
    let data = req.query;
    console.log(data);
    res.send('get data');
});

app.put('/login',(req,res)=>{
    res.end('put data');
});

app.delete('/login',(req,res)=>{
    res.end('delete data');
});

app.listen(3000,()=>{
    console.log('running...');
});
```

## 在express中使用模板引擎
- [express中使用模板引擎](http://www.expressjs.com.cn/guide/using-template-engines.html)
- 在需求目录下安装art-template模板

```
// 当前目录下view文件夹的list.art
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>模板</title>
</head>
<body>
    <div>{{title}}</div>
    <div>
        <ul>
            {{each list}}
                <li>{{$value}}</li>
            {{/each}}
        </ul>
    </div>
</body>
</html>


// node处理的js
/*
    模板引擎整合：art-template
*/
const express = require('express');
const path = require('path');
const template = require('art-template');
const app = express();

// 设置模板的路径
/*
  第一个参数views键是固定的，
  后面的views是路径
*/
app.set('views',path.join(__dirname,'views'));
// 设置模板引擎，在 views 目录下生成名为list.art的art模板文件
app.set('view engine','art');

// 使express兼容art-template模板引擎
app.engine('art', require('express-art-template'));

app.get('/list',(req,res)=>{
    let data = {
        title : '水果',
        list : ['apple','orange','banana']
    }
    // 参数一：模板名称；参数二：渲染模板的数据
    res.render('list',data);
});

app.listen(3000,()=>{
    console.log('running...');
});
```
