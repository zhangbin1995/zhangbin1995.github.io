---
title: tensorflow基础
date: 2018-11-05 10:54:50
tags:
	- python
	- 人工智能
	- 深度学习
	- tensorflow
categories:
	- python
---
# 一些tensorflow学习过程中需要注意的随笔

tensorflow 可以分解为tensor（张量）和flow（流或流动两部分）。

任意维度的数据可以称作“张量”，如一维数组、二维矩阵、N维数据。tensorflow如果直译为中文，那就是“张量流”，它最初想要表达的含义是保持计算节点不变，让数据在不同的计算设备上传输并计算。

因为tensorflow主要用于深度学习方面，在深度学习的计算过程中有前向计算和后向传播的过程，所以深度学习中间的每个节点基本上都要执行前向的数值计算，以及后向的残差传播和参数更新。tensorflow把内部的数据都包装成tensor的类型，并且在tensor中包含了前向计算和反向传播时的残差计算，让所有的计算过程都练了起来。

tensorflow通过一个叫数据流图的方式来组织它的数据和运算。在使用tensorflow实现深度学习算法时，先将所有操作（operation）表达成一张图。张量从算法的开始走到结束完成一次向前运算，而残差从后往前就完成了一次后向传播来更新我们要训练的参数。

### 会话
TensorFlow的计算需要在会话中执行。当创建一个会话时，如果没有传递参数，默认情况下它会启动默认的图来构造图结构，并且会将“图”中定义的op根据定义的情况分发到CPU或者GPU的设备上去。

```python
	import tensorflow as tf

	v1 = tf.constant(1, name="value1")
	v2 = tf.constant(1, name="value2")
	add_op = tf.add(v1, v2, name="add_op_name")
	
	with tf.Session() as sess:
	  result = sess.run(add_op)
	  print("1 + 1 = %.0f" % result)

```

采用with语句包装，可以在结束with语句块的时候，自动销毁会话的资源。

为什么TensorFlow非得弄一个会话，并且让这些TensorFlow内部的Tensor必须在会话中才能执行，而不是直接使用Python的对象执行呢？这有点类似我们平时用到的Numpy库。我们经常会使用Numpy包来进行一些复杂的计算，比如矩阵乘法。这些计算的内部实现可能是Numpy采用非Python语言写的，从而使得运行效率更高。TensorFlow也类似。因为深度学习的计算很大部分是矩阵和向量的运算，采用GPU的计算运行速度比CPU会有一个数量级上的差别。如果直接使用Python的对象执行，以为python的变量是在CPU上的，所以数据就会频繁地在GPU和CPU的内存传送，效率会大大降低。这对于本来计算量就很大的训练任务简直无法承受了，所以TensorFlow为了更好地避免这些开销，采用会话中的实际计算是在Python之外独立运行的。

### 使用placeholder填充方式读取数据
placeholder填充方式的用法就像它的名字一样，在构建计算图的时候，在要输入的数据的变量的位置采用占位的方式先保留一个placeholder的张量，表示在构建图的时候并不知道这里实际的值是什么，需要在图执行时填充进来。

如果构建了一个包含placeholder操作的图，当在session中调用run方法时，placeholder占用的变量必须通过feed_dict参数传递进去，否则执行会报错。

```python
	#coding=utf-8
	import tensorflow as tf
	
	v1 = tf.placeholder(tf.float32)
	v2 = tf.placeholder(tf.float32)
	v_mul = tf.multiply(v1,v2)
	
	with tf.Session() as sess:
	  while True:
	
	    value1 = input("value1: ")
	    value2 = input("value2: ")
	
	    mul_result = sess.run(v_mul,feed_dict={v1:value1,v2:value2})
	    print(mul_result)

```