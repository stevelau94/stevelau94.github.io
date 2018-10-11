---
title: python 文本处理
date: 2018-07-13 02:05:52
categories: 
    - "python"
    - "python cookbook"
tags: 
    - python
---
python cookbook中关于文本处理部分的笔记
<!--more-->
## 1.处理任意分隔符，拆分字符串

### 字符串的split()无法处理多中分隔符的情况
### re.split()可以提供更强大的功能
```python
>>> L
'asd fee, fedjc;dww fexsa    wdca. dfefacx'
>>> import re

>>> re.split(r'[;,\s]\s*', L)
['asd', 'fee', 'fedjc', 'dww', 'fexsa', 'wdca.', 'dfefacx']
```
### 注意re.split()的捕获组写法
```python
>>> re.split(r'[;|,|\s]\s*', L)
['asd', 'fee', 'fedjc', 'dww', 'fexsa', 'wdca.', 'dfefacx']
>>> re.split(r'(;,\s)\s*', L)
['asd fee, fedjc;dww fexsa    wdca. dfefacx']
>>> re.split(r'(;|,|\s)\s*', L)
['asd', ' ', 'fee', ',', 'fedjc', ';', 'dww', ' ', 'fexsa', ' ', 'wdca.', ' ', 'dfefacx']
```
### 进一步将值和分隔符解析出来
```python
f = re.split(r'(;|,|\s)\s*', L)
>>> f
['asd', ' ', 'fee', ',', 'fedjc', ';', 'dww', ' ', 'fexsa', ' ', 'wdca.', ' ', 'dfefacx']

>>> values = f[::2]
>>> values
['asd', 'fee', 'fedjc', 'dww', 'fexsa', 'wdca.', 'dfefacx']

>>> delimiters = f[1::2] + ['']
>>> delimiters
[' ', ',', ';', ' ', ' ', ' ', '']
```
### 用(?:...)使用非捕获组，不取分隔符
```python
>>> f = re.split(r'(;|,|\s)\s*', L)
>>> f
['asd', ' ', 'fee', ',', 'fedjc', ';', 'dww', ' ', 'fexsa', ' ', 'wdca.', ' ', 'dfefacx']
>>> f = re.split(r'(?:;|,|\s)\s*', L)
>>> f
['asd', 'fee', 'fedjc', 'dww', 'fexsa', 'wdca.', 'dfefacx']
```

## 2.匹配字符串开头/结尾（文件匹配用的上）

### str.startswith() & str.endswith()
```python
>>> filename = 'file.txt'
>>> filename.startswith('a')
False
>>> filename.startswith('f')
True
>>> filename.endswith('.sh')
False
>>> filename.endswith('.txt')
```

### 传入多项检查，需要传入元组
**如果选项在list或集合中，需要先转为元组**
```python
>>> import os
>>> filenames = os.listdir('.')
>>> filenames
['ubuntu-shadowsocks.md', 'ubuntu-hexo-cross-device-deploy.md', 'ubuntu-xmind.md', '.git', 'python_cookbook']

>>> [file for file in filenames if file.endswith('.git')]
['.git']

>>> any(name.endswith('.md') for name in filenames)
True
```

### 检查有无特定文件
```python
if any(name.endswith('.py','.h') for name in listdir(dirname))
```

## 3.使用通配符进行字符串匹配
UNIX下，使用常见的通配符进行匹配(*.py, Dat[0-9]*.csv)

### fnmatch() & fnmatchcase()

```python
>>> from fnmatch import fnmatch, fnmatchcase
>>> fnmatch('foo.txt','*.txt')
True
>>> fnmatch('foo.txt','*.TXT')
False

>>> fnmatchcase('foo.txt','*.TXT')
False

>>> names = ['Dat1.csv','Dat34.csv', 'add.py']
>>> [name for name in names if fnmatch(name, 'Dat[0-9]*')]
['Dat1.csv', 'Dat34.csv']
```

## 4.文本模式的匹配和查找

### 简单匹配 str.find() str.endswith() str.startswith()
```python
>>> text = 'ad, erefea, fea, fge. sfe, fea'
>>> text.startswith('a')
True
>>> text.startswith('ad')
True
>>> text.startswith('ad,')
True
>>> text.startswith('ad,e')
False
>>> text.find('a')
0
>>> text.find('ad')
0
>>> text.find('d')
1
```
### re.match(), re.findall(), re.findite() re.compile()
```python
>>> text1 = '07/13/2018'
>>> text2 = 'Nov 13, 2018'

# re.match() 匹配第一个
>>> if re.match(r'\d+/\d+/\d+', text1):
...     print('yes')
... else:
...     print('no')
yes

# re.compile() 将正则表达式模式编译成一个模式对象
datepat = re.compile(r'\d+/\d+/\d+')
if datepat.match(text1):
    pass

# re.findall() 全局找
datepat.findall(text1)
['07/13/2018']
```

### 捕获组
使用re.compile()编译模式对象的时候，用括号包起来可以引入捕获组  

捕获组会方便之后的处理，因为每个组的内容可以单独提取

```python
datepat = re.compile(r'(\d+)/(\d+)/(\d+)/')
>>> m = datepat.match('7/15/2018')
>>> m.group(0)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: 'NoneType' object has no attribute 'group'

# 找不同，尾端不能多/
>>> datepat = re.compile(r'(\d+)/(\d+)/(\d+)')
>>> m = datepat.match('7/15/2018')
>>> m.group(0)
'7/15/2018'
>>> m.group(2)
'15'
>>> m.groups()
('7', '15', '2018')
```

### re
> 基本功能，首先用re.compile()对匹配模式进行编译  
> 然后使用re.match(), re.findall(), re.finditer()
> 如果只是简单的文本匹配或者搜索，可以省略编译操作

**精确匹配，需要在模式中包含一个结束标记 $**
```python
>>> datepat_ = re.compile(r'(\d+)/(\d+)/(\d+)$')
```

## 5.文本查找和替换

### 简单替换，str.replace()
```python
>>> text = 'asd,zxc'
>>> text.replace('asd', 'yyy')
'yyy,zxc'
```
### 复杂替换，re.sub()

sub()第一个参数是匹配模式，第二个参数是要替换上去的模式  

**第一个参数也可以是函数**

如果模式经常使用，可以将匹配模式编译起来以便更好的性能  

```python
>>> import re

>>> text = 'Today is 7/13/2018, FIIA is 7/15/2018'
>>> re.sub(r'(\d+)/(\d+)/(\d+)', r'\3-\1-\2', text)
'Today is 2018-7-13, FIIA is 2018-7-15'
```

## 6.不区分大小写，对文本的查找和替换

### re的各种操作都要加上re.IGNORECASE
```python
>>> text = 'UPPER python, lower python, Mixed Python'
>>> re.findall('python', text)
['python', 'python']
>>> re.findall('python', text, flags=re.IGNORECASE)
['python', 'python', 'Python']
```

## 7.实现最短匹配

### 出现自动匹配到长模式的问题举例
```python
# 匹配的目标是引号内的文本
>>> str_pat = re.compile(r'\"(.*)\"')
>>> text1 = 'Computer say "no."'
>>> str_pat.findall(text1)
['no.']
>>> text2 = 'Computer say "no." Phone say "yes."'
# * 是贪心策略，所以匹配到了最长的
>>> str_pat.findall(text2)
['no." Phone say "yes.']
```

```python
# 加上？ 不会贪心匹配
>>> text2 = 'Computer say "no." Phone say "yes."'
>>> str_pat = re.compile(r'\"(.*?)\"')
>>> str_pat.findall(text2)
['no.', 'yes.']
```

**在模式中，句号除了换行符之外可以匹配到任意字符**  
但是如果以开始和结束文本将句号括起来的话，匹配过程中将尝试找出最长的可能匹配结果  
**在* +后面添加？，会强制将匹配算法调整为寻找最短匹配**

## 8.编写多行模式的正则

### 多行匹配举例
```python
>>> comment = re.compile(r'/\*(.*?)\*/')

>>> text1 = '/* this is a comment */'
>>> text2 = '''/* this is a
...      multiline comment */'''
>>> comment.findall(text1)
[' this is a comment ']
# . 无法匹配到换行符
>>> comment.findall(text2)
[]

# (?:.|\n) 指定一个非捕获组
>>> comment = re.compile(r'/\*((?:.|\n)*?)\*/')
>>> comment.findall(text2)
[' this is a \n     multiline comment ']
```

### re.compile()中可以标记 re.DOTALL 让.匹配换行符
```python
>>> comment = re.compile(r'/\*(.*?)\*/', re.DOTALL)
>>> comment.findall(text2)
[' this is a \n     multiline comment ']
```

## 9.Unicode文本统一表示为规范形式
对于一个字符串来说，同一个文本拥有多种不同的表示形式是个大问题，为了解决这个问题，需要将文本统一表示为规范形式

NFC 表示字符应该是全组成的(如果可能的话就是用单个代码点)
NFD 表示应该使用组合字符，每个字符可以完全分解开

### unicode解决字符串多表示问题
```python
>>> s1 = 'steve Jalape\u00f1o'
>>> s2 = 'steve Jalapen\u0303o'
>>> s1
'steve Jalapeño'
>>> s2
'steve Jalapeño'
>>> s1 == s2
False
>>> len(s1)
14
>>> len(s2)
15
>>> import unicodedata
>>> t1 = unicodedata.normalize('NFC', s1)
>>> t2 = unicodedata.normalize('NFC', s2)
>>> t1 == t2
True
>>> print(ascii(t1))
'steve Jalape\xf1o'
>>> t3 = unicodedata.normalize('NFD', s1)
>>> t4 = unicodedata.normalize('NFD', s2)
>>> t3 == t4
True
>>> print(ascii(t3))
'steve Jalapen\u0303o'
```

## 10.正则处理Unicode

### Unicode加re是可怕的，需要使用更高级的RE工具

## 11.字符串中去掉不需要的字符
### strip(), lstrip(), rstrip()
```python
>>> s = '      hello     '
>>> s.strip()
'hello'
>>> s.lstrip()
'hello     '
>>> s.rstrip()
'      hello'
>>> s = '      hello===='
>>> s = '      hello===='
>>> s.strip(' =')
'hello'
```

### strip() 不会处理字符串中间的文本起作用，需要其他辅助，如replace()
```python
>>> s = 'hello      world'
>>> s.strip()
'hello      world'
>>> s.replace(' ','')
'helloworld'
```

## 12.文本过滤和清理

### 简单处理 str.upper() str.lower()
### 简单替换 str.replace() re.sub()
### 规范化文本 unicodedata.normalize()

### str.translate() 做清理,可以接受转换函数

## 13.对齐文本字符串

### ljust(), rjust() center()
```python
>>> s
'helloworld'
>>> s.ljust(20)
'helloworld          '
>>> s.rjust(20)
'          helloworld'
>>> s.center(20)
'     helloworld     '
>>> s.rjust(20, '=')
'==========helloworld'
>>> s.center(20, '*')
'*****helloworld*****'
```

### format()
```python
# 实现和ljust, rjust center同样功能
>>> s
'helloworld'
>>> format(s, '>20')
'          helloworld'
>>> format(s, '<20')
'helloworld          '
>>> format(s, '^20')
'     helloworld     '
>>> format(s, '+^20')
'+++++helloworld+++++'

# 多个值也可使用
>>> '{:>10s}{:>10s}'.format('hello','world')
'     hello     world'

# 类型不局限于字符串
>>> format(1.2354, '>10')
'    1.2354'
>>> format(1.2354, '^10')
'  1.2354  '
>>> format(1.2354, '^10f')
' 1.235400 '
```

## 14.字符串连接及合并

### 简单合并字符串，用 + 或者 ''.join() 或者 format()
```python
>>> a = 'a'
>>> b = 'b'
>>> a + ' ' + b
'a b'
>>> pa = ['asd', 'qwr', 'fd']
>>> ''.join(pa)
'asdqwrfd'
>>> '{} {}'.format(a, b)
'a b'
```
### + 合并是非常低效的，不适合大量字符串拼接

### 生成器是个重要的工具
```python
>>> data = ['ALL', 2300, 098.2]
>>> ','.join(str(d) for d in data)
'ALL,2300,98.2'
```

### I/O与拼接同时存在是，需要根据业务判断适合的方案
```python
# version 1 str较小时，拼接开销小于I/O开销
f.write(str)

# version 2 每部分str都很大时
f.write(str1)
f.write(str2)
```
### 如果代码要从短字符串中构建输出，使用生成器
```python
>>> def sample():
...     yield 'Is'
...     yield 'APPLE'
...     yield 'THE'
...     yield 'GREATEST'
...     yield '?'
...
>>> text = ''.join(sample())
>>> text
'IsAPPLETHEGREATEST?'

>>> for part in sample(): f.write(part)
```

## 15.给字符串中变量名做替换处理

### format()模拟替换操作
```python
>>> s = '{name} is {n}'
>>> s.format(name='apple', n=234)
'apple is 234'
```
## 16.以固定列数 重新格式化文本

### textwrap
```python
import textwrap
s = 'XXX'
# 定义宽度
print(textwrap.fill(s, 70))
```
### 获取终端尺寸
```python
import os
os.get_terminal_size().columns
```

## 17.处理HTML XML

## 18.文本分词

## 19.字节串上执行文本操作
字节串很多时候可以当做字符串处理，使用字符串的大部分函数

### 注意，字节串需要解码打印
在打印的时候，需要加上.decode('ascii')或者别的 解码为文本字符串
```python
>>> s = b'hello'
>>> print(s)
b'hello'
>>> print(s.decode('ascii'))
hello
```

### 注意，字节串在进行格式化的时候，需要使用文本字符串然后再编码(encode)
```python
>>> '{:10s}{:10d}'.format('allp', 100).encode('ascii')
b'allp             100'
```