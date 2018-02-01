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


#### 代码示例

```
import React from 'react';
// 第三方包prop-type 属性值校验
import ReactTypes from 'prop-types';

export default class Counter extends React.Component{
    constructor(props){
        super(props);

        this.state = {
            msg:"ok"
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
            <input type="button" value="+1" />
            <hr />
            <p>当前Counter的值是：{this.props.initCounter}</p>
        </div>
    }

    // 自定义函数
    myselfFunc(){
        console.log("自定义函数");
    }
}
```
