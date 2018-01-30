---
layout: original
title: "react项目总结三"
date: 2018-01-25 20:00:00 +0800 
categories: 前端框架研究
tag: react
---
* content
{:toc}

关于基本的react的相关整理，主要是梳理一下react的知识体系react 16版本之后的变更，从项目角度整合知识框架。
- [英文网官网](http://facebook.github.io/react/docs/getting-started.html)
- [中文文档](http://www.react-cn.com/docs/getting-started.html)
- 
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