---
title: springboot集成zookeeper注册中心
date: 2019-03-03 16:09:55
tags:
	- java
	- springboot
	- dubbo
	- zookeeper
categories:
	- java
---


更多文章欢迎访问个人博客 [www.herobin.top](http://www.herobin.top)

前面的博客我们配置了springboot集成dubbo开发环境搭建，这里我们在之前的基础上集成zookeeper注册中心。

首先不要忘记在consumer和provider的pom中引入zookeeper的依赖

![](/uploads/190303java4/1.png)

修改配置文件，由无注册中心的改为zookeeper注册中心的

![](/uploads/190303java4/2.png)

这时启动我们项目就可以将这个服务注册到zookeeper上了，可以从zookeeper日志上看出。

去consumer消费者中填写注册中心地址

![](/uploads/190303java4/3.png)

修改消费者接口将原来的直连提供者方式改为zookeeper配置。

![](/uploads/190303java4/4.png)

启动consumer模块，得到目标输出，springboot配置dubbo成功。

![](/uploads/190303java4/5.png)


