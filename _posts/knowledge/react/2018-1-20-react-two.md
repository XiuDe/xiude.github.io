---
layout: original
title: "react学习笔记"
date: 2018-01-20 22:23:49 +0800 
categories: 前端框架研究
tag: react
---
* content
{:toc}

关于基本的react的相关整理，主要是梳理一下react的知识体系react 16版本之后的变更，从项目角度整合知识框架。

- [英文网官网](http://facebook.github.io/react/docs/getting-started.html)
- [中文文档](http://www.react-cn.com/docs/getting-started.html)

<!-- more -->

### 1. React项目搭建之前
1. 搭建项目目录，配置好webpack文件。

### 2. 开始编写React项目
1. react项目js中最先安装`react`和`react-dom`两个包，html写父容器标签
    - `cnpm i react react-dom -S`。
    - `react`专门创建React组件、组件生命周期等。
    - `react-dom`里面主要封装和DOM操作相关的包。

2. 使用JS创建虚拟DOM节点`React.createElement()`：
    - 在 react 中，如要要创建 DOM 元素，只能使用 React 提供的 JS API来创建`React.createElement()`
    - `React.createElement()` 方法有三个及以上参数
    - 参数1：是个字符串类型的参数，表示要创建的元素类型，如`h1 div 等`
    - 参数2：是一个属性对象，表示创建的这个元素上有哪些属性，没有则写`null`
    - 参数3：从第三个参数的位置开始，后面可以放好多的虚拟DOM对象，这写参数，表示当前元素的子节点

    ```

     var myH1 = React.createElement('h1', null, '这是h1标签')

     var myDiv = React.createElement('div', { title: 'this is a div', id: 'mydiv' }, '这是一个div', myH1)
    ```

3. 使用`ReactDOM`把元素渲染到页面指定的容器中`ReactDOM.render()`
    - `ReactDOM.render('要渲染的虚拟DOM元素', '要渲染到页面上的哪个位置中')`
    - 参数1：像myH1这样的虚拟DOM元素
    - 参数2：和vue不一样，不接受 `"#app"` 这样的字符串，而是需要传递一个 原生的 DOM 对象
    
    ```
    ReactDOM.render(myDiv, document.getElementById('app'))
    ```

### 3. JSX语法简化创建虚拟DOM节点
1. 使用 JSX 语法，必须先运行 `cnpm i babel-preset-react -D`，然后再 `.babelrc` 中添加语法配置
2. JSX语法的本质：还是以 React.createElement 的形式来实现的，并没有直接把 用户写的 HTML代码，渲染到页面上
3. 如果要在 JSX 语法内部，书写 JS 代码了，那么，所有的JS代码，必须写到 {} 内部
4. 当 编译引擎，在编译JSX代码的时候，如果遇到了`<`那么就把它当作 HTML代码去编译，如果遇到了 `{}` 就把 花括号内部的代码当作 普通JS代码去编译
5. 在{}内部，可以写任何符合JS规范的代码
6. 在JSX中，如果要为元素添加`class`属性了，那么，必须写成`className`，因为 `class`在ES6中是一个关键字；和`class`类似，label标签的 `for` 属性需要替换为 `htmlFor`
7. 在JSX创建DOM的时候，所有的节点，必须有唯一的根元素进行包裹
8. 如果要写注释了，注释必须放到 {} 内部

```
 var mytitle = '这是使用变量定义的 tilte 值'

 var arr = []
 for (var i = 0; i < 10; i++) {
   var p = <p className="myp" key={i}>JSX语法糖</p>
   arr.push(p)
 }

 var mydiv = <div>
   <h1 title={mytitle + 'aaaaa'}>JSX语法</h1>
   <p className="myp">JSX语法</p>
   <label htmlFor=""></label>

   {arr}

   { 
     // 注释
   }

   { /* 这是注释 */ }
   {/* 1234 */}
 </div>
```

### 4. React中创建组件的第一种方式
#### 1. 通过函数创建组件
1. 在React中，构造函数是一个最基本的组件，如果想要把组件放到页面中，可以把 构造函数的名称，当作组件的名称，以HTML标签形式引入页面中

```
注意：React在解析所有的标签的时候，是以标签的首字母来区分的，如果标签的首字母是小写，那么就按照普通的HTML标签来解析，如果首字母是大写，则按照组件的形式去解析渲染

结论：组件的首字母必须是大写

 function Hello() {
   return <div>
     <h1>这是在Hello组件中定义的元素</h1>
   </div>
 }

 ReactDOM.render(<div>
  <Hello></Hello>
</div>, document.getElementById('app'))
```
#### 2. 父组件向子组件传递数据
1. (值少的情况)向子组件内传参

```
var name = "xd";
var age = 1;

function Hello(){
  return <div>
    <h1>这是在Hello组件中定义的元素，姓名为{name}，年龄是{age}</h1>
  <div>
}

ReactDom.render(<div>
       <Hello name={name} age={age}></Hello>
    </div>,document.getElementById("app"));
```

2. (值多的情况)如果想要使用外部传递过来的数据，必须显示的在构造函数参数列表中，定义 props 属性(自定义的其他名不影响)来接收

```
var person = {
    name:"xd",
    age:2,
    gender:"男",
    address:"earth"
}

function Hello(props){
    return <div>
       <h1>向Hello组件中传多个值，姓名为{props.name}，地址为{props.address}<h1>
    </div>
}

ReactDom.render(<div>
      <Hello {...person}></Hello>
    </div>,document.getElementById("app"));
```

3. 通过 props 得到的任何数据都是只读的，函数组件内不能重新赋值


