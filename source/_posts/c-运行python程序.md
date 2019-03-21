---
title: 'c#运行python程序'
date: 2018-10-23 19:51:53
tags:
	- python
	- 其他语言
categories:
	- 其他语言
---
最近做实验室的水体项目，需要在c#中运行python程序。

开始百度到可以通过IronPython直接在c#中调用py文件，在试了一个简单的helloworld确实可以之后就以为搞定搁置了一段时间，在python程序写好后运行发现了问题，IronPython中并不能导入python的第三方模块如numpy。在google之后发现，IronPython是基于.Net平台的，只能加载CLR Assembly，除了标准库，不能加载一般的C Extension。而numpy这种恰好是编译过的，根本不可能在IronPython上正常使用，至此，IronPython方式作废。

想到的第二种方式是将Python文件打包为exe文件，再在c#中调用exe，这里是在python文件中写好main方法的，这样调用exe就可以直接执行，只要传入参数就好了。

```
if __name__ == '__main__':
    render(sys.argv[1],sys.argv[2],sys.argv[3],sys.argv[4])
```
这里面传入的四个参数是水体渲染需要的四个参数，第一个是文件路径，第二个是左上右下坐标，第三个是样本点坐标，第四个是样本点对应的值。这里本来后三个是数组格式的，但是在c#中传入double类型的二维数组之后出错，估计是因为python中没有指定数据类型的概念导致，所以将c#中的数组转化为拼接的字符串传了进来，在python程序中再将字符串按照对应的参数转化为数组。至此搞定了。

将python程序打包成exe是通过pyinstaller

通过pip安装pyinstaller

```
pip install pyinstaller
```

生成exe

```
pyinstaller yourprogram.py
```

这样生成的是一个文件夹，生成单一可直接执行的最好用 -F的 方式

```
pyinstaller -F yourprogram.py
```
