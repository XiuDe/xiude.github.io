---
layout: original
title: "webpack与vue01"
date: 2017-09-26 11:42:49 +0800 
categories: 前端框架研究
tag: [webpack, webpack vue, ES6语法]
---
* content
{:toc}

[webpack中文文档](https://doc.webpack-china.org/)<br>
[webpack github page](http://webpack.github.io/docs/)<br>

webpack解析.vue组件页面一定要注意相互之间的版本，避免不必要的错误。

<!-- more -->
#### 1. webpack实现vue组件页面.vue文件的解析
##### 1.1关于webpack和vue项目
- Vue项目中的每个页面其实都是一个.vue的文件，这种文件，Vue称之为组件页面，必须借助于 webpack的vue-loader才能使用。
- 必须安装相关包：
    + vue :  vuejs核心包
    + vue-loader： .vue文件编译loader
    + vue-template-compiler ： .vue模板编译,被vue-loader所依赖
    + babel-plugin-transform-runtime : es6实时转换成es5语法

- 在项目根目录下打开cmd命令面板，输入：
    + `npm install vue-loader vue-template-compiler babel-plugin-transform-runtime --save-dev`
    + `npm install vue --save`

- `--save-dev`和`--save`
    + `--save-dev`保存到的是`package.json`文件中的`"devDependencies"`下
    + `--save`保存到的是`package.json`文件中的`"dependencies"`下
    + `--save`与`--save-dev`保存的文件地址不一样
    + `--save`表示在发布以后的运行阶段会依赖
    + `--save-dev`表示在开发阶段要使用，在运行阶段不用了

- 在webpack.config.js中添加如下配置（只能在webpack1.0中使用）：

```
 babel:{
         presets: ['es2015'],  
         plugins: ['transform-runtime']  //这句代码就是为了解决打包.vue文件不报错
    }
```
- 在webpack2.0中在webpack.config.js中添加 babel:{}是不认识的，要改成如下方式：

```
 在项目根目录下新建 .babelrc文件，内容填写如下：
     {
         presets: ['es2015'],  
         plugins: ['transform-runtime']  //这句代码就是为了解决打包.vue文件不报错
    }
```
- 在webpack.config.js中的loaders中增加

```
{
    // 打包.vue文件
    test:/\.vue$/,   //表示当前要打包的文件的后缀正则表达式
    loader:'vue-loader' //
}
```
##### 1.2 .vue组件页面的写法结构

```
分为三个元素：

    1、<template><div class="tmpl"></div>由于是vue2.0 所以这个里面一定要放一个根元素，可以放vue的指令 v-</template>

    2、<script> export default{data:{}} -> new Vue({data:{}}) 就是导出一个 Vue的实例  </script>

    3、<style></style>  中的样式是全局的
        <style scoped></style> 中的样式是当前组件的
```

> 代码示例

```
<!-- 以后项目的根组件 -->
<template>
    <div>
        <!-- 1.0 template主要是放html元素的（html的页面结构） -->
        <span v-text="msg" class="red"></span>
    </div>
</template>

<script>
// 按需导入
    // import {add} from './calc.js';

    // 负责导出 .vue这个组件对象(它本质上是一个Vue对象,所以Vue中该定义的元素都可以使用)
    export default{  // es6的导出对象的写法
        data(){  //等价于 es5的 data:function(){
            return {
                msg :'hello vuejs'
            }
        },
        methods:{
            
        },
        created(){

        }
    }
</script>

<style scoped>
/*当前页面的css样式写到这里，其中scoped表示这个里面写的css代码只是在当前组件页面上有效，不会去影响到其他组件页面*/
    .red{
        color: red;
    }
</style>
```

##### 1.3 将.vue中的内容解析编译并且展示在浏览器中

```
1、npm install vue  --save
2、在main.js中编写解析.vue的代码
        // 1.0 导入vue这个包，import后面的Vue与后面new后面的Vue一样
        import Vue from 'vue';

        // 2.0 导入 App.vue文件
        import App from './App.vue';

        // 3.0 将App中的内容编译解析出来替换index.html中的<div id="app"></div>
        new Vue({
            el:'#app',
            // render:function(create){create(App);}  es5语法
            render:create=>create(App) //es6语法
        });
```

> 代码示例

```
// 1.0 导入vue核心包
import Vue from 'vue';

// 2.0 导入App.vue的vue对象
import App from './App.vue';

// 3.0 利用Vue对象进行解析渲染
new Vue({
    el:'#app',
    // render:function(create){create(App)} //es5的写法
    render:c=>c(App)  // es6的函数写法 =>：goes to
});
```

##### 1.4 报错情况-->`Uncaught SyntaxError: Unexpected token import`

```
在使用"vue": "^2.4.4"版本和"vue-loader": "^13.0.5"以及webpack1.14.0，
不能创建.babelrc文件后，否则会出现Uncaught SyntaxError: Unexpected token import
错误，错误原因可能是ES6转ES5出现错误，这个错误暂时没有好的方式解决。
为避免这种错误package.json文件下版本号应用如下：
  "devDependencies": {
    "babel-core": "^6.24.0",
    "babel-loader": "^6.4.1",
    "babel-plugin-transform-runtime": "^6.23.0",
    "babel-preset-es2015": "^6.24.0",
    "css-loader": "^0.28.0",
    "file-loader": "^0.11.1",
    "html-webpack-plugin": "^2.28.0",
    "less": "^2.7.2",
    "less-loader": "^4.0.3",
    "node-sass": "^4.5.2",
    "sass-loader": "^6.0.3",
    "style-loader": "^0.16.1",
    "url-loader": "^0.5.8",
    "vue-loader": "^11.3.4",
    "vue-template-compiler": "^2.2.6",
    "webpack": "^1.14.0",
    "webpack-dev-server": "^1.16.0"
  },
  "dependencies": {
    "vue": "^2.2.6"
  }

重新删除node_modules，重新下载。
```

#### 2. vue中的ES6语法
```
   1、对象的写法
        es5中对象： {add:add,substrict:substrict}
        es6中对象： {add,substrict}  注意这种写法的属性名称和值变量是同一个名称才可以简写，否则要想es5那样的写法,例如： {addFun:add}

    2、在对象中的方法的写法
        es5中对象： {add:function(){},substrict:function(){}}
        es6中对象： {add(){},substrict(){}}
    
    3、对象的导出写法
         es5两种形式：
         1、module.exports = fucntion (){};
         2、exprots.add =  fucntion (){};

        es6中写法：
        1、export default{
            add(){}
        }
        2、export fucntion add(){} 相当于 将add方法当做一个属性挂在到exports对象


    4、对象的导入
        es5: var add  = require('./calc.js');
        es6:
        如果导出的是：export default{ add(){}}
        那么可以通过  import obj from './calc.js'

        如果导出的是：
        export fucntion add(){} 
        export fucntion substrict(){} 
        export const PI=3.14

        那么可以通过按需加载 import {add,substrict,PI} from './calc.js'

    5、es6中的箭头函数的写法
        箭头的演变过程：
        //需求：利用函数实现倒序排列
        [2,1,3].sort(function(x,y){return y - x;});
    
        //用箭头函数实现 =>读 goes to
        [2,1,3].sort((x,y)=>{return y - x;});
        [2,1,3].sort((x,y)=> {x++;y++; y - x;});
        [2,1,3].forEach(x=> {console.log(x)});

        //如果没有参数
        ()=>函数体
        当函数体有多条语句需要加{}
```

#### 3. webpack项目结构中集成vue-router步骤（Vue2.0写法）
- 1、安装vue-router: npm install vue-router --save
    + 这里我用的是 vue-router 的2.3.1 版本；
    + 指定版本的语法`cnpm i vue-router@2.3.1`

- 2、在webpack打包入口js文件中(entry指定的那个文件main.js)配置如下

> 代码示例

```
// 1.0 导入vue核心包
import Vue from 'vue';

// 2.0 导入App.vue的vue对象
import App from './App.vue';

// 3.0 将vue-router集成到这个项目中来
import vueRouter from 'vue-router';
// 3.1 将vue-router对象绑定到Vue对象上
Vue.use(vueRouter);
// 3.2 导入路由规则对应的组件对象，我们项目中需要跳转的组件
import login from './components/account/login.vue';
import register from './components/account/register.vue';
// 3.3 定义路由规则
var router1 = new vueRouter({
    routes:[
       {path:'/login',component:login},
       {path:'/register',component:register}
    ]
    });
// 3.4 在new Vue中使用路由对象实例


// 3.0 利用Vue对象进行解析渲染
new Vue({
    el:'#app',
    // router,ES6
    router:router1,//ES5
    // render:function(create){create(App)} //es5的写法
    render:c=>c(App)  // es6的函数写法 =>：goes to
});
```
> 图片实例

![](/styles/images/vuewebpack/d5-1.png)

- 3、在入口App.vue组件中添加如下代码

> 代码示例

```
<template>
    <div>
        <!-- 1.0 template主要是放html元素的（html的页面结构） -->
        <span v-text="msg" class="red"></span>

        <!-- 单页面程序路由跳转 router-link to="/login"，不用使用a标签 -->
        <router-link to="/login">登录</router-link>
        <router-link to="/register">注册</router-link>

        <!-- 路由占位 -->
        <router-view></router-view>

    </div>
</template>
```

> 图片实例

![](/styles/images/vuewebpack/d5-2.png)

- 4、执行 npm run dev

#### 4. Vue移动组件mint-ui使用
- Vue拥有很多的第三方开发的PC端或者移动端UI组件，
    + 移动组件[mint-ui中文文档](http://mint-ui.github.io/#!/zh-cn)

- 类似的移动端组件有
    + 1.vux
    + [vux关于升级到vue2.0](https://vux.li/?x-page=github_readme#/zh-CN/upgrade-to-2)
    + [vux 组件使用文档](https://vux.li/?x-page=github_readme#/zh-CN/components)
    + [vux 组件效果演示](https://vux.li/demos/v2/?x-page=v2-doc-home#/)
    + [vux github](https://github.com/airyland/vux)
    + 2.淘宝团队开发的：[SUI](http://m.sui.taobao.org/components/#toolbar)
    + 3.[muse-ui](https://museui.github.io/#/gridList)

- PC端组件
    + 饿了么团队开发的：[element](http://element.eleme.io/#/zh-CN/component/installation)
    + iView 是一套基于 Vue.js 的开源 UI 组件库，主要服务于 PC 界面的中后台产品, [iView2](https://www.talkingcoder.com/article/6395692494071220203)，全面支持 Vue.js 2.x。

##### 4.1 mint-ui资源介绍
- [官网](http://mint-ui.github.io/#!/zh-cn)
- [文档](http://mint-ui.github.io/docs/#/zh-cn2)

##### 4.2 安装mint-ui集成到项目中
- 1、利用：npm install mint-ui --save 命令将mint-ui安装到项目中
- 2、在main.js中全局导入mint-ui和它的css后即可在任何组件的`<template></template>`中使用mint-ui组件

![d5-3.png](imgs/d5-3.png "")

- 3、举例使用mint-ui的按钮组件：[可以参考文档](http://mint-ui.github.io/docs/#!/zh-cn2/button)

![d5-4.png](imgs/d5-4.png "")

- 4、mint-ui中css组件可以在入口文件中导入一次，整个项目都能使用，js组件使用时在当前的.vue页面的`<script></script>`中再次导入`import { Toast } from 'mint-ui';`
