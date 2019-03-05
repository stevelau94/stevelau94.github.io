---
title: 探索Python函数 (PART1)
date: 2018-05-27 15:39:28
categories: 
    - "Python"
    - "explore Python"
tags: 
    - Python
---
本文主要介绍了Python一些需要注意的基础细节问题，包括作用域(global/nonlocal)和各种参数传递方法

<!--more-->

## Basic
关于函数基础部分，虽然像def，return这些语句很简单，但是还是有一些需要留意的地方。

### Python函数是实时执行的  
def语句实际上是一个可执行语句(当然，py里面的所有语句都是实时运行的)，在运行的时候会创建一个新的函数对象并将其赋值给一个变量。

明确一些：def在运行时候才会进行评估，而def中的代码实在函数调用后才会进行评估

因此我们可以不用像C那样提前定义好所有的一切

```Python
def func():
    pass

# 赋值给变量名
call_func = func
# 函数调用
call_func()
```

### Python函数是对象
def就像Python中的其他语句一样，函数也是对象

所以函数允许任意的属性附加到已经定义好的函数中(这点有点儿神奇，而且其实可能会不符合一些设计规范，但是有时候还挺有用的)
```Python
def func():
    pass

func()
func.attr = 1
```

### 多态
Python是动态类型的编程语言，所以多态在py中随处可见，这给py带来了极大的灵活性

那么应该如何理解多态呢

简单来说就是py不关心特定的数据类型(和C++，java他们大不相同)

```Python
def mul_func(x, y):
    return x * y

>>> mul_func(1, 2)
2
>>> mul_func(1, 2.34)
2.34
>>> mul_func('123', 2)
'123123'
```

多态的实现是个很有意思的话题，Python在底层如何实现这种神奇的操作的(提示：py中一切都是对象噢！)，我以后也会再探索一下

## 作用域

Python创建，改变和查找变量名都是在命名空间中进行的。也就是说，在说到变量对应代码中的值的时候提到的作用域就是这个命名空间。

所有的变量名，包括作用域在内的概念都是在Python赋值的时候生成的

在代码中赋值的位置决定了这个变量名的作用域

所以简答来讲：
1. def内定义的变量能够都def内的代码使用，通常不能被外部引用
2. def内和def外的变量名不会冲突

### 作用域法则

在Python中，有一套变量名的解析原则：LEGB
简单来说就是从变量名最近的def或者lambda作用域中查找这个变量名的关联值(local function)，
如果找不到就去上层(嵌套def或者lambda)继续查找(Enclosing function locals),
如果本地函数和外层嵌套函数中都不存在的话就继续向外部查找(Global module),
如果全局作用域中还是没有，那么Python会在内置作用域中查找(Built-in),
最后如果还是找不到的话会报错

##### 需要注意的是：
变量名是可以被覆盖的  
内层变量名会覆盖掉外层的变量(这点倒没什么)  
**全局变量名不要覆盖掉内置变量名**  

```Python
>>> print(1, 2)
1 2
>>> def print(a, b):
...     return a + b
...
>>> print(1, 2)
3
```

### global语句

上文已经提及，作用域相当于限制了变量名的被识别范围

那么如果我们有在def中改变全局变量的需求呢

这就需要global了

```Python
>>> a = 99
# local变量不影响全局变量
>>> def func():
...     a = 199999
...
>>> func()
>>> a
99

# 使用global定义为全局变量
>>> def func_a():
...     global a
...     a = 100000
...
# def内部语句在被调用后生效
>>> func_a()
# a被函数内部的修改改变
>>> a
100000
```
##### 需要注意的是：
**全局变量在很多情况下是有问题的**

要在真的有需要的情况下才设置全局变量(通常情况下没这个需要 --)

还有一种类似访问局变量的方法，通过模块访问
```Python
# 1st.py
var = 999

# 2ed.py
def func():
    import 1st
    # 访问1st.py中的var
    1st.var

def func_a():
    import sys
    mod = sys.modules['1st']
    # 访问1st.py中的var
    mod.var
```

### 工厂函数

其实嵌套作用域还蛮好理解的

那么来看看工厂函数吧

```Python

>>> def maker(x):
...     def action(y):
...         return x ** y
...     return action
...
>>> worker = maker(2)
>>> worker(3)
8
```

内嵌函数记住了maker取得的参数值，虽然maker已经被调用结束了

或许上个例子也还好  

那么再看看有lambda的情况
```Python
>>> def func():
...     x = 4
...     action = (lambda n: x ** n)
...     return action
...
>>> x = func()
>>> x(2)
16
>>> x(4)
256
```


### nonlocal语句

nonlocal是Python3中新添加的语句

nonlocal提供了嵌套def对嵌套函数中的名称进行读取和写入的功能

**nonlocal应用于嵌套函数中，而非def之外的全局模块作用域**

**在声明nonlocal的时候，必须已经存在于该嵌套函数的作用域中**

nonlocal对其后的变量名查找直接从嵌套的def作用域开始，而非声明函数的本地作用域


在执行nonlocal的时候，其后的变量名必须在嵌套函数中已经定义过，否则会报错

**nonlocal主要作用在于，对允许嵌套的作用域中的名称进行修改而非只是引用**

```Python
# 这是通常的情况
>>> def tester(start):
...     state = start
...     def nested(label):
...         print(label, state)
...     return nested
...
>>> t = tester(0)
>>> t('first')
first 0
>>> t('second')
second 0

# 如果想直接修改嵌套函数变量会报错
>>> def tester(start):
...     state = start
...     def nested(label):
...         print(label, state)
...         state += 1
...     return nested
...
>>> t = tester(0)
>>> t('first')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "<stdin>", line 4, in nested
UnboundLocalError: local variable 'state' referenced before assignment

# 使用nonlocal修改嵌套函数变量
>>> def tester(start):
...     state = start
...     def nested(label):
...         nonlocal state
...         print(label, state)
...         state += 1
...     return nested
...
>>> t = tester(0)
>>> t('first')
first 0
>>> t('second')
second 1
```


## 参数

### 参数传递

正如变量赋值时有的两种情况
1. 值传递，不可变参数
2. 引用传递，可变参数

这点通过例子来阐述比较清楚

```Python

>>> def func(a):
...     a = 999
...
>>> b = 888
>>> func(b)
>>> b
888

>>> def changer(a, b):
...     a = 2
...     b[0] = 'bbb'
...
>>> x = 1
>>> l = [1, 2, 3]
>>> changer(x, l)
>>> x
1
>>> l
['bbb', 2, 3]
```

可变参数的传递可能会带来问题，所以尽量避免

可以这样传递可变参数(copy)

```Python

l = [1, 2]
changer(x, l[:])
```

### 参数匹配

#### 简单的： 

常规的参数通过位置来匹配

关键字参数通过变量名匹配

#### *和**的任意参数匹配：

*args,接受元祖并匹配
**kargs,接受字典进行关键字匹配

```Python
# 任意参数匹配
>>> def func(*args, **kargs):
...     print(args)
...     print(kargs)
...
>>> func(1, 2, 3, a=2, b=3)
(1, 2, 3)
{'a': 2, 'b': 3}
# 任意参数和位置参数共用
>>> def func(a, *args, **kargs):
...     print(a, args, kargs)
...
>>> func(1, 2, 3, 5, 6, v=9)
1 (2, 3, 5, 6) {'v': 9}
```
##### 需要注意的是：

***args接受的不仅仅是元祖，而是可迭代对象**
```Python
# 也就是说可以这样操作
func(*open('filename'))
```

#### keyword-Only参数

上面可以知道一般定义参数接受的时候，要把*/**接受的语句放在函数定义的后半部分，因为他们会接受任意数量的参数，可能会影响前面普通参数的赋值

keyword-Only却是放在*后面的 

要求，函数必须接受keyword-Only参数才行

```Python
>>> def kwonly(a, *b, c):
...     print(a, b, c)
...
>>> kwonly(1, 2, 3, c=4)
1 (2, 3) 4
>>> kwonly(1, 2, 3, 4)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: kwonly() missing 1 required keyword-only argument: 'c'
```

##### 需要注意的是：

**keyword-Only放在*后面，但是必须放在**前面**

这些情况会导致语法错误
```Python
>>> def kwonly(a, **args, b, c): pass
  File "<stdin>", line 1
    def kwonly(a, **args, b, c): pass
                        ^
SyntaxError: invalid syntax
>>> def kwonly(a, **, b, c): pass
  File "<stdin>", line 1
    def kwonly(a, **, b, c): pass
                    ^
SyntaxError: invalid syntax
>>> def kwonly(a, *b, **c, c=9): pass
  File "<stdin>", line 1
    def kwonly(a, *b, **c, c=9): pass
                         ^
SyntaxError: invalid syntax
```

看一个各种参数种类混在一起的例子吧  

会发现keyword-Only可以在**参数中被赋值
```Python
>>> def func(a, *b, c=999, **d):
...     print(a, b, c, d)
...
>>> func(1, 2, 3, 4, c=100000, w=21, o=90)
1 (2, 3, 4) 100000 {'w': 21, 'o': 90}
>>> func(1, *(2, 3), c=4, w=21, o=90)
1 (2, 3) 4 {'w': 21, 'o': 90}
# keyword-Only可以在**参数中被赋值
>>> func(1, *(2, 3), **dict(c=100000, w=21, o=90))
1 (2, 3) 100000 {'w': 21, 'o': 90}
```

## 浅析高级话题

### 函数设计观念
这些建议来自*learning Python*
1. 耦合性，函数独立于外部
2. 全局变量，真正需要的时候才用
3. 通常不要改变可变参数
4. 每个函数有一个单一的目标
5. 每个函数尽量较小
6. 避免直接改变另外函数/模块中的变量

### 函数注解

Python3中新增加的功能

可以__annotations__查看

直接看例子

```Python

def func(a:'sss', b:(1,2), c:float) -> int:
    return a + b +c

>>> func(1, 2, 3)
6

>>> func.__annotations__
{'a': 'sss', 'b': (1, 2), 'return': <class 'int'>, 'c': <class 'float'>}
```


### lambda
1. lambda是一个表达式而非语句
2. lambda是一个单个表达式而非代码块


### map

Python3中map返回可迭代对象

我感觉map能做的列表解析式一般也都能做

但是某些情况map函数比列表解析式运行的快

```Python
>>> numbers = [1, 2, 3, 4]
# 普通for循环
>>> l = []
>>> for i in numbers: l.append(x + 100)
...
>>> l
[101, 101, 101, 101]

# map+函数
>>> def append_number(x): return x + 10
...
>>> map(append_number, numbers)
<map object at 0x7f4bb7316ba8>
>>> list(map(append_number, numbers))
[11, 12, 13, 14]

# map+lambda
>>> list(map(lambda x : x+10, numbers))
[11, 12, 13, 14]


# 列表解析式
>>> [i + 10 for i in numbers]
[11, 12, 13, 14]

```

当然 map可以接受N个参数和N个序列
```Python
>>> list(map(pow, [1,2,3], [3, 2, 1]))
[1, 4, 3]
```

### filter
Python3中filter返回可迭代对象
```Python
>>> list(filter(lambda x : x>=5, [4, 5 ,6, 1]))
[5, 6]
```


### reduce
Python3中reduce被放在了functools中

Python3中reduce返回迭代器

```Python
>>> reduce((lambda x,y :x+y), [1,2,3,4,5])
15
```