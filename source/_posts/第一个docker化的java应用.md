---
title: 第一个docker化的java应用
date: 2019-02-23 10:30:24
tags:
	- java
	- 服务器
categories:
	- java
---

更多文章欢迎访问个人博客 [www.herobin.top](http://www.herobin.top)

# 制作镜像Dockerfile
这里制作镜像主要分为三步
> Dockerfile
> docker build
> Jpress：http://jpress.io/ (开源java web应用 博客平台)

## 下载tomcat镜像
首先我们的镜像是要依托tomcat运行的，所以先到网易云的镜像中心下载tomcat的镜像
`docker pull hub.c.163.com/library/tomcat:latest`
在tomcat镜像的介绍中可以看到使用方法，如找到启动方法，查看tomcat路径等。

![](/uploads/190222java3/1.png)

## 新建编辑Dockerfile
`vi Dockerfile` 

![](/uploads/190222java3/2.png)

## 通过docker build生成镜像

![](/uploads/190222java3/3.png)

这里`-t jpress:latest`是指定了镜像名字和标签，注意后面有个 . 用于查找当前目录下的构建文件，即Dockerfile
用`docker images`查看，发现有了jpress。

![](/uploads/190222java3/4.png)

## 运行容器
在tomcat的镜像中心我们可以看到运行指令，执行：
`docker run -d -p 8888:8080 jpress`
启动镜像，发现可以访问我们的jpress程序了。

![](/uploads/190222java3/5.png)

这里要正常使用jpress需要配置好数据库信息。
我们也可以在docker中安装运行一个mysql数据库。
去镜像中心下载mysql镜像：
`docker pull hub.c.163.com/library/mysql:latest`
同样可以在介绍中看到使用方式。

![](/uploads/190222java3/6.png)

下载完后新建一个名为jpress的数据库并将mysql运行起来：
 `docker run -d -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 -e MYSQL_DATABASE=jpress hub.c.163.com/library/mysql:latest`
回到网站将信息配置好。

![](/uploads/190222java3/7.png)

注意这里点击下一步的时候失败了，使用docker ps命令发现mysql镜像并没有执行，再次执行run发现启动报错。

![](/uploads/190222java3/8.png)

因为本机有mysql服务正在使用，3306端口是占用状态的，将3306改为3307即可。
再下一页配置好网站信息，jpress网站即搭建成功可以正常使用了。

![](/uploads/190222java3/9.png)

