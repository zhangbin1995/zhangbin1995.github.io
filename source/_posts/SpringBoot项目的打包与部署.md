---
title: SpringBoot项目的打包与部署
date: 2018-12-28 20:15:13
tags:
	- java
	- springboot
categories:
	- java
---

## SpringBoot项目的打包与部署
### 1.打包
到我们的项目目录下执行打包命令

```
mvn clean package -Dmaven.test.skip=true
```

![](/uploads/181228springboot/1.png)

打包成功后可以看到 `BUILD SUCCESS`。

![](/uploads/181228springboot/2.png)

target目录下的o2o-0.0.1-SNAPSHOT.jar就是我们打包好的jar包了。
这里生成这个版本的jar包的原因是我们在pom.xml中的配置。
这里可以通过命令 java -jar o2o-0.0.1-SNAPSHOT.jar来启动我们的项目（注意要将IDE中已经启动的服务关掉否则会冲突）。

![](/uploads/181228springboot/3.png)

这个时候访问我们项目 `localhost:8080/frontend/index` 发现报404。
原因是我们并没有将html页面成功打包进去。
其实我们将jar改为war就好了。
我们常规需要的也是要打包成war包。

![](/uploads/181228springboot/4.png)

### 2.部署
将war包上传到服务器上
`scp o2o-0.0.1-SNAPSHOT.war work@101.132.138.185:~`
将之前的java和tomcat进程都`kill`掉。
去服务器中启动我们的war包程序
`java -jar o2o-0.0.1-SNAPSHOT.war` 
这里默认是8080端口，可以在`application.properties`中设置成其它端口。
输入地址，可以访问到了，部署成功。

![](/uploads/181228springboot/5.png)

这里java -jar 启动有一个问题，当我们的终端与服务器失去连接后，
程序就不再运行了
那么要如何让服务器能持续的运行呢
创建一个脚本文件 startup.sh  

```
vim startup.sh
#!/bin/sh nohup后台运行 （守护进程）
nohup java -jar /home/work/o2o-0.0.1-SNAPSHOT.war &
```

保存退出后启动startup.sh就好了
`sh startup.sh`

![](/uploads/181228springboot/6.png)

这样就一直跑在我们的服务器了如果要关掉kill掉就好了。






