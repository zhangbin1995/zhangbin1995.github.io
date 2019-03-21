---
title: centos下安装docker
date: 2019-02-22 14:08:05
tags:
	- java
	- 服务器
categories:
	- java
---

更多文章欢迎访问个人博客 [www.herobin.top](http://www.herobin.top)

## 1. 检查内核版本
`uname -r`
返回的值大于3.10即可。

## 2. 使用sudo或root权限的用户登录终端。
## 3. 卸载旧版本（如果安装过旧版本的话）
```
$ yum remove docker \
			docker-common \
			docker-selinux \
			docker-engine
```

## 4. 安装需要的软件包
```
#yum-util提供yum-config-manager功能
#另外两个是devicemapper驱动依赖的
$ yum install -y yum-utils \
  device-mapper-persistent-data \
  lvm2
```
## 5. 设置yum源
```
$ yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
```
## 6. 安装docker
### 6.1. 安装最新版本
`yum install -y docker-ce`

### 6.2. 安装指定版本
```
#查询版本列表
$ yum list docker-ce --showduplicates | sort -r
已加载插件：fastestmirror, langpacks
已安装的软件包
可安装的软件包
 * updates: mirrors.163.com
Loading mirror speeds from cached hostfile
 * extras: mirrors.163.com
docker-ce.x86_64            17.09.1.ce-1.el7.centos            docker-ce-stable
docker-ce.x86_64            17.09.0.ce-1.el7.centos            docker-ce-stable
...
#指定版本安装(这里的例子是安装上面列表中的第二个)
$ yum install -y docker-ce-17.09.0.ce
```
## 7. 启动docker
`systemctl start docker.service`

## 8.验证安装是否成功（有client和service两部分表示docker安装启动都成功了）
```
$ docker version
Client:
 Version:      17.09.0-ce
 API version:  1.32
 Go version:   go1.8.3
 Git commit:   afdb6d4
 Built:        Tue Sep 26 22:41:23 2017
 OS/Arch:      linux/amd64

Server:
 Version:      17.09.0-ce
 API version:  1.32 (minimum version 1.12)
 Go version:   go1.8.3
 Git commit:   afdb6d4
 Built:        Tue Sep 26 22:42:49 2017
 OS/Arch:      linux/amd64
 Experimental: false
```

原文链接：
[https://www.imooc.com/article/16448](https://www.imooc.com/article/16448)
