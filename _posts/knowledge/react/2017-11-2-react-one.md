---
layout: original
title: "react相关整理一"
date: 2017-11-14 24:23:49 +0800 
categories: 前端框架研究
tag: react
---
* content
{:toc}

关于基本的react的相关整理，版本更新太快英文文档比较好。

- [英文网官网](http://facebook.github.io/react/docs/getting-started.html)
- [中文文档](http://www.react-cn.com/docs/getting-started.html)

<!-- more -->

### 1. 开始react之前
#### 1. react的由来
- React是Facebook开源的一个用于构建用户界面的Javascript库。
- React专注于MVC架构中的V，即视图。
- 组件化开发。

### 2. react
> react四个核心内容虚拟DOM、React组件、Jsx语法和Data Flow。

#### 1. Virtual DOM
- 虚拟DOM的优势
    + 性能优势：web应用不同于网站，web应用会在单页面中有大量的DOM操作，大量的DOM操作很慢。React中，应用程序在虚拟DOM上操作，React可以优化。即：React在每次需要渲染时，会先比较当前DOM内容和待渲染内容的差异，然后再决定如何最优的更新DOM(reconciliation过程)。
    + 提供了一种一致的开发方式来开发服务端应用、Web应用和手机端应用：有了虚拟DOM层，通过配备不同的渲染器，就可以将虚拟DOM的内容渲染到不同的平台。

- Virtual DOM速度快的原因
    + React为此引入了虚拟DOM（Virtual DOM）的机制：在浏览器端用Javascript实现了一套DOM API。基于React进行开发时所有的DOM构造都是通过虚拟DOM进行，每当数据变化时，React都会重新构建整个DOM树，然后React将当前 整个DOM树和上一次的DOM树进行对比，得到DOM结构的区别，然后仅仅将需要变化的部分进行实际的浏览器DOM更新。
    + [原文链接](http://blog.csdn.net/yczz/article/details/49585313)

#### 2. React组件
- 组件：即封装起来的具有独立功能的UI部件。MVC的思想做到视图-数据-控制器的分离，那么组件化的思考方式则是带来了UI功能模块之间的分离。
- 即：整个UI是一个通过小组件构成的大组件，每个组件只关心自己部分的逻辑，彼此独立。
- 组件的特性：自定义标签、高内聚的资源、独立的作用域和规范化接口。
- 组件化开发的特性：可组合、可重用、可维护和可测试。
- `react-dom`将各个组件整合到一块。

#### 3. Jsx语法
- Jsx语法：实质上是一个语法糖，HTML语言直接写在JavaScript语言之中，不加任何引号，这就是 JSX 的语法，它允许HTML与JavaScript的混写。
- Jsx语法作用：使组件之间的关系看上去更加清晰。
- JSX 的基本语法规则：遇到 HTML 标签（以 < 开头），就用HTML规则解析；遇到代码块（以 { 开头），就用 JavaScript 规则解析。

#### 4. Data Flow(单项数据流)
- 只能从后往前走，从生命周期，函数的state对象里赋值触发视图的渲染。
- react中的单项数据流框架：
    + [Flux](https://hulufei.gitbooks.io/react-tutorial/content/flux.htm)
    + [Redux](http://camsong.github.io/redux-in-chinese/index.html)

### 3. react的调试工具
> chrome插件React-devtools

### 4. react的jsx
#### 1. 视图概念
- Props：属性，从外部传入内部的一些数据都会挂载到Props上。
- State：State可以挂载对象、数据之类的。Props和State可以决定组件的ui和行为。
- Event：事件。

#### 2. jsx之Html转义  
- React 会将所有要显示到 DOM 的字符串转义，防止 XSS。所以如果 JSX 中含有转义后的实体字符比如 `&copy; (©)`最后显示到 DOM 中不会正确显示，因为 React 自动把 `&copy;` 中的特殊字符转义了。
    + 直接使用 UTF-8 字符 ©
    + 使用对应字符的 Unicode 编码
    + 使用数组组装 <div>{['cc ', <span>&copy;</span>, ' 2015']}</div>
    + 直接插入原始的 HTML
    
    ```
    <div dangerouslySetInnerHTML={{__html: 'cc &copy; 2015'}} />
    ```

- [dangerouslySetInnerHTML参考文档](http://reactjs.cn/react/tips/dangerously-set-inner-html.html)

#### 3. jsx之自定义 HTML 属性 
- 如果在 JSX 中使用的属性不存在于HTML的规范中，这个属性会被忽略。如果要使用自定义属性，可以用 `data-` 前缀。属性的前缀 `aria-` 也是支持的。
- [与dom的区别文档](http://reactjs.cn/react/docs/dom-differences.html)

#### 4. jsx之支持的标签和属性
- 要使用的某些标签或属性不在这些支持列表里面就可能被 React 忽略，必须要使用的话可以用 `dangerouslySetInnerHTML`。
- [支持列表](http://reactjs.cn/react/docs/tags-and-attributes.html)
- 所有的属性都是驼峰命名的，`class` 属性和 `for` 属性分别改为 `className` 和 `htmlFor`，来符合 `DOM API` 规范。

#### 4. jsx之属性扩散
- 给组件设置属性直接设置，如果多个属性利用属性扩散。
- 单个属性：`<Hello name="xd">`。
- 多个属性

    ```
    const obj = {
        name:"xd",
        age:"21"
    }

    <Hello {...obj} />
    ```

- 属性可以被覆盖，写在后面的属性值会覆盖前面的属性

    ```
    var props = { foo: 'default' };
    var component = <Component {...props} foo={'override'} />;
    console.log(component.props.foo); // 'override'

    ```

#### 5. jsx之自闭合标签
- 一般如果只有一个组件，就用单闭合标签形式，如果有多个组件嵌套就用双闭合标签形式。
- [自闭和标签文档](http://reactjs.cn/react/tips/self-closing-tag.html)

#### 6. jsx之注释
- 在一个组件的子元素位置使用注释要用 {} 包起来

```
var content = (
  <Nav>
      {/* child comment, put {} around */}
      <Person
        /* multi
           line
           comment */
        name={window.isLoggedIn ? window.name : ''} // end of line comment
      />
  </Nav>
);

```