---
layout: original
title: "webpack总结"
date: 2017-09-25 11:42:49 +0800 
categories: 前端框架研究
tag: webpack
---
* content
{:toc}

[webpack中文文档](https://doc.webpack-china.org/)<br>
[webpack github page](http://webpack.github.io/docs/)<br>


总结webpack的安装使用以及webpack打包各种常见文件相应的命令。



<!-- more -->
#### 1. webpack安装
- webpack是一个资源的打包工具，分为1.0和2.0版本，可以将 .js,  .css , image等静态资源当做一个模块来进行打包，那么每一种模块都是有一个对应的 loader来实现
- [webpack 1.0版本官网](https://webpack.github.io/docs/usage.html)
- [webpack 2.0版本官网](https://webpack.js.org/)
- 版本webpack 1.14.0

##### 1.1 webpack和nodejs的关系
- webpack是node的第三方资源包，使用webpack首先安装node环境。
- [nodejs官网](https://nodejs.org/en/)
    + LTS版本是长期稳定版本。
    + current是当前最新版本。
    + 安装完成后检测版本`node -v`和`npm -v`出现对应版本则安装完成。
- 修改npm下载资源包的来源
    + 使用`npm install` 安装第三方包是去国外网站上下载，有可能会被墙而安装失败，所以我们要将下载源切换到国内淘宝上因此需要利用 `npm install nrm -g`安装一个全局的`nrm`。
    + 安装好nrm以后，在cmd命令面板中输入： `nrm use taobao` 将下载源切换到淘宝，可以使用 `nrm ls` 查看当前使用的下载源，使用`nrm ls`显示`*`的位置为我们用的下载源的地址。
    + 也可安装淘宝提供的类似于npm的工具cnpm来替代npm安装node包,安装包命令和npm一样，安装cnpm命令： `npm install cnpm -g`。

##### 1.2 安装webpack步骤
  
- 第一种安装方式： 
    + 在cmd命令行面板中 执行： `npm install webpack@1.14.0 -g` 将webpack1.14.0版本安装为全局就能够在cmd命令行面板中使用webpack指令。

- 第二种安装方式： 
    + 在cmd命令行面板中 执行： `cnpm install webpack@1.14.0 -g` 将webpack1.14.0版本安装为全局就能够在cmd命令行面板中使用webpack指令。 

#### 2. webpack打包
```
需求：index.html实现简单的计算器
      calc.js实现实现add加的功能
      main.js实现获取Dom元素，并且调用calc.js中的add方法
      1. 在index.html中导入main.js <script src="main.js"></script>
      2. main.js中获取calc.js的add方法 var add = require("./calc.js");
      3. calc.js将add方法作为参数传递 module.exports = add;

      以上是最初的实现，这样会报错，解决方案：
      1. sublime下安装一个插件terminal，安装这个插件是为了方便webpck打包，
         在要打包的文件夹下用命令提示符也行，不过比较麻烦。
      2. 在sublime需要打包的文件夹下右键Open Terminal Here执行命令webpack main.js build.js
      3. 这条命令是将main.js，以及main.js中require的文件都打包成build.js文件
      4. 打包完成后在index.html中导入的main.js改成我们打包好的build.js，完成打包。
      5. 打包过的所有js文件如果修改，修改后，要重新打包。
```

#### 3. webpack配置文件以及webpack常用指令
> 除了上面的打包方式，也可以利用默认的配置文件实现打包，默认配置文件打包，更实现分类。

```
// 默认文件名：webpack.config.js

module.exports={
    entry:'./src/main.js',// 指定打包的入口文件，即需要打包的文件
    output:{// 打包后的文件路径，以及打包后的文件名。
        path:__dirname + '/dist',// webpack1.14.0要求是绝对路径
                                 // 写死后文件换地址不好维护
                                 // __dirname代表需要打包的这个文件
        filename:'build.js'      // 打包后的文件名
    }
}


```

> webpack.config.js设置完成后，该文件夹下打开terminal执行`webpack`命令即可，下面有详解。

##### 3.1 webpack常用指令
```
      webpack 入口文件.js 输出文件.js
      webpack         // 最基本的启动webpack的方法，默认查找名称为 webpack.config.js文件
      webpack --config webpack.config.js    // 指定配置文件    
      
      webpack -p      // 对打包后的文件进行压缩
      webpack -d      // 提供source map，方便调式代码
```

##### 3.2 webpack.config.js配置

###### 3.2.1 webpack.config.js配置文件的作用
```
如果只在cmd命令面板中输入 webpack指令，后面不跟任何参数的话，则默认查找的是 webpack.config.js文件，在这个文件中可以配置入口文件，输出文件以及相关loader和插件等,以增强webpack的功能
```

###### 3.2.2 常用webpack1.0版本的webpack.config.js文件结构
```
javascript
// 导入html-webpack-plugin 包，用来根据模板自动生成index.html
var htmlwp = require('html-webpack-plugin');

module.exports={    
    entry:'./src/main.js', // 1.0 定义打包的入口文件路径
    output:{
        path:'./dist',   //打包以后的文件存放目录
        filename:'build.js'  // 打包以后生成的文件名称
    },
    module:{
        loaders:[
            {
                // 将当前项目中所有的.js文件都要进行es6转es5操作，node_moudels除外
                test:/\.js$/,   //表示当前要打包的文件的后缀正则表达式
                // loader:'babel-loader?presets[]=es2015', //如果写到这里，将来在打包.vue文件的时候会报错，表示先利用css-loader解析.css文件，再调用style-loader打包
                loader:'babel-loader',
                exclude:/node_modules/  //node_modules中的所有.js文件不去转换，提高打包性能
            }           
        ]
    },
    babel:{
         presets: ['es2015'],  //表示es6转es5
         plugins: ['transform-runtime']  //这句代码就是为了解决打包.vue文件不报错
    },
    plugins:[
        new htmlwp({
          title: '首页',  //生成的页面标题
          filename: 'index.html', //webpack-dev-server在内存中生成的文件名称，自动将build注入到这个页面底部，才能实现自动刷新功能
          template: 'index1.html' //根据index1.html这个模板来生成(这个文件请你自己生成)
        })
    ]
}
```


#### 4. webpack指令打包
##### 4.1 webpack实现css文件打包
###### 4.1.1 loader介绍
```
webpack本身不支持css,less,sass,js,image等相关资源的打包工作的，它仅仅提供了一个基础的框架，在这个框架上借助于相关的loader才可以实现css,less,sass,js,image等相关资源的打包工作 
```

###### 4.1.2 使用loader前的webpack配置
```
1. 在使用loader之前需要在当前项目目录下打开cmd命令面板，输入: npm init 初始化一个 package.json文件来存放相关的 loader包
2. 如果不执行这个操作，在这个文件夹下安装css-loader时，会产生node_modules
文件，这个文件夹下存放第三方零碎的包，如果几个人同是工作不易维护。利用npm init
存放第三方包名及版本，更方便。
```

###### 4.1.3 打包css资源
```
webpack中使用css-loader和style-loader这两个loader来处理css资源的打包工作，所以使用前必须在项目中先安装这两个包:
npm i css-loader style-loader --save-dev

1. --save-dev的意思是将css-loader和style-loader的文件名和版本号存放到
package.json文件下。如果不写--save-dev默认是不加的。
2. 删除node_moduls后，在当前文件夹下打开cmd，执行命令npm install，这个命令会查找package.json下的devDependencies，安装里面相应包及对应版本。

实现css的打包：
1. 需要打包的的文件夹下的statics文件夹下的css文件夹中的site.css打包到build.js中。
2. 配置webpack.config.js文件设置module。
3. 打包

```

> 实现css的打包的步骤图片实例

- 在webpack.config.js中配置这两个loader

![](/styles/images/vuewebpack/d4-11.png)
- 在项目中建立一个site.css文件，并且在main.js中导入

![](/styles/images/vuewebpack/d4-12.png)
- 在cmd中执行webpack命令

![](/styles/images/vuewebpack/d4-13.png)

##### 4.2 webpack实现scss文件打包
> 在实现scss打包之前，需要进行以下设置。

```
webpack中使用sass-loader，css-loader，style-loader来处理.scss文件的打包工作,
而sass-loader需要依赖于node-sass所以使用前必须在项目中先安装这些包，
并且node-sass的某些文件下载是需要去google上的，为了防止被墙而导致安装失败，
所以建议使用cnpm来安装：
cnpm install node-sass sass-loader css-loader style-loader --save-dev
注意：在安装cnpm之前，需要把cnmp安装成全局的npm install cnpm -g
```
> scss文件打包实现步骤

- 在webpack.config.js中配置这两个loader

![](/styles/images/vuewebpack/d4-14.png)

- 在项目中建立一个site1.scss文件，并且在main.js中导入

![](/styles/images/vuewebpack/d4-15.png)

- 在cmd中执行webpack命令

```
在项目根目录下打开cmd命令面板，输入：webpack  回车即可打包完成
此时检查build.js文件的内容，sass语法是变成了css语法表示打包成功
```
##### 4.3 webpack实现less文件打包
> 在实现scss打包之前，需要进行以下设置。

```
需要安装的node包有：
    css-loader：  编译css
    style-loader：编译css
    less-loader： 编译less
    less:  less-loader的依赖包
    
    在项目根目录下打开cmd命令面板，输入：
    npm install less less-loader style-loader css-loader --save-dev 回车即可完成安装
```
> less文件打包实现步骤

- 在webpack.config.js中配置这两个loader，下图中的sass指的是less

![](/styles/images/vuewebpack/d4-16.png)

- 在项目中建立一个site1.scss文件，并且在main.js中导入

![](/styles/images/vuewebpack/d4-17.png)

- 在cmd中执行webpack命令

```
  在项目根目录下打开cmd命令面板，输入：webpack  回车即可打包完成
  此时检查build.js文件的内容，less语法是变成了css语法表示打包成功
```
##### 4.4 webpack实现url()请求资源的打包
> 在实现url资源打包之前，需要进行以下设置。

```
需要安装的node包有：
    url-loader：打包通过url()方式的请求资源
    file-loader: url-loader的依赖loader
    
    在项目根目录下打开cmd命令面板，输入：
    cnpm install url-loader file-loader --save-dev 回车即可完成安装
```

> 重命名的文件夹如果出现路径错误，把node_moduls删除再重新安装一遍即可，尤其是用`cnpm`安装过的包，经常会出现这种问题。<br>
> `npm i`<br>
> 一般安装出错的时候用`cnpm`试试。一旦使用`--save-dev`保留过loader名和版本号，删除node_moduls后，利用`cnpm i`即可重新安装。<br>
> <br>
> url请求的资源如果大于webpack.config.js中`loader:'url-loader?limit=40000'`limit的值，则本地请求访问不到。原因是：当资源小于这个值的时候webpack会把资源文件直接编码到我们这里的build.js文件中，而当资源的大小大于这个临界值的时候build.js中保存的是路径。在sublime中右键打开.html文件不会出现大的资源。用服务器解决。

- 在webpack.config.js中配置这两个loader

![](/styles/images/vuewebpack/d4-18.png)

- 在site.css文件导入一个图片

![](/styles/images/vuewebpack/d4-19.png)

- 在cmd中执行webpack命令

```
在项目根目录下打开cmd命令面板，输入：webpack  回车即可打包完成
  检查是否成功分两种情况：
  1、如果打包的图片大小大于配置文件中 url-loader?limit= 中的limit值的话，则会在目录下看到一张单独的一个图片
  2、如果打包的图片大小小于等于配置文件中 url-loader?limit= 中的limit值的话，则会将图片以base64格式存储在build.js中
```
###### 4.4.1 打包url请求的第三方框架mui中的字体资源
1. 在main.js文件下导入资源`require('../statics/mui/css/icon-extra.css')`。
2. 在配置文件webpack.config.js文件url请求的资源模块下加上后缀`.ttf`。
3. webpack打包。

##### 4.5 webpack指令实现ECMAScript6语法转ECMAScript5语法
> 打包前需要安装的loader

```
需要安装的node包有：
  babel-core
  babel-loader
  babel-preset-es2015
  babel-plugin-transform-runtime：这个包主要是在打包.vue组件页面中的es6语法，
                                 没有使用vue就不用导入这个包。

  需要在项目根目录下打开cmd命令面板，输入：
  npm install babel-core babel-loader babel-preset-es2015 babel-plugin-transform-runtime  --save-dev 回车即可完成安装
```

- 在webpack.config.js中配置这两个loader，其中babel是在module外。

![](/styles/images/vuewebpack/d4-20.png)

- 在main.js中使用es6语法导入site.css

```
    import '../statics/css/site.css'// 导入没有返回对象的css
```

- 在main.js中使用es6语法导入calc.js

```
    import addObject from './calc.js' // 导入有返回对象add的js
                                      // addObject是一个对象
    res.value = addObject.add(v1value,v2value);
```

- 在calc.js中使用es6语法导出add

```
//module.exports = add; // ES5，导出的是变量
export default{
    add  //ES6中当属性名称和属性值变量同名的时候可以只写一个
         //相当于ES5 add:add
         //这里导出的是对象，对象名的对象的值同名了
}
```

- 在cmd中执行webpack命令

```
  1. 在项目根目录下打开cmd命令面板，输入：webpack 回车即可打包完成
  检查build.js文件中，如果出现了类似于 require('../statics/css/site.css');
  但是看不到import '../statics/css/site.css' 表示转换成功
  2. 如果搭建了webpack-dev-server执行npm run dev
  3. main.js中导入的ES6不能放在函数内，否则会报错。
```

#### 5. webpack-dev-server的使用
##### 5.1 利用webpack-dev-server实现热刷新配置
> 一定要注意webpack的版本和webpack-dev-server的版本是1.0版本的。

- 使用webpack-dev-server当代码更新的时候自动重新打包和刷新浏览器，不用每次执行webpack指令。
- 需要安装的包有：

```
  webpack-dev-server ： webpack开发服务器
  html-webpack-plugin ：结合webpack在内存中自动生成index.html的入口文件
```
- 安装命令是：

```
npm install webpack-dev-server html-webpack-plugin  --save-dev
// 我比较喜欢用cnpm
```

- 安装完成后在package.json文件中配置webpack-dev-server命令

```
    "scripts": {
        "dev":"webpack-dev-server --inline --hot --open --port 5008"
      }
    
    这四个指令一个不能少。
    参数说明：
    --inline :自动刷新
    --hot :热加载
    --port 指定监听端口为 5008
    -- open : 自动在默认浏览器中打开
    -- host： 可以指定服务器的ip，不指定默认为127.0.0.1(localhost)
```

- 配置html-webpack-plugin组件

```
    webpack-dev-server要实现浏览器自动刷新，必须要利用html-webpack-plugin在内存中生成index.html页面才能实现
    html-webpack-plugin 配置步骤：
    1、在webpack.config.js中加入如下代码：
        // 导入html-webpack-plugin 包,获取到插件对象
        var htmlwp = require('html-webpack-plugin');
        
        // plugins是在module后面用。
        plugins:[
        new htmlwp({
          title: '首页',  //生成的页面标题
          filename: 'index.html', //webpack-dev-server在内存中生成的文件名称，自动将build注入到这个页面底部，才能实现自动刷新功能
          template: 'index1.html' //根据index1.html这个模板来生成(这个文件请程序员自己生成)
                                  //这是路径，按路径规则
        })
    ]
```

- index1.html 模板页面代码
    + 以上步骤设置完成后就不用在index1.html中导入build.js。
    + 因为webpack.config.js的"script"中的设置会将index1.html指向build.js。

```
<!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>Document</title>
        <meta name="viewport" content="width=device-width,initial-scale=1,minimum-scale=1,maximum-scale=1,user-scalable=no"/>
    </head>
    <body>
        <div id="app">
        </div>  
    </body>
    </html>
```
- 运行

```
在cmd中执行npm run dev 命令开启 webpack-dev-server服务器来运行vue项目
这时修改一个css样式，就会自动刷新看到效果   
```

- 错误调试-->找了好长时间，想到了版本才幡然醒悟...


```
按以上命令执行后肯定不成功，原因如下：
1. 执行npm install webpack-dev-server html-webpack-plugin  --save-dev
这条命令安装的webpack-dev-server和html-webpack-plugin版本都是最新版，
至今这两个的最新版都到了2.X，而webpack版本和webpack-dev-server都是1.X或是2.X。
2. 由于webpack是1.14.0，所以安装webpack-dev-server的要指定版本，语法npm i webpack-dev-server@1.3.0指定版本。然后执行npm run dev
就会成功跳转至localhost:5009页面。
3. 如果还报错检查一下package.json下的"devDependencies"，查看一下对应版本，如果
没有相应的package，可以把node_modules文件删除，在package.json下的"devDependencies"填上对应的包再执行cnpm i，全部重新安装一下。

4. 此处我的package.json下的"devDependencies"为：
"devDependencies": {
    "webpack": "^1.14.0",
    "css-loader": "^0.28.7",
    "file-loader": "^0.11.2",
    "html-webpack-plugin": "^2.30.1",
    "less": "^2.7.2",
    "less-loader": "^4.0.5",
    "node-sass": "^4.5.3",
    "sass-loader": "^6.0.6",
    "style-loader": "^0.18.2",
    "url-loader": "^0.5.9",
    "webpack-dev-server": "^1.3.0"
  }


```
