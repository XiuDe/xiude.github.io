---
layout: post
title: "js高程3第六章阅读回顾"
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

> 对象的 constructor 属性初是用来标识对象类型的。但是，提到检测对象类型，还是 instanceof 操作符要更可靠一些。所有对象均继承自 Object。

```
console.log(person1 instanceof Object);// true
console.log(person1 instanceof Person);// true
console.log(person2 instanceof Object);// true
console.log(person2 instanceof Person);// true
```

- 将构造函数当作函数 
    + 构造函数与其他函数的唯一区别，就在于调用它们的方式不同。
    + 任何函数，只要通过 new 操作符来调用，那它就可以作为构造函数；而 任何函数，如果不通过 new 操作符来调用，那它跟普通函数也不会有什么两样。
- 构造函数的问题 
    + 每个方法都要在每个 实例上重新创建一遍。
    + 构造函数声明的函数是不同实例上的同名函数，是不相等的。
    + `alert(person1.sayName == person2.sayName);  //false `

```
 this.sayName = function(){         
    alert(this.name);     
}; 
与 
this.sayName = new Function("alert(this.name)"); 
逻辑等价
```

- 构造函数问题解决方案
    + 利用指针将函数指向全局作用域下的函数，没有封装性可言。

```
function Person(name, age, job){     
    this.name = name;     
    this.age = age;     
    this.job = job;     
    this.sayName = sayName; 
} 
 
function sayName(){     
    alert(this.name); 
}
```

- 构造函数问题解决方案的解决方案 -->原型模式

##### 2.3 原型模式

1. 创建的每个函数都有一个prototype（原型）属性，这个属性是一个指针，指向一个对象(原型对象)， 而这个对象的用途是包含可以由特定类型的所有实例共享的属性和方法。
2. prototype是构造函数的属性。

```
Person.prototype.sayName = function(){     
    alert(this.name); 
};

var person1 = new Person();
var person2 = new Person();
console.log(person1.sayName == person2.sayName);  //true 
```

###### 2.3.1 原型对象
1. 只要创建了一个新函数，就会根据一组特定的规则为该函数创建一个 prototype 属性，这个属性指向函数的原型对象。
2. 在默认情况下，所有原型对象都会自动获得一个constructor（构造函数）属性，这个属性包含一个指向 prototype 属性所在函数的指针。
3. 调用构造函数创建一个新实例后，该实例的内部将包含一个指针（内部 属性），指向构造函数的原型对象。ECMA-262第5版中管这个指针叫`[[Prototype]]`。Firefox、Safari 和 Chrome 在每个对象上都支持一个属性`__proto__`；

> 图片实例

![](/styles/images/ProfessionalJavaScriptForWeb3/01prototype__proto__.png)

- 判断实例与该实例的原型对象是否存在`[[Prototype]]`关系-->`isPrototypeOf()`方法

```
console.log(Person.prototype.isPrototypeOf(person1));  //true
```
- ES5增加了一个新方法`Object.getPrototypeOf()`，在所有支持的实现中，这个 方法返回`[[Prototype]]`的值。

```
console.log(Object.getPrototypeOf(person1) == Person.prototype); //true 
// 返回的是这个对象的原型
console.log(Object.getPrototypeOf(person1).name); //"Nicholas" 
```

- 实例的construction是由原型对象继承过来的，所以`实例.constructor`指向构造函数。

```
console.log(p1.constructor = Person.prototype.constructor);// true
```
- 可以通过对象实例访问保存在原型中的值，但却不能通过对象实例重写原型中的值。
- 实例中添加与原型对象的同名属性，该属性将会屏蔽原型中的那个同名属性。
    + 如果在实例中把这个属性为null，不会恢复其指向原型的连接。
    + 使用`delete`操作符则可以完全删除实例属性，从而让我们能够重新访问原型中的属性。`delete person1.name; `
- 使用`hasOwnProperty()`方法可以检测一个属性是存在于实例中，还是存在于原型中。`console.log(person1.hasOwnProperty("name"));  //false`

###### 2.3.2 原型与in操作符
- 有两种方式使用 in 操作符：单独使用和在 for-in 循环中使用。
     + 在单独使用时，in 操作符会在通过对象能够访问给定属性时返回 true，无论该属性存在于实例中还是原型中。
     
     ```
     console.log(person1.hasOwnProperty("name"));  //false 
     console.log("name" in person1);  //true 
     ```
- 确定属性到底是存在于对象中，还是存在于 原型中。

```
function hasPrototypeProperty(object, name){     
    return !object.hasOwnProperty(name) && (name in object); 
} 
// true:在原型不再实例上
// false：在实例上
```
- 取得对象上所有可枚举的实例属性，用ES5的`Object.keys()`方法。
    + 接收一个对象作为参数，返回一个包含所有可枚举属性的字符串数组。

```
function Person(){ } 
 
Person.prototype.name = "Nicholas"; 
Person.prototype.age = 29; 
Person.prototype.job = "Software Engineer"; 
Person.prototype.sayName = function(){     
    alert(this.name);  
}; 
 
var keys = Object.keys(Person.prototype); 
alert(keys);       //"name,age,job,sayName" 
 
var p1 = new Person(); 
p1.name = "Rob"; 
p1.age = 31; 
var p1keys = Object.keys(p1); 
alert(p1keys);    //"name,age
```

- 要得到所有实例属性，无论它是否可枚举，都可以使用` Object.getOwnPropertyNames() `方法。

```
var keys = Object.getOwnPropertyNames(Person.prototype); 
alert(keys);    //"constructor,name,age,job,sayName" 
```

- `Object.keys()`和 `Object.getOwnPropertyNames()`方法都可以用来替代 `for-in` 循环。

###### 2.3.3 原型语法
```
function Person(){ } 
 
Person.prototype = {     
    name : "Nicholas",     
    age : 29,     
    job: "Software Engineer",     
    sayName : function () {         
        alert(this.name);     
    } 
};
```
- 将 Person.prototype 设置为等于一个以对象字面量形式创建的新对象。 终结果相同，但有一个例外：constructor 属性不再指向 Person 。
- 本质上完全重写了默认的 prototype 对象，因此 constructor 属性也就变成了新 对象的 constructor 属性（指向 Object 构造函数），不再指向 Person 函数。
- instanceof 操作符还能返回正确的结果，但通过 constructor 已经无法确定对象的类型。

```
var friend = new Person(); 
 
alert(friend instanceof Object); //true 
alert(friend instanceof Person);  //true 
alert(friend.constructor == Person);//false 
alert(friend.constructor == Object); //true 
```
- 解决方案：将constructor设为特定值

```
function Person(){ } 
 
Person.prototype = {     
    constructor : Person,     
    name : "Nicholas",     
    age : 29,     
    job: "Software Engineer",     
    sayName : function () {         
        alert(this.name);     
    } 
};
```
- 存在的问题：以这种方式重设 constructor 属性会导致它的`[[Enumerable]]`特性被设置为 true。默认 情况下，原生的 constructor 属性是不可枚举的，重新设置为不可枚举用`Object.defineProperty()`。

```
Object.defineProperty(Person.prototype, "constructor", {     
    enumerable: false,     
    value: Person 
});
```
###### 2.3.4 原型的动态性
- 原型中查找值的过程是一次搜索，因此我们对原型对象所做的任何修改都能够立即从实例上 反映出来。
- 重写整个原型对象时会出现问题。
    + 调用构造函数时会为实例添加一个指向初原型的 `[[Prototype]]`指针，而把原型修改为另外一个对象就等于切断了构造函数与初原型之间的联系。
    + 调用构造函数时会为实例添加一个指向初原型的 `[[Prototype]]`指针，而把原型修改为另外一个对象就等于切断了构造函数与初原型之间的联系。 

```
function Person(){ } 
 
var friend = new Person();          
Person.prototype = {     
    constructor: Person,     
    name : "Nicholas",     
    age : 29,     
    job : "Software Engineer",     
    sayName : function () {         
        alert(this.name);     
    } 
}; 
 
friend.sayName();   //error 
```

![](/styles/images/ProfessionalJavaScriptForWeb3/02rewriteconstructor.png)

<br>

![](/styles/images/ProfessionalJavaScriptForWeb3/03rewriteconstructor.png)

###### 2.3.5 原生对象的原型 
- 原型模式的重要性不仅体现在创建自定义类型方面，就连所有原生的引用类型，都是采用这种模式 创建的。

###### 2.3.6 原生存在的问题
1. 它省略了为构造函数传递初始化参数这一环节，结果所有实例在默认情况下都将取得相同的属性值。
2. 最大的问题对于包含引用类型值的属性来说，修改原型上的值，所有的实例都会改变。

```
function Person(){ } 
 
Person.prototype = {     
    constructor: Person,     
    name : "Nicholas",     
    age : 29,     
    job : "Software Engineer",     
    friends : ["Shelby", "Court"],     
    sayName : function () {         
        alert(this.name);     
        } 
}; 
 
var person1 = new Person(); 
var person2 = new Person(); 
 
person1.friends.push("Van"); 
 
alert(person1.friends);    //"Shelby,Court,Van" 
alert(person2.friends);    //"Shelby,Court,Van" 
alert(person1.friends === person2.friends);  //true 
```

> 一般情况下不会单独使用原型模式。

##### 2.4 组合使用构造函数模式和原型模式 
> 构造函数模式用于定义实 例属性，而原型模式用于定义方法和共享的属性。

- 优势
    + 大限度地节省了内存。
    + 支持向构造函数传递参数。

```
function Person(name, age, job){     
    this.name = name;     
    this.age = age;     
    this.job = job;     
    this.friends = ["Shelby", "Court"]; 
} 
 
Person.prototype = {     
    constructor : Person,     
    sayName : function(){         
        alert(this.name);     
        } 
} 
 
var person1 = new Person("Nicholas", 29, "Software Engineer"); 
var person2 = new Person("Greg", 27, "Doctor"); 
 
person1.friends.push("Van"); 
alert(person1.friends);    //"Shelby,Count,Van" 
alert(person2.friends);    //"Shelby,Count" 
alert(person1.friends === person2.friends);    //false 
alert(person1.sayName === person2.sayName);    //true
```

##### 2.5 动态原型模式
- 通过 检查某个应该存在的方法是否有效，来决定是否需要初始化原型。

```
function Person(name, age, job){ 
    //属性     
    this.name = name;     
    this.age = age;     
    this.job = job;      
    //方法     
    if (typeof this.sayName != "function"){              
        Person.prototype.sayName = function(){             
            alert(this.name);         
        };              
     } 
} 

var friend = new Person("Nicholas", 29, "Software Engineer"); friend.sayName(); 

```

- 这段代码只会在初次调用构造函数时才会执行。

##### 2.5 寄生构造函数模式 
- 前面的模式都不适用
- 想创建一个具有额外方法的特殊数组。由于不能直接修改Array构造函数，可以使用这个模式。 

##### 2.6 稳妥构造函数模式
- 稳妥对象适合在 一些安全的环境中（这些环境中会禁止使用 this 和 new）。
- 稳妥构造函数遵循与寄生构造函数类似的模式，但有两点不同：
    + 新创建对象的 实例方法不引用 this；
    + 不使用 new 操作符调用构造函数。