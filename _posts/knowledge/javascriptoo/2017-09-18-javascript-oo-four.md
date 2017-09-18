---
layout: post
title: "JavaScriptoo第四课"
date: 2017-09-18 22:33:20 +0800 
categories: javascript面向对象
tag: [javascript, javascript块级作用域, javascript预解析]
---
* content
{:toc}

> [JavaScript面向对象MDN](https://developer.mozilla.org/zh-CN/search?q=javascript%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1&topic=apps&topic=html&topic=css&topic=js&topic=api&topic=canvas&topic=svg&topic=webgl&topic=mobile&topic=webdev&topic=http&topic=webext)

本章主要总结了块级作用域，词法作用域，js预解析以及作用域链相关问题的汇总。

<!-- more -->

#### 1. 块级作用域与词法作用域

> 什么是作用域: 所谓的作用域就是指起作用( 可使用 )的范围.<br>
  一般在讨论作用域的时候是指某一个变量的可使用范围. <br>一般是指变量在定义后可以被使用,而到某一个地方的时候就不能使用了. 就是指得这个范围.<br>


##### 1.1 块级作用域
> 所谓的块, 是指一个代码的逻辑结构, 其中使用 {} 表示逻辑上的一句话. 但是里面可以包含多条语句.这里的花括号就是语句块.<br>
>  js中的块级作用域，ES6中的`let`语法。

1.块级作用域是指从变量定义开始, 到变量所在的语句块结束, 在这样一个范围内可以被使用.

    {//这个花括号就是一个语句块
     //在ES6中引入 let 关键字，使用let与var一样可以声明变量，但是let变量具有块级作用域
     //console.log(num);报错 num is not defined
     let num = 123; console.log(num);//123
     num = 456;
     console.log(num);//456
     // num从定义开始，即let num=123;之后到右花括号内都可以使用
     console.log(num);//456
    }
    //console.log(num);超出词法作用域后也会报错num is not defind

2.在块级作用域中 子块 可以访问 父块, 反之不行.

     {
        let num = 123;
        {
           console.log(num);//子块访问父块
           let str = "子块可以访问父块";
        }
        console.log(str);// 报错 str is not defined
    }

3.如果子块中含有与父块同名的变量( 声明 ),那么会在定义该变量的时候隐藏父块中的变量.在子块中操作变量就是在操作子块中定义的 数据, 不会影响到 父块中的变量. 超出子块作用域后,父块中定义的变量就又可以使用了.

    {
        let num = 123;
        {
            // console.log(num);//会报错，在子块中定义了父块同名的变量，隐藏了父块的变量
            let num = 456;
            console.log(num);//456
        }
        console.log(num);//123
    }

4.以上的情况都是子块`let`重新声明变量的情况,如果子块中没有声明

    {
        let num = 123;
        {
            console.log("子块中的 num = " + num);// 123
            num = 456;
            console.log("子块中的 num = " + num);// 456 
            num = 789;
            console.log("子块中的 num = " + num);// 789
        }
        console.log("父块中的 num = " + num);// 789
    }

##### 1.2 词法作用域

1. 词法作用域: 所谓的词法作用域,就是指变量的访问规则按照词法定义的规则进行使用.
2. 词法定义规则就是: 只有函数才可以限定作用域. 
3. 访问变量从当前作用域开始往上进行查找.这里的查找是指往作用域的上方进行查找, 而非代码的定义书写顺序查找.

```
/*  1. //词法作用域不受代码块的影响
    {
        var num = 123;
    }
    console.log(num);*/



/*  2.// 在 js 中，所有的变量作用域由函数来定义
    function foo(){
        var str = "只有函数限定作用域";
    }
    foo();
    console.log(str);//报错 str is not defined*/



    // if结构的花括号，不限定变量num的定义。因此不会报未定义的错误。
    // 但是if结构为假，因此赋值运算不进行，那么变量中就没有数据。
    // 所以打印出来的是 undefined
    if (0) {
        var num = 123;
    }
    console.log(num);//undefined
    while(0){
        var str = "数据";
    }
    console.log(str);//undefined
```

> 词法作用域的含义

```
    // 词法作用域的向上查找是指从函数内部向函数外查找
    foo();//undefined
    function foo(){
        console.log(num);
    }
    // foo();
    var num = 123;
```

*因此在词法作用域中存在作用域的上下关系( 作用域链 )*


#### 2. 变量访问规则
##### 2.1 只有函数可以定义作用域
1. 所有 script 标签可以看成一个独立的全局作用域. 记为 0 级作用域
2. 从上往下, 凡是看到函数才会出现一个子级作用域. 其父作用域如果为 n 级作用域, 那么他就是 n+1 级作用域.

##### 2.2 变量搜索原则
> 凡是在访问变量的时候, 首先会在当前作用域中查找变量声明定义,如果在当前作用域中含有变量的声明或定义, 那么就会使用该变量,如果没有在当前作用域中找到变量的声明与定义, 那么就会去父级作用域中查找.

*案例一*

    var num= 123;// 0级
    function foo(){
        var num = 456;// 1级
        function func(){
            var num = 789;// 2级
            console.log(num);//如果2级中有，则打印789，没有找1级
                             //如果1级中有，则打印456，没有找0级
                             //如果0级中有，则打印123，没有会报错。
        }                    
        func();               
    }
    foo();//789

*案例二*

    var num = 123;
    function foo(){
        console.log(num);
    }
    function func(){
        var num = 456;
        foo();
    }
    func(); //123


#### 3. javascript的执行原理
##### 3.1 js是解释型的语言

> 就是将代码解释一句执行一句.为了保证程序的高效运行.在程序运行一开始的时候一个检查过程. 如果这个检查没有问题, 才会继续往下运行, 将代码从上往下, 从左至右的 一句一句的执行.<br>
> 这个检查过程就是预解析, 而执行过程就是解释执行过程。
*不同于c++，java等语言，在执行前需要编译，js是解释型语言，在执行前预解析*

##### 3.2 预解析

1. 在预解析的时候需要检查基本的语法.
2. 在当前作用域中检查所有的变量名声明. `var`就是变量声明的标志.就是在此时在运行环境中就已经记录了有什么变量了. ( 变量名提升 )
3. 在预解析的过程中会记录含有的函数声明. 如果不是函数声明, 那么是不会被记录的.
4. 变量的预解析要比函数的早`console.log(typeof a); function a(){}; var a;`,输出function

```
/*  //1.
    console.log("start");
    try{
        var num = {；};语法的错误在预解析的时候就发生了
    }catch(e){
       
    }
    console.log("hello");*/


    
/*   2.
    //检查所有声明
    console.log("start")
    console.log(num);// 在没有var num = 123;的情况下这里会报执行错误 num is not defined
                     // 有 var num = 123;这里会在控制台打印 undefined
    num = 123;
    // 如果含有 var 就表示这个 num 是一个声明，声明与赋值是分开的
    // 凡是声明的内容在预解析的时候就已经被运行环境记录下来了，那么自然在一开始的时候使用就不会报错
    // 但是只是记录下来这个名字，可以使用，但是里面的数据是什么，还不清楚，因此打印undefined.
    // 而在 var num = 123;之后再次打印，就有结果123了

    // 但是如果没有 var 那么在预解析的时候就不会将 num 当做一个变量，只是在简单的检查是否会有语法错误
    // 从上往下执行，遇到num的时候发现还没有定义，因此报错。*/



 /* 4.
    // 记录函数声明
    foo();
    function foo(){
        console.log("hello js");
    }*/

```


__函数的定义形式__<br>
> 函数拥有的属性叫静态属性；函数的实例拥有的属性是实例属性
1.声明式

     function foo ( args ) {
          // 声明式函数
      }

2.函数表达式( 函数字面量 )

     var func = function foo () {
          // 函数表达式
     };

3.使用 Function 构造函数

> 函数声明必须在一个独立的代码结构中, 不允许将函数的声明嵌入到 语句中( if, while, for ... ).凡是嵌入到语句中, 函数声明会自定的将其转换成函数表达式. ( 嵌入花括号中是一个特例 )，不包括把函数放到函数内。

*示例代码*

    foo1();
    function foo1(){
        // 是函数声明
        // 在一个独立的结构中
    }
    
    
    // foo2(); 报错 foo2 is not a function
    if (1) {
        foo2();//不报错
        function foo2(){
            // 函数表达式(注意)
        }
    }
    
    
    //foo3();报错 foo3 is not a function
    while(1){
        foo3();//成功调用
        function foo3(){
    
        }
        break;
    }
    
    
    //foo4();报错 foo3 is not a function
    {
        foo4();// 成功调用
        function foo4(){
    
        }
    }
    
    
    //foo5();报错 foo3 is not defined
    (function foo5(){
       // 将函数放到圆括号中，那么函数就成为表达式了，因此不会调用，更不会预解析
    })
    
    //foo5();foo5();报错 foo3 is not defined

> 函数声明的特例<br>

__1.__在 大约 2013 年之前, 谷歌浏览器在处理的时候, 凡是在花括号中的如下函数都是声明
```
 function 名字 () {
  
  }
```

在火狐浏览器中不这么处理. 在火狐中好像是在花括号内的声明
```
// 如果是声明应该在外面可以访问, 但是这个规则只有在花括号内起作用
f();  // 是不会报错的
{

    function f() {}
} 
```

2013年以前经典题现在无法测试
```
if ( 1 ) {
   function f() {
      console.log( 'true' );
    } 
} else {
    function f() {
       console.log( 'false' );
    }
 }
f();

 // 给我们的感觉是 f 应该打印 'true', 但是, 13 年以前 谷歌 打印的是 false
 // 在 谷歌浏览器中 两个函数都是声明, 因此从上往下解析, 先解析得到是 true, 
 // 而后 又有一个 false, 覆盖了 true. 因此最后是 false.
 // 但是即使在 13 年 以前, 狐火中还是认为 if 起到了作用在 花括号的内部才是声明
 // 但是花括号的逻辑需要利用 if 来处理.
```

__2.__但是在今天 谷歌与火狐已经一样的处理这样的代码了

     //同样执行上面的代码
     //f(); 报错:f is not a function
     if ( 1 ) {
        function f() {
           console.log( 'true' );
        } 
     } else {
        function f() {
           console.log( 'false' );
        }
     }
     f();//true

> 在函数内部定义的函数, 也是在函数内部有一个声明的解析结构

     //foo6();访问不到,报错 foo6 is not defined
     function func(){
        foo6();//运行正确，在函数内部也有一个预解析的过程
        // 一进入函数，那么首先将所有的声明记录下来(一个局部的预解析过程)
        function foo6(){
          console.log(6);
        }
     }
     func();//输出6
     // 如果在function foo6(){ ... } 前加!function foo6(){ ... },就将函数
     // 放入到了表达式中了，这个函数就不是函数声明了，而是函数表达式，就不会被
     // 函数预解析的过程记录下来，就会报错，foo6 is not defined，
     // 如果把调用foo6();当如函数表达式的下面，但是这里还是报错foo6 is not defined，ie8可以调用

__函数表达式__<br>

> 函数名只允许在函数内部使用,这种情况是在做测试的时候把函数名写上去。

     var foo = function func () {
          // 函数表达式
     };

> 代码示例

    var foo = function func(){
        console.log(func);// 打印的是他自己
        console.log( func == foo);// true
        
        var o = {};
        //  o.func();报错 func is not a function 
    }
    foo();
    /*ƒ func(){
        console.log(func);
    }*/
    // 调用的时候一定要用 foo(); 调用，
    // 如果用func();调用会报错误 func is not defined
    // 除了IE8

> 一般将函数写在 表达式中: 赋值, 圆括号, 取反, 等

##### 3.3 解释执行


#### 4. 代码分析

> 案例1

    //in 运算符
    //语法： 字符串 in 对象 -> boolean
    //作用： 判断字符串所描述的名字属性 是否在 右边的对象中，
    // 只要能在原型链中找到左边的属性就打印true
    
    // 凡是在全局作用域下声明的变量，都是window的属性
    
    if ( ! 'a' in window) {
        var a = 123;
    }
    console.log( a );// undefined
    
    // 分析：预解析阶段，vars声明的变量提前，因为js中只有函数作用域，
    //所以，if中的var a = 123;提前，然后执行代码 ，预解析阶段已经知道
    //a 在 window下所以 (! 'a' in window)为false，a = 123不执行。

> 案例 2

    // 分析：
    // 1. 代码运行需要预解析，有两个声明，一个是变量num，另一个是函数num声明
    //    从上往下解析，记录全局变量 num 然后函数 num
    // 函数预解析阶段做的事情：
    //      > 声明一个与函数名同名的变量，但是该变量已存在，因此是重复声明
    //      > 将函数体与该变量关联，所谓的关联就是指变量可以调用函数体
    // 2. 开始执行代码，首先执行的是 赋值语句，给num赋值为123，
    //    此时函数变量与函数体的关联覆盖,
    // 3. 直接调用，报错
     
    var num = 123; //如果这里是var num; 则最后输出456
      
    function num(){
        console.log( 456 );
    }
     
    num();// 报错： num is not a function
    

> 以下这种情况也会报错

    function num(){
        console.log( 456 );
    }
    var num = 123; 
    num();// Uncaught TypeError: num is not a function

> 案例 3

    // 1. 预解析
    //    在全局范围内保留变量num 和 函数 func
    //    函数的声明分为两部分
    //         1> 定义了一个与函数名同名的变量，即 func
    //         2> 函数体与 func 连接到一起
    // 2. 执行代码
    //    赋值给 num 得值为 123
    //    函数调用，调用的时候传入 num 中存储的数据(将数据123拷贝一份传入函数中)
    // 3. 进入函数
    //    在进入函数的时候，在执行代码之前有一个预解析
    //    此时在解析之前已有 value 变量，并且其中的值为 123
    //    预解析，检查到函数声明，并且名字为 value
    //          1> 声明一个与函数名同名的变量(重复声明)
    //          2> 将函数体与变量名连接起来，将原有的数值覆盖
    //    执行代码
    
    var num = 123;
    function func(value){
        value();
        function value(){
            console.log('value 函数');
        }
    }
    func(num);// 输出 value 函数

> 变体

    var num = 123;
    function func(value){
        value = 456;
        value();
        function value(){
            console.log('value 函数');
        }
    }
    func(num);// value not a function

> 案例 4

    var arr ='a,b,c,d,e,f,g'.split(',');
    for(var i=0; i<arr.length;i++){
        // console.log(arr[i]);
        setTimeout(function(){
            console.log(arr[i]);//七个 undefined
        },0);
    }
    //在js中，js是一个单线程的程序，因此只可能是一个函数完成以后才可以继续执行其它函数。
    // 而在setTimeout方法中，实际上并不是立即执行函数，而是将这个函数放到一个数组中(事件队列)。
    // 分析：setTimeout在事件队列中，所以他最后执行，for执行完成后i=7；
    //       所以打印七个 arr[7] = undefined;

> 事件队列

     /* 1.
    setTimeout(function(){
        console.log(1);
    },0);
    setTimeout(function(){
        console.log(2);
    },2000);
    setTimeout(function(){
        console.log(3);
    },0);
    
    console.log('over');*/
    //over 1 3 2
    
    setTimeout(function(){
        console.log(1);
    },0);
    setTimeout(function(){
        console.log(2);
    },0);
    setTimeout(function(){
        console.log(3);
    },0);
    
    console.log('over');
    // over 1 2 3

> 如何解决输出`a,b,c,d,e,f,g`，而不是输出 `undefind` ，利用闭包

    // 要解决这个问题就是在执行循环的时候，调用 setTimeout 的时候将 i 值记录下来，
    // 自调用函数，在循环过程中保留了i的取值
    var arr ='a,b,c,d,e,f,g'.split(',');
    for(var i=0; i<arr.length;i++){
        // console.log(arr[i]);
        setTimeout((function(i){return function(){
            console.log(arr[i]);
        };})(i),0);
    }
    //a,b,c,d,e,f,g


#### 5. 绘制作用域链
1.  将 0 级链中所有的变量, 函数等标识符放在一起. 绘制的时候就将其绘制成一条直线即可.
2. 凡是函数可以创建新的作用域, 因此在函数下拉一条直线. 着条直线所在的函数是 n 级链, 那么这条链就是 n+1 级链
3. 如果需要分析代码的执行结果, 将图画出来以后再来看代码的执行. 从代码的运行开始来在图中标注变量的值.

代码

    var num = 123;
    function func(){
        function foo(){
            var num = 456;
        }
    }

> 作用域链图<br>
![]({{'/styles/images/javascriptoo/varChain.png'}})