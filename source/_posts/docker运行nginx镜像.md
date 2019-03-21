---
title: docker运行nginx镜像
date: 2019-02-22 16:09:17
tags:
	- java
	- 服务器
	- docker
categories:
	- java
---
@[TOC](mac下安装docker)

更多文章欢迎访问个人博客 [www.herobin.top](http://www.herobin.top)

# 下载并运行nginx镜像
在网易云的镜像中心找到nginx的镜像
https://c.163yun.com/hub#/m/home/

复制nginx的下载地址：hub.c.163.com/library/nginx:latest
使用`docker pull hub.c.163.com/library/nginx:latest`
下载nginx镜像到本地

这时再使用`docker images`可以查看到本地已有的nignx镜像

使用`docker run hub.c.163.com/library/nginx`
发现没有任何输出，新建窗口输入`docker ps `可以查看本机当前运行的所有容器。

![](/uploads/190222java2/1.png)

这时只要通过`ctrl+c`既可以停止程序，所以我们通常推荐在后台运行镜像 通过加 `-d`
```
docker run -d hub.c.163.com/library/nginx
```
运行后可以通过docker exec命令进入指定镜像：

![](/uploads/190222java2/3.png)

可以看到
> 1. docker run nginx镜像成功后返回一串此镜像运行的唯一标识id。
> 2. 通过docker exec --help查看 docker exec命令帮助。
> 3. 通过docker exec -it 603 bash来进入已运行的nginx镜像  这里只输入了604因为已经可以唯一标识确定，若不能再多输。
> 4. 可通过exit命令退出镜像。
> 5. 可以使用docker stop 603命令来结束nginx镜像的运行。

# 网络访问nginx
运行了docker中的nginx后要怎样才能在网络（浏览器）中访问到nginx呢？
## docker网络类型
> * 1. bridge模式（桥接，默认类型，实现网络独立性）：docker默认的网络设置，此模式会为每一个容器分配Network Namespace、设置IP等，并将一个主机上的Docker容器连接到一个虚拟网桥上。
> * 2. host模式：容器不会获得一个独立的Network Namespace，而是和宿主机共用一个Network Namespace。容器将不会虚拟出自己的网卡，配置自己的IP等，而是使用宿主机的IP和端口。
> * 3. none模式：Docker容器拥有自己的Network Namespace，但是，并不为Docker容器进行任何网络配置。docker将不会和外界的任何网络进行通讯。
> * 4. container模式：新创建的容器和已经存在的一个容器共享一个Network Namespace，而不是和宿主机共享。新创建的容器不会创建自己的网卡，配置自己的IP，而是和一个指定的容器共享IP、端口范围等。

## 进行访问
> docker启动 -p 的使用： docker run -d -p 主机端口:容器端口 容器的名称
> docker启动  -P 的使用（所有的监听端口都会和主机建立一个映射）： docker run -d -P 容器名称
> 停止docker：docker stop 容器Id

**-p 的使用** (注意这里是小写的p，下面是大写的P)

![](/uploads/190222java2/4.png)

![](/uploads/190222java2/5.png)

**-P 的使用**

![](/uploads/190222java2/6.png)

![](/uploads/190222java2/7.png)
