---
title: python 数据结构与算法（1）
date: 2018-07-10 23:13:45
categories: 
    - "python"
    - "python cookbook"
tags: 
    - python
    - data structure
    - python数据结构
---

## 1.对象分解

### 将含N个元素的元祖或序列分解成单独变量
```python
data = ['steve', 18, 100, (2017.7.7)]
name, age, weight, date = data
```
### 一切可迭代的对象(字符串，文件，迭代器，生成器)都可以这样拆分
```python
s = 'hello'
# 可以使用 _ 方式丢弃指定值
a, b, c, e, _ = s
```

## 2.任意长度对象分解

### 分解对象长度太长
**'*'表达式可以解决**
```python
# 适用于python3
record = ('steve', 'steve@gmail.com', '1234566-88', '9876543=99')
name, email, *phone = record
```
### 迭代变长序列
```python
records = [
    ('foo', 1, 2),
    ('bar', 'hello'),
    ('foo', 3, 4)
]

def do_foo(x, y):
    print('foo', x, y)
def do_bar(s):
    print('bar', s)
for tag, *args in records:
    if tag == 'foo':
        do_foo(*args)
    elif tag == 'bar':
        do_bar(*args)
```

## 3.保存最后N个元素

### 保存有限的历史记录是collections.deuqe的完美使用场景
```python
# 有匹配时就返回当前匹配行以及最后检查过得N行文本
from collection import deque

def search(lines, pattern, histort=5):
    previous_lines = deque(maxlen=history)
    for line in lines:
        if pattern in line:
            yield line, previous_lines
        previous_lines.append(line)
```
### deque
```python
>>> q = deque(maxlen=2)
>>> q
deque([], maxlen=2)
>>> q.append(1)
>>> q.append(2)
>>> q
deque([1, 2], maxlen=2)
>>> q.append(4)
>>> q
deque([2, 4], maxlen=2)
```    
**deque 比 list 速度快的多**
**deque可以从左右两边添加和弹出，两边的时间复杂度都是O(1)**
**list从头部插入或者移出的时间复杂度是O(N)**    
```python
# deque 可以不指定长度
>>> q = deque()
>>> q.append(999)
>>> q.append(888)
>>> q
deque([999, 888])
>>> q.appendleft(1)
>>> q
deque([1, 999, 888])
>>> q.pop()
888
>>> q.popleft()
1
>>> q
deque([999])
```

## 4.找到最大/小的N个元素

### heapq模块中的 nlargest() & nsmallest() 
```python
>>> import heapq

>>> nums = [1, 8, 5, 44, 2323, 4, 0, -1, 54]
>>> print(heapq.nlargest(3, nums))
[2323, 54, 44]
>>> print(heapq.nsmallest(3, nums))
[-1, 0, 1]
```
### 两个函数可以通过key加lambda函数实现复杂功能
```python
>>> protofilo = [
... {'name':'IBM', 'shares':100, 'prices':99},
... {'name':'APP', 'shares':1000, 'prices':999},
... {'name':'FB', 'shares':88, 'prices':88},
... {'name':'BABA', 'shares':111, 'prices':100},
... {'name':'DIDI', 'shares':99, 'prices':99.5}]
>>> cheap = heapq.nsmallest(3, protofilo, key=lambda s: s['prices'])
>>> cheap
[{'prices': 88, 'shares': 88, 'name': 'FB'}, {'prices': 99, 'shares': 100, 'name': 'IBM'}, {'prices': 99.5, 'shares': 99, 'name': 'DIDI'}]
>>> exp = heapq.nlargest(3, protofilo, key=lambda s: s['prices'])
>>> exp
[{'prices': 999, 'shares': 1000, 'name': 'APP'}, {'prices': 100, 'shares': 111, 'name': 'BABA'}, {'prices': 99.5, 'shares': 99, 'name': 'DIDI'}]
```

## 5.优先级队列

> 优先级队列：按照优先级对元素排序，每次pop都返回优先级最高的元素
```python
>>> import heapq

>>> class PriorityQueue:
...     def __init__(self):
...         self._queue = []
...         self._index = 0
...     def push(self, item, priority):
            # -priority为了按优先级从高到低排序
            # (pri, index, item)形式是为了避免在pri相同的情况下出错
...         heapq.heappush(self._queue, (-priority, self._index, item))
...         self._index += 1
...     def pop(self):
...         return heapq.heappop(self._queue)[-1]
... 
>>> class Item:
...     def __init__(self, name):
...         self.name = name
...     def __repr__(self):
...         return 'Item({!r})'.format(self.name)
... 
>>> q = PriorityQueue()
>>> q.push(Item('foo'), 1)
>>> q.push(Item('bar'), 5)
>>> q.push(Item('spam'), 4)
>>> q.push(Item('grok'), 1)
>>> q.pop()
Item('bar')
>>> q.pop()
Item('spam')
>>> q.pop()
Item('foo')
>>> q.pop()
Item('grok')
```

## 6.字典，键映射到多个值

### 原生dict实现多值字典
```python
# 将多个值保存在list或者set中

# list保持有序
>>> d = {
...     'a':[1, 3, 2],
...     'b':[4, 5]
... }
# set保持去重
>>> e = {
...     'a':{1, 2, 3, 3},
...     'b':{4, 5}
... }
>>> d
{'b': [4, 5], 'a': [1, 3, 2]}
>>> e
{'b': {4, 5}, 'a': {1, 2, 3}}
```

### collections.defaultdict
```python
>>> from collections import defaultdict

>>> d = defaultdict(list)
>>> d
defaultdict(<class 'list'>, {})
>>> d['a'].append(1)
>>> d['a'].append(3)
>>> d['a'].append(2)
>>> d['b'].append(4)
>>> d['b'].append(5)
>>> d
defaultdict(<class 'list'>, {'b': [4, 5], 'a': [1, 3, 2]})
>>> e = defaultdict(set)
>>> e
defaultdict(<class 'set'>, {})
>>> e['a'].add(1)
>>> e['a'].add(2)
>>> e['a'].add(2)
>>> e
defaultdict(<class 'set'>, {'a': {1, 2}})
```

defaultdict优势在于，做初始化比较优雅


## 7.字典，保持有序

### collections.OrderedDict
```python
>>> from collections import OrderedDict

>>> d = OrderedDict()
>>> d['foo'] = 1
>>> d['bar'] = 2
>>> d['spam'] = 3
>>> d
OrderedDict([('foo', 1), ('bar', 2), ('spam', 3)])
>>> d
OrderedDict([('foo', 1), ('bar', 2), ('spam', 3)])
```

**OrderedDict特别适合构造JSON**
```python
>>> import json

>>> json.dumps(d)
'{"foo": 1, "bar": 2, "spam": 3}'
```

### 进一步理解 OrderedDict
OrderedDict内部维护了双向链表，新加入的元素会存在链表末尾    
OrderedDict对已存在的键会重新赋值而不会改变顺序   
OrderedDict大小是普通dict的两倍多(由于额外的链表)   

## 8.字典，相关计算问题

### 字典，最大值/最小值/排序
**最好的办法是使用zip()对字典进行键值互换**
```python
# 最值
>>> prices = {
... 'APPL':654.2,
... 'FB':267.2,
... 'IBM':213.1}
>>> min(prices)
'APPL'
>>> min_prices = min()
>>> min_prices
(213.1, 'IBM')
```

```python
# 排序
>>> prices_sorted = sorted(zip(prices.values(), prices.keys()))
>>> prices_sorted
[(213.1, 'IBM'), (267.2, 'FB'), (654.2, 'APPL')]
```
**注意：zip创建的是迭代器，只能消费一次**
```python
>>> temp = zip(prices.values(), prices.keys())

>>> print(min(temp))
(213.1, 'IBM')

>>> print(max(temp))
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: max() arg is an empty sequence
```

## 9.字典，找出相同(键/值)

### 对 keys/items 进行集合操作
```python
>>> a = {
... 'x':1,'y':2,'z':3}
>>> b = {'w':10, 'x':11, 'y':2}
>>> a
{'z': 3, 'y': 2, 'x': 1}
>>> b
{'w': 10, 'y': 2, 'x': 11}
>>> a.keys() & b.keys()
{'y', 'x'}
>>> a.keys() - b.keys()
{'z'}
>>> a.items() & b.items()
{('y', 2)}
# values是不可以操作的
>>> a.values() & b.values()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: unsupported operand type(s) for &: 'dict_values' and 'dict_values'
```

## 去除序列中重复项，保持元素间顺序不变

> 哈希：如果一个对象是可哈希的，那么在它的生命周期中是不可变的，需要有个hash()方法
### 如果序列中的值可哈希
```python
>>> def deduque(items):
...     seen = set()
...     for item in items:
...         if item not in seen:
...             yield item
...             seen.add(item)
... 
>>> a = [1, 5, 2, 1, 9, 1, 5, 10]
>>> list(deduque(a))
[1, 5, 2, 9, 10]
```
### 如果序列中的值不可哈希
```python
>>> def deduque(items, key=None):
...     seen = set()
...     for item in items:
            val = item if key is None else key(item)
...         if val not in seen:
...             yield item
...             seen.add(val)
```

### 如果只是去重
```python
>>> a = [1, 5, 2, 1, 9, 1, 5, 10]
>>> set(a)
{1, 2, 10, 5, 9}
```
