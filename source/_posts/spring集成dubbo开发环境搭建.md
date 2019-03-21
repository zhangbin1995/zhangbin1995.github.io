---
title: spring集成dubbo开发环境搭建
date: 2019-03-02 16:21:15
tags:
	- java
	- spring
	- dubbo
categories:
	- java
---

更多文章欢迎访问个人博客 [www.herobin.top](http://www.herobin.top)

首先新建maven项目选择quickstart为项目原型。

![](/uploads/190302java1/1.png)

点击项目添加两个module分别命名provider和consumer，原型同样选择quickstart分别作为服务提供者和消费者。（注意新建后暂时没有src文件夹，要等maven配置全部加载完才有，如果过慢，记得检查是否配置好idea的maven配置是否改为aliyun仓库）。
这里我们用不到测试用例，可以将test文件删掉。

添加放置资源文件的resources文件夹，并在project structrue中添加进Resources中。

![](/uploads/190302java1/2.png)

将需要的jar包依赖拷贝到父工程的pom中，这样各个模块就都可以使用了，注意在实际开发中，应该是各个模块分别引入该模块要用到的jar包，再在各模块的resources中新建applicationContext.xml即spring配置文件。

![](/uploads/190302java1/3.png)

这样简单的spring环境就搭好了，接下来我们要集成dubbo环境。
可以参考dubbo用户手册：https://dubbo.gitbooks.io/dubbo-user-book/content/quick-start.html

![](/uploads/190302java1/4.png)

在provider中新建一个服务接口DemoService和它的实现类。
用Spring配置声明暴露服务。

![](/uploads/190302java1/5.png)

编写服务启动类App.java

![](/uploads/190302java1/6.png)

至此，我们的provider模块就写好了，将它启动起来。
编辑consumer里的applicationContext.xml文件通过spring配置引用远程服务。

![](/uploads/190302java1/7.png)

这样就已经可以访问到provider接口提供的服务了。
编写App类加载consumer的spring配置文件并进行测试。

![](/uploads/190302java1/8.png)

启动消费者服务，输入zhangbin，得到服务提供者接口的返回值，由此，spring与dubbo集成环境配置成功。

项目代码已放到github：[https://github.com/zhangbin1995/springdubbo.git](https://github.com/zhangbin1995/springdubbo.git)