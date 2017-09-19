---
layout: original
title: "Jekyll安装总结"
date: 2017-09-17 22:59:49 +0800 
categories: Jekyll安装总结
tag: jekyll
---
* content
{:toc}

 本篇文章主要是关于在windows下的安装，以及安装过程中出现的问题，因为在前端学习的过程中，有很多知识很零散，就把知识点书写到了笔记上，但是由于知识的越多，查看回顾知识点不可能带着全部家当，虽没有学富五车的知识储量，但是笔记越来越多，实在不便，于是把知识积累放到了[github](https://github.com/XiuDe)上，虽然这样不用带五车书--，但是随着接触的东西越多发现这种方式无法实现知识快速查找以及项目实例的整合，于是就想写一个自己的博客，但是作为前端小学生不想自己的博客太low，所以就想先用jekyll写一个，随知识的积累会不断完善，以下是我在jekyll本地windows操作系统下搭建过程中所进行的步骤以及出现的问题，在这里整合一下，有不当之处希望大家指正。

<!-- more -->



#### 1. 安装jekyll需要了解的知识
- ruby与jekyll的关系
    + jekyll是基于ruby的，所以搭建jekyll之前必须确保ruby的正常安装，ruby必须大于2.0.0。
- 什么是ruby
    + ruby是一种脚本语言，由日本的一位程序员开发。
- ruby与ruby gem的关系
    + ruby的其中一个程序叫做`rubygems`，简称`gem`(ruby 1.9.2及以上的版本中默认安装了ruby gem，所以无需手动安装gem)
- 为什么特别介绍gem
    + 在windows下搭建jekyll，需要安装完ruby后用gem安装bunder(ruby bunder是rails框架中用来管理项目gem的)
- jekyll与python的关系
    + jekyll3.0之前，有一个语法高亮插件"Pygments"，这个插件是基于python的，所以才会有各种教程里面都说搭建jekyll之前需要python环境。
    + 但是,jekyll3.0以后，语法高亮插件已经默认改成了“rouge”而它是基于ruby的，也就是说 现在搭建jekyll,我们完全不必要再安装python。

#### 2. 安装Ruby
> [ruby官网](https://rubyinstaller.org/downloads/)下载

- 根据不同的机型选择不同的版本，为了稳妥起见建议和我安装的版本一样。由于要下载的东西挺多，一定要看好这里我们下载的是RubyInstalls下的版本。<br>

![](/styles/images/jekyll/download01.png)

- 下载完成后安装在默认目录，__注意这里的默认目录会在第3.安装Devkit配置config.yml用到记录下来__，系统会自动帮你完成环境变量的配置。勾选如下选项安装。<br>

![](/styles/images/jekyll/install01.png)

- 安装完成后再cmd中执行`ruby -v`，如果安装没有出错会出现安装的版本号。

#### 3. 安装Devkit
- 这次我们下载的是对应上面已安装的版本选择，这里下载的是Other Useful Downloads的7-ZIP ARCHIVES下的对应版本。以及DEVELOPMENT KIT下的版本。<br>

![](/styles/images/jekyll/download02.png)

- 下载完成后运行DEVELOPMENT KIT解压至你的指定目录，如C:\DevKit
- 通过初始化来创建 config.yml 文件。在命令行窗口内，输入下列命令：
```
cd “C:\DevKit”

ruby dk.rb init

notepad config.yml
```
- 在打开的记事本窗口中，于末尾添加新的一行你安装ruby记录下的根路径，保存文件并退出。
- 回到命令行窗口内，审查（非必须）并安装。
```
ruby dk.rb review

ruby dk.rb install
```

#### 4. 更改ruby默认得到source源
> 因为默认的官方源在国外，国内无法访问，如果不修改则无法使用，但是，现在淘宝源已经停止维护了，最新搭建jekyll 都应该要改成 ruby china的源。

- 在命令行先键入命令`gem sources -l` 查看当前已经添加的源(默认应该是同时有官方源和淘宝源)
- 然后通过 `gem sources -r https://rubygems.org/`和`gem sources -r https://ruby.taobao.org/`分别移除官方源和淘宝源 (注意，请对比实际，移除自己已经添加的源即可，可以改为自己上一步中查询出来的地址)
- 通过`gem sources -a http://gems.ruby-china.org` 添加了ruby china的可用源
- 修改来源后可以通过`gem sources -l`查看是否正确修改

#### 5. 安装jekyll
- 安装jekyll前先按照依赖包bundler，下述命令即可安装`gem install bundler`。
- 然后安装jekyll`gem install jekyll`。

#### 6. 利用官网运行实例
> [中文网](http://jekyll.com.cn/docs/usage/)<br>
> [英文网](http://jekyllrb.com/)

- 可以用git bash启动jekyll服务器。在项目目录下执行`jekyll serve`，成功后在浏览器中输入`http://localhost:4000`即可访问。