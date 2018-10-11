---
title: python可迭代对象，迭代器和生成器的剖析
date: 2018-07-27 22:06:56
categories: 
    - "python"
tags: 
    - python
---
感觉在python中，"迭代"是个很重要的关键词，而且python3将所有内置函数的返回都改成了迭代器。所以python的可迭代对象，迭代器和生成器这些知识，是时候进行一番详细的梳理了
<!--more-->

## 关于"重复"的基本概念

一般程序语言中都有这样一些关于"重复"的概念，如loop、iterate、traversal 和 recursion，他们的通常的定义是:

- loop(循环): 在一定条件下，重复执行同一段代码，如python中的while;
- iterate(迭代): 按照某种顺序逐个访问容器中的每一项，如python中的for;
- traversal(递归): 不断调用自身，如斐波那契数列;
- recursion(遍历): 按照一定的规则访问结构中的每个节点，而且每个节点都只访问一次

通过这些定义，我们会发现python中的for语句并不能够像别的编程语言那样实现for循环，python的循环都是while来实现的；python的for用来实现迭代。

## 关于"迭代"的基本概念

那么在python中，关于"迭代"的概念，又有这些: iterable,iterator和generators

- iterable(可迭代对象): 定义了可以返回一个迭代器的__iter__方法，或者定义了可以支持下标索引的__getitem__方法
- iterator(迭代器): 定义了__next__方法
- generators(生成器): 含义yield的一种特殊的迭代器

## 可迭代对象与迭代器的关系

那么他们之间的关系是这样的:

**iter()这个函数，可以使可迭代对象转换为迭代器**

![Iterable_vs_iterator](python可迭代对象，迭代器和生成器的剖析/Iterable_vs_iterator.jpg)

## 详解可迭代对象和迭代器

下面稍微详细介绍一下他们

#### 可迭代对象（Iterable）
具有__iter__ 方法，用于返回一个迭代器，或者定义了 __getitem__ 方法，可以按 index 索引的对象（并且能够在没有值时抛出一个 IndexError 异常）

可迭代对象可以通过for来迭代其中的每个元素；

可迭代对象可以通过index索引里面的每个元素(__getitem__)；

可迭代对象可以通过iter()返回迭代器(__iter__)；

可以通过isinstance(obj, collections.Iterable) 来判断对象是否为可迭代对象
```python
>>> import collections
>>> a = 'string is iterable'
>>> isinstance(a, collections.Iterable)
```

#### 迭代器（Iterator）
含有 next (Python 2) 或者 __next__ (Python 3) 方法

定义了__next__方法返回下一个值，在结尾处抛出StopIteration

可以通过 isinstance(obj, collections.Iterator) 来判断对象是否为迭代器
```python
>>> import collections
>>> a = 'string is iterable but not a iterator'
>>> isinstance(a, collections.Iterator)
False
>>> iter_a = iter(a)
>>> isinstance(iter_a, collections.Iterator)
True
```

#### for语句原理

for语句在python中适用于迭代的，而while才是真正的循环  

深究其差别在于，循环是可以增加跳过的条件的，但是迭代只能一个接着一个取值

在for语句的内部，是调用可迭代对象的iter()方法将可迭代对象转化为迭代器，然后在调用迭代器中的next()方法进行的迭代。for语句会自动捕获迭代器结束时的StopIteration异常并终止迭代。


## 详解生成器

#### 生成器(generator)
一个特殊的迭代器

任何包含 yield 语句的函数被称为生成器


#### 惰性计算
生成器表达式有一个特点，就是 **惰性计算**

这里有一段代码，对理解生成器的惰性计算非常重要
```python
def add(s, x):
    return s + x

def gen():
    for  i in range(4):
        yield i

base = gen()
for n in [1, 10]:
    base = (add(i, n) for i in base)

print(list(base))

### 代码返回的是
### [20, 21, 22, 23]
```

有个python代码执行的[可视化网站](http://pythontutor.com)可以帮助对这段代码进行理解
