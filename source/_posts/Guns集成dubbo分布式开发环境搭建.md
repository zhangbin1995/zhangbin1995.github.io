---
title: Guns集成dubbo分布式开发环境搭建
date: 2019-03-04 13:20:37
tags:
	- java
	- springboot
	- dubbo
	- Guns
categories:
	- java
---

更多文章欢迎访问个人博客 [www.herobin.top](http://www.herobin.top)

首先项目是基于开源软件Guns为基础的。

码云地址：[https://gitee.com/stylefeng/guns](https://gitee.com/stylefeng/guns)

官网介绍如下：Guns基于Spring Boot 2，致力于做更简洁的后台管理系统，完美整合springmvc + shiro + mybatis-plus + beetl，Guns项目代码简洁，注释丰富，上手容易，同时Guns包含许多基础模块(用户管理，角色管理，部门管理，字典管理等10个模块)，可以直接作为一个后台管理系统的脚手架！同时提供spring cloud版本！

这里我们使用的不是最新版本，而是比较方便集成dubbo进行分布式开发的一个版本，资料和本博客的项目源码在文章末尾的github中。

将压缩包在我们理想的目录下解压出来。

![](/uploads/190304java1/1.png)

首先我们使用guns-rest/db/guns-rest.sql语句创建guns-rest数据库。

![](/uploads/190304java1/2.png)

去yml中将数据库信息改为本机信息，启动GunsRestApplication，发现报错缺少log4j，在pom中将其引入。

![](/uploads/190304java1/3.png)

题外话：我们使用开源软件的时候，如果一个版本使用的很好就不要老换，容易出现问题，如维护的人不多导致提交不规范等，可能会使软件变得不再好用。

上面的问题我们在rest的pom中加上依赖(去maven repository中找就好)。
```
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
```

再次运行发现还有报错MybatisPlusException，这里是因为数据库信息中使用了一个zeroDateTimeBehavior，这里我们将它换成serverTimezone就好了。

![](/uploads/190304java1/4.png)

再次启动，运行成功。

这时我们访问rest提供的验证接口：http://localhost/auth?userName=admin&password=admin 

![](/uploads/190304java1/5.png)

如图，成功返回了token和随机密钥，也说明了我们的guns环境搭建成功了。

这里我们添加模块可以直接在项目上new module，也可以直接拷贝guns-rest然后改名即可，这里推荐第二种拷贝的方式。

我们通过copy rest模块新建一个模块guns-gateway，记住要将他添加到总工程的pom中。

![](/uploads/190304java1/6.png)

在gateway的pom中将artifactId改为正确的名称。

![](/uploads/190304java1/7.png)

这里会发现我们的gateway模块后面跟着一个guns-rest(1)，这是因为我们从rest拷贝过来的名字默认是rest(1)，去项目的project structure中修改掉即可。

![](/uploads/190304java1/8.png)

启动一下gateway，启动成功了，再次访问auth接口，还是成功的。

接下来我们在Guns中集成dubbo。

首先将dubbo和zookeeper的依赖包拷贝进来。

![](/uploads/190304java1/9.png)

在yml中配置dubbo信息：

![](/uploads/190304java1/10.png)

启动类上开启dubbo注解：

![](/uploads/190304java1/11.png)

然后我们编写一个模拟验证登录状态的服务接口以及他的实现类并通过@Service将它暴露给dubbo。

![](/uploads/190304java1/12.png)

这样dubbo的一个基本环境就已经完成了。

将zookeeper启动，再启动我们的项目，会在zookeeper的日志中发现注册了新的服务，至此，集成dubbo成功。

再次执行auth接口，一切正常。

这里我们可以发现有一个问题，我们的模块中的接口和实现类都是一对一的，可是每个模块又要有其他模块的接口，这样接口的冗余会非常高，这样我们可以再单独建一个子工程，他的作用就是承载我们的业务接口以及各个模块中需要的实体类。
这样接口我们只写一遍，所有的工程就都可以共享了。

我们拷贝guns-core新命名为guns-api，将用不到的target删掉。
在父项目的pom中将它添加进去，再到project structure中将它改好名字，还要改好它自身的pom里的配置。
将原来的模块都删掉。

![](/uploads/190304java1/13.png)

新建我们自己的模块，包名也可以改为我们想要的。然后就可以将我们在gateway中编写的UserAPI接口拷贝过来。

![](/uploads/190304java1/13.1.png)

然后在gateway中继承一下api模块，就可以直接使用里面的接口了。

![](/uploads/190304java1/14.png)

这样就可以将我们gateway里面的UserAPI接口删掉了，会自动继承我们写在api里的UserAPI。
注意要将我们的api模块放到maven库中，才能读取的到。

![](/uploads/190304java1/15.png)

不要忘了在api的pom中将名字由core改为api。

![](/uploads/190304java1/16.png)

再点击maven中api模块的Lifecycle中的install就可以安装api到maven中了，这样gateway里就能读到api里的接口了。

![](/uploads/190304java1/17.png)

不要忘了去父节点中引入api包。

![](/uploads/190304java1/18.png)

这时我们可以看到gateway中UserImpl引用的UserAPI接口就是来自api模块中的了。

![](/uploads/190304java1/19.png)

以后将所有需要暴露的接口都放在api中就好了。

这样开发的架子就已经搭建好了。

项目github地址：https://github.com/zhangbin1995/guns.git

