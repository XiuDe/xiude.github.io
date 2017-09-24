---
layout: post
title: "js高程3阅读笔记第六章"
date: 2017-09-24 11:36:20 +0800 
categories: javascript面向对象
tag: js高级程序设计3
---
* content
{:toc}

ECMA-262把对象定义为：“无序属性的集合，其属性可以包含基本值、对象或者函数。”整合一下js面向对象部分的知识体系。

<!-- more -->


### 1. 理解对象

> 最初创建对象new Object()，到使用字面量的方式。

#### 1.1 属性类型

1. ES5定义只有内部ECMA-262第5版在定义只有内部才用的特性（attribute）时，描述了属性（property）的各种特征。
2. ES中有两种属性：数据属性和访问器属性。

##### 1.1.1 数据属性
> 数据属性包含一个数据值的位置。在这个位置可以读取和写入值。<br>
> 描述数据属性行为的特性。

1.  `[[Configurable]]`：表示能否通过delete删除属性从而重新定义属性，能否修改属性的特 性，或者能否把属性修改为访问器属性。`delete person.name;`
2.  `[[Enumerable]]`：表示能否通过 for-in 循环返回属性。
3.  `[[Writable]]`：表示能否修改属性的值。
4.  `[[Value]]`：包含这个属性的数据值。读取属性值的时候，从这个位置读；写入属性值的时候， 把新值保存在这个位置。

> 修改属性默认的特性，必须使用 ECMAScript 5的 Object.defineProperty()方法。这个方法 接收三个参数：属性所在的对象、属性的名字和一个描述符对象。其中，描述符（descriptor）对象的属性必须是：configurable、enumerable、writable 和 value。<br>
> <br>
> 如果对特性进行了修改，在非严格模式下，操作将被忽略；在严格模式下，操作将会导 致抛出错误。<br>
> <br>
> 可以多次调用 Object.defineProperty()方法修改同一个属性，但在把 configurable 特性设置为 false 之后就会有限制了。<br>
> <br>
> 在调用 Object.defineProperty()方法时，如果不指定，configurable、enumerable 和 writable 特性的默认值都是 false。

##### 1.1.2 访问器属性
> 访问器属性不包含数据值；它们包含一对儿getter和setter函数（不过，这两个函数都不是必需的）。<br>
> 访问器属性的4个特性。

1. `[[Configurable]]`：表示能否通过delete删除属性从而重新定义属性，能否修改属性的特 性，或者能否把属性修改为数据属性。
2. `[[Enumerable]]`：表示能否通过for-in循环返回属性。对于直接在对象上定义的属性，这 个特性的默认值为 true。 
3. `[[Get]]`：在读取属性时调用的函数。默认值为 undefined。
4. `[[Set]]`：在写入属性时调用的函数。默认值为 undefined。

> 访问器属性不能直接定义，必须使用 Object.defineProperty()来定义。<br>
> <br>
> 在严格模式下，尝试写入只指定了 getter 函数的属性会抛出错误，在非严格模式下则会忽略。类似地，只指定 setter 函数的属性也 不能读，否则在非严格模式下会返回 undefined，而在严格模式下会抛出错误。 <br>
> <br>
> 在Object.defineProperty()方法之前使用的是非标准的__defineGetter__()和__defineSetter__()。

##### 1.2 定义多个属性

1. ES5 定义了一个 Object.defineProperties()方法，用来定义多个属性。
2. 接收两个对象参数：第一 个对象是要添加和修改其属性的对象，第二个对象的属性与第一个对象中要添加或修改的属性一一对应。
3. 对一个空对象想要定义数值属性和访问器属性，可以同时定义。
4. 在调用 Object.defineProperties()方法时，如果不指定，configurable、enumerable 和 writable 特性的默认值也都是 false。

##### 1.3 读取属性的特性

1.  ES5的Object.getOwnPropertyDescriptor()方法，可以取得给定属性的描述符。
2.  接收两个参数：属性所在的对象和要读取其描述符的属性名称。
3.  返回值是一个对象，如果是访问器属性，这个对象的属性有configurable、enumerable、get 和 set；
4.  如果是数据属性，这 个对象的属性有 configurable、enumerable、writable 和 value。

#### 2. 创建对象
> Object构造函数或对象字面量都可以用来创建单个对象，但这些方式有个明显的缺点：使用同 一个接口创建很多对象，会产生大量的重复代码。

##### 2.1 工厂模式
> 工厂函数抽象了创建具体对象的过程，，用函数来封装以特定接口创建对象的细节。

```
function createPerson(name, age, job){     
    var o = new Object();     
    o.name = name;     
    o.age = age;     
    o.job = job;     
    o.sayName = function(){         
        alert(this.name);     
    };         
    return o; 
} 
 
var person1 = createPerson("Nicholas", 29, "Software Engineer"); 
var person2 = createPerson("Greg", 27, "Doctor"); 
```

> 工厂模式虽然解决了创建多个相似对象的问题，但却没有解决对象识别的问题（即怎样知道一个对象的类型）。--> 新模式构造函数模式

##### 2.2 构造函数模式
> 利用构造函数模式重写工厂模式

```
function Person(name, age, job){     
    this.name = name;     
    this.age = age;     
    this.job = job;     
    this.sayName = function(){         
        alert(this.name);     
    };     
} 

var person1 = new Person("Nicholas", 29, "Software Engineer"); 
var person2 = new Person("Greg", 27, "Doctor"); 
```

> 构造函数模式与工厂模式的区别

1. 没有显式地创建对象；
2. 直接将属性和方法赋给了 this 对象； 
3. 没有 return 语句。 

> 构造函数本身也是函数，只不过可以用来创建对象。<br>
> <br>
> 要创建构造函数 Person 的新实例，需要用new操作符，这种方式调用构造函数的四个步骤：

1. 创建一个新对象； 
2. 将构造函数的作用域赋给新对象（因此 this 就指向了这个新对象）； 
3. 执行构造函数中的代码（为这个新对象添加属性）； 
4. 返回新对象。


##### 2.3 原型模式

