---
title: springboot集成dubbo环境搭建
date: 2019-03-03 12:31:12
tags:
	- java
	- springboot
	- dubbo
categories:
	- java
---

更多文章欢迎访问个人博客 [www.herobin.top](http://www.herobin.top)

新建project

![](/uploads/190303java1/1.png)

next取好项目名，一路next即可。
新建两个module，provider和consumer同样是springboot项目创建步骤同上。
将两个模块分别启动，均启动成功，说明springboot没有问题，接下来集成dubbo。
SpringBoot版本Dubbo集成中文地址：https://github.com/alibaba/dubbo-spring-boot-starter/blob/master/README_zh.md

将dubbo的依赖引入到两个模块的pom中

![](/uploads/190303java1/2.png)

在application.properties中添加dubbo的相关配置信息

![](/uploads/190303java1/3.png)

接下来在ProviderApplication的上添加@EnableDubboConfiguration，表示要开启dubbo功能. (dubbo provider服务可以使用或者不使用web容器)

![](/uploads/190303java1/4.png)

编写服务提供者的接口和实现类
编写你的dubbo服务，只需要添加要发布的服务实现上添加@Service（import com.alibaba.dubbo.config.annotation.Service）注解，其中interfaceClass是要发布服务的接口

![](/uploads/190303java1/5.png)

启动服务者的Spring Boot应用，观察控制台，可以看到dubbo启动相关信息.
至此，provider的dubbo配置成功。

接下来配置consumer的dubbo配置
添加依赖

![](/uploads/190303java1/6.png)

在application.properties中添加dubbo的相关配置信息
```
spring.application.name=dubbo-spring-boot-starter 
```
这里这个name是可以自己命名的。

开启ConsumnerApplication启动类的`@EnableDubboConfiguration`注解
```
@EnableDubboConfiguration
```
在consumer中编写服务实用类，记得要将provider中的接口DemeService拷贝过来

![](/uploads/190303java1/7.png)

编写ConsumerApplication类并执行

![](/uploads/190303java1/8.png)

获得目标输出，springboot集成dubbo环境搭建成功。

代码github地址：[https://github.com/zhangbin1995/springbootdubbo.git](https://github.com/zhangbin1995/springbootdubbo.git)

当然上面只是简单的springboot+dubbo的环境集成，这样是有瑕疵的。

我们的上面示例里消费者是直连提供者的，这种方式只可以在测试环境中使用，因为直连提供者限制了分布式的易扩展性，如果提供者部署在多节点，而我们在消费者中是直连写死的状态，那就只会访问一个节点，也违背了我们的分布式的初衷。

所以通常我们的消费者和提供者是要通过注册中心来注册调用的关于注册中心，后面会继续介绍，本篇博文介绍的springboot+dubbo的集成到此结束。

![](/uploads/190303java1/9.png)

![](/uploads/190303java1/10.png)
