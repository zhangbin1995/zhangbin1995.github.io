---
title: zookeeper下载与安装
date: 2019-03-03 14:47:13
tags:
	- java
	- zookeeper
categories:
	- java
---

更多文章欢迎访问个人博客 [www.herobin.top](http://www.herobin.top)

在zookeeper官网：[zookeeper.apache.org](http://zookeeper.apache.org) 中点击download进入下载页面

![](/uploads/190303java2/1.png)

点击进入下载，这里我们下载的版本是3.4.10

下载完成后，拷贝到我们想要放到的路径进行解压，注意路径不要有中文。

注意zookeeper的安装是要建立在jdk的安装上的，不过安装zookeeper的应该也都有jdk了。。。

首先我们要去conf文件夹中修改一个配置文件`zoo_sample.cfg`改名为`zoo.cfg`

去bin文件夹下可以看到我们启动执行文件`zkServer.sh`(windows则为`zkServer.cmd`)
执行该文件` ./zkServer.sh start`，如下图执行成功。

![](/uploads/190303java2/2.png)

ZooKeeper下载与安装完毕。

另外说明一下，这里的`./zkServer.sh start`是后台启动，若要查看日志可用前台启动
`zkServer.sh start-foreground`