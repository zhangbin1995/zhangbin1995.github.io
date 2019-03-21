---
title: git coding的使用
date: 2017-10-17 17:20:15
tags: 
	- git
categories:
	- 其他
---
### coding上传项目步骤
> * step1:在coding上面创建一个项目myboke
> * step2：使用git init创建.git文件夹和.gitigonre文件，帮组本地与远程的链接
> * step3：使用git pull <项目地址>同步项目上面的文件
> * step4: git add * 添加项目下面的所有文件
> * step5: git commit -m ‘备注’ 将文件添加在cache中并添加备注
> * step6:git remote add <名字（随意）> shh地址
  git remote add origin http://coding.....
> * step7:git push  
> * step8：最后在coding网站中查看是否上传成功

创建分支

```
git branch dev
```

切换分支

```
git checkout dev
```

上传代码到分支

```
git add .
git commit -m ' '
git remote add origin 远程仓库地址
git push -u origin dev
```