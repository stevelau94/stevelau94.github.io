---
title: python 迭代器和生成器
date: 2018-07-17 00:01:08
categories: 
    - "python"
    - "python cookbook"
tags: 
    - python
    - itertools
---
python cookbook中关于迭代器和生成器部分的笔记，比较偏向与应用层面而非对概念的理解
<!--more-->
## 0.可迭代对象，迭代器，生成器

**注意区分 可迭代对象 和 迭代器**

可迭代对象,Iterable, __iter__,__getitem__  
迭代器,Iterator,__next__  
迭代,Iteration  
生成器,Generators  



## 1.手动访问迭代器元素
**可迭代对象需要转换成迭代器之后，才可以执行next()操作**
```python
>>> items = [1, 2, 3]
>>> it = iter(items)
>>> it
<list_iterator object at 0x7f256ddc8d68>
>>> next(it)
1
>>> next(it)
2
>>> next(it)
3
>>> next(it)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
```

## 2.创建迭代器，内部有可迭代对象，执行迭代

**使自定义的容器实现迭代，就是实现__iter__**
```python
>>> class Node:
...     def __init__(self, value):
...         self._value = value
...         self._children = []
...     def __repr__(self):
...         return 'Node ({!r})'.format(self._value)
...     def add_child(self, node):
...         self._children.append(node)
...     def __iter__(self):
...         return iter(self._children)
...
>>> root = Node(0)
>>> child1 = Node(1)
>>> child2 = Node(2)
>>> root.add_child(child1)
>>> root.add_child(child2)
>>> for ch in root: print(ch)
...
Node (1)
Node (2)
```

iter(s)实际上是调用s.__iter()来返回底层迭代器的　　

## 3.生成器，新的迭代模式
**有yield就会变成生成器**

```python
>>> def countdown(n):
...     print('Starting to count from', n)
...     while n > 0:
...         yield n
...         n -= 1
...     print('Done!')
...
>>> c = countdown(3)
>>> c
<generator object countdown at 0x7f256b46caf0>
>>> next(c)
Starting to count from 3
3
>>> next(c)
2
>>> next(c)
1
>>> next(c)
Done!
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
```
结束的时候会返回StopIteration


## 4.自定义可迭代对象(实现简单的迭代协议)

**推荐使用生成器来实现迭代器**

因为，如果直接自定义迭代器，需要维护内部的__next__状态，非常繁琐

```python
>>> class Node:
...     def __init__(self, value):
...         self._value = value
...         self._children = []
...     def __repr__(self):
...         return 'Node ({!r})'.format(self._value)
...     def add_child(self, node):
...         self._children.append(node)
...     def __iter__(self):
...         return iter(self._children)
...     def depth_first(self):
...         yield self
...         for c in self:
...             yield from c.depth_first()
...
>>> root = Node(0)
>>> child1 = Node(1)
>>> child2 = Node(2)
>>> root.add_child(child1)
>>> root.add_child(child2)
>>> child1.add_child(Node(3))
>>> child1.add_child(Node(4))
>>> child1.add_child(Node(5))
>>> for ch in root.depth_first():print(ch)
...
Node (0)
Node (1)
Node (3)
Node (4)
Node (5)
Node (2)

```

## 5.反向迭代
反向迭代只有在待处理对象拥有可确定的大小，或者对象实现了__reversed()__方法才能奏效。  
否则需要将对象转换为list

### reversed()反向迭代
```python
>>> a = [1, 2, 3, 4,5]
>>> a
[1, 2, 3, 4, 5]
>>> for x in reversed(a): print(x)
...
5
4
3
2
1
```
### 自定义类使用__reversed__()实现反向迭代
```python
>>> class Countdown:
...     def __init__(self, start):
...         self.start = start
...     def __iter__(self):
...         n = self.start
...         while n > 0:
...             yield n
...             n -= 1
...     def __reversed__(self):
...         n = 1
...         while n <= self.start:
...             yield n
...             n += 1
...
>>> c = Countdown(3)
>>> c
<__main__.Countdown object at 0x7f256b48cf28>
>>> for x in c: print(x)
...
3
2
1
>>> for x in reversed(c): print(x)
...
1
2
3
```

## 6.生成器函数，有额外状态

```python
>>> class linehistory:
...     def __init__(self, lines, histlen=3):
...         self.lines = lines
...         self.history = deque(maxlen=histlen)
...     def __iter__(self):
...         for lineno, line in enumerate(self.lines, 1):
...             self.history.append((lineno, line))
...             yield line
...     def clear(self):
...         self.history.clear()
```

## 7.迭代器，切片操作

### itertools.islice()
islice()的结果是返回一个迭代器　　

islice()会消耗原迭代器中的数据　　

如果之后需要重新使用之前的数据，需要提前将数据转入list中

```python
>>> def count(n):
...     while True:
...         yield n
...         n += 1
...
>>> c = count(0)
>>> c[10:20]
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'generator' object is not subscriptable
>>> import itertools
>>> for x in itertools.islice(c, 10, 20):print(x)
...
10
11
12
13
14
15
16
17
18
19
```

## 8.跳过可迭代对象的前一部分

### itertools.dropwhile()
满足条件的都会丢弃知道有元素不满足为止
### itertools.islice()
在知道需要剔除的数量的时候，可以使用这个函数
```python
>>> from itertools import islice

>>> it = ['a', 'b', 'c', 'd', 1, 4, 65, 2]
>>> for x in islice(it, 4, None):print(x)
...
1
4
65
2

```
## 9.迭代所有可能的组合和排列

### itertools.permutations()
接受一个元素序列，将其中所有的元素重新排列为所有可能的情况
```python
>>> from itertools import permutations
>>> items = ['a', 'b', 'c']
>>> for p in permutations(items):print(p)
...
('a', 'b', 'c')
('a', 'c', 'b')
('b', 'a', 'c')
('b', 'c', 'a')
('c', 'a', 'b')
('c', 'b', 'a')
>>> for p in permutations(items, 2):print(p)
...
('a', 'b')
('a', 'c')
('b', 'a')
('b', 'c')
('c', 'a')
('c', 'b')
```
### itertools.combinations()
可产生输入序列中的所有元素的全部组合（与顺序无关）
```python
>>> from itertools import combinations
>>> for p in combinations(items, 3):print(p)
...
('a', 'b', 'c')
>>> for p in combinations(items, 2):print(p)
...
('a', 'b')
('a', 'c')
('b', 'c')
```
### itertools.combinations_with_replacement()
可产生输入序列中的所有元素的全部组合（相同元素可以得到多次选择）
```python
>>> from itertools import combinations_with_replacement
>>> for p in combinations_with_replacement(items, 2):print(p)
...
('a', 'a')
('a', 'b')
('a', 'c')
('b', 'b')
('b', 'c')
('c', 'c')
```

## 10.迭代序列，并记录索引

### enumerate()
```python
>>> for index,value in enumerate(items):print(index,value)
...
0 a
1 b
2 c
>>> for index,value in enumerate(items, 100):print(index,value)
...
100 a
101 b
102 c
```

enumerate()返回是一个enumerate对象实例，是一个迭代器，可返回连续的元祖。

元祖由索引值和传入调用next()得到的值组成

### 处理嵌套序列
```python
>>> data = [[1, 2], [3, 4]]
>>> for i, (x,y) in enumerate(data):print(i,(x,y))
...
0 (1, 2)
1 (3, 4)
```

## 11.同时迭代多个序列

### zip() 按照短的序列迭代
```python
>>> a
[1, 4, 7, 5, 3, 5357, 13]
>>> b
[234, 6, 7, 8, 235, 87, 100]
>>> len(a),len(b)
(7, 7)
>>> c
[1, 43, 67, 8, 34, 4, 67, 100]
>>> len(c)
8

>>> for x,y in zip(a,b):print(x,y)
...
1 234
4 6
7 7
5 8
3 235
5357 87
13 100

>>> for x,y in zip(a,c):print(x,y)
...
1 1
4 43
7 67
5 8
3 34
5357 4
13 67
```

可以接受多于两个序列
```python
>>> for x,y,z in zip(a,c,b):print(x,y,z)
...
1 1 234
4 43 6
7 67 7
5 8 8
3 34 235
5357 4 87
13 67 100
```
### itertools.zip_longest() 按照长的迭代
```python
>>> for x,y in zip_longest(a,c):print(x,y)
...
1 1
4 43
7 67
5 8
3 34
5357 4
13 67
None 100

# 设置fillvalue
>>> for x,y in zip_longest(a,c, fillvalue=99999):print(x,y)
...
1 1
4 43
7 67
5 8
3 34
5357 4
13 67
99999 100
```
## 12.迭代不同的容器
### itertools.chain()
```python
>>> from itertools import chain
>>> a = [1, 2, 3]
>>> b = ['x', 'f', 's']
>>> for x in chain(a, b): print(x)
...
1
2
3
x
f
s
```
### chain常见的用途是一次性对所有元素执行某项特定的操作
chain()可接受多个可迭代对象作为参数，然后会创建一个迭代器，该迭代器可连续访问并返回每个可迭代对象的元素

使用chain比将各序列合并之后再迭代高效
## 13.创建处理数据的管道
## 14.嵌套序列的扁平化处理

### yield from


## 15.合并多个有序序列，并迭代整个有序序列

### heapq.merge()
```python
>>> a = [1,2,3]
>>> b=[2,5,8]
>>> for c in heapq.merge(a, b):print(c)
...
1
2
2
3
5
8
```
heapq.merge开销很小，但是要求输入的数据必须有序

## 16.迭代器取代while循环