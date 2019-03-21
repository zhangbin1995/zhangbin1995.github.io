---
title: django入门
date: 2018-03-11 09:23:45
tags:
	- python
categories:
	- python
---

## django初体验
### Django-python web开发框架
目前最流行的Python Web开发框架

Django框架负责处理大部分web开发底层细节，我们可以专注于开发web应用，避免重复制造轮子

### 为什么使用Django
> * 1.快速开发  用python开发	数据库ORM系统
> * 2.大量内置应用  后台管理系统admin 	用户认证系统auth	会话系统sessions
> * 3.安全性高  表单验证	SQL注入	跨站点攻击
> * 4.易于拓展 

指定在python3中安装django

`python3 -m pip install django`

安装django 

`pip install django`

新建django项目并启动

```
django-admin startproject mysite
cd mysite
python manage.py runserver
```

现在服务已经启动 可以去 `127.0.0.1:8000`查看了 

**工程目录详解**
> * manage.py	管理项目：包括数据库建立、服务器运行、测试...
> * mysite目录	
> * settings.py	配置文件：应用、中间件、数据库、静态目录各类配置...
> * urls.py URL映射配置文件：决定一个url访问被哪个程序（函数）响应
> * wsgi.py Python应用程序或框架和Web服务器之间接口 
> * manage.py	 可以自己指定端口 python manage.py runserver 0.0.0.0:8080

### 最常用的几个manage.py的命令

```
runserver		启动服务器
migrate、makemigrations 数据库相关的
shell  
django中使用应用来分割功能	例如 游戏网站 -> 分为四个应用：游戏新闻、游戏论坛、Blog系统、在线商城
```
### 创建应用
> * 1.创建应用blog	python manage.py startapp blog
> * 2.添加blog应用	mysite/settings.py -> INSTALLED_APPS

**应用目录详解**
> * views.py		相应客户请求返回html页面
> * models.py	定义数据库中的表
> * admin.py		admin相关
> * test.py		测试相关

### django概述

一次web访问的实质

1.客户发送http请求到web服务器

2.web服务器返回html页面给客户
> * 1.URL配置	建立URL和与响应函数之间的关系
> * 2.视图Views	响应客户http请求，进行逻辑处理，返回给用户html页面
> * 3.模型Models	描述我们服务器存储的数据（数据库的表）
> * 4.模板templates	用来生产html页面，返回给用户的html，是由数据（模型）和模板渲染出来的。

### django入门与实践
#### 什么是Django
Django是一个基于Python的高级WEB开发框架

它能够让开发人员进行高效且快速的开发

高度集成（不用自己造轮子），免费并且开源

#### 浏览器浏览网页的基本原理
本质是网络通信，即通过网络进行数据传递

浏览器经过通信后获取到该页面的源代码文档（HTML等）

浏览器解析文档后以适当的形式展现给用户

#### 安装Django两种方式

```
pip install Django==1.10.2
```

下载源码，进入根目录执行python setup.py install

#### 创建项目

打开命令行，进入想要安置项目的目录
命令行输入：django-admin startproject myblog
若没有报错，则创建项目成功
进入到项目中 cd myblog
启动项目 python manage.py runserver
指定端口 python manage.py runserver 9999


#### Templates介绍

**什么是Templates**
> * HTML文件
> * 使用了Django模板语言（Django Template Language，DTL）
> * 可以使用第三方模板（如Jinja2）

#### 开发第一个Template
**步骤**
> * 在APP的根目录下创建名为Templates的目录
> * 在该目录下创建HTML文件
> * 在views.py中返回render()

**DTL初步使用**
> * render()函数中支持一个dict（字典）类型函数
> * 该字典是后台传递到模板的参数，键为参数名
> * 在模板中使用{{ 参数名 }}来直接使用 
**注意**

Django查找Template
> * Django按照INSTALLED_APPS中的添加顺序查找Templates
> * 不同APP下Templates目录中的同名.html文件会造成冲突

解决Templates冲突方案
> * 在APP的Templates目录下创建以APP名为名称的目录
> * 将html文件放入新创建的目录下

#### Models介绍
**Django中的Models是什么？**

> * 通常，一个Model对应数据库的一张数据表
> * Django中Models以类的形式表现
> * 它包含了一些基本字段以及数据的一些行为

**ORM**

> * 对象关系映射（Object Relation Mapping）
> * 实现了对象和数据库之间的映射
> * 隐藏了数据访问的细节，不需要编写SQL语句

**编写Models**

步骤

> * 在应用根目录下创建models.py，并引入models模块（自动生成）
> * 创建类，继承models.Model，该类即是一张数据表
> * 在类中创建字段

字段创建

> * 字段即类里面的属性（变量）
> * attr = models.CharField(max_length=64)

[http://docs.djangoproject.com/en/1.10/ref/models/fields/](http://docs.djangoproject.com/en/1.10/ref/models/fields/)

**生成数据表**

步骤

> * 命令行进入manage.py同级目录
> * 执行python manage.py makemigretions app名（可选）
> * 再执行python manage.py migrate

查看

> * Django会自动在app/migrations/目录下生成移植文件
> * 执行python manage.py sqlmigrate 应用名 文件id 查看SQL语句
> * 默认sqlite3的数据库在项目根目录下db.sqlite3

查看并编辑db.sqlite3

> * 使用第三方软件
> * SQLite Export Personal
> * 轻量级，完全免费

**页面呈现数据**

后台步骤

> * views.py中import models
> * article = models.Article.objects.get(pk=1)
> * render(request,page,{'article':article})

前端步骤

> * 模板可直接使用对象以及对象的 . 操作
> * {{article.title}}

#### Admin简介

**什么是Admin**

> * Admin是Django自带的一个功能强大的自动化数据管理界面
> * 被授权的用户可直接在Admin中管理数据库
> * Django提供了许多针对Admin的定制功能

**配置Admin**

创建用户

> * python manage.py createsuperuser	创建超级用户
   （zhangbin	752658686@qq.com	zhangbin）
> * localhost:8000/admin/	Admin入口
> * 修改settings.py中LANGUAGE_CODE = 'zh_Hans

配置应用

> * 在应用下admin.py中引入自身的models模块（或里面的模型类）
> * 编辑admin.py：admin.site.register(models.Article)

修改数据

> * 点击Article超链接进入Article列表页面
> * 点击任意一条数据，进入编辑页面修改
> * 编辑页面下方一排按钮可执行相应操作

修改数据默认显示名称（在admin下的）

步骤
> * 在Article类下添加一个方法
> * 根据Python版本选择`__str__(self)(python3)`或`__unicode_(self)(python2.7)`
> * return self.title


模板For循环
`{% for xx in xxs %}`

HTML语句
`{% endfor %}`


#### Django中的超链接

超链接目标地址

```
`href`后面是目标地址

`Template`中可以用 `{% url 'app_name: url_name' param %}`

其中`app_name`和`url_name`都在`url`中配置

使用`request.POST['参数名']`获取表单数据

`models.Article.objects.create(title,content)`创建对象

涉及`POST`方法提交表单的，前端页面加上`{% csrf_token %}`
```

#### Templates过滤器

什么是过滤器

写在模板中，属于Django模板语言

可以修改模板中的变量，从而显示不同的内容

怎么使用过滤器？

```
{{ value | filter }}
例子：{{ list_nums | length }}  这样显示的就是长度而不是集合了
过滤器可叠加：{{ value | filter1 | filter2 | ... }} 
```

django访问不存在的对象时不会报错 只会传空

更多Django内建过滤器
[https://docs.djangoproject.com/en/1.10/ref/templates/builtins/#ref-templates-builtins-filters](https://docs.djangoproject.com/en/1.10/ref/templates/builtins/#ref-templates-builtins-filters)

#### Django Shell

什么是Django Shell？

它是一个Python的交互式命令行程序

它自动引入了我们的项目环境

我们可以使用它与我们的项目进行交互

#### 如何使用Django Shell？

```
python manage.py shell
from blog.models import Article
Article.objects.all()
```

有什么用？

我们可以使用Django shell来进行一些调试工作

测试未知的方法

#### Admin增强

创建admin配置类

```
classArticleAdmin(admin.ModelAdmin)
注册：admin.site.register(Article,ArticleAdmin)
显示其他字段
list_display = ('title','content')
list_displaty同时支持tuple和list(tuple类型只有一个数据，别忘了加逗号)
过滤器
list_filter = ('pub_time',)  这样就会有今天 过去七天 过去一个月 这些...
```

官方文档
[http://docs.djangoproject.com/en/1.10/ref/contrib/admin/](http://docs.djangoproject.com/en/1.10/ref/contrib/admin/)

