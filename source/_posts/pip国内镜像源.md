---
layout: python
title: pip国内镜像源
date: 2018-04-09 10:17:24
tags:
	- python
categories:
	- python
---

pip使用国内镜像 提升下载速度和安装成功率

国内源
> * 清华：https://pypi.tuna.tsinghua.edu.cn/simple
> * 阿里云：http://mirrors.aliyun.com/pypi/simple/
> * 中国科技大学 https://pypi.mirrors.ustc.edu.cn/simple/
> * 华中理工大学：http://pypi.hustunique.com/
> * 山东理工大学：http://pypi.sdutlinux.org/ 
> * 豆瓣：http://pypi.douban.com/simple/

临时使用：

可以在使用pip的时候加参数`-i https://pypi.tuna.tsinghua.edu.cn/simple`

例如：

```
pip install -i https://pypi.tuna.tsinghua.edu.cn/simple pyspider
```

这样就会从清华这边的镜像去安装pyspider库
