---
layout: original
title: "react项目总结二"
date: 2018-01-25 20:00:00 +0800 
categories: 前端框架研究
tag: react
---
* content
{:toc}

关于基本的react的相关整理，主要是梳理一下react的知识体系react 16版本之后的变更，从项目角度整合知识框架。

- [英文网官网](http://facebook.github.io/react/docs/getting-started.html)
- [中文文档](http://www.react-cn.com/docs/getting-started.html)

<!-- more -->
### 1. 组件中引入样式
#### 1. 外部样式
- 在组件标签中写`className='样式名'`，应用`import`引入的样式。
- 用 `import 'style.css'`在入口js文件中直接引入组件的外部样式表，用这种方式在子组件中`import`引入的css样式，父组件用相同的css样式名，样式会重叠(解决方案：css文件样式表的模块化)。


#### 2. 行内样式
- 组件内`style={{fontSize:16,color:'blue'}}`。
- 用js语法写样式，外层{}表示要写js代码，内层{}表示用js对象表示样式。
- style的样式规则中，属性用驼峰命名，如果属性值是px可以省略只写数值即可。
- 优化行内样式方式一：`style={titleStyle}`，根据需求提前声明`const titleStyle={fontSize:16,color:'blue'};`
- 优化行内样式方式二：针对多标签多类型直接将样式名放入一个对象中:
     
     ```
      const inlineStyle = {
        titleStyle:{fontSize:16,color:'blue'},
        bodyStyle:{fontSize:14,color:'#abcedf'}
      }
      // 行内样式中引用 
      // style={inlineStyle.titleStyle}
      // style={inlineStyle.bodyStyle}
     ```
- 优化行内样式方式三：将样式对象抽取成独立的js文件

     ```

     // CommentList.js样式文件
     export default {
       titleStyle:{fontSize:16,color:'blue'},
       bodyStyle:{fontSize:14,color:'#abcedf'}
     }

     // 需使用样式的js组件文件
     import React from 'react';
     import inlineStyle from './CommentStyle.js';


     export default function CommentItem(props){
         return <div>
             <h3 style={inlineStyle.titleStyle}>评论人：{props.user}</h3>
             <h3 style={inlineStyle.bodyStyle}>评论内容：{props.content}</h3>
         </div>
     }

     ```

### 2. React中启用css样式表文件的模块化
- 启用css样式表文件模块化，首先修改webpack开发环境依赖配置的`.css模块`的modules及参数。

```

module: {
    rules: [
      { test: /\.css$/, use: ['style-loader', 'css-loader?modules&localIdentName=[name]_[local]-[hash:5]'] }
    ]
  }

```

- 将`.css`在子组件中引入，以对象的形式使用

     ```

     import React from 'react';
     
     import CommentStyle from '../../css/CommentItem/CommentItem.css'

     console.log(CommentStyle);// CommentStyle.title='CommentItem_body-3d1c8'
                               // CommentStyle.body='CommentItem_title-5d085'
                               // 

     export default function CommentItem(props){
         return <div>
             <h3 className={CommentStyle.title}>评论人：{props.user}</h3>
             <h3 className={CommentStyle.body}>评论内容：{props.content}</h3>
         </div>
     }

     ```

#### 1. React中css样式表模块化存在的问题
- 当启用css模块化以后，'.css'样式文件中所有的类名，都是私有的，如果想要把类名设置成全局的一个类，可以把类名用`:global()`包裹起来。
- 当使用`:global()`设置了全局的类样式之后，这个类不会被重命名，只有私有的类才会重命名为自定义+几位hash值形式。

### 3. 组件的生命周期
+ 参考文章：
     - [React Native 中组件的生命周期](http://www.race604.com/react-native-component-lifecycle/)
     - [vue中的生命周期图](https://cn.vuejs.org/v2/guide/instance.html#生命周期图示)
+ 概念：在组件创建、到加载到页面上运行、以及组件被销毁的过程中，总是伴随着各种各样的事件，这些在组件特定时期，触发的事件，统称为 组件的生命周期；
+ 组件生命周期分为三部分：
      - 组件创建阶段
      - 组件运行阶段
      - 组件销毁阶段

#### 1. 组件创建阶段
> 特点：创建阶段的生命周期函数，在组件的一生中，只执行一次；

- `componentWillMount`: 组件将要被挂载，此时还没有开始渲染虚拟DOM。
- `render`：第一次开始渲染真正的虚拟DOM，当render执行完，内存中就有了完整的虚拟DOM了。
- `componentDidMount`: 组件完成了挂载，此时，组件已经显示到了页面上，当这个方法执行完，组件就进入都了 运行中的状态。

#### 2. 组件运行阶段
> 特点：根据组件的state和props的改变，有选择性的触发0次或多次；

- `componentWillReceiveProps`: 组件将要接收新属性，此时，只要这个方法被触发，就证明父组件为当前子组件传递了新的属性值；
- `shouldComponentUpdate`: 组件是否需要被更新，此时，组件尚未被更新，但是，`state` 和 `props` 肯定是最新的
- `componentWillUpdate`: 组件将要被更新，此时，尚未开始更新，内存中的虚拟DOM树还是旧的
- `rende`r: 此时，又要重新根据最新的 `state` 和 `props` 重新渲染一棵内存中的 虚拟DOM树，当 `render` 调用完毕，内存中的旧DOM树，已经被新DOM树替换了！此时页面还是旧的
- `componentDidUpdate`: 此时，页面又被重新渲染了，state 和 虚拟DOM 和 页面已经完全保持同步

#### 3. 组件销毁阶段
> 特点：组件一生只执行一次；

- `componentWillUnmount`: 组件将要被卸载，此时组件还可以正常使用；

#### 4. React中组件的生命周期

![]({{'/styles/images/react/reacttable.png'}})

#### 5. 组件生命周期的调用顺序
+ Mounting：
 - constructor()
 - componentWillMount()
 - render()
 - componentDidMount()
+ Updating：
 - componentWillReceiveProps(nextProps)
 - shouldComponentUpdate(nextProps, nextState)
 - componentWillUpdate(nextProps, nextState)
 - render()
 - componentDidUpdate(prevProps, prevState)
+ Unmounting：
 - componentWillUnmount()

### 4. 组件的属性
#### 1. `defaultProps`设置组件默认属性值
- 固定写法`static defaultProps = { pName:pValue}`
- `render()`函数中引用`this.props.pName`
- 根据需求如果外界没有传递属性值pValue是设置的默认属性值

#### 2. 用`prop-types`对`props`进行属性值类型校验
- 使用`prop-types`要安装第三方包`yarn add prop-types`并引入`import ReactTypes from 'prop-types'`。
- 固定写法`static propTypes = { pName:固定写法}`。
     + PropTypes.array
     + PropTypes.bool
     + PropTypes.func
     + PropTypes.number
     + PropTypes.object
     + PropTypes.string
     + PropTypes.symbol

- 检验类型错误控制台会警告

#### 3. `componentWillMount()`函数
- 类似于vue的`creat()`函数
- 在此函数内部会访问到 属性值、状态值、普通函数值，不能访问到页面元素，因为`render()`未执行

#### 4. `render()`函数
- 返回页面元素
- 不能访问到页面元素

#### 5. `componentDidMount()`函数
- 类似于vue中的`mounted()`函数。
- 能够访问到页面元素

#### 6. `shouldComponentUpdate()`函数
- 必须返回一个boolean值，返回true会继续执行后续生命周期函数页面更新，返回false则不执行页面不更新。
- 参数nextProps和nextState，nextState表示最新的数据对象，而this.state拿到的是页面数据的旧值不是最新值。

#### 7. `componentWillUpdate()`函数
- 页面上的值都是旧的
- 读取页面上的内容`this.refs.id名或标签名`

#### 8. `componentDidUpdate()`函数
- 页面上Dom、数据都是最新的，可以操作新的数据。

#### 9.代码示例

```
import React from 'react';
// 第三方包prop-type 属性值校验
import ReactTypes from 'prop-types';

export default class Counter extends React.Component{
    constructor(props){
        super(props);

        this.state = {
            msg:"ok",
            count: props.initCounter
        }
    }

    static defaultProps = {
        initCounter:0
    }

    // 属性校验
    static propTypes = {
        initCounter:ReactTypes.number

    }

    // 组件创建时 将要挂载函数
    componentWillMount(){
        console.log("属性值："+this.props.initCounter);
        console.log("状态值："+this.state.msg);
        console.log(this.myselfFunc());
    }

    render(){
        return <div>
            <h1>这是Counter组件</h1>
            <input type="button" value="+1" id="btn" onClick={this.increment}/>
            <hr />
            <h3 id="myh3">当前Counter的值是：{this.state.count}</h3>
        </div>
    }

    /*react方式实现自增*/
    increment=()=>{
        this.setState({
            count:this.state.count + 1  
        });
    }


    componentDidMount(){
        /*原生方法
        console.log(document.getElementById("myh3"));
        document.getElementById("btn").onclick=()=>{
            console.log(this.state);
            this.setState({
                count:this.state.count + 1
            })
        }*/

    }

    // 组件运行阶段函数
    shouldComponentUpdate(nextProps,nextState){
        console.log(nextProps.count+" "+nextState.count);// undefined count的实际值
        /*return nextState.count%2===0?true:false;*/
        return true;
    }
    componentWillUpdate(){
        /*console.log(document.getElementById("myh3").innerHTML);
        // react写法
        console.log(this.refs.myh3);*/
    }
    componentDidUpdate(){
        /*console.log(this.refs.myh3);读取不到*/
        console.log(document.getElementById("myh3").innerHTML);
    }



    // 自定义函数
    myselfFunc(){
        console.log("自定义函数");
    }
}
```

#### 10.`componentWillReceiveProps()`函数
- 当子组件第一次渲染到页面上的时候，不会触发这个函数，当父组件传递的值相较第一次改变了，才会触发

```
import React from 'react';

export default class Parent extends React.Component{
    constructor(props){
        super(props);
        this.state = {
            msg:'父组件的msg'
        }
    }

    render(){
        return <div>
            <h1>父组件</h1>
            <input type="button" onClick={this.changeMsg} value="修改msg的值"/>
            <hr />
            <Son pmsg={this.state.msg}></Son>
        </div>
    }
    // 自定义函数
    changeMsg=()=>{
        this.setState({
            msg:"已修改的msg值"
        });
    }
}

class Son extends React.Component{
    constructor(props){
        super(props);
    }

    render(){
        return <div>
            <h3>子组件：{this.props.pmsg}</h3>
        </div>
    }

    componentWillReceiveProps(nextProps){
        console.log("props值已经被修改了");
        console.log("props的最新值为:"+nextProps.pmsg);
    }
}
```

### 5.React中绑定this传参
- 事件中直接用bind绑定要修改参数的函数。
- 在构造函数区域绑定this，用bind的返回值。
- 在事件上使用箭头函数包裹要执行的修改参数的函数。

```
import React from 'react';

export default class TestThis extends React.Component{
    constructor(props){
        super(props);

        this.state={
            msg:"原始值"
        }
        this.changeMsg2 = this.changeMsg2.bind(this,"参数2-1","参数2-2");
    }

    render(){
        return <div>
            <h1>绑定this传参</h1>
            <hr />
            <input type="button" value="This传参方式一" onClick={this.changeMsg1.bind(this,"参数1-1","参数1-2")} />
            <hr />
            <input type="button" value="This传参方式二" onClick={this.changeMsg2} />
            <hr />
            <input type="button" value="This传参方式三" onClick={()=>{this.changeMsg3("参数3-1","参数3-2")}} />
            <h3>{this.state.msg}</h3>
        </div>
    }

    // 方式一
    changeMsg1(arg1,arg2){
        this.setState({
            msg:arg1 + arg2
        });
    }
    // 方式二
    changeMsg2(arg1,arg2){
        this.setState({
            msg:arg1 + arg2
        });
    }
    // 方式三
    changeMsg3(arg1,arg2){
        this.setState({
            msg:arg1 + arg2
        });
    }

}
```

### 6.React页面数据同步到state
> react中没有双向数据绑定，不同于vue中的`v-model`。

- 如果为表单元素提供value属性绑定，必须为表单元素绑定`readOnly`或者`onChange`事件。
- 提供`readOnly`，表示这个元素只读不能被修改。
- 提供`onChange`事件，表示这个值可以被修改，但是要自己定义修改的逻辑，可以实现页面数据同步state。
- 拿到表单value值，并用`onChange`修改值得三种方式：
    + 用原生`document.getElementById()`获取
    + 用ref来拿
    + 用事件的e来拿

```
import React from 'react';

export default class TestThis extends React.Component{
    constructor(props){
        super(props);

        this.state={
            msg:"原始值"
        }
    }

    render(){
        return <div>
            <h1>绑定this传参</h1>
            <input type="button" value="This传参方式三" onClick={()=>{this.changeMsg3("参数3-1","参数3-2")}} />
            <h3>{this.state.msg}</h3>
            <hr />
        {/*<input type="text" value={this.state.msg} ref="txt" readOnly/>*/}
            <input type="text" value={this.state.msg} ref="txt" id="text" onChange={this.txtChange}/>
        </div>
    }
    
    // onChange事件从页面修改state值：有三种方式
    txtChange=(e)=>{
        // 方式一：用原生document.getElementById()获取
        /*console.log(document.getElementById("text").value);
        this.setState({
            msg:document.getElementById("text").value
        });*/
        // 方式二：用ref来拿
        /*console.log(this.refs.txt.value);
        this.setState({
            msg:this.refs.txt.value
        });*/
        // 方式三：用事件的e来拿
        console.log(e.target.value)
        this.setState({
            msg:e.target.value
        });

    }

    // 方式三
    changeMsg3(arg1,arg2){
        this.setState({
            msg:arg1 + arg2
        });
    }

}
```

### 7.react中父组件向子组件传递方法
- react中，只要是传递给子组件数据，不管是普通的类型，还是方法，都可以使用this.props来调用。
- Vue，父组件传递给子组件的普通属性和方法属性，区别对待，普通属性用props接收，方法使用`this.$emit("方法名")`

### 8.父组件使用context特性为子组件传递数据
- 传统方式，由父组件一级一级往子孙组件传递。context方式，在父组件中共享一个context对象，不用逐层传递数据。

1. 父组件使用`getChildContext()`方法返回一个对象
2. 父组件启用属性检验(引入`prop-types`包)，传递的每个属性数据都需要校验，用静态属性`static childContextTypes={}`
3. 子组件使用数据，首先进行属性校验(如果子组件在单独的文件中也需要引入`prop-types`包)，要用静态的属性`static contextTypes={}`
4. 使用数据用`this.context.数据名`
5. 以上`getChildContext()`、`static childContextTypes={}`和`static contextTypes={}`都是固定写法。

```
import React from 'react';

import ReactTypes from 'prop-types';

export default class Com1 extends React.Component{
    constructor(props){
        super(props)

        this.state={
            color:'red'
        }
    }


    render(){
        return <div>
            <h1>这是父组件</h1>
            <Com2></Com2>
        </div>
    }
    
    // 需要给子孙组件传递的数据
    getChildContext(){
        return {
            color:this.state.color
        }
    }
    // 启用属性校验
    static childContextTypes = {
        color:ReactTypes.string
    }


}



class Com2 extends React.Component{
    constructor(props){
        super(props)
    }


    render(){
        return <div>
            <h3>这是子组件</h3>
            <Com3></Com3>
        </div>
    }
}


class Com3 extends React.Component{
    constructor(props){
        super(props)
    }



    // 首先启用属性校验
    static contextTypes = {
        color:ReactTypes.string
    }

    render(){
        return <div>
            <h5 style={{color:this.context.color}}>这是孙子组件</h5>
        </div>
    }
}
```

### 9. react中的路由
#### 1. `react-router-dom`的基本使用
- [安装第三方包](https://reacttraining.com/react-router/web/guides/quick-start)
- 按需导入`import {HashRouter,Route,Link} from 'react-router-dom'`
     + `HashRouter`表示路由根容器，所有跟路由相关的东西都由`HashRouter`包裹，一个网站中只需要使用一次`HashRouter`。
     + `Route`表示路由规则，两个比较重要的属性`path`和`component`；也表示站位符，将匹配到的组件放到这个位置。
     + `Link`表示一个路由链接
     
- 当使用`<HashRouter></HashRouter>`把根组件包裹，这样就启用路由了，`<HashRouter></HashRouter>`内部只能使用一个根节点。

#### 2. 代码示例
```
// 入口js文件
import React from 'react'
import ReactDOM from 'react-dom'

// 启用路由组件
import App from './components/router/Router.jsx';


ReactDOM.render(<App> 
  
</App>, document.getElementById('app'));


// 路由文件
import React from 'react';

import {HashRouter,Route,Link} from 'react-router-dom';

import Home from './app/Home.jsx';
import Movies from './app/Movies.jsx';
import About from './app/About.jsx';

export default class router extends React.Component{
    constructor(props){
        super(props);

    }

    render(){
        return <HashRouter>
            <div>
                <h1>这是一个路由组件</h1>
                <hr />
                <Link to='/home'>首页</Link>&nbsp;&nbsp;
                <Link to='/movie'>电影</Link>&nbsp;&nbsp;
                <Link to='/about'>关于</Link>
                <hr />
                <Route path='/home' component={Home}></Route>
                <Route path='/movie' component={Movies}></Route>
                <Route path='/about' component={About}></Route>
            </div>
        </HashRouter>
    }
} 

```


### 10. 参考文章
[类型校验](https://facebook.github.io/react/docs/typechecking-with-proptypes.html)
[Animation Add-Ons](https://reactjs.org/docs/animation.html#high-level-api-reactcsstransitiongroup)
