---
title: 全面解析Java注解
date: 2018-03-05 16:30:12
tags:
	- java
categories:
	- java
---

**为什么学习注解？学习注解有什么好处？学完能做什么？**
> * 1. 能够读懂别人写的代码，特别是框架相关的代码
> * 2. 让编程更加简洁，代码更加清晰
> * 3. 让别人高看一眼...

概念：Java提供了一种原程序中的元素关联任何信息和任何元数据的途径和方法

**JDK自带注解**
> * @Override    @Deprecated  @Suppvisewarnings  

**常见第三方注解**
> * Spring：@Autowired  @Service  @Respository 
> * Mybatis：@InsertProvider  @UpdateProvider  @Options

**注解的分类**

按照运行机制分：
> * 源码注解：注解只在源码中存在，编译成.class就不存在了
> * 编译时注解：注解在源码和.class文件中都存在（如jdk的三种注解）
> * 运行时注解：在运行阶段还起作用，甚至会影响运行逻辑的注解（如@Autowired）

按照来源分：
> * 来自JDK的注解
> * 来自第三方的注解（大部分）
> * 我们自己定义的注解

**自定义注解**

![](/uploads/180305java/1.png)

使用@interface关键字定义注解

成员以无参无异常方式声明

可以用default为成员指定一个默认值

成员类型是受限的，合法的类型包括原始类型及String，Class，Annotation，Enumeration

如果注解只有一个成员，则成员名必须取名为value()，在使用时可以忽略成员名和赋值号（=）

注解类可以没有成员，没有成员的注解称为标识注解

**元注解：（注解的注解）**

![](/uploads/180305java/2.png)

> * @Targer：注解的作用域
> * @Retention：生命周期
> * @Inherited：辨识性的元注解，标识允许子类（子注解）继承
> * @Documented：生成javadoc时会包含注解

![](/uploads/180305java/3.png)

**解析注解：**

概念：通过反射获取类、函数或成员上的运行时注解信息，从而实现动态控制程序运行的逻辑


**@Produces 表示类或者方法返回的MIME数据类型。**
> * （1）@Produces("text/plain") 文本类型
> * （2）@Produces("text/html")  Html类型
> * （3）@Produces({"application/xml"}) Xml类型
> * （4）@Produces({ "application/json"}) Json类型



