---
layout: post
title: "JavaScriptoo第三课"
date: 2017-09-18 21:53:20 +0800 
categories: javascript面向对象
tag: [javascript, javascript函数]
---
* content
{:toc}

> [JavaScript面向对象MDN](https://developer.mozilla.org/zh-CN/search?q=javascript%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1&topic=apps&topic=html&topic=css&topic=js&topic=api&topic=canvas&topic=svg&topic=webgl&topic=mobile&topic=webdev&topic=http&topic=webext)

本章总结了javascript面向对象部分的函数，Function的知识，附加图解以及代码案例对js面向对象函数部分作了解释。

<!-- more -->

#### 1. 函数的参数
##### 1.1 length属性
> 函数名的length属性。

  1. 在 js 中凡是定义了一个函数, 就是定义了一个对象. 
  2. 函数与一般的数据是一样的使用: 赋值, 可以调用.
  3. 函数作为对象有一个属性 length 该属性用于描述在定义的时候函数的参数个数<br>
  ```
    function foo1(){};
    function foo2( a,b,c ){}
    // 函数也是一个对象，也就是说 foo1 就是对象
    console.log(foo1.length);// 0
    console.log(foo2.length);// 3
  ```

   4. 应用：在工具台中调试回忆方法，检测函数的参数个数；<br>
  ```
   document.querySelectorAll.length 结果为：1
  ```
   
   
##### 1.2 arguments对象
> 所谓 arguments, 就是参数的复数形式.也就是在调用函数的时候,会给函数传入参数,但是有时不确定需要传入多少参数, 所有在调用时传入的参数都会被 arguments 获取到.简单的说 arguments 中存储的就是参数的集合。<br>在实际调用的时候 arguments 就是实际调用时传入的所有参数。

    //在js中，函数的参数也是动态的
    //argument并不是真正的数组，是伪数组
    function foo(){
     //判断调用函数的参数是否等于我们定义的函数参数
     console.log(foo.length==arguments.length);//false
     
     // 打印输出，调用函数时传入的所有参数
     for(var i=0;i<arguments.length;i++){
             console.log(arguments[i]);
        }
    }
    foo(1,2,3,4,5,6,7,8,9);

    问题: 如何判断函数参数调用符合函数的定义?
          如何判断调用时的参数个数与函数定义时的参数个数一样?
            函数名.length === arguments.length

__arguments使用注意事项：__<br>
> 使用 arguments 可以保证在函数定义不写参数的情况下, 也可以获得函数调用时传入的所有参数.因此在不确定函数有多少参数和需要函数有动态参数的时候使用.

1.比如, 求两个数组中较大的数字
     
    function max( a, b ) {
        return a > b ? a : b;
    }
*如果需要判断多个数字中较大的数字：*

    function max(){
        var args = arguments;//减少代码书写错误
        var maxNum = arguments[0];
        for(var i=1;i<arguments.length;i++){
            if (maxNum<arguments[i]) {
                maxNum=arguments[i];
            }
        }
        return maxNum;
    }
    
    console.log(max(1,5,3,9,11));//11

2.希望函数有动态参数, 在传入不同参数的时候有不同的结果.
> jq 中, css, attr, val, html, text, ...

*需求：实现一个 css 方法, 该方法提供两个参数, 一个是 dom 元素, 第二个是一个字符串用于解释获得什么样式属性值; 也可以带有三个参数, 第一个参数是 dom 元素, 第二个参数是字符串, 用于告知需要处理哪一个样式, 第三个参数就是对应的样式值.*

     css( div, 'border' ) -> 获得 div 元素的 border 样式属性的值
     css( div, 'border', '1px solid red' ) 为元素 div 设置 border 的样式.
> 实现

     //未处理传入类型，不严谨
    function css(){
        var args = arguments;
        if (args.length == 3) {
            //设置样式
            args[0].style[args[1]] = args[2];
        }else{
            //返回样式
            return args[0].style[args[1]];
        }
    }
    var dv = document.getElementById("dv");
    css(dv,"border","1px solid red");
    css(dv,"width","200px");
    css(dv,"height","100px");
    console.log(css(dv,"height"));//100px

#### 2. 将字符串作为代码执行
##### 2.1 eval函数
> 该函数可以将一个字符串作为一段 js 代码来执行。

    //console.log(num);会报错，num is not defined
    eval("var num = 123;");
    console.log(num);//123

> 应用，js学习网站

     <textarea id="txt" cols="50" rows="20"></textarea>
     <script type="text/javascript">
         var txt = document.getElementById("txt");
         txt.onkeyup = function(){
             // console.log(this.value);
             try{
                 eval(this.value);
             }catch(e){
                 console.log(e);
             }
         }
     </script>
     //敲代码实时展示
     var dv = document.createElement("div");
     dv.style.border = "1px solid red";
     dv.style.width = "100px";
     dv.style.height = "50px";
     document.body.appendChild(dv);

##### 2.2 Function函数

> `'var num = 123;'`创建`eval( 'var num = 123;' );`会在当前作用域中创建该变量,如果是在全局范围内,就会造成全局污染.使用 Function 函数来代替 eval 进行调用, 执行 字符串代码.

*Function可看做函数的构造函数*

    Function 就是用于生成一个函数. 使用语法
           new Funtion( '参数1', '参数2', ..., '参数N', '方法体' ) -> 返回函数

> 案例

    //求两数之和
    var func = new Function('a','b','return a + b;');
    console.log(func(25,10));//35
     /*
       Function 有三个参数
       最后一个参数相当于生成函数的函数体
       等价于
       function func(a,b){
          return a+b;
       }
    */
    
    // 求两个数字的乘积
    var func2 = new Function('a','b','return a*b;');
    console.log(func2(5,4));//20
    //打印9的乘法表
    for(var i=1;i<10;i++){
        console.log(i + ' * 9 = ' + func2(i,9));
    }
       
    // 求任意个数字的和
    // 方法一：字符串拼接
    var func3 = new Function('var sum = 0;for(var i=0;i<arguments.length;i++){sum+=arguments[i]} return sum;');
    console.log(func3(1,2,3,4,5,6));//21
    }
    //方法二：带有格式的字符串拼接:利于阅读
    var func4 = new Function(
           'var sum = 0;'+
           'for(var i=0;i<arguments.length;i++){'+
              'sum+=arguments[i]'+
           '} '+
           'return sum;'
        );
    //方法三：利用Ajax请求得到代码
    //方法四：使用模板字符串(ES6的语法，使用标准键盘数字键左边的点),轻松实现可换行字符串，普通字符串是不能实现换行的，会报错token。

#### 3. 函数与Function的关系

> 函数就是 Function 的实例. 即 Function就是函数的构造函数.实例对象有一个继承的 constructor 方法, 该方法指向就是构造函数.自己定义一个函数( 任意的函数 ), 可以知道函数的 constructor 就是 Function得到结论 函数就是 Function 的实例.

    function f1234567(){}  
    console.log(f1234567.constructor); //ƒ Function() { [native code] }
    console.log(f1234567.constructor == Function);// true
    


##### 3.1 函数，Function，与Function.prototype
![]({{'/styles/images/javascriptoo/prototypeFunction01.png'}})

##### 3.2基于函数的原型构造实例三角形

    Function 也是函数, 那么有什么结论呢?
        Function 就是 Function 的实例. 即 自己就是自己的实例.
        //证明：
        console.log(Function.constructor == Function)
    
> 图解

![]({{'/styles/images/javascriptoo/prototypeFunction02.png'}})

#### 4. Object 与 Function
> 原型对象的原型对象是谁？<br>
> 怎么访问原型对象的原型对象?
##### 4.1 Object 与 Function 的关系
> Object 是 函数, 因此在 Function 中, 它就是实例对象. 因此就构成一个三角形.

![]({{'/styles/images/javascriptoo/prototypeFunctionObject01.png'}})


*验证：*<br>

     console.log(Function.prototype.constructor == Function);// true
     console.log(Object.constructor == Function);// true

> 怎么实现Object访问Function.prototype?<br>
*无法实现直接访问，能实现间接访问*<br>
     console.log(Object.constructor.prototype == Function.prototype);//true

> 如何在调试的时候由当前的实例对象直接访问到它的原型对象？

![]({{'/styles/images/javascriptoo/prototypeFunctionObject02.png'}})<br>

<!-- __只有在页面调试的时候能用，编程中不能用__<br> -->
> 在控制台调试当前对象访问原型对象的过程中`Object.__proto__`和`Object.constructor.prototype`是一样的。`__proto__`每边都是两个下划线。<br>

![]({{'/styles/images/javascriptoo/prototypeFunctionObject03.png'}})<br>

    var arr = [];//数组实际上Array的实例
    //创建数组,[],[1,2,3]
    //new Array(),相当于[]
    //new Array(1,2,3),相当于[1,2,3]
    //new Array(10) 表示创建一个长度为10的数组，但是目前没有数据
    // var arr = [];arr.length=10;


这段代码继承关系和原型关系为:<br>
![]({{'/styles/images/javascriptoo/prototypeArray01.png'}})

> 自定义对象的关系

    function Person(){}
    var p = new Person();

在控制台中操作：<br>

![]({{'/styles/images/javascriptoo/prototypePerson01.png'}})

__得出结论：实例对象通过`__proto__`是可以访问原型对象的。__<br>
__在讨论 `.prototype` 的时候是通过 函数 访问原型__<br>
__在讨论 `.__proto__` 的时候是通过 实例对象 访问原型__<br>


> 在实际构建对象的时候, 扩展实例成员, 一般都使用 原型增加方法的形式( 原型继承 ).<br>
> 一般在使用的时候利用 构造函数`.prototype` 来添加, 不要使用 实例对象`.__proto__` 来添加.

<br>

> 原型对象继承于谁？<br>
> 原型对象的原型对象又继承于谁？-->原型继承的整个关系（原型链）

     //创建
     function Foo(){}
     var o = new Foo();
     //查看原型继承在控制台的Source查看比较方便
     //o -> Foo.prototype ->Object.prototype -> null
     //构成了对象的链式结构

![]({{'/styles/images/javascriptoo/prototypeChain01.png'}})<br>

![]({{'/styles/images/javascriptoo/prototypeChain02.png'}})<br>

__可以知道所有的对象都有原型,一步一步往上走,都会汇集到`Object.prototype`中,`Object.prototype`是顶级的对象.__<br>
<br>
__函数继承自Function，那么`Function.prototype`继承自谁？__<br>
*任意一个函数继承自 Function.prototype. 可以看做是 Function 的实例.而 Function.prototype 又是继承自 Object.prototype.*

![]({{'/styles/images/javascriptoo/prototypeChain03.png'}})<br>

![]({{'/styles/images/javascriptoo/prototypeChain04.png'}})<br>

##### 4.2 instanceof 运算符
>  该运算符判断某一个对象是否为某个构造函数的实例. 现在需要纠正.判断一个对象的原型链中是否含有这个构造函数的 `prototype` 所表示的对象.`o instancof Func`如果`Func.prototype`在`o`对象的原型链中,那么返回值就是 `true`, 否则就是 `false`. 
<br>
> 代码示例 1

    function Func(){}
    var o1 = new Func();
    console.log( o1 instanceof Func);//true 

> 代码示例 2

    function Func(){}
    function Foo(){}
    
    Func.prototype = new Foo();
    var o1 = new Func();
    console.log( o1 instanceof Func);//true 

![]({{'/styles/images/javascriptoo/prototypeinstanceof01.png'}})

> 代码示例 3

    function Func(){}
    var o1 = new Func();//o1移到这里
    function Foo(){}
    
    Func.prototype = new Foo();
    //赋值之后Func指向Func.prototype的线没有了。
    var o2 = new Func();
    console.log( o1 instanceof Func);

![]({{'/styles/images/javascriptoo/prototypeinstanceof02.png'}})

> 代码示例 4

    function Func(){}
    var o1 = new Func();
    function Foo(){}
    
    Func.prototype = new Foo();
    
    var o2 = new Func();
    console.log( o1 instanceof Func);//false
    
    // 能否想一个办法，让 new Foo() 在o1所表示的原型链上 
    // 原始的Func.prototype 就是 o1.__proto__
    // 将new Foo() 放在 o1.__proto__的原型中
    // 找到前面的 new Foo(),注意不能再new 一个新的
    // 原来的那一个就是 Func.prototype
    o1.__proto__.__proto__ = Func.prototype;
    console.log( o1 instanceof Func);//true

![]({{'/styles/images/javascriptoo/prototypeinstanceof03.png'}})


#### 5. 原型链的完整形式
1. 写一个构造函数, 该构造函数继承自 `Function.prototype`, 构造函数是 `Function` 的实例
2. 凡是写一个构造函数, 就有一个原型存在, 例如 `Foo.prototype`
3. 由构造函数所创建出来的对象, 继承自 `构造函数.prototype`
4. `构造函数.prototype` 继承自 `Object.prototype`
5. 使用`__proto__`可以同时实例直接访问原型

![]({{'/styles/images/javascriptoo/prototypeComplete.png'}})


> 补充常识

1<br>
![]({{'/styles/images/javascriptoo/test01.png'}})

2<br>

```
console.log(Object.constructor);//ƒ Function() { [native code] }
console.log(Function); //ƒ Function() { [native code] }
console.log(Array.constructor);//ƒ Function() { [native code] }
console.log(Function.constructor);//ƒ Function() { [native code] }
console.log(Object);// ƒ Object() { [native code] }
```

3<br>

![]({{'/styles/images/javascriptoo/test02.png'}})