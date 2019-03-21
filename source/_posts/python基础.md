---
title: python基础
date: 2017-10-26 10:55:34
tags:
	- python
categories:
	- python
---
###python list

```
L = ['adna',87,true]
```

可以是不同类型的

索引从0开始  可以倒叙调用  L[-1]最后一个元素

添加 append是将新元素添加到末尾

insert接受两个参数 第一个参数是索引，第二个是待添加的新元素

```
L = ['adam','Lisa','Bart']
L.insert(0,'Paul')
```
这时L = ['Paul','adam','Lisa','Bart']

`pop`删除最后一个元素

`pop`可以加索引参数指定删除第几个

`pop(2)`删除第三个元素

替换元素  直接覆盖 `L[2] = 'Paul'`就将原来的替换掉了

### python tuple（元组）

与list区别：tuple一旦创建就不能修改

而且不是[ ]而是( )

`t = ('Adam','Lisa','Bart')`

元组创建后只可以通过索引调用不能修改

`append insert pop`这些方法都不能用
单元素元组要在后面加`，`多元素可加可不加
`t = (1,)`

#### 可变的tuple

```
t = ('a','b',['A','B'])
L = t(2)
L[0] = 'X'
L[1] = 'Y'
print t
('a','b',['X','Y'])
```

### if-else
这里的非不适用`！`而是用`not`
例如判断未成年人可以这样写：

```
if not age >= 18:
print 'teenager'
```
多种选择结构的时候`if elif else`

```
if age >= 18:
    print 'adult'
elif age >= 6:
    print 'teenager'
elif age >= 3:
    print 'kid'
else:
    print 'baby'
```

### for的用法

```
L = ['Adam', 'Lisa', 'Bart']
for name in L:
    print name
```

在循环过程中，可以用`break`退出当前循环，还可以用`continue`跳过后续循环代码，继续下一次循环

###python dict
`key-value`形式的

```
d = {
'Adam':95,
'Lisa':85,
'Bart':59
}
```
花括号{}表示这是一个dict，然后按照key:value写出来即可。

`len(d)`  这里等于3

访问要用`d[key]`的形式

key不存在时会报错：`KeyError`

要避免KeyError发生，有两个方法：

一是先判断一下key是否存在，用in操作符：

```
if 'Paul' in d:
print d['Paul']
```
如果'Paul'不存在，if语句判断为Falue，自然不会执行pring语句，从而避免了错误

二是使用dict本身提供的一个get方法，在Key不存在的时候，返回None

```
print d.get('Bart')
59
print d.get('Paul')
None
```

### dict的特点
dict的第一个特点是查找速度快，无论dict有10个元素还是10万个元素，查找速度都一样。而list的查找速度随着元素增加而逐渐下降。

不过dict的查找速度快不是没有代价的，dict的缺点是占用内存大，还会浪费很多内容，list正好相反，内存占用小，但是查找速度慢。

由于dict是按key查找，所以，在一个dict钟，key不能重复！！！

dict的第二个特点就是存储的key-value序对是没有顺序的，内部无序，每次打印顺序都可能不同

dict的第三个特点是作为key的元素必须不可变，Python的基本类型如字符串、整数、浮点数都是不可变的，都可以作为key。但是list是可变的，就不能作为key

### 更新dict
dict是可变的，也就是说，我们可以随时往dict中添加新的key-value。比如已有dict：

```
d = {
'Adam'：95,
'Lisa'：85,
'Bart'：59
}
```

要把新同学'Paul'的成绩72加进去，用赋值语句：

```
d['Paul'] = 72
```
这样里面就有四个元素了

如果key已经存在，则赋值会用新的value替换掉原来的value

### 遍历dict

```
for key in d:
print key + ':' + str(d[key])
```

### python set

dict的作用是建立一组key和一组value的映射关系，dict的key是不能重复的.

有的时候，我们只想要dict的key，不关心key对应的value吗，目的就是保证整个集合的元素不会重复，这时，set就派上用场了。

set持有一系列元素，这一点和list很想，但是set的元素没有重复，而且是无序的，这点和dict的key很像。

创建set的方式是调用set()并传入一个list，list的元素将作为set的元素：

```
s = set(['A','B','C'])
print s #set(['A','B','C'])
```

请注意，上述打印的形式类似list，但它不是list，set内部存储的元素是无序的.

set不能包含重复的元素，当传入包含重复元素的list时，set会自动去掉重复的元素.

#### 访问set
由于set存储的是无序集合，所以我们没法通过索引来访问

访问set中的某个元素实际上及时判断一个元素时候在set中

`'A' in s #True`

### set的特点

set的内部结构和dict很像，唯一区别是不存储value，因此，判断一个元素是否在set中速度很快

set存储的元素和dict的key类似，必须是不变对象，因此，任何可变对象是不能放入set中的。

最后，set存储的元素也是没有顺序的

所以set能应用在哪些地方呢

例如我们用set列出所有的星期 星期一到星期日 然后验证用户输入的是不是一个有效的日期等等.

### 遍历set

```
for name in s:
print name
```

### 更新set

由于set存储的是一组不重复的无序元素，因此，更新set主要做两件事：

一是把新的元素添加到set中，二是把已有元素从set中删除

### 添加 add()

如果添加的元素已经存在于set中，add()不会报错，但是不会加进去了

删除set中的元素时，用set的remove()方法

如果删除的元素不存在set中，remove()会报错

所以用add()可以直接添加，而remove()前需要判断


### python函数返回多值

```
import math
def move(x, y, step, angle):
    nx = x + step * math.cos(angle)
    ny = y - step * math.sin(angle)
    return nx, ny
```
这样我们就可以同时获得返回值：

```
x, y = move(100, 100, 60, math.pi / 6)
 print x, y
输出：151.961524227 70.0
```

但其实这只是一种假象，Python函数返回的仍然是单一值：

```
r = move(100, 100, 60, math.pi / 6)
print r #(151.96152422706632, 70.0)
```

用print打印返回结果，原来返回值是一个tuple！

但是，在语法上，返回一个tuple可以省略括号，而多个变量可以同时接收一个tuple，按位置赋给对应的值，所以，Python的函数返回多值其实就是返回一个tuple，但写起来更方便。

### python之递归函数
在函数内部，可以调用其他函数。如果一个函数在内部调用自身本身，这个函数就是递归函数

### Python之定义可变参数
如果想让一个函数能接受任意个参数，我们就可以定义一个可变参数：

```
def fn(*args):
    print args
```

可变参数的名字前面有个 * 号，我们可以传入0个、1个或多个参数给可变参数：

```
fn()
()
fn('a')
('a',)
fn('a', 'b')
('a', 'b')
fn('a', 'b', 'c')
('a', 'b', 'c')
```
可变参数也不是很神秘，Python解释器会把传入的一组参数组装成一个tuple传递给可变参数，因此，在函数内部，直接把变量 args 看成一个 tuple 就好了。

定义可变参数的目的也是为了简化调用。

### 对list进行切片
`L[0:3]`取前三个元素 如果第一个索引是0 可以省略

切片可以指定第三个参数

第三个参数表示每N个取一个

`L[::2]`表示每两个元素取出一个来

把`list`换成`tuple`，切片操作完全相同，只是切片的结果也变成了`tuple`

切片也支持倒序

`L[-4:-1:2]`

字符串 'xxx'和 Unicode字符串 u'xxx'也可以看成是一种list，每个元素就是一个字符。因此，字符串也可以用切片操作，只是操作结果仍是字符串：

```
'ABCDEFG'[:3]
'ABC'
'ABCDEFG'[-3:]
'EFG'
'ABCDEFG'[::2]
'ACEG'
```

### 什么是迭代
在Python中，如果给定一个list或tuple，我们可以通过for循环来遍历这个list或tuple，这种遍历我们成为迭代（Iteration）。

在Python中，迭代是通过 for ... in 来完成的

### 索引迭代
Python中，迭代永远是取出元素本身，而非元素的索引。

对于有序集合，元素确实是有索引的。有的时候，我们确实想在 for 循环中拿到索引，怎么办？

方法是使用 enumerate() 函数：

```
L = ['Adam', 'Lisa', 'Bart', 'Paul']
for index, name in enumerate(L):
...     print index, '-', name
... 
0 - Adam
1 - Lisa
2 - Bart
3 - Paul
```

### 迭代dict的value
我们已经了解了dict对象本身就是可迭代对象，用 for 循环直接迭代 dict，可以每次拿到dict的一个key。

如果我们希望迭代 dict 对象的value，应该怎么做？

dict 对象有一个 values() 方法，这个方法把dict转换成一个包含所有value的list，这样，我们迭代的就是 dict的每一个 value：

```
d = { 'Adam': 95, 'Lisa': 85, 'Bart': 59 }
print d.values() # [85, 95, 59]
for v in d.values():
    print v  #85 95 59
```

### 迭代dict的key和value
我们了解了如何迭代 dict 的key和value，那么，在一个 for 循环中，能否同时迭代 key和value？答案是肯定的。

首先，我们看看 dict 对象的 items() 方法返回的值：

```
d = { 'Adam': 95, 'Lisa': 85, 'Bart': 59 }
print d.items()
[('Lisa', 85), ('Adam', 95), ('Bart', 59)]
```

可以看到，items() 方法把dict对象转换成了包含tuple的list，我们对这个list进行迭代，可以同时获得key和value：

```
for key, value in d.items():
...     print key, ':', value
... 
Lisa : 85
Adam : 95
Bart : 59
```

和 values() 有一个 itervalues() 类似， items() 也有一个对应的 iteritems()，iteritems() 不把dict转换成list，而是在迭代过程中不断给出 tuple，所以， iteritems() 不占用额外的内存。

### 生成列表
要生成list [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]，我们可以用range(1, 11)：

```
range(1, 11)
[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```
但如果要生成[1x1, 2x2, 3x3, ..., 10x10]怎么做？方法一是循环：

```
L = []
for x in range(1, 11):
    L.append(x * x) 
L
[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
```

但是循环太繁琐，而列表生成式则可以用一行语句代替循环生成上面的list：

```
[x * x for x in range(1, 11)]
[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
```

这种写法就是Python特有的列表生成式。利用列表生成式，可以以非常简洁的代码生成 list。

写列表生成式时，把要生成的元素 x * x 放到前面，后面跟 for 循环，就可以把list创建出来，十分有用

### 条件过滤
列表生成式的 for 循环后面还可以加上 if 判断。例如：

```
[x * x for x in range(1, 11)]
[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
```

如果我们只想要偶数的平方，不改动 range()的情况下，可以加上 if 来筛选：

```
[x * x for x in range(1, 11) if x % 2 == 0]
[4, 16, 36, 64, 100]
```

有了 if 条件，只有 if 判断为 True 的时候，才把循环的当前元素添加到列表中。

### 多层表达式
for循环可以嵌套，因此，在列表生成式中，也可以用多层 for 循环来生成列表。

对于字符串 'ABC' 和 '123'，可以使用两层循环，生成全排列：

```
[m + n for m in 'ABC' for n in '123']
['A1', 'A2', 'A3', 'B1', 'B2', 'B3', 'C1', 'C2', 'C3']
```

翻译成循环代码就像下面这样：

```
L = []
for m in 'ABC':
    for n in '123':
        L.append(m + n)
```


















