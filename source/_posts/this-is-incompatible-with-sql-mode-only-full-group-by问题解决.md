---
title: this is incompatible with sql_mode=only_full_group_by问题解决
date: 2019-01-02 18:34:24
tags:
	- java
categories:
	- java
---

在编写好xml代码在测试类中测试时报错

```
this is incompatible with sql_mode=only_full_group_by
```

看一下`ONLY_FULL_GROUP_BY`的意思是：对于GROUP BY聚合操作，如果在SELECT中的列，没有在GROUP BY中出现，那么这个SQL是不合法的，因为列不在GROUP BY从句中，也就是说查出来的列必须在group by后面出现否则就会报错，或者这个字段出现在聚合函数里面。

进入到mysql中查看mysql版本 `select version();`
查看sql_model参数命令：`SELECT @@GLOBAL.sql_mode;`
      					 `SELECT @@SESSION.sql_mode;`
可以看到当前sql_mode。

![](/uploads/190102mysql/1.png)

这里是我改过了的，所以已经没有ONLY_FULL_GROUP_BY了。
而我们的解决办法也是去掉这里的ONLY_FULL_GROUP_BY。

### 方法一：

命令行输入：
```
set @@GLOBAL.sql_mode='';
set sql_mode ='STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION';
```
默认关掉ONLY_FULL_GROUP_BY！
 
这个时候 在用工具select 一下
`SELECT @@sql_mode;`
`SELECT @@GLOBAL.sql_mode;`
 
发现已经不存在ONLY_FULL_GROUP_BY,代码运行正常。
但是如果你重启Mysql服务的话，发现ONLY_FULL_GROUP_BY还是会存在的。

### 方法二：（推荐）

想要彻底解决这个问题还是要去配置文件my.cnf中修改，加入下图内容，保存，重启mysql。

![](/uploads/190102mysql/2.png)

注意这里开始的时候重启有报错，原因是因为mysql8.0以上已经取消了
`NO_AUTO_CREATE_USER`这个关键字，删掉sql语句中的这个关键字即可。

![](/uploads/190102mysql/3.png)

```
sql_mode=STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION
```

```
service mysqld stop
service mysqld start
```

问题解决。