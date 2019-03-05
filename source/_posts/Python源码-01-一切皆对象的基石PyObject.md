---
title: Python源码 01 一切皆对象的基石PyObject
date: 2018-04-29 02:01:17
categories: 
    - "Python"
    - "Python source code"
tags: 
    - Python
    - Python源码
---
最近工作涉及到C、C++等底层语言，一方面回顾了相关语法应对工作需求，一方面也满足了自己一直想读读Python源码的渴望

终于有机会了

这篇主要介绍Python源码中最底层的"对象"-PyObject

+++++++++++++++话说这部分未完待续
1. c代码未添加
2. 关于PyType_Type部分缺少详述

<!--more-->

对象是Python中最核心的概念，在Python中一切都是对象

这句话几乎在所有Python的书里面都存在

不过，为什么说Python中一切皆是对象呢？

## PyObject

在object.h头文件中

定义了PyObject

其核心是PyObject_HEAD
```c
def_PyObject

```

看看PyObject_HEAD有什么
```c
PyObject_HEAD

```

在release模式下，PyObject定义的更为简洁
```c
release_def_PyObject

```
![release_def_PyObject](SC_01/release_def_PyObject.png)

就有最核心的两项

1. ob_refcnt，计数信息
2. _typeobject *ob_type，类型信息

### ob_refcnt
ob_refcnt与Python的内存管理机制相关

### _typeobject *ob_type
*ob_type是一个指向_typeobject结构体的指针

结构体_typeobject则是Python内部的一种特殊对象

用来指定一个对象类型的类型对象


PyObject定义了Python中每个对象都一定含有的内容，即计数信息和类型信息

这两个信息会出现在每个Python对象所占内存的最开始的字节中


## PyVarObject

PyVarObject是Python中为不定长对象提供的"基类”

看源码
```c
def_PyVarObject

```
![def_PyVarObject](SC_01/def_PyVarObject.png)

和PyObject的结构很相似，里面有个核心的东西叫PyVarObject_HEAD

再看PyVarObject_HEAD
```c
PyVarObject_HEAD

```
![PyVarObject_HEAD](SC_01/PyVarObject_HEAD.png)

发现了猫腻

原来 PyVarObject_HEAD 就是 PyObject_HEAD 的拓展而已

多了一项 ob_size

这就很好理解了

由于变长对象的特性，为了保证定义对象的内存空间不用经常"搬迁"

所以定义了ob_size来指明对象容纳元素的个数(这里不是字节的数量哦！是元素的个数！)

那么也就可以得出结论，其实 PyVarObject 也就是 PyObject 的一个拓展

## 定长对象和变长对象

上面介绍了 PyObject 和 PyVarObject

Python中最基本的几个类型int,list,string和dict都是什么对象呢

其实很容易想到

int一定是定长对象，它的内存开始位置有ob_refcnt和ob_type信息

string和list一定是变长对象，它的内存开始位置除了有ob_refcnt和ob_type信息之外，还会有ob_size信息

dict呢？(等我读到了再来解答。。。哈哈哈)

## ob_refcnt

引用计数，是Python中管理和维护对象在内存中存在与否的变量

ob_refcnt 主要通过 Py_INCREF(op) 和 Py_DECREF(op) 两个宏来实现增加和减少对象的引用计数的

当引用计数减小到0的时候，Py_DECREF会调用析构函数释放资源
实际就是调用 tp_dealloc

每次创建一个新的对象的时候，都会使用 _Py_NewReference(op)宏将对象的引用计数初始化为1


## _typeobject和PyTypeObject

```c
_typeobject

```

<!-- PyTypeObject -->

我们可以看到，很多关于对象的类型信息以及内存开辟的信息(这些常被称为元信息)都在_typeobject中

### 对象分配内存空间
创建类型对象时分配内存空间大小的信息，是 tp_basicsize 和 tp_itemsize 决定的


### 对象创建
```Python
>>> a = int(10)
>>> a
10
>>> type(a)
<class 'int'>
>>> int.__base__
<class 'object'>
```

创建int对象的时候，调用的是Python内部的PyInt_Type

而int继承自object,即 PyBaseObject_Type

那么就可以大概简述一下a被创建的过程:
1. PyInt_Type 中调用 tp_new
2. (若这层找不到)进而 tp_base 会在基类中寻找 tp_new
3. 因为所有类的基类最终都是PyBaseObject_Type， 所以最终一定会找到 tp_new
4. PyBaseObject_Type 中的 tp_new 指向的是 object_new
5. object_new 会访问 PyInt_Type 中记录的 tp_basicsize 信息，完成内存申请
6. tp_new 完成之后，会在 PyInt_Type 中调用 tp_init 完成创建

### 对象行为

PyTypeObject 中定义了很多函数指针

这些函数指针就是类型对象中所定义的操作，决定这个对象表现的行为

如我们之前所看到的 tp_new 和 tp_init 可以完成对象被创建并初始化

这些操作信息中有三个非常重要的操作族:

1. tp_as_number -> PyNumberMethods
2. tp_as_sequence -> PySequenceMethods
3. tp_as_mapping -> PyMappingMethods

这三个操作族其实可以从字面意思理解

就是为对象提供支持的操作类型

如 tp_as_number.nb_add 就是为对象提供了add功能

不过需要注意的是，PyTypeObject是允许一种类型制定两/三种行为的

举个有趣的例子
```Python
>>> class MyFloat(float):
...   def __getitem__(self, key):
...     return key+str(self)
... 
>>> a = MyFloat(100)
>>> b = MyFloat(88)
>>> a
100.0
>>> b
88.0
>>> a+b
188.0
>>> a['key']
'key100.0'
```

可以看到 PyTypeObject 是允许一种类型同时拥有 PyNumberMethods 和 PyMappingMethods 的行为

### 类型的类型

这部分很容易让人产生困惑

PyTypeObject 开始的部分，囊括了 PyVarObject_HEAD

也就是说明，PyTypeObject 本身也是一个对象

<!-- 未完待续 -->
