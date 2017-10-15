---
layout: post
title: "JavaScriptoo第一课"
date: 2017-09-18 21:12:20 +0800 
categories: javascript面向对象
tag: javascript
---
* content
{:toc}

> [JavaScript面向对象MDN](https://developer.mozilla.org/zh-CN/search?q=javascript%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1&topic=apps&topic=html&topic=css&topic=js&topic=api&topic=canvas&topic=svg&topic=webgl&topic=mobile&topic=webdev&topic=http&topic=webext)

本章主要是面向对象的引入，由面向过程到面向对象编程以及javascript面向对象部分的相关概念的理解。

<!-- more -->

#### 1. 面向对象概念
##### 1.1 传统的面向过程编程
   * 特点：所有的事情都是按照先后顺序执行
   * 开发方式：注意细节与步骤的开发方式
   
##### 1.2 面向过程案例
> 需求：英文输入法下控制台输出键盘输入的字符

     <body>
       <input type="text" id="txt">
     </body>
     <script type="text/javascript">
        var input = document.getElementById('txt');
        input.onkeydown = function(e){
           //触发键盘事件
           //KeyboardEvent {isTrusted: true, key: "5", code: "Numpad5", location: 3, ctrlKey: false, …}
           console.log(e);
           //上边的数字Digit，不是右边的数字键盘Numpad
           if (e.keyCode<48 || e.keyCode>58) {
            return false;
           }
        }
     </script>

> 技巧：编程时忘了有哪些事件，除了搜索意外，可以利用控制台找到该对象的id，在控制台输入id，回车返回的是对象则遍历，以这个input为例`for(var k in input){ if(k.slice(0,5)==='onkey'){console.log(k)}}`获取的就是以onkey开始的事件`onkeydown` 
`onkeypress` `onkeyup`

__取消事件的默认行为`return false`和`e.preventDefault(false)`。__

##### 1.3面向对象编程概念
1. 对象：泛化的概念，含义你所关注和使用的目标，对象可以是具体的可以显示出来的, 可以摸得到的看得到的 物品或 看不到的抽象的思想等。侧重点就是你所关注的东西。
2. 面向对象编程：使用对象来编程。要完成某件事情, 先看看有没有对象可以完成, 如果有直接让对象来完成。否则在自己实现。

##### 1.4面向对象封装input案例

> 可以在任何需要的地方添加input，写一个对象完成所需要的事。

        function NumberInput(){
        this.Dom = document.createElement('input');
        this.Dom.onkeydown = function(e){
            if (e.keyCode < 48 ||e.keyCode >58) {
               console.log(e);
               return false;
            }
        };
    
        this.appendTo = function(dom){
            dom.appendChild(this.Dom);
        };
    }
    
    new NumberInput().appendTo(document.body);
    
    new NumberInput().appendTo(document.getElementById('dv'))

##### 1.5 面向对象与面向过程的关系

> 面向对象和面向过程是交织在一起的，
> 在开发的时候, 一般都是在考虑使用对象去完成任务

1. 如果对象存在, 则直接使用对象来完成
2.  如果对象不存在, 则自己实现完成. 这个就是 面向过程的.
3.  如果对象不存在, 我使用 面向对象的 方式进行开发. 那么第一次是面向过程的. 而第二次或第 n 次就是面向对象.
4.  面向过程才是具体的实现, 面向对象只是对面向过程的一个封装.

#### 2.面向对象的实现

#####  2.1 什么是对象  
> 在代码中需要完成某些事情, 就需要对象来完成
        这个对象一定需要包含行为( 函数 ), 同时需要包含一些变量存储数据特征。

1. 对象的行为常常称为 方法( method )
2. 对象存储数据的变量 称为 对象的属性 ( property )

#####  2.2 javascript中的对象
> 在 JavaScript 中 键值对 就是 对象。 如果键值存储的是数据那么这个就是属性， 如果键值存储的是函数那么就是方法。


##### 2.3 创建对象
> 传统编程语言的方式（注意微软的Typescript TSC）<br>
 1. 定义好类好比有了一个模板。<br>
 2. 利用这个模板创建对象。<br>

     //Es6
     <script type="text/javascript">
         // 创建一个Person类型的对象，它有name，age的gender属性，方法sayHello
         class Person{
             // 固定写法
             // constructor 构造器
             // 使用var p = new Person()调用
             constructor(name,age,gender){
                this.name = name;
                this.age = age;
                this.gender = gender;
             }
     
             sayHello(){
                 console.log('name:'+this.name+',age:'+this.age+',gender:'+this.gender);
             }
             /*这个类定义了一个Person类型，同时有一个sayHello方法，还有name，age与gender属性*/
          }
     
          var p = new Person('xd',21,'男');
     </script>

> javascript的方式<br>
> 1.字面量<br>
> 2.工厂函数<br>
> 3.构造器<br>

1. 字面量

    1.  什么是字面量( literal )： 简单的说 就是 写出来就有数据值 就有类型的 量。<br>
    `123 "123". '123'`
    2. 在 js 中使用 花括号表示 一个 对象. 对象的成员 采用 键值对的 语法放在花括号中. 如果有多个成员采用逗号分隔.<br> 
    `var o4 = { name: 'jim', age: 19, gender: '男', sayHello: function () { console.log( '你好' ) } };`<br>一般在书写对象的时候会采用换行与缩进的办法使得代码利于阅读.
    3. 结合对象的动态特性来创建对象，所谓的 对象的 动态特性 就是指 对象原本没有成员, 我们可以人为的为其赋值一个属性或方法, 那么这个对象就有这个成员了.`var o5 = {};` `o5.name = 'jack';`这个写法有一个缺点, 就是必须是硬编码. 如果说可以允许在代码执行的时候 动态的给对象增加成员. 那才是真正的动态.换句话说, 希望有一个变量, 存储属性的名字, 然后有一个变量存储对应的属性的值, 那么利用这个两个变量动态给对象增加属性。
    4. 关联数组的用法`var o6 = {}; o6[ 'name' ] = 'tom';   /* 等价于 o6.name = 'tom';*/  o6[ key ]  = value`，能够动态的增加成员。

    __使用字面量创建对象很难实现复用__

2.  工厂函数
    
     1. 什么是复用( 可重用 )<br>
         字面量是写出来的对象, 如果需要重复使用, 就需要重新再写.因此没有办法复用.
     2. 可以考虑将创建对象的过程封装成函数,那么每次调用函数 就可以 创建一次 对象<br>
     
          `function createPerson() {`<br>
          &nbsp;`var o = {}; // 创建对象了`  <br>             
          &nbsp;`// ...`<br>
          &nbsp;`return o;`<br>
           `}` 

     3. 这样的函数有一个特点, 它就是为创建对象而存在的.因此该方法( 函数 ) 常常被称为 工厂方法( 函数 ),也因此在命名的时候, 也使用 factory<br>
          `function factory ( name, age, gender ) {` <br>
          &nbsp;`var o = {};` <br>
          &nbsp;`o.name = name;` <br>
          &nbsp;`o.age = age;` <br>
          &nbsp;`o.gender = gender;` <br>
          &nbsp;`return  o;` <br>
          `}`<br>

        __代码多的话很难维护，而且对象没有类型名字，每个对象可以对应一个类型__
，__查看对象数据类型`Object.prototype.toString.call()`__

3. 构造器  
     1. 使用工厂函数的缺点
           * 不方便与其他函数或方法区分开来
           * 利用工厂创建对象本质与字面量创建对象一样. 但是我们希望对象有类型. 而使用字面量的方式创建的对象都是统一的 Object, 因此需要新的方法.
     
     2. 使用构造函数的方式来创建对象
           * 构造函数与普通函数类似. 唯一的不同是 约定 构造函数的名字采用 Pascal 命令规则,字母首全部大写.
           * 调用构造函数 采用语法 new 构造函数名()
           * 调用构造函数以后就是返回新创建的对象. 不需要写 return.
           * 在构造函数的内部使用 this 来表示刚创建的对象. 如果需要给对象提供成员使用 语法  this.XXX = XXX;
           
               `function Person( name, age, gender )` {<br>
               &nbsp;&nbsp;`this.name = name;`<br>
               &nbsp;&nbsp;`this.age = age;`<br>
               &nbsp;&nbsp;`this.gender = gender;`<br>

               &nbsp;&nbsp;`this.sayHello = function () {`<br>
               &nbsp;&nbsp;&nbsp;`console.log( 'xixixi哈哈哈' );`<br>
               &nbsp;&nbsp;`};`<br>
               &nbsp;`}`<br>

              &nbsp;`var p = new Person( 'jim', 19, '女' );`<br>

     3. 打印类型
           * p instanceof Person --> true
           * Object.prototype.toString.call(p) -->[ Object Object]
           * p.constructor.name --> "Person"
           * 技巧控制台source监视输入p后面就会跟着出来Person

##### 2.4 使用对象

1. 根据需求，将需求的实现封装在构造函数内。`面向对象其实是对面向过程的封装？`
2. 创建对象 `var numInput = new NumInput();`
3. 具体实现在页面的哪个地方。

> jQuery中css方法为DOM添加样式 <br>
> `$('#dv1').css('border','1px solid red');` <br>
> 如果有多个样式属性需要添加到DOM中，可以利用对象给元素增加样式，这样避免的空值传空、传入参数顺序、传入参数数量的问题。

     //body中
     <div></div>
    
    //script中，简写
    function css(dom,styles){
       for(var k in styles){
          dom.style[k] = styles[k];
       }
    }
     
     var dom = document.querySelectorAll('div')[0];
     css(dom,{
        color:'red',
        backgroungColor:'blue',
        width:'100px',
        height:'100px'
        });

#### 3.面向对象的基本特征

 - 抽象性
 - 封装性
 - 继承性
 - 多态性

> 解释

1. 抽象性：所谓的抽象性就是说只有在具体的环境中对象才可以表示具体的事物. 而在程序设计中实际只考虑对象的目标数据.
2. 封装性：所谓封装性就是将具体的操作步骤打包起来, 那么不需要知道内部是如何处理的, 内部是多么的复杂, 只需要知道怎样用即可.
3. 继承在 [OOP](https://baike.baidu.com/item/OOP/1152915?fr=aladdin) 中表示的就是 扩展. 原来已经有一个对象了.在其基础上又增加一些东西得到一个新的对象.此时这个新对象就是继承自原来的那个对象.
4. 多态性:多态就是多种状态. 也就是说一个变量调用某一个方法. 得到的是不同的结果.js作为弱类型语言比较简单 <br> `var o;
o = 123;console.log( o.toString() );o = [ 1,2,3 ];console.log( o.toString() );o = function () {};console.log( o.toString() );`

####  4.Error对象

##### 4.1 异常的概念
> 原意: 异常就是在代码的运行过程中得到一个与预期结果不相同的答案, 这个就称为出现了异常.简单的解释, 就是在运行的时候出现了错误.

##### 4.2 处理异常

      //不会处理语法异常，处理逻辑异常
      try { // 尝试
                可能出现异常的代码                 
      } catch ( e ) {   // exception
                如果出现了异常就执行
      }

##### 4.3 异常对象的传递

> 如果代码中出现了异常, 那么异常代码的后面的内容就不再执行了。

     //foo2 就被称为被调用函数，foo1 就是调用函数
     function foo1 () {
                foo2()
      }

> 凡是代码中出现了异常, 那么如果没有 try-catch 处理异常，异常就会向该函数的调用函数进行传递，如果调用函数处理了 try-catch, 那么就停下来。如果调用函数没有处理, 那么就会传递放到调用函数的调用函数，直到最顶层, 出现错误. 后面的代码不再执行.

     //示例1
     function foo1(){
        foo2();
        console.log('foo1');
     }
      
      function foo2(){
        foo3();
        console.log('foo2');
      }
      
      function foo3(){
        var o = {};
        o.func();
        console.log('foo3');
      }
    
      foo1();

> 示例1中报错，什么也不输出。

     //示例2
     function foo1(){
        foo2();
        console.log('foo1');
     }
      
      function foo2(){
        foo3();
        console.log('foo2');
      }
      
      function foo3(){
        try{
             var o = {};
             o.func();
             console.log('foo3');
        catch(e){
     
        }
      
      }
    
      foo1();

> 示例2中，不报错，输出foo2，foo1，try中要尽可能少的包含代码，如果有错误，这里面的所有代码都不执行。

     //示例3
     function foo1(){
        foo2();
        console.log('foo1');
     }
      
      function foo2(){
        try{
            foo3();
        }catch(e){
    
        }
        console.log('foo2');
      }
      
      function foo3(){
        var o = {};
        o.func();
        console.log('foo3');
      }
    
      foo1();
    
> 示例3中 输出foo2，foo1。


##### 4.4 异常对象

 1. 在出现异常的时候，异常出现的位置以及异常的类型，内容等数据都会被封装起来。以 一个对象的形式被传递给catch后面的e中。
 2. 可以用语法 `throw 异常对象`。
 3. 异常对象 `new Error(错误消息)`。
 4. 异常的应用：如果定义了一个函数，需要函数提供指定类型的参数，如果传入的参数不正确，一般会抛出一个异常。

```
   //案例1
   console.log('123');
   throw new Error('自己定义的异常对象');
   console.log('456');
```

> 案例1只会输出123，会报错，错误之后的内容不执行。

     //案例二
    console.log('123');
    try{
        var e1 = new Error('自己定义的异常对象');
        throw e1;
    } catch(e){
        console.log( e1 === e);
    }
    console.log('456');

> 案例二，输出结果为 123 true 456 

#### 5.DOM对象

##### 5.1 DOM基本操作

1. 什么是DOM？<br>

> 文档对象模型就是将每一个 HTML 标签, 当做一个 节点 对象来看待, 那么所有的标签就可以看成一个 节点树。<br>`<html><head><title>123</title></head><body>123</body></html>`如果没有DOM对象，页面由字符串组成，将页面中的 123 换成 文本 456，遍历会很麻烦，所含标签越多解析就会越麻烦，但是<br>__如果将html当做节点树来处理的话，可以利用查找算法，js中提供的所有查找的方法，都是在解决DOM元素的查找问题，所以有了DOM树我们就可以用数据结构很容易对页面中的元素进行查找。__    

2. 基本操作
   - 增
   - 删
   - 改
   - 查

##### 5.2 DOM树 
> 重要

     //案例
     <body>
       你好, 今天<i>天气很好</i>.
     <body> 

> 在 html 中所有的内容都是节点对象 因此这里的文本标签全部都是对象<br>
因此 "你好, 今天" 是一个对象<br>
i 标签也是一个对象<br>
"添加很好" 也是一个对象<br>

    //结构
    body
        你好, 今天
                i
                    天气很好


#### 6.引用类型和值类型

##### 6.1 数据类型

1. 基本类型( 简单类型, 值类型 ): 数字 number, 字符串 string, 布尔 boolean；
2. 复杂类型( 复合类型, 引用类型 ): 对象( 数组, Object, 正则, ... ), 函数；
3. 空类型: undefined, null；

##### 6.2 存储模型

1. 基本类型的变量存储数据本身, 比如 var num = 123; 在内存中开辟一段内存, 这个内存就是 num, 里面存储的 就是 数字 123。
2. 引用类型就不一样了, 因为引用类型内的数据个数可以很大也可以很小. 因此不太容易说清楚要开辟多少内存存储数据.引用类型的变量只是存储对象的引用( 地址 ), 真正的数据存储在一个单独的内存中。<br>
`var p = { name: 'jim', age: 19, gender: '男', jiejie: { name: 'jiejie', age: 20, gender: '女' } }`

##### 6.3 作为参数传递的特征

1. 在 赋值 过程中 值类型与引用类型的区别
    
    - 值类型: 值类型在赋值的时候是将数据取出来, 拷贝一份, 然后将拷贝的结果存储到 等号 左边的 变量中. 因此数据有两份.修改一个值，另一个值不变。数据有两份。
    - 引用类型: 引用类型在使用的时候都是使用变量来处理数据. 那么在赋值的时候,就是将一个变量中的数据__(这个数据就是引用类型的地址)__取出来, 拷贝一份,将拷贝的数据(地址)存储到另一个变量(地址)中.由于引用类型的变量中存储的是引用(地址)而不是数据本身. 因此并没有将数据拷贝一份.内存中依旧只有一份数据,而是有两个变量同时执行着一个数据，p1和p2同一个地址修改p1.name那么p2.name也会变。

2. 在 参数传递过程中值类型与引用类型的区别

```
    function foo( num ){

    }
```

 - 值类型: 在传入数据后, 是将值类型变量中存储的数据传入到函数中.因此函数内的数据变化与函数外无关.如果需要得到数据, 必须将数据返回才可以得到数据.
 -  引用类型: 在传入数据的时候, 是将变量的数据拷贝一份, 但是拷贝的是引用. 就是说在函数内部的这个变量依旧指向最开始的对象. 也就是说在函数内外, 虽然两个变量不同, 但是指向的是同一个对象.

##### 6.4 深拷贝与浅拷贝对于对象
1.普通拷贝：所谓的拷贝是需要将目标复制一份, 也就是说需要存在两个个体。

```
    var num = 123;
    var n = num;
    // 因为在内存中有两个 123 因此这里是拷贝

    var o = { num: 123 }
    var obj = o;
    // 不是拷贝, 而是变量的引用发生了拷贝, 而数据只有一份
```

2.浅拷贝 

```
            var p = {
                name: 'jim'
            }
            // 如果要实现拷贝的话 该怎么去做?
            1> 需要有一个对象    var p1 = {}
            2> 需要保证两个对象有相同的属性与方法
                p1.name = p.name
            3> 如果有很多属性?
                function clone( obj ) {
                    var o = {};
                    for ( var k in obj ) {
                        o[ k ] = obj[ k ];
                    }
                    return o;
                }
```

```
  //浅拷贝案例
  function clone(obj){
    var o = {};
    for(var k in obj){
        o[k] = obj[k];
    }
    return o;
  }
  var o1 = { num:123 };
  var o2 = clone(o1);
  console.log('o1.num = '+o1.num);//123
  console.log('o2.num = '+o2.num);//123

  o1.num = 456;
  console.log('o1.num = '+o1.num);//456
  console.log('o2.num = '+o2.num);//123 
```

> 所谓的浅拷贝就是只拷贝当前对象, 而不拷贝对象中引用的其他对象( 不拷贝所有的 对象 )

3.深拷贝
> 所谓的深拷贝就是将所有涉及的对象全部拷贝一份, 在内存中两个对象是完全独立的。

```     
       //待拷贝对象
        var p = {
             name: 'jim',
             car: {
                 name: '法拉利'
             }
         }
```

```
       // deep clone
            function deepClone( obj ) {
                var o = {};
                for ( var k in obj ) {
                    if ( typeof obj[ k ]  == 'object' ) {
                        o[ k ] = deepClone( obj[ k ] );
                    } else {
                        o[ k ] = obj[ k ];
                    }
                    //简化
                    // o[ k ] = typeof obj[ k ] === 'object' ? deepClone( obj[ k ] ) : obj[ k ];
                }
                return o;
            }

         var p = {
             name: 'jim',
             car: {
                 name: '法拉利'
             }
         }

         var p1 = clone(p);
         console.log('p.name = ' + p.name',p.car.name = ' + p.car.name);//jim 法拉利
         console.log('p1.name = ' + p1.name',p1.car.name = ' + p1.car.name);//jim 法拉利

         p1.name = 'jimi';
         p1.car.name = '兰博基尼';
         console.log('p.name = ' + p.name',p.car.name = ' + p.car.name);//jim 法拉利
         console.log('p1.name = ' + p1.name',p1.car.name = ' + p1.car.name);//jimi 兰博基尼
```

>图文对比

1. 浅拷贝<br>
![浅拷贝]({{'/styles/images/javascriptoo/clone.png'}})
2. 深拷贝<br>
![深拷贝]({{'/styles/images/javascriptoo/deepclone.png'}})