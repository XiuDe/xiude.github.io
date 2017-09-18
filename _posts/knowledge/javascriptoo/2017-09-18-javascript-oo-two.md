---
layout: post
title: "JavaScriptoo第二课"
date: 2017-09-18 21:25:20 +0800 
categories: javascript面向对象
tag: [javascript, javascript原型]
---
* content
{:toc}

> [JavaScript面向对象MDN](https://developer.mozilla.org/zh-CN/search?q=javascript%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1&topic=apps&topic=html&topic=css&topic=js&topic=api&topic=canvas&topic=svg&topic=webgl&topic=mobile&topic=webdev&topic=http&topic=webext)

本章主要总结了原型，原型继承，混入以及混合式继承的概念包括图解和案例。

<!-- more -->

#### 1. 原型的基本概念

##### 1.1 为什么需要使用原型 

> 使代码的复用率达到最高

```
            //需求：写一个构造函数, 要求创建一个 Person 对象, 包含 name, age, gender 和 说话 sayHello 的方法
            function Person( name, age, gender ) {
                this.name = name;
                this.age = age;
                this.gender = gender;
                this.sayHello = function () {
                    console.log( '你好, 我是 ' + this.name );
                };
            }

            var p1 = new Person('jim',19,'man');
            var p2 = new Person('jim',19,'man');
    
            console.log( p1.sayHello == p2.sayHello );//false 
```
> 上面代码的问题是：如果将方法写在 构造函数的内部, 那么函数每次执行的时候 方法的 代码就会被解释一次. 因此每一个对象都包含一个一模一样的不同的方法. 因此会浪费资源. 需要考虑将这个方法复用.

__构造函数的执行本质是什么, 这里对数据的优化有没有什么问题?__<br>

__构造函数的执行本质就是 对象的 动态特性。__

1. 首先 new 是创建对象的运算符
2.  然后该对象中什么东西都没有, 再调用构造函数, 在构造函数中 使用 this 表示当前对象.
3. 利用对象的 动态特性 给 当前对象( this ) 增加成员.

> 解决方案：如果将方法写在 构造函数的内部, 那么函数每次执行的时候 方法的 代码就会被解释一次. 因此每一个对象都包含一个一模一样的不同的方法. 因此会浪费资源. 需要考虑将这个方法复用.将公用的函数提取出来，减少内存的开销。

    //优化   
    function sayHello(){
        console.log("Hello,I'm "+ this.name);
    }
    
     
    function Person(name,age,gender,say){
       this.name = name;
       this.age = age;
       this.gender = gender;
       this.sayHello = say;
    }
    
    var p1 = new Person('jim',19,'man');
    var p2 = new Person('jim',19,'man');
    
    console.log( p1.sayHello == p2.sayHello );//true

> 有没有更好的解决方案？<br>
> 让每一个由构造函数所创建出来的对象都共享一个方法就好了。*原型*

__在 js 中凡是定义了一个函数,就随之有一个对象存在.函数的prototype属性指向该对象.该对象默认只包含一个属性 constructor,再指向回构造函数.凡是由构造函数创建的对象都默认的与这个神秘对象连接起来,所谓的连接就是说凡是在这个神秘对象中提供的方法( 属性 ) 我们的对象都可以直接访问到__

__梳理一下:__

1. 凡是创建函数, 就会有一个对象存在( 神秘对象 ).
2. 这个神秘对象默认含有 constructor 属性, 指向这个函数.
3. 函数作为构造函数创建出来的对象可以直接使用这个神秘对象中的成员.


```
   function Person() {}
   Person.prototype.sayHello = function () { 
      console.log("Hello");
   };
   var p = new Person();
   p.sayHello();//Hello
   p.constructor.prototype.sayHello();//Hello
   console.log(p);
```

> 疑问 ：`p.sayHello();`与`p.constructor.prototype.sayHello();`的关系？

*原型继承：当前对象没有sayHello方法，是从原型对象上继承过来的，可以直接使用。*

##### 1.2 原型与构造函数和对象之间的关系

1.创建构造函数对象后

![]({{'/styles/images/javascriptoo/prototype01.png'}})

2.该构造函数含有函数

![]({{'/styles/images/javascriptoo/prototype02.png'}})

3.该构造函数声明的其他对象

![]({{'/styles/images/javascriptoo/prototype03.png'}})

__一定要区别开原型对象和原型属性__

#### 2. 原型继承

> js中没有规定具体标准语法来实现继承，只要按需求实现的方法都行，这里总结以下。除了这些方法，混入也是一种特殊的继承。

##### 2.1 继承的概念
1. 什么是继承：所谓的继承就是扩展, 在已有的对象上增加一些新的功能, 得到一个新的对象, 此时就是一个继承。`总结一下, 就是要从别人那里拿点东西过来`
2. 所谓的继承, 就是原本我没有, 但是我又需要, 那么有一个对象提供了对应的东西, 我将其拿过来, 让他成为我自己的东西. 那么我就有了.此时就称为 我继承自 这个对象。
3. 由于在 js 中没有继承语法结构( ES6 提供了继承的语法 )所以凡是涉及到原来一个对象没有, 但是从另一个对象中获得到了数据, 那么就是继承.

##### 2.2 原型继承
 对象 p 没有 sayHello 方法, 因为构造函数中什么都没有.但是 p 连接到 原型中了, 因此 对象 p 就可以调用 sayHello 方法.这个就是原型继承, 因为 p 没有, 但是 p 继承自原型对象, 所以 p 有了。

##### 2.3 代码实现
> 三种方式 1.一般的使用 2.替换原型的方式使用 3.Object.create方法

__1.__ 一般的使用：将所有的方法都加到 原型中, 使用对象的动态特性增加成员。

     //代码实现
    function Person(name,age,gender){
       this.name = name;
       this.age = age;
       this.gender = gender;
    }
    Person.prototype.sayHello = function(){
        console.log('你好，我是'+this.name);
    }
    var p1 = new Person('jim',19,'男');
    var p2 = new Person('tom',18,'女');
    p1.sayHello();//你好，我是jim
    p2.sayHello();//你好，我是tom
    console.log(p1.sayHello == p2.sayHello);//true
    
    //如果想继续向原型对象上添加
    Person.prototype.run = function () {};

__2. __替换原型的方式使用

1.想在原型对象上添加多个方法，之前的方式很麻烦
![]({{'/styles/images/javascriptoo/prototypeReplace01.png'}})

> 灰的是不用的，但是内存中还保存着。

2.新的方式<br>
![]({{'/styles/images/javascriptoo/prototypeReplace02.png'}})

    function Person(){ }//会创建一个原始的原型对象，在原始的原型对象中含有constructor属性
    
    Person.prototype = {
        sayHello : function(){
            console.log("Hello prototypeReplace");
        }
    }
    
    var p = new Person();//此时p就是最新的带有sayHello方法的对象
    p.sayHello();//Hello prototypeReplace

3.替换原型需要注意的问题

> 案例一

    function Person(){ }
    Person.prototype.sayHello = function(){
        console.log("原始对象的sayHello");
    }
    Person.prototype = {
        sayHello:function(){
            console.log("替换对象的sayHello");
        }
    }
    
    var p = new Person();
    p.sayHello();//替换对象的sayHello

__替换对象的sayHello的原因__<br>
![]({{'/styles/images/javascriptoo/prototypeReplace03.png'}})

> 案例二

    function Person(){ }
    
    var p1 = new Person();
    
    Person.prototype.sayHello = function(){
        console.log("原始对象的sayHello");
    }
    
    var p2 = new Person();
    
    Person.prototype = {
        sayHello:function(){
            console.log("替换对象的sayHello");
        }
    }
    
    var p3 = new Person();
    p1.sayHello();//原始对象的sayHello
    p2.sayHello();//原始对象的sayHello
    p3.sayHello();//替换对象的sayHello

4.替换法实现继承的问题分析

案例二的实现<br>

![]({{'/styles/images/javascriptoo/prototypeReplace04.png'}})

> 原始对象`Person.prototype.sayHello = function(){...}`声明后，由于构造函数Person默认会指向原始的原型对象p1和p2都会执行原始对象的sayHello方法。但是使用原型继承替换`Person.prototype = { sayHello : function(){ ... }}`后构造函数Person将指向新的原型对象，在声明`Person.prototype`之后的p3对象将指向新的替换的原型，执行新的替换的sayHello方法。

__3. __ Object.create方法

     语法: 
         Object.create ( 对象 ) -> 对象
         该方法的含义返回一个原型对象为指定参数对象的对象

> 代码

    var o = {
        sayHello : function(){
            console.log("带有sayHello方法的对象");
        }
    }
    
    var newObj = Object.create(o);
    
    //返回 的对象是 newObj 的原型对象就是o，即newObj继承自o。
    
    newObj.sayHello();//带有sayHello方法的对象

> Object.create方法由来，早在ES3的标准中是没有的,最早是道格拉斯(JavaScript 语言精粹的作者)提出的办法来实现继承.

     //ES3中的实现语法
    if (!Object.create) {
    
        Object.create = function(obj){
            function F(){}
            F.prototype = obj;
            return new F();
        }
     
    }

> 在ES5中该方法被标准化，成为了内置函数，可以直接使用。

#### 2. 相关概念

##### 2.1 实例，对象，构造器

> 类 class,一般将js中的构造函数称为类.所以一个对象的类型名就是构造函数的名字.构造函数, 构造器( constructor ), 构造方法 概念是一样的.利用构造函数 创建 对象的过 程称为 实例化,因此将构造函数所创建出来的对象又称作为构造函数的实例.有些时候会将属性称为 字段.

##### 2.2 静态成员, 实例成员
> 凡是定以后由实例对象所使用的成员就是实例成员<br>凡是由构造方法所使用的成员就是静态成员

__访问规则__<br>
__1.__ 实例成员只允许在实例方法中直接被访问, 而在静态方法中不能访问

    function Person(){
        this.name = 'jim';
        this.sayHello = function(){
            console.log(this.name);
        }
    }
    
    Person.sayHello = function(){
        // 在方法当中this表示的是当前对象
        // 
        //
        console.log( this == Person);// true
        console.log( this.name);//Person,无法访问到jim
    }
    
    Person.sayHello();

> 如何访问？如果要在静态方法中使用实例成员1.将实例对象传入方法中使用；2.在静态方法中创建实例对象，然后使用；

    function Person(){
        this.name = 'jim';
        this.sayHello = function(){
            console.log(this.name);
        }
    }
    
    // 解决方案 1 将实例对象传入方法中使用
     Person.method1 = function(obj){
       console.log( obj.name );
    }
    var obj1 = new Person();
    Person.method1(obj1);//jim
    
    // 解决方案 2 在静态方法中创建实例对象，然后使用
    Person.method2 = function(){
        var o = new Person();
        console.log( o.name );
    }
    Person.method2();//jim

__2. __无论是在实例方法中还是在静态方法中, 都可以访问静态成员( 只要保证可以访问到构造函数即可 )

    /*function Person(name,age,gender){
        this.name = name;
        this.age = age;
        this.gender = gender;
    }*/
    
    /*两种写法完全等价*/
    
    /*低版本浏览器不支持，大部分浏览器不支持*/
    // 语法糖
    class Person{
     
        constructor(name,age,gender){
           this.name = name;
           this.age = age;
           this.gender = gender;
        }
        // sayHello方法默认就加到了原型中
        sayHello(){
            console.log("你好，我是" + this.name);
        }
    }
    
    var p = new Person("张三",20,"男");
    p.sayHello();//你好，我是张三


#### 3. 混入

##### 3.1 混入的概念

> 就是将一个对象的成员加入到另一个对象中, 那么就可以完成对象功能的一个扩展( 继承 )。

    var o1 = {
        name:"张三"
    };
    var o2 = {
        sayHello:function(){
            console.log(this.name);
        }
    }
    
    o2.sayHello();//undifined
    // 混入
    o2.name = o1.name;
    o2.sayHello();//张三，此时说o2继承自o1
    

> 如果对象的成员越来越多怎么办？封装mix方法

            var o1 = { 
                name: '张三',
                age: 19,
                gender: '男',
                phone: '12345678909',
                qq: '123245678'
            };
            var o2 = {
                sayHello: function () {
                    console.log( this.name );
                }
            }


##### 2.2 封装mix函数

     //obj1 继承 obj2
     function mix(obj1,obj2){
         for(var k in obj2){
            obj1[k] = obj2[k];
         }
     }

##### 2.3 extend

> 这个单词的含义原意就是扩展, 所谓的混入就是将对象加入到一个对象中, 扩展该对象.

     //obj1 继承 obj2
     function mix(obj1,obj2){
         for(var k in obj2){
            obj1[k] = obj2[k];
         }
     }

#### 4. 混合式继承

##### 4.1 已有的常用继承
__1.__原型式继承: 所谓原型式继承就是对象会自动的连接到其原型对象中, 而使用原型对象中所提供的成员。<br>
 一般在编写代码的时候都会将数据( 字段, 属性 )定义在构造器中, 而将方法定义在原型中.所以代码分成两部分.

     function Person( name ) {
         this.name = name;
     }
     Person.prototype.sayHello = function () {
         console.log( this.name );
     };
    

__2.__混入继承: 所谓的混入继承, 就是将一个对象中的成员遍历一遍, 依次加到另一个对象中, 那么另一个对象就具有了这个对象的功能. 从而实现继承.
 一般代码都会封装一个 mix 或 extend 函数( 方法 ). 使用该方法混入.

        function extend( obj1, obj2 ) {
            for ( var k in obj2 ) {
                obj1[ k ] = obj2[ k ];
            }
        } 

##### 4.2 混合式继承

*js忍者秘籍中有解释*

> 混合式继承就是将 原型式继承与 混入继承组合到一起使用。由于只介绍了这两种继承方式, 因此这么规定. 但是混合式继承的原意就是说将多种继承方式用到一起来完成继承.

__规则__

1. 基本的继承还是使用原型式. 也就是说在原型中提供方法和共享的属性.在构造函数中提供独立的属性。
2. 在原型中提供成员的时候采用混入式继承。

```
    //
    function extend(obj1,obj2){
        for(var k in obj2){
           obj1[k] = obj2[k];
        }
    }

    function Person(options){
        extend( this,options);
    }
    
    //简化Person.prototype.extend
    Person.fn = Person.prototype;
    Person.fn.extend = function(obj){
        // 将obj中的成员添加到 Person.prototype
        // 这里的this就是指的 Person.prototype
        extend(this,obj);
    }
    
    //扩展
    Person.fn.extend({
        sayHello:function(){
            // 技巧
            console.log("你好，我是" + (this.name||"无名") );
        }
    });

    var p1 = new Person({ 
        name:"张三"
    });
    
    var p2 = new Person({ 
        
    });
    
    p1.sayHello();//你好，我是张三
    p2.sayHello();//你好，我是无名
```

#### 5. 属性的搜索原则

1. 所谓的属性搜索原则, 就是说对象在访问某一个属性的时候采取的一种数据检索机制.
一般我们的对象都是动态, 因此对象中有没有属性或方法是不确定. 在js 中一个对象访问一个成员,首先会在当前对象中检索是否包含该成员,如果包含则使用.如果不包含,那么就会自动的在其原型对象中查找是否有这个成员.
2. 这是读取数据, 如果是设置数据. 凡是设置数据都是在当前对象中进行操作.

> 实例

     /* 一、当前对象和原型对象都有name
    function Person(){
        this.name = "张三";
    }
    Person.prototype.name = "张大三";
    var p = new Person();
    console.log(p.name);//张三*/
    
    /*  二、只有原型对象有name
    function Person(){
        // this.name = "张三";
    }
    Person.prototype.name = "张大三";
    var p = new Person();
    console.log(p.name);//张大三*/
    
    /*  三、设置修改属性，都是在当前对象，无法操作原型对象
    function Person(){
        // this.name = "张三";
    }
    Person.prototype.name = "张大三";
    var p = new Person();
    p.name = "李四";
    console.log(p.name);//李四，当前Person下，命名为李四*/

#### 6.原型构造实例三角形
![]({{'/styles/images/javascriptoo/prototype01.png'}})

