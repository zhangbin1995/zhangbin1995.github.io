---
title: Eclipse远程调试
date: 2018-12-17 16:24:19
tags:
	- java
categories:
	- java
---
项目现在部署在服务器上，我们想要通过本地Eclipse来远程调试
在服务器上输入命名

```
ps -ef | grep tomcat
```

可以看到我们tomcat是由root用户启动的,这里需要我们改为其他用户,root启动的进程不支持远程调试,
这里我们要改为用普通的账号启动tomcat.

这里要注意,普通账号是没办法运行root账号安装的软件的.所以我们要用普通账号安装一下tomcat.

创建新用户的指令

```
adduser work
```

输入密码

```
passwd work
```

![](/uploads/181217eclipse/1.png)

`shudown`掉现在的tomcat程序,在tomcat目录下执行`bin/shutdown.sh`

切换账号

```
su work
```

切换回这个账号的根目录`cd ~`,可以看到当前的用户路径`/home/work`,再次上传本地的tomcat到服务器

```
scp apache-tomcat-8.5.35.tar.gz work@101.132.138.185:~
```

解压这个tomcat

```
tar -xzvf file.tar.gz
```

再将我们的代码部署到上面因为没有改端口号用过`ip:8080/o2o`就可以访问到了.可是微信登录只能访问80端口,所以我们要做一个转发操作.

切换回`root`用户关掉`firewalld`服务,安装`iptables-services`服务.

```
su root
systemctl stop firewalld.service
systemctl disable firewalld.service
yum install iptables-services
```

将对80端口的请求转发到8080

```
iptables -t nat -A PREROUTING -p tcp --dport 80 -j REDIRECT --to-port 8080
service iptables save #让上面的指令生效
```

这个时候已经生效了,但是重启服务器就会失效,执行下面两条指令让它永久生效.

```
systemctl restart iptables.service
systemctl enable iptables.service
```

修改tomcat配置(work下的)
修改`bin/catalina.sh`  `vim catalina.sh`
加入的部分：

![](/uploads/181217eclipse/2.png)

这句话的意义是 告诉tomcat你要开启一个端口支持远程调试,同时别忘了将服务器的这两个端口号打开

![](/uploads/181217eclipse/3.png)

切换回`work` 重启`tomcat` `shutdown` `start`

这个时候再 `ps -ef | grep tomcat`,就可以发现这个进程是work的了,包括我们设置的调试信息也有了

![](/uploads/181217eclipse/4.png)

回到我们的eclipse,在启动debug服务器（小昆虫）那里找到 `Debug` `Configuration`修改配置如下

![](/uploads/181217eclipse/5.png)

Eclipse远程调试总结
添加新用户，并赋予root权限

```
adduser work
passwd work
```

上传tomcat软件，上传程序war包

将80端口的请求转发至8080端口（永久生效）

```
systemctl stop firewalld.service #停止firewall
systemctl disable firewalld.service # 禁止firewall开机启动
yum install iptables-services #安装
iptables -t nat -A PREROUTING -p tcp --dport 80 -j REDIRECT --to-port 8080
service iptables save
systemctl restart iptables.service
systemctl enable iptables.service
```

远程调试tomcat`vim bin/catalina.sh`添加

```
CATALINA_OPTS="-Xdebug  -Xrunjdwp:transport=dt_socket,address=8888,server=y,suspend=n"
```

开通阿里云控制台安全组策略8888端口
在eclipse debug做对应的配置即可