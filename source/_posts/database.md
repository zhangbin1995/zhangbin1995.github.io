---
layout: could
title: could not create connection to database问题解决
date: 2018-10-25 11:29:09
tags:
	- java
	- 数据库
categories:
	- java
---
could not create connection to databasewen问题解决

今天要将项目部署到阿里云服务器上时，先将项目里的数据库配置信息改为了阿里云上面的数据库信息，运行启动后报错

```
could not create connection to databasewen
```

查找后发现本机是mysql5.6版本的，而阿里云上装的是mysql8.于是去pom文件中将数据库版本改掉，问题解决。

```
<dependency>
	<groupId>mysql</groupId>
	<artifactId>mysql-connector-java</artifactId>
	<!-- <version>5.1.37</version> -->
	<version>8.0.11</version>
	<scope>runtime</scope>
</dependency>
```