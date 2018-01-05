---
layout: original
title: "react相关整理一"
date: 2018-01-05 22:23:49 +0800 
categories: 前端框架研究
tag: react
---
* content
{:toc}

关于基本的react的相关整理，主要是基本概念的概括。

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

- 虚拟DOM的本质：使用js对象模拟DOM树
- 虚拟DOM的目的：实现DOM节点的高效更新
- 虚拟DOM的优势
    + 性能优势：web应用不同于网站，web应用会在单页面中有大量的DOM操作，大量的DOM操作很慢。React中，应用程序在虚拟DOM上操作，React可以优化。即：React在每次需要渲染时，会先比较当前DOM内容和待渲染内容的差异，然后再决定如何最优的更新DOM(reconciliation过程)。
    + 提供了一种一致的开发方式来开发服务端应用、Web应用和手机端应用：有了虚拟DOM层，通过配备不同的渲染器，就可以将虚拟DOM的内容渲染到不同的平台。

- Virtual DOM速度快的原因
    + React为此引入了虚拟DOM（Virtual DOM）的机制：在浏览器端用Javascript实现了一套DOM API。基于React进行开发时所有的DOM构造都是通过虚拟DOM进行，每当数据变化时，React都会重新构建整个DOM树，然后React将当前 整个DOM树和上一次的DOM树进行对比，得到DOM结构的区别，然后仅仅将需要变化的部分进行实际的浏览器DOM更新。
    + [原文链接](http://blog.csdn.net/yczz/article/details/49585313)

##### 1. 虚拟DOM树中的diff算法
 - tree diff:新旧DOM树，逐层对比的方式，就叫做 tree diff,每当我们从前到后，把所有层的节点对比完后，必然能够找到那些 需要被更新的元素；
 - component diff：在对比每一层的时候，组件之间的对比，叫做 component diff;当对比组件的时候，如果两个组件的类型相同，则暂时认为这个组件不需要被更新，如果组件的类型不同，则立即将旧组件移除，新建一个组件，替换到被移除的位置；
 - element diff:在组件中，每个元素之间也要进行对比，那么，元素级别的对比，叫做 element diff；
 - key：key这个属性，可以把 页面上的 DOM节点 和 虚拟DOM中的对象，做一层关联关系；

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
    + 使用数组组装 `<div>{['cc ', <span>&copy;</span>, ' 2015']}</div>`
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

### 5. react的API
#### 1. 顶层API
- [顶层API](http://facebook.github.io/react/docs/top-level-api.html)

#### 2. 组件API
- [组件API](http://facebook.github.io/react/docs/component-api.html)

### 6. 组件的生命周期
- [gitbooks生命周期](https://hulufei.gitbooks.io/react-tutorial/content/index.html)

#### 1. 组件生命周期的三个状态
- Mounting：已插入真实DOM
- Updating：正在被重新渲染
- Unmounting：已移出真实DOM

#### 2. 处理函数
> React 为每个状态都提供了两种处理函数，will函数在进入状态之前调用，did 函数在进入状态之后调用，三种状态共计五种处理函数。

- componentWillMount()：组件将要被渲染到真实Dom节点
- componentDidMount()：组件已经被渲染到真实Dom节点
- componentWillUpdate(object nextProps, object nextState)：组件将要被重新渲染
- componentDidUpdate(object prevProps, object prevState)：组件已经被重新渲染
- componentWillUnmount()：组件将要被卸载方法

> 此外，React 还提供两种特殊状态的处理函数

- componentWillReceiveProps(object nextProps)：已加载组件收到新的参数时调用
- shouldComponentUpdate(object nextProps, object nextState)：组件判断是否重新渲染时调用

#### 3. 函数的调用顺序

![]({{'/styles/images/react/react01.png'}})

- [react生命周期参考博客](http://pinggod.com/2015/React-%E7%BB%84%E4%BB%B6%E7%9A%84%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F/)

### 7. react 的 ES5 与 ES6 写法对照
- [React](http://www.tuicool.com/articles/equ2my)
- [ReactNative](http://bbs.reactnative.cn/topic/15/react-react-native-%E7%9A%84es5-es6%E5%86%99%E6%B3%95%E5%AF%B9%E7%85%A7%E8%A1%A8/2)

### 8. react 事件处理
#### 1. 事件使用
- onClick这种进行驼峰命名ES5和ES6的写法不一样，在ES6中要用bind方法绑定this(具体可参照ES5和ES6写法对照表)

#### 2. 参数传递
- ES6写法：给事件处理函数传递额外参数的方式：`bind(this, arg1, arg2, ...)`
- `<p onClick={this.handleClick.bind(this, param1,param2,param3)}>`

#### 3. React支持的事件列表
- [英文文档](https://reactjs.org/docs/events.html)
- [中文文档](https://doc.react-china.org/docs/events.html)

### 9. react 的 Dom 操作
#### 1. 组件的Dom事件监听
- 监听事件(包括ajax请求等)要放到`componentDidMount`方法及以后，目的是等dom渲染结束后继续做操作
- 组件卸载时一定要移除监听事件

#### 2. 参数传递的判断
> 属性校验

- [PropTypes的改变](https://reactjs.org/docs/typechecking-with-proptypes.html#proptypes)
- [PropTypes的改变](https://doc.react-china.org/docs/typechecking-with-proptypes.html)


### 10. react 的受控表单组件
- [react表单](https://doc.react-china.org/docs/forms.html)
- [react Forms](https://reactjs.org/docs/forms.html)

#### 1. 受控表单组件
> react里只要表单的`状态属性`与state进行绑定了，那么这些表单组件，就是受控表单组件。

- 受控表单组件与react单项数据流的思想有关：页面中所有的UI和行为都应该为`state`所控制。而表单中控制UI和行为的属性就是状态属性。
- 受控表单属性的特点：如果想改变它的值，一定得通过onChange事件改变

#### 2. 状态属性
> 表单元素中属于状态的属性。

- `value`，对应`<input>`和`<textarea`的所有
- `checked`，对应`checkbox`和`radio`的`<input>`所有
- `selected`，对应`<option>`所有

### 11. react 的组合组件
- [react composition](https://reactjs.org/docs/composition-vs-inheritance.html)
- [react 组合组件](https://doc.react-china.org/docs/composition-vs-inheritance.html)
- key(+ diff算法)提高渲染效率问题：只会渲染变化的dom元素，未发生改变不会渲染。

### 12. Context
- [react context](https://reactjs.org/docs/context.html)
- [react 不建议使用context](https://doc.react-china.org/docs/context.html)

### 13. 动画
- [动画](https://doc.react-china.org/docs/animation.html)
- [React 源码剖析系列 － 玩转 React Transition](https://zhuanlan.zhihu.com/p/20419592)
- [csdn react文章](http://blog.csdn.net/lihongxun945/article/details/46778723)

### 14. react常用插件
- [https://js.coach/react/react-infinite](https://js.coach/react/react-infinite)
- [https://react.parts/native](https://react.parts/native)

### 15. react的diff算法
- [协调（Reconciliation）](https://doc.react-china.org/docs/reconciliation.html)
- [react的精髓Virtual Dom](http://blog.csdn.net/lihongxun945/article/details/46640503)
- [diff算法一](http://blog.csdn.net/yczz/article/details/49585283)
- [React源码解析diff算法](http://blog.csdn.net/yczz/article/details/49886061)