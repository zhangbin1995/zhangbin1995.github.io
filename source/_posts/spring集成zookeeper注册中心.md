---
title: spring集成zookeeper注册中心
date: 2019-03-03 15:58:34
tags:
	- java
	- spring
	- zookeeper
	- dubbo
categories:
	- java
---

更多文章欢迎访问个人博客 [www.herobin.top](http://www.herobin.top)

在spring+dubbo开发环境搭建好，及下载安装好zookeeper之后（相关步骤在前面的博客中），现在我们来配置spring集成zookeeper注册中心。
在provider的`application.xml`中配置提供者到注册中心zookeeper中，这里zookeeper为本机的zookeeper，zookeeper的默认端口号为`2181`。
```
<dubbo:registry address="zookeeper://localhost:2181"/>
```

![](/uploads/190303java3/1.png)

记得将之前没有配置注册中心的写在`dubbo:service`中的`register="N/A"`删掉。

使用命令行 `zkServer.sh start-foreground` 前台启动zookeeper后，再运行provider程序，可以看到日志输入，即我们服务者接口注册成功了。

![](/uploads/190303java3/2.png)

接下来我们去consumer服务消费者这边配置，同样是在`application.xml`中配置注册中心

![](/uploads/190303java3/3.png)

启动consumer服务，输入得到输出，配置成功。

![](/uploads/190303java3/4.png)
