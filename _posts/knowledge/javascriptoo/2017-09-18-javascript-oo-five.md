---
layout: post
title: "JavaScriptoo第五课"
date: 2017-09-18 22:53:20 +0800 
categories: javascript面向对象
tag: [javascript, javascript闭包, javascript函数调用模式]
---
* content
{:toc}

> [JavaScript面向对象MDN](https://developer.mozilla.org/zh-CN/search?q=javascript%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1&topic=apps&topic=html&topic=css&topic=js&topic=api&topic=canvas&topic=svg&topic=webgl&topic=mobile&topic=webdev&topic=http&topic=webext)

本章对闭包，闭包的使用场景，js四种常用的函数调用模式进行了总结。


<!-- more -->

#### 1. 闭包的概念

1. 字面理解: 
      - 闭, 是说封闭, 简单的说就是在函数中内部定义的变量无法在外界被访问到.
      - 包, 即为包裹, 一个环绕的结构. 简单的说就是函数. 在 js 中 包的结构很多,只要是使用 {  } 包裹的语句都是包裹结构.只是结合闭的要求, 函数就是符合闭包结构的一种语法.
2. 所谓的闭包就是 函数. 即在函数中定义的变量无法在外界直接被访问. 而函数具有包裹的作用. 因此函数就是闭包.
3. 常见的说法
      - 可以访问函数内部的数据的函数
      - 所谓的闭包就是有未绑定到特定对象的代码组成的语法块( 含有的自由变量的语法结构 )

#### 2. 闭包解决的问题
> 函数内部定义的变量, 外界是无法访问的. 即 闭包中的数据外界无法直接访问.<br>
> 闭包提供了一个受限制的访问区域.

##### 2.1 如何去访问闭包( 函数 )中的数据
__1.__利用`return`返回值

     function foo(){
        var num = 123;
        return num;
    }
    var res = foo();
    //函数执行完成后，由于123是硬编码，num将会被销毁，此时的函数并未构成严格意义上的闭包.
    //每次调用函数，里面的变量会重新创建虽然变量名一样，
    //通过闭包访问的是同一个数据，这里并不是同一个数据
    //将var num = Math.random();每次调用获取的数据不一样。
    console.log(res);

> 单用`return`返回值不能解决。

__2.__返回一个对象返回一个函数

     /* 1.
    function foo(){
        var num = Math.random();
        function tmpFn(){
            return num;
        }
        return tmpFn();
    }
    var fn1 = foo();// 第一次调用
    var fn2 = foo();// 第二次调用
    // foo 还是只允许调用一次
    console.log(fn1);//0.10114160029646424
    console.log(fn1);//0.10114160029646424
    console.log(fn2);//0.163634973094277*/
    
    
    //不同的调用函数形式，第一种调用输出，处理的是fn1和fn2变量，
    //而第二次是返回的函数，输出使用函数，在调用输出的时候可以再附加逻辑
    function foo(){
        var num = Math.random();
        function tmpFn(){
            return num;
        }
        return tmpFn;
    }
    var fn1 = foo();// 第一次调用
    var fn2 = foo();// 第二次调用
    console.log(fn1());//0.176404838665021
    console.log(fn1());//0.176404838665021
    console.log(fn2());//0.8722766873502745

> 返回一个对象返回一个函数，能解决变量只生成一次的问题。？<br>
> 闭包中返回多个变量

    //
    function foo(){
        var num1 = Math.random();
        var num2 = Math.random();
        return [
            function(){
               return num1;
            },
            function(){
                return num2;
            }
        ]
    }
    
    var arr = foo();
    console.log(arr[0]());//0.9611613739435345
    console.log(arr[1]());//0.10380755366192695
    console.log(arr[0]());//0.9611613739435345
    console.log(arr[1]());//0.10380755366192695
    

> 如果变量太多，数组索引不方便，将数组改成对象

    function foo(){
        var num1 = Math.random();
        var num2 = Math.random();
        return {
            num1: function(){
               return num1;
            },
            num2: function(){
                return num2;
            }
        }
    }
     
    var o = foo();
    
    console.log(o.num1());
    console.log(o.num1());
    console.log(o.num2());
    console.log(o.num2());

#### 3. 使用闭包的场景

##### 3.1 缓存
> 闭包允许函数带有一个缓存结构,也就是说只有该函数可以使用的一个私有的独立的内存空间。

__语法__

     var func = (function () {  
           // 内存私有
                
         return function () {
                    // 函数体
          };
      })(); 

> 应用场景 1 <br>
> 例如, 在实际开发中会有频繁的获取元素的行为.获取元素最简单的处理形式就是使用选择器.

```
<div>div</div>
<span>span</span>
<p>p</p>
<div>div</div>
<span>span</span>
<p>p</p>
<div>div</div>
<span>span</span>
<p>p</p>
<div>div</div>
<span>span</span>
<p>p</p>

<script type="text/javascript">
    // 需求：给div和p标签增加边框
    var $ = function(selector){
        return document.querySelectorAll(selector);
    }
    var list = $('div,p');
    for( var i=0;i<list.length;i++){
       list[i].style.border = '1px solid red';
    }

    list1 = $('div,p');// 第二次获取数据，从缓存中读取，
    //不用再调用js方法重新查找，提高了性能
</script>
```

> 如果多次用到需要添加边框的需求，如何提高效率？<br>
> 在完成一个页面应用的时候,一般会频繁的对元素进行获取操作.为了提高效率我们可以将元素缓存.

```
        var $ = (function () {

            var cache = {};
        
            return function ( selector ) {
                // 首先判断缓存中有没有, 有的就直接使用缓存中的, 如果没有就再来获取
                var nodeList;
                if ( cache[ selector ] ) {
                    // 有
                    nodeList = cache[ selector ];
                } else {
                    // 没有
                    nodeList = document.querySelectorAll( selector ); 
                    cache[ selector ] = nodeList; // 在 缓存中存起来
                }
                return nodeList;
            };
        })();

        var list = $('div,p');
        for( var i=0;i<list.length;i++){
            list[i].style.border = '1px solid red';
        }
```

*上面代码中存在的问题：缓存大小无法控制，逐渐积累内存崩溃*<br>

> 优化

      /*为了更高效的时候, 缓存需要一个容量. 所以给定一个 length 属性, 缓存中存储数据如果超过了这个 length 那么就将最早加入的数据删除.
      最先存储的数据最先删除，这种数据结构我们用数组*/


```
<div>div</div>
<p>p</p> 
<span>span</span>
<p>p</p>
<span>span</span>
<b>b</b>
<div>div</div>
<p>p</p>
<span>span</span>
<p>p</p>
<b>b</b>
<span>span</span>
<div>div</div>
<p>p</p>

<script type="text/javascript">
    var length = 3;
    var $ = (function (){
        var cacheKeys = [];
        var cache = {};
    
        return function ( selector ) {
            // 首先判断缓存中有没有, 有的就直接使用缓存中的, 如果没有就再来获取
            var nodeList;
            if ( cache[ selector ] ) {
                // 有
                nodeList = cache[ selector ];
            } else {
                // 没有
                // 将伪数组变为数组 [].slice.call()
                nodeList = [].slice.call(document.querySelectorAll( selector ));
                // 存储之前需要判断长度
                if ( cacheKeys.length >= length ) {
                    // 需要删除
                    var key = cacheKeys.shift();
                    delete cache[ key ];
              
                cacheKeys.push( selector );
                cache[ selector ] = nodeList; // 在 缓存中存起来
            }
            return nodeList;
        };
    };
    })();
    
    $('div').forEach(function(v){
        v.style.border = '1px solid red';
    });


    $('p').forEach( v => v.style.border = '1px dashed blue');//ES6 箭头函数

    $('span').forEach( v=> v.style.backgroundColor = "yellow");

    $('b').forEach( v=> v.style.color = "pink");// 执行到这里之前的缓存会删除第一个
</script>   
```

##### 3.2 避免污染
> 1.什么是污染. 在说道污染的时候, 就是说变量名的覆盖.也就是说以前定义的函数, 变量, 对象等数据可能会被重新生成的变量所覆盖, 以至于无法使用.<br>
> 2.处理办法: 尽可能的不要在全局范围内( 0 级作用域 )定义过多的数据. 如果必须要定义尽量之定义一个.<br>
> 3.我们在实现一些特殊功能的时候, 有需要很多辅助函数或变量, 那么有不应该定义在全局范围内, 那么如何处理呢?

```
        可以定义一个函数, 那么这个函数名就是放在全局范围内的一个名字( 不要重名 )

        var func = function () {

            // 所有的代码逻辑写在这里
            function foo() {
            }

            function tool () {
            
            }

            // ...
        }

        func();
```

> 简化

```
        // 不会影响到全局的变量污染
        // 代码的执行会和写在外面的一样
        //()中的函数即为语句，!也行，一般写在()中，作用是把函数声明变为语句
        // 第二个()为调用，而函数声明是不能直接被调用的，
        //所以将函数声明写在()变为语句
        (function () {

            // 所有的代码逻辑写在这里
            function foo() {
            }

            function tool () {
            
            }

            // ...

        })()

        // 沙箱模式
        (function(){

         })();
```



##### 3.3 模块块级作用域
> 利用闭包模仿块级作用域:

```
/* 1.
    var sum = 0;
    for(let i=1;i<=100;i++){
       sum += i;
    }
    console.log(sum);//5050
    //console.log(i);报错 i is not defined*/

/*  2.
    var sum = 0;
    for(var i=1;i<=100;i++){
       sum += i;
    }
    console.log(sum);//5050
    console.log(i);//101*/


    var sum = 0;
    (function(){
       for(var i=1;i<=100;i++){
          sum += i;
        }
    })();
    console.log(sum);//5050
    //console.log(i);// 报错 i is not defined
```
#### 4. 函数的四种调用模式
> 实际上就是在讨论一个函数中 this 的指向问题.

##### 4.1 分类
1. 函数调用模式
2. 方法调用模式
3. 构造器调用模式
4. 上下文调用模式
5. bind模式

##### 4.2 函数调用模式
> 分辨: 函数调用没有任何引导数据, 就只有一个函数名来调用.在函数调用模式中 this 表示 全局对象( 在浏览器中 就是 window )



##### 4.3 方法调用模式
> 分辨: 方法调用模式一定要有一个对象, 是对象引导方法来调用的.在方法模式中 this 表示引导对象( 当前对象 )


<br>
> 函数调用和方法调用示例


```
    function foo(){
        console.log( this );
    }
    foo();// window ，函数调用

    var o = { name:'jim'};
    o.func = foo;// 此时 func 和 foo都指向 函数体
    o.func();// 前面有对象引导，这是一个方法调用 ，输出对象 o
```

> 非常巧妙的一个题

```
    var length = 10;
    function fn(){
        console.log( this.length );
    }

    var o = {
        length:5,
        method: function(f){// fn
           f();//fn，前面没有东西做引导，是函数调用
           arguments[0]();//arguments是一个对象，一共传入两个参数，
                          //这里是方法调用，
           //console.log(arguments[0]);这里输出这个，这是this.length
           //ƒ fn(){                   就是 arguments传入参数的个数
           //   console.log( this.length );
           //  } 
        }
    };

    o.method(fn,1);// 10 2
    // 函数传参的时候是将 地址取出来，再赋给对象里的参数
```

##### 4.4 构造器调用模式
> 分辨: 方法在调用的时候 前面有一个 new 关键字引导.在方法中 this 的含义就是刚刚被创建出来的 对象

__1.__ 构造函数的执行过程原理

      使用 new 创建对象
      构造函数调用, 将刚刚创建的对象引用传入到构造函数中的 this 中
      因此 this 就是当前刚刚创建出来的对象
      利用 对象的 动态特性来给对象增加成员
     
      在调用 new的时候,new后面所跟的函数是什么,创建出来的对象就继承什么的原型.


__2.__ 构造函数中的 `return`

> 1.在构造函数中 使用 return 可以结束构造函数的执行. 直接将当前的 this 直接返回.<br>

```
    function Person(){
        this.name = 'jim';

        return;

        this.age = 123;
    }

    var p = new Person();
    console.log(p.name);// jim
    console.log(p.age);// undefined
```

> 2.如果 return 后跟是一个基本数据类型( 值类型 )或空类型, 这个数据无效, return 起作用.<br>

```
    function Person(){
        this.name = 'jim';

        return 123;//无效
        //return true;无效
        //return "123";无效
        //return undefined;无效
        //return null;无效


        this.age = 123;
    }

    var p = new Person();
    console.log(p);// 输出的还是Person对象，return的123无效，
    //基本类型和null都无效,return有效，return后面的无效
```

> 3.如果 return 后跟的是一个引用类型( 对象 ), 那么调用构造函数就会返回该数据. 创建出来的对象就会被 "丢弃"，这时创建对象时的this也被丢弃，如果想保存this，利用全局变量截取出来<br>

```
    var obj; 
    function Person(){
        this.name = 'jim';
        obj = this;
        return [1,2,3];//覆盖
        //return /^d/;       覆盖
        //return function(){}覆盖


        this.age = 123;
    }

    var p = new Person();
    console.log(p);// 返回的数组对象覆盖了之前的Person
    console.log(this);//Window {stop: ƒ, open: ƒ, alert: ƒ, confirm: ƒ, prompt: ƒ, …}
```

##### 4.5 上下文调用模式

> 允许调用者控制函数中 this 的含义, 那么这样的调用就是上下文调用.<br>

```
    上下文, 就是环境, 依托的对象.
    divElem.getElementsByName( 'div' )//就是在divElem中查找div
    this上下文就是divElem
```

> 上下文调用有两个方法可用`apply`和`call`，功能一样参数不同，第一个参数一样。

1. apply

```
如果函数调用需要提供参数, apply 方法的第一个参数用于指定 函数中 this 的含义.
如果第一个参数传入的是 null 或 undefined, 那么 this 就是 window. 
如果第一个参数传入的是 基本数据类型( 值类型 ), 那么会将值类型转换成对应的包装类型, this 指向包装对象
如果第一个参数传入的是 对象, 那么 this 就是这个对象.

apply 方法的第二个参数是一个数组, 里面包含需要传给函数的所有参数
```

> 示例

```
    var name = "全局的name";

    function method(){
        console.log(this.name);
    }
    
    var o1 = { name:'zhangsan'};
    var o2 = { name:'李四'};

    method();// 全局的name

    //以前的方式-->方法的调用
    o1.func = method;
    console.log(o1.func());

    //apply 是Function.prototype提供的方法
    //上下文调用需要函数的apply方法来引导调用，并提供一个参数来指定this的含义

    method.apply(o1);//将o1传入，因此就是指定函数method中的this就是o1表示的对象
    // 这里打印 zhangsan

    method.apply(o2);//李四
```

2. call

```
如果函数调用需要提供参数, call 方法的第一个参数用于指定 函数中 this 的含义.
如果第一个参数传入的是 null 或 undefined, 那么 this 就是 window. 
如果第一个参数传入的是 基本数据类型( 值类型 ), 那么会将值类型转换成对应的包装类型, this 指向包装对象
如果第一个参数传入的是 对象, 那么 this 就是这个对象.

call 方法的第二个参数, 以及其他参数分别表示需要传入函数的各个参数


共同点: call和apply两个方法都是在指定函数中 this 是指向什么对象.
js 是一个弱类型的语言, 是表示数据不是严格的要求数据类型
```

> 代码示例

```
    var name = "全局的name";

    function method(){
        console.log(this.name);
    }
    
    var o1 = { name:'zhangsan'};
    var o2 = { name:'李四'};

    method();// 全局的name
    // call与apply是一样的，唯独从第二个参数开始不同
    // 但是只是调用的形式不同，本质是一样的
    method.call({
        name:"call"     //输出call
    });
    
    method.call(o2); //李四
```

> js 是一个弱类型的语言, 是表示数据不是严格的要求数据类型<br>
> apply 和call的使用。

```
/* 1.数组的操纵方法
    //合并数组 concat方法返回一个新数组
    var arr1 = [1,2,3];
    var arr2 = [4,5,6];
    arr3 = arr1.concat(arr1);
    console.log(arr3);*/

    
    //2. 伪数组
    var warr1 = { length:0};
    // 添加元素

    //1> 最简单
    // warr1[0] = 1;
    // warr1.length++;
    //2> 结合算法
    // warr1[warr1.length++] = 1;// 繁琐
    //3>可以使用数组的方法，但是假设this不是数组，而是伪数组
    // [].push.apply(warr1,[1]);//元素进来了但是warr1不是一个数组
    [].push.call(warr1,1);
    [].push.call(warr1,2);
    [].push.call(warr1,3);

    var warr2 = { length:0};
    [].push.call(warr2,3);
    [].push.call(warr2,4);
    [].push.call(warr2,5);
    //使用push
    [].push.apply(warr1,warr2);
```

> apply和call在使用上可以是互补的两个操作，apply需要提供一个数组作为参数，call需要提供的是散列作为参数列表的一些参数<br>
> apple和call借用构造函数实现继承<br>

```
   function Person(name,age,gender){
     this.name = name;
     this.age = age;
     this.gender = gender;
   }
   // Person是一个构造函数，但是他也是一个普通函数
   // 只有在使用new来引导的时候Person才是构造函数
   // 而this就是刚刚创建出来的对象

   // 有了上下文调用以后，可以给任意对象增加name属性
   // var o={};
   // Person.call(o,'tom');
   // console.log(o.name);// tom

   // 利用该模式可以实现经典继承
   // 需求提供一个Student构造函数，
   // 该构造函数的实例继承Person
   function Student(name,age,gender,chinese,math,english){
      Person.call(this,name,age,gender);//借用构造函数初始化name，age和gender

      this.chinese = chinese;
      this.math = math;
      this.english = english;
   }
   Student.prototype.sayHello = function(){
      console.log('你好，我是'+ this.name);
   }
   var s = new Student('张三',12,'男',98,99,100);
   s.sayHello();//你好，我是张三
```

##### 4.5 bind调用模式

> ES5中给出的新方法，在特殊情况下会用到。<br>
> 如果说使用 apply 或 call 是在调用的时候指定 this 的含义, 那么 bind 就是在调用之前指定 this 的含义.提前绑定. 

```
语法
   函数.bind( 对象 ) -> 返回函数
```

> 返回函数的函数体和调用bind的函数体是一摸一样的，不同的是返回的函数已经将this指向的bind中指定的对象了。


```
/*  1. 原操作
    var dv = document.getElementsByTagName('div')[0];
    dv.style.border = '1px dashed red';
    dv.style.width = '100px';
    dv.style.height = '50px';*/
    

/*  2.在电泳的时候进行绑定，改变this指向//apply,call
    var get = document.getElementsByTagName;//存函数体
    var dv = get.call(document,'div')[0];// call和apply在调用的时候进行绑定

    dv.style.border = '1px dashed blue';
    dv.style.width  = '100px';
    dv.style.height = '50px';*/

    
    var tmpFn = document.getElementsByTagName;
    var get = tmpFn.bind(document);// 临时函数，修改this指向为document函数，
                                   // 返回的还是函数
    var dv = get('div')[0];// 已经隐含了一个条件，就是说get中的this就是绑定
                        // 函数对象 document
    dv.style.border = '1px dashed green';
    dv.style.width  = '100px';
    dv.style.height = '50px';
```

> 其他用处详见[MDN bind](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)