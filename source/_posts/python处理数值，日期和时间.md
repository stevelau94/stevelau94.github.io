---
title: python处理数值，日期和时间
date: 2018-07-15 23:59:39
categories: 
    - "python"
    - "python cookbook"
tags: 
    - python
---
## 1.数值，取整

### round()
```python
>>> round(1.23, 1)
1.2
>>> round(1.23, 12)
1.23
>>> round(-1.5, 1)
-1.5
>>> round(-1.5)
-2
>>> round(1.25)
1
>>> round(1.25, 1)
1.2
```

### 注意进位问题
```python
>>> round(1.254, 1)
1.3
>>> round(1.244, 1)
1.2
```

### 反向取近似
```python
>>> round(a, -1)
12340
>>> round(a, -2)
12300
```

## 2.数值，精确的小数
浮点数是有误差的
### decimal
```python
>>> a = 4.32
>>> b = 3.23
>>> c = a + b
>>> c
7.550000000000001
>>> from decimal import Decimal
>>> a = Decimal('4.32')
>>> b = Decimal('3.23')
>>> c = a+b
>>> c
Decimal('7.55')
```

### decimal 使用上下文环境 localcontext 对运算进行控制
```python
>>> from decimal import Decimal

>>> a = Decimal('4.32')
>>> b = Decimal('3.23')

>>> from decimal import localcontext

>>> with localcontext() as ctx:
...     ctx.prec = 3
...     print(a / b)
...
1.34
>>> with localcontext() as ctx:
...     ctx.prec = 50
...     print(a / b)
...
1.3374613003095975232198142414860681114551083591331
```
**decimal比float性能低很多**

## 3.数值，格式化输出

### format（）
```python
>>> x = 123.45678

>>> format(x, '0.2f')
'123.46'
>>> format(x, '>10.1f')
'     123.5'
>>> format(x, '^10.1f')
'  123.5   '

>>> x = 123643.45678

>>> format(x, ',')
'123,643.45678'

>>> format(x, 'e')
'1.236435e+05'
>>> format(x, '.2e')
'1.24e+05'
```

**当限制数值的位数时，数值会根据round()取整**

## 4.数值，各种进制

**八进制前缀0o**
### bin(),oct(),hex()
```python
>>> bin(x)
'0b10011010010'
>>> oct(x)
'0o2322'
>>> hex(x)
'0x4d2'
```

### 去掉前缀，format()
```python
>>> format(x, 'b')
'10011010010'
>>> format(x, 'o')
'2322'
>>> format(x, 'x')
'4d2'
```

## 5.数值，大整数 字节串处理

### 字节串和大整数转换
```python
>>> data = b'\x00\x124V\x00x\x90\xab\x00\xcd\xef\x01\x00#\x004'
>>> data
b'\x00\x124V\x00x\x90\xab\x00\xcd\xef\x01\x00#\x004'
>>> len(data)
16

>>> int.from_bytes(data, 'little')
69120565665751139577663547927094891008
>>> int.from_bytes(data, 'big')
94522842520747284487117727783387188

>>> x = 94522842520747284487117727783387188

>>> x.to_bytes(16, 'big')
b'\x00\x124V\x00x\x90\xab\x00\xcd\xef\x01\x00#\x004'
>>> x.to_bytes(16, 'little')
b'4\x00#\x00\x01\xef\xcd\x00\xab\x90x\x00V4\x12\x00'
```

## 6.数值，复数

### complex(real, image)
```python
>>> a = complex(2, 4)
>>> a
(2+4j)
>>> b = 3 -5j
>>> b
(3-5j)
>>> a.real
2.0
>>> b.imag
-5.0
# 可以进行算数操作
>>> abs(a)
4.47213595499958
```

### numpy可以直接创建复数
### 复数的函数操作需要使用cmath
```python
>>> import cmath
>>> cmath.sin(a)
(24.83130584894638-11.356612711218173j)
```
### math无法实现复数的操作
```python
>>> import math
>>> math.sqrt(-1)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: math domain error
```

## 7.inf和NaN　(无穷大和空)

### inf
```python
>>> a = float('inf')
>>> b = float('-inf')
>>> a
inf
>>> b
-inf
```

### nan
```python
>>> c = float('nan')
>>> c
nan
```

### inf＆nan　可传递
```python
>>> a + 267
inf
>>> a * 267
inf
>>> 10 / a
0.0
>>> c +1234
nan
>>> c /2
nan
```


## 8.数值，分数

### fractions
```python
>>> from fractions import Fraction
>>> a = Fraction(5, 4)
>>> b = Fraction(7, 16)
>>> a
Fraction(5, 4)
>>> b
Fraction(7, 16)
>>> print(a+b)
27/16
>>> print(a*b)
35/64

>>> c = a*b
>>> c
Fraction(35, 64)
>>> c.numerator
35
>>> c.denominator
64

>>> float(c)
0.546875

>>> x = 4.5677
>>> y = Fraction(x)
>>> y
Fraction(2571386502242527, 562949953421312)

>>> y = Fraction(x.as_integer_ratio())
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/usr/lib/python3.5/fractions.py", line 169, in __new__
    raise TypeError("argument should be a string "
TypeError: argument should be a string or a Rational instance
>>> y = Fraction(*x.as_integer_ratio())
>>> y
Fraction(2571386502242527, 562949953421312)
```

## 9.numpy，大型数组
## 10.numpy，矩阵和线性代数
## 11.随机数

### 随机挑一个，random.choice(iter)
```python
>>> import random
>>> v = [1, 2, 3, 5, 76, 7 ,8]
>>> random.choice(v)
3
>>> random.choice(v)
5
>>> random.choice(v)
8
```

### 随机挑Ｎ个，random.sample(iter, N)
```python
>>> import random
>>> v = [1, 2, 3, 5, 76, 7 ,8]
>>> random.sample(v, 4)
[5, 1, 2, 3]
>>> random.sample(v, 4)
[7, 5, 76, 8]
```

### 随机打乱顺序，random.shuffle(iter)
```python
>>> random.shuffle(v)
>>> v
[8, 5, 3, 2, 76, 1, 7]
>>> random.shuffle(v)
>>> v
[8, 7, 76, 1, 5, 3, 2]
```

### 随机生成整数，random.randint()
```python
>>> random.randint(0, 10)
4
>>> random.randint(0, 10)
9
>>> random.randint(0, 10)
5
```

### 产生０－１均匀分布，random.random()
```python
>>> random.random()
0.38724656594495876
>>> random.random()
0.29254861331979654
```
### 生成Ｎ个比特位整数，random.getrandbits()
```python
>>> random.getrandbits(4)
10
>>> random.getrandbits(200)
1590692804788838728577302013081218164430847841095397859330670
```

## 12.日期时间，时间换算
```python
>>> from datetime import timedelta
>>> a = timedelta(days=2, hours=6)
>>> b = timedelta(hours=4.5)
>>> c = a+ b
>>> c
datetime.timedelta(2, 37800)
>>> c.days
2
>>> c.hours
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: 'datetime.timedelta' object has no attribute 'hours'
>>> c.seconds
37800
>>> c.seconds / 3600
10.5
>>> c.total_seconds() / 360
585.0
>>> c.total_seconds() / 3600
58.5
>>> from datetime import datetime
>>> a = datetime(2018,7,18)
>>> print(a + timedelta(days=10))
2018-07-28 00:00:00
>>> print(a + timedelta(days=30))
2018-08-17 00:00:00
>>> b = datetime(2018,8,19)
>>> d = b - a
>>> d
datetime.timedelta(32)
>>> d.days
32
>>> now = datetime.today()
>>> now
datetime.datetime(2018, 7, 18, 22, 49, 44, 579282)
```
## 13.日期时间，计算上周某日的日期
```python
>>> from datetime import datetime,timedelta
>>> weekdays = ['Monday','Tuesday','Wednesday','Thursday','Friday','Saturday','Sunday']
>>> def get_previous_byday(dayname, start_date=None):
...     if start_date is None:
...         start_date = datetime.today()
...     day_num = start_date.weekday()
...     day_num_target = weekdays.index(dayname)
...     days_ago = (7 + day_num - day_num_target) % 7
...     if days_ago == 0:
...         days_ago = 7
...     target_date = start_date - timedelta(days=days_ago)
...     return target_date
...
>>> datetime.today()
datetime.datetime(2018, 7, 18, 22, 56, 3, 292476)
>>> get_previous_byday('Monday')
datetime.datetime(2018, 7, 16, 22, 56, 23, 420262)
>>> get_previous_byday('Sunday')
datetime.datetime(2018, 7, 15, 22, 56, 27, 960619)
```

## 14.日期时间，当月日期范围
```python
>>> def get_month_range(start_date=None):
...     if start_date is None:
...         start_date = date.today().replace(day=1)
...         _,days_in_month = calendar.monthrange(start_date.year, start_date.month)
...         end_date = start_date + timedelta(days=days_in_month)
...     return (start_date, end_date)
...
>>> a_day = timedelta(days=1)
>>> first_day, last_day = get_month_range()
>>> while first_day < last_day:
...     print(first_day)
...     first_day += a_day
...
2018-07-01
2018-07-02
2018-07-03
2018-07-04
2018-07-05
2018-07-06
2018-07-07
2018-07-08
2018-07-09
2018-07-10
2018-07-11
2018-07-12
2018-07-13
2018-07-14
2018-07-15
2018-07-16
2018-07-17
2018-07-18
2018-07-19
2018-07-20
2018-07-21
2018-07-22
2018-07-23
2018-07-24
2018-07-25
2018-07-26
2018-07-27
2018-07-28
2018-07-29
2018-07-30
2018-07-31
```
## 15.日期时间，字符串转换为日期
### datetime.strptime()
### datetime.strftime()
```python
>>> z
datetime.datetime(2018, 7, 20, 23, 53, 4, 150624)

>>> nice_z = datetime.strftime(z, '%A %B %d, %Y')
>>> nice_z
'Friday July 20, 2018'
```

## 16.日期时间，时区问题
