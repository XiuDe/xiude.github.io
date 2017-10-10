---
layout: original
title: "本地项目局域网和外网访问"
date: 2017-10-10 17:08:49 +0800 
categories: 服务器搭建
tag: wamp
---
* content
{:toc}

1.将本地项目映射到外网，外网也能访问。
   - itun + wamp

2.局域网访问。
   - wamp配置

<!-- more -->

#### 1.将本地项目映射到外网
1. 安装wamp，并且下载适合机器的ittun。
2. 找到httpd.conf文件查看默认端口号，一般为80。
3. 解压ittun压缩包，编辑startup-vueapi.bat文件。
   ```
@echo on
cd %cd%
ngrok -config ittun.yml -subdomain xiude 80
## vuesy -> xiude.ittun.com/01ajax.html
pause
#ngrok 9090
   ```
4. 双击打开startup-vueapi.bat文件即可连接成功。

#### 2.wamp局域网内部访问配置
- 安装好wamp，找到httpd.conf。
    + 打开服务器后点击图标找到Appache的httpd.conf
    + 安装文件wamp->bin->apache->Apache2.4.4->conf->httpd.conf
- 将第268行的Deny修改为Allow，重启服务器
- 可以根据需要修改默认访问的路径，在httpd.conf的235行找到DocumentRoot，找到239行将默认路径修改为自定义路径，240行的Directory也修改。※
- 修改虚拟主机就可以然后再修改windows下的hosts文件建立映射关系。※
    + 修改wamp的虚拟主机，apach支持多个主机修改了httpd.conf的239,240,268之后，在499行去掉#，然后在conf->extra->httpd-vhosts.conf进行虚拟主机主体的配置，根据需要可以配置多个。
    
```
<VirtualHost *:80>
    DocumentRoot "c:/myAjax/demo/php"
    ServerName 192.168.1.103
    ServerAlias 192.168.1.103
</VirtualHost>

<VirtualHost *:80>
    DocumentRoot "c:/myAjax/demo/php"
    ServerName 192.168.1.102
    ServerAlias 192.168.1.102
</VirtualHost>
```




   + 建立域名与IP地址的关联映射关系，Windows->System32->drivers->etc->HOSTS
    
```
127.0.0.1       localhost
127.0.0.1       xiude.com
127.0.0.1       www.xiude.com
127.0.0.1       tom.com
127.0.0.1       www.tom.com
127.0.0.1       jerry.com
127.0.0.1       www.jerry.com
127.0.0.1       192.168.1.103
127.0.0.1       127.0.0.1
```

以上设置完成后就可根据需要用域名访问自己的项目，将※设置完成后。当前局域网下就可访问，当前局域网下别的机器访问，访问的是你当前的ip。
> 局域网配置完成后，如果设置映射外网会连接不上，
> 将局域网设置还原即可。