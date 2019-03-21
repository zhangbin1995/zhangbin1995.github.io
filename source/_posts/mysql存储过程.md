---
title: mysql存储过程
date: 2017-12-13 19:19:47
tags:
	- mysql
	- 数据库
categories:
	- 数据库
---

![](/uploads/171213mysql/1.png)

#### 存储过程

存储过程是SQL语句和控制语句的预编译集合，以一个名称存储并作为一个单元处理。

#### 存储过程的优点

> * 增强SQL语句的功能和灵活性
> * 实现较快的执行速度
> * 减少网络流量

![](/uploads/171213mysql/2.png)

> * IN：表示该参数的值必须在调用存储过程时指定
> * OUT：表示该参数的值可以被存储过程改变，并且可以返回。
> * INOUT：表示该参数的调用时指定，并且可以被改变和返回。

![](/uploads/171213mysql/3.png)

![](/uploads/171213mysql/4.png)

#### 存储引擎

MySQL可以将数据以不同的技术存储在文件（内存）中，这种技术就成为存储引擎。

每一种存储引擎使用不同的存储机制、索引技巧、锁定水平，最终提供广泛且不同的功能。

![](/uploads/171213mysql/5.png)

#### 并发控制

当多个连接对记录进行修改时保证数据的一致性和完整性。

![](/uploads/171213mysql/6.png)

![](/uploads/171213mysql/7.png)

#### 事物

![](/uploads/171213mysql/8.png)

![](/uploads/171213mysql/9.png)

外键：是保证数据一致性的策略。

索引：是对数据表中一列或多列的值进行排序的一种结构。

![](/uploads/171213mysql/10.png)

![](/uploads/171213mysql/11.png)

![](/uploads/171213mysql/12.png)

![](/uploads/171213mysql/13.png)
