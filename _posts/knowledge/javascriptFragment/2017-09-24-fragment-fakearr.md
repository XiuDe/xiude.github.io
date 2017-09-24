---
layout: post
title: "js伪数组的length属性"
date: 2017-09-24 21:15:20 +0800 
categories: javascript
tag: javascript伪数组
---
* content
{:toc}

js中伪数组的理解，以及伪数组中length的变化包括apply与call指向数组和伪数组利用`.`和`[]`length的变化，以及将伪数组利用slice拷贝时length的变化。

<!-- more -->

#### 1. 伪数组中添加数据length自增与不自增的情况

- 伪数组的条件：
    1. 对象
    2. 具有length
    3. 按索引方式存储数据
    4. 不具有数组的相应方法

##### 1.1 length不自增的情况
1. 伪数组利用对象.属性的方式，length不自增。
2. 伪数组利用对象[属性]的方式，length不自增。

> 代码示例

```
var o1 = {};
console.log(o1 instanceof Object);// true
o1.length=5;
console.log(o1);// length 5
o1.name="伪数组";
console.log(o1);// length 5
```

##### 1.2 length自增的情况
1. 伪数组利用call或者是apply修改this指向数组中的方法，length会出现自增的情况。

> 代码示例

```
// 问题：改变伪数组的this指向调用数组的push方法能否实现伪数组中的length自增。
var fakearr01 = {length:0};
console.log(fakearr01);//Object length: 0 __proto__: Object
[].push.call(fakearr01,"sample01");
console.log(fakearr01);// {0: "sample01", length: 1}-->得出结论--能实现
console.log(fakearr01.length);// 1
console.log(fakearr01 instanceof Array);//false
console.log(fakearr01 instanceof Object);//true
```

#### 2. length自减同理。

#### 3. 伪数组能变为数组
> 利用apply或者call方法改变数组方法slice的this指向，但是会出现问题。

1. 伪数组索引非数字，length的值为数字，拷贝的数组的长度以伪数组中的length为准。
2. 伪数组索引是数字，length的值非数字，拷贝的数组的长度为0。
3. 伪数组中的都是数字，拷贝的数组的长度以伪数组中的length为准。
4. 伪数组中的都非数字，拷贝的数组的长度为0。

> 代码示例

```
//情景一：
var fakearr02 = {a:'a',b:'b',length:2};
var arr01 = Array.prototype.slice.call(fakearr02);// 返回数组，并且将key变成了索引，值变成了undefined。
console.log(fakearr02 instanceof Object);//true
console.log(arr01 instanceof Array);// true
for(var i=0;i<arr01.length;i++){
    console.log(arr01[i]);// undefined undefined
}

console.log(arr01.length);//2 -->将除length之外的属性改变？
var fakearr03 = {a:'a',b:'b',length:1};
var arr02 = Array.prototype.slice.call(fakearr03);
console.log(arr02.length);//1


//情景二：
var fakearr04 = {0:'a',1:'b',length:'num'};
var arr03 = Array.prototype.slice.call(fakearr04);
console.log(arr03.length);// 0

//情景三：
var fakearr05 = {0:'a',1:'b',length:4};
var arr05 = Array.prototype.slice.call(fakearr05);
console.log(arr05.length);//4

//情景四：
var fakearr06 = {a:'a',b:'b',length:'str'};
var arr06 = Array.prototype.slice.call(fakearr06);
console.log(arr06.length);//0
```
