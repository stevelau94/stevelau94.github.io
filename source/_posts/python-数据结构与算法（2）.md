---
title: python 数据结构与算法（2）
date: 2018-07-11 21:37:40
categories: 
    - "python"
    - "python cookbook"
tags: 
    - python
    - data structure
---

## 1.切片命名
**主要目的是为了减少硬编码**
### slice()

slice会创建切片对象，可以用在任何允许切片的地方

```python
>>> items = [0, 1, 2, 3, 4, 5, 6]
>>> a = slice(2, 4)
>>> a
slice(2, 4, None)
>>> items[a]
[2, 3]
>>> items[2:4]
[2, 3]
>>> items[a] = [10, 11]
>>> items
[0, 1, 10, 11, 4, 5, 6]
>>> del items[a]
>>> items
[0, 1, 4, 5, 6]
```

## 2.序列中，出现次数最多的元素

### collections.Counter

```python
>>> from collections import Counter

>>> words = ['look','into','me','look','eye','look','me','sleep','into','yes']
>>> word_counter = Counter(words)
>>> word_counter
Counter({'look': 3, 'into': 2, 'me': 2, 'yes': 1, 'sleep': 1, 'eye': 1})
>>> top_3 = word_counter.most_common(3)
>>> top_3
[('look', 3), ('into', 2), ('me', 2)]
```

### Counter 可以使用数学运算操作
```python
>>> words = ['look','into','me','look','eye','look','me','sleep','into','yes']
>>> a = Counter(words)
>>> morewords = ['why','are','you','not','in','this','is']
>>> b = Counter(morewords)
>>> a
Counter({'look': 3, 'into': 2, 'me': 2, 'yes': 1, 'sleep': 1, 'eye': 1})
>>> b
Counter({'you': 1, 'in': 1, 'this': 1, 'not': 1, 'is': 1, 'are': 1, 'why': 1})
>>> c = a+b
>>> c
Counter({'look': 3, 'into': 2, 'me': 2, 'yes': 1, 'in': 1, 'this': 1, 'not': 1, 'is': 1, 'why': 1, 'are': 1, 'you': 1, 'sleep': 1, 'eye': 1})
>>> d = a - b
>>> d
Counter({'look': 3, 'into': 2, 'me': 2, 'yes': 1, 'sleep': 1, 'eye': 1})
```

**任何需要对数据制表或者计算的问题，Counter都是优秀的工具**

## 3.字典组成的列表，根据字典的一个或多个值对列表进行排序 (operator.itemgetter)

### operator.itemgetter
itemgetter接受的参数是可作为查询的标记

```python
>>> from operator import itemgetter

>>> rows = [
... {'fname':'steve', 'lname':'jobs', 'age':23},
... {'fname':'david', 'lname':'jobs', 'age':33},
... {'fname':'jhon', 'lname':'cleese', 'age':13}]
>>> rows_by_fname = sorted(rows, key=itemgetter('fname'))
>>> rows_by_age = sorted(rows, key=itemgetter('age'))
>>> rows_by_fname
[{'fname': 'david', 'lname': 'jobs', 'age': 33}, 
{'fname': 'jhon', 'lname': 'cleese', 'age': 13}, 
{'fname': 'steve', 'lname': 'jobs', 'age': 23}]
>>> rows_by_age
[{'fname': 'jhon', 'lname': 'cleese', 'age': 13}, 
{'fname': 'steve', 'lname': 'jobs', 'age': 23}, 
{'fname': 'david', 'lname': 'jobs', 'age': 33}]
```

### lambda & itemgetter

**itemgetter速度更快！！！**
```python
import time
from timeit import timeit as timeit 
from operator import itemgetter


def test():
    rows = [
        {'fname':'steve', 'lname':'jobs', 'age':23},
        {'fname':'david', 'lname':'jobs', 'age':33},
        {'fname':'jhon', 'lname':'cleese', 'age':13}
        ]

    time_start=time.time()
    sorted(rows, key=itemgetter('fname'))
    time_end=time.time()
    print('time cost',time_end-time_start,'s')

    time_start=time.time()
    sorted(rows, key=lambda r:r['fname'])
    time_end=time.time()
    print('time cost',time_end-time_start,'s')

# ('time cost', 2.09808349609375e-05, 's')
# ('time cost', 4.0531158447265625e-06, 's')
```

## 4.对原生不支持比较的对象排序(operator.attrgetter)

### operator.attrgetter
### lambda & attrgetter

**attrgetter速度更快！！！**
```python
>>> class User:
...     def __init__(self, user_id):
...         self.user_id = user_id
...     def __repr__(self):
...         return 'User({})'.format(self.user_id)
... 
>>> users = [User(22), User(13), User(99)]
>>> users
[User(22), User(13), User(99)]

>>> sorted(users, key=lambda r: r.user_id)
[User(13), User(22), User(99)]

>>> from operator import attrgetter
>>> sorted(users, key=attrgetter('user_id'))
[User(13), User(22), User(99)]
```

**attrgetter可以获取多个字段值**

## 5.根据字段将记录分组

### itertools.groupby()
使用groupby()必须首先将序列排序  
因为groupby只能检查连续的项  

groupby()创建的是迭代器，每次迭代会返回一个值和子迭代器， 子迭代器产生该分组内
```python
>>> rows = [
... {'address':'shanghai','date':'2018/01/08'},
... {'address':'hangzhou','date':'2018/01/08'},
... {'address':'shenzhen','date':'2018/08/08'},
... {'address':'guangzhou','date':'2018/09/08'},
... {'address':'chengdu','date':'2018/09/08'}]
>>> from operator import itemgetter
>>> from itertools import groupby

>>> for date,items in groupby(rows, key=itemgetter('date')):
...     print(date)
...     for i in items:
...         print(' ', i)
... 
2018/01/08
  {'date': '2018/01/08', 'address': 'shanghai'}
  {'date': '2018/01/08', 'address': 'hangzhou'}
2018/08/08
  {'date': '2018/08/08', 'address': 'shenzhen'}
2018/09/08
  {'date': '2018/09/08', 'address': 'guangzhou'}
  {'date': '2018/09/08', 'address': 'chengdu'}
```

### 其实用defaultdict也可以实现（多值字典），而且效率更高

## 6.筛选序列中的元素（列表解析式/生成器/filter()）

### 列表解析式 list comprehension
```python
>>> my_list = [1, 4, 3, -5, 2, 4, 11 ,4, 5, 677]
>>> [n for n in my_list if n//2 == 1]
[3, 2]
>>> [n for n in my_list if n>0]
[1, 4, 3, 2, 4, 11, 4, 5, 677]
>>> [n for n in my_list if n<0]
```

列表解析式可能存在的问题：如果原始输入很大，则可能产生非常大的结果


### 生成器
```python
>>> po = (n for n in my_list if n>0)

>>> po
<generator object <genexpr> at 0x7f959745a5c8>

>>> for p in po:
...     print(p)
... 
1
4
3
2
4
11
4
5
677
```

### filter
**如果筛选的过程很复杂，可以考虑将筛选过程设为函数，用filter处理**
```python
>>> values = [1, 3, -5, 's', '-']
>>> def is_int(val):
...     try:
...         x = int(val)
...         return True
...     except ValueError:
...         return False
... 
>>> ivals = list(filter(is_int, values))
>>> ivals
[1, 3, -5]
```

### itertools.compress()
**通过布尔值筛选**
```python
>>> rows
[{'date': '2018/01/08', 'address': 'shanghai'}, {'date': '2018/01/08', 'address': 'hangzhou'}, {'date': '2018/08/08', 'address': 'shenzhen'}, {'date': '2018/09/08', 'address': 'guangzhou'}, {'date': '2018/09/08', 'address': 'chengdu'}]
>>> counts = [1, 2, 3, 4, 5]
>>> biger_3 = [n > 3 for n in counts]
>>> biger_3
[False, False, False, True, True]

>>> from itertools import compress

>>> list(compress(rows, biger_3))
[{'date': '2018/09/08', 'address': 'guangzhou'}, {'date': '2018/09/08', 'address': 'chengdu'}]
```

## 7.字典，提取子集

### 字典解析式（dict comprehension）
```python
>>> prices = {
... 'APPL':199.9,
... 'GOOL':99.9,
... 'AMZO':299,
... 'FB':33}
>>> {key:value for key,value in prices.items() if value > 150}
{'AMZO': 299, 'APPL': 199.9}
>>> GREAT_COM = ['APPL','GOOL','AMZO']
>>> {key:value for key,value in prices.items() if key in GREAT_COM}
{'AMZO': 299, 'APPL': 199.9, 'GOOL': 99.9}
```

## 8.将名称映射到序列中的元素

### collections.nameedtuple()

**nameedtuple** 是一种工厂方法，需要为他提供类型名称和相应字段
```python
>>> from collections import namedtuple

>>> Subscriber = namedtuple('Subscriber', ['addr','joined'])
>>> sub = Subscriber('jhon@google.com', '2018-8-8')
>>> sub
Subscriber(addr='jhon@google.com', joined='2018-8-8')
>>> sub.addr
'jhon@google.com'
>>> sub.joined
'2018-8-8'
```

### nameedtuple的主要作用在于，将代码与元素位置解耦  
若果出现大型的元组列表，那么在按照位置读取的情况下，增加数据的操作将会使代码崩溃

### 根据位置的引用常常会使得代码表达能力不足，而且也使代码依赖于记录的结构

### namedtuple可以替代字典
在涉及到大型字典时，namedtuple会更高效  

```python
>>> from collections import namedtuple
>>> Stock = namedtuple('Stock', ['name','shares','price'])
>>> s = Stock('APPL',100, 123)
>>> s
Stock(name='APPL', shares=100, price=123)

```
**namedtuple** 同样是不可变的

**如果需要修改任何属性，可以通过修改namedtuple的实例的_replace()方法实现**
```python
>>> s
Stock(name='APPL', shares=100, price=123)
>>> 
>>> s.shares
100
>>> s.shares = 99
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: can't set attribute
>>> s = s._replace(shares = 99)
>>> s
Stock(name='APPL', shares=99, price=123)
```

### dict_to_namedtuple
```python
>>> from collections import namedtuple

>>> Stock = namedtuple('Stock', ['name','shares','price','date','time'])

>>> stock_proto = Stock('',0,0,None,None)

>>> def dict_2_namedtuple(s):
...     return stock_proto._replace(**s)
... 
>>> a = {'name':'APPL','shares':199, 'price':123}
>>> dict_2_namedtuple(a)
Stock(name='APPL', shares=199, price=123, date=None, time=None)

>>> b = {'name':'GOOL','shares':99, 'price':113}
>>> dict_2_namedtuple(b)
Stock(name='GOOL', shares=99, price=113, date=None, time=None)
```

**注意，若需要一个高效的数据结构并且需要修改各种实例属性，namedtuple不是最佳选择**

## 9.对数据做转换和换算

### 生成器，可以将转换和换算优雅的结合
```python
# 一个例子
files = os.listdir('dirpath')
if any(name.endwith('.py') for name in files):
    pass
else:
    pass

# 一个例子
>>> s = ('apple',40, 209.9)
>>> print(','.join(str(x) for x in s))
apple,40,209.9


# 一个例子
>>> pro = [
... {'name':'APPL','shares':99},
... {'name':'GOOL','shares':59},
... {'name':'FB','shares':39}]

>>> min_share = min(s['shares'] for s in pro)
>>> min_share
39
>>> min_share = min(pro, key=lambda s: s['shares'])
>>> min_share
{'shares': 39, 'name': 'FB'}
```

## 10.对个映射合并成单个映射

### collections.ChainMap()

**ChainMap接受多个映射，在逻辑上表现为单独映射**

```python
>>> from collections import ChainMap
>>> a = {'a':1, 'c':3}
>>> b = {'b':2}
>>> D = ChainMap(a, b)
>>> D
ChainMap({'c': 3, 'a': 1}, {'b': 2})
>>> D['a']
1
>>> D['c']
3
```

### ChainMap的表现会随着接受的映射变化而变化（浅复制，与ChainMap实现有关）
```python
>>> a['a'] = 100
>>> D
ChainMap({'c': 3, 'a': 100}, {'b': 2})
```

### ChainMap在与带作用域的值一起工作时很有用
```python
>>> values = ChainMap()
>>> values['x'] = 1
>>> values = values.new_child()
>>> values['x'] = 2
>>> values = values.new_child()
>>> values['x'] = 3
>>> values
ChainMap({'x': 3}, {'x': 2}, {'x': 1})
>>> values['x']
3
>>> values = values.parents
>>> values['x']
2
>>> values = values.parents
>>> values['x']
1
>>> values
ChainMap({'x': 1})
```