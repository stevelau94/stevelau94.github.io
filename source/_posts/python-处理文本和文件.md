---
title: python 处理文本和文件
date: 2018-07-19 22:16:51
categories: 
    - "python"
    - "python cookbook"
tags: 
    - python
---
## 1.file,读写文本数据

### with open() as f:
```python
# 读取
# Read the entire file as a single string
with open('somefile.txt', 'rt') as f:
    data = f.read()

# Iterate over the lines of the file
with open('somefile.txt', 'rt') as f:
    for line in f:
        # process line
        ...

# 写入
# Write chunks of text data
with open('somefile.txt', 'wt') as f:
    f.write(text1)
    f.write(text2)
    ...

# Redirected print statement
with open('somefile.txt', 'wt') as f:
    print(line1, file=f)
    print(line2, file=f)
    ...

# 追加
with open('somefile.txt', 'at') as f:
    f.write(text1)
    f.write(text2)
    ...
```

### 编码
```python
with open('somefile.txt', 'rt', encoding='latin-1') as f:

```

### wt,rt,at中的t指以文本文件的形式写入
在输出时会将换行符 \n 转换为系统默认的换行符

如果不希望这种默认的处理方式，可以给 open() 函数传入参数 newline='' 
```python
# Read with disabled newline translation
with open('somefile.txt', 'rt', newline='') as f:
    ...
```

### errors处理编码错误
```python
>>> # Replace bad chars with Unicode U+fffd replacement char
>>> f = open('sample.txt', 'rt', encoding='ascii', errors='replace')
>>> f.read()
'Spicy Jalape?o!'
>>> # Ignore bad chars entirely
>>> g = open('sample.txt', 'rt', encoding='ascii', errors='ignore')
>>> g.read()
'Spicy Jalapeo!'
>>>
```

## 2.print(),打印输出至文件中

### 在 print() 函数中指定 file 关键字参数，将输出重定向到一个文件中
```python
with open('d:/work/test.txt', 'wt') as f:
    print('Hello World!', file=f)
```

## 3.print(),使用其他分隔符或行终止符打印

### print() 函数中使用 sep 和 end 关键字参数
```python
>>> print('ACME', 50, 91.5)
ACME 50 91.5
>>> print('ACME', 50, 91.5, sep=',')
ACME,50,91.5
>>> print('ACME', 50, 91.5, sep=',', end='!!\n')
ACME,50,91.5!!
>>>
```
### 使用 end 参数也可以在输出中禁止换行
```python
>>> for i in range(5):
...     print(i)
...
0
1
2
3
4
>>> for i in range(5):
...     print(i, end=' ')
...
0 1 2 3 4 >>>
```

## 4.file,读写字节数据

### rb 或 wb 的 open() 函数来读取或写入二进制数
```python
# Read the entire file as a single byte string
with open('somefile.bin', 'rb') as f:
    data = f.read()

# Write binary data to a file
with open('somefile.bin', 'wb') as f:
    f.write(b'Hello World')
```

### 从二进制模式的文件中读取或写入文本数据，必须确保要进行解码和编码操作
```python
with open('somefile.bin', 'rb') as f:
    data = f.read(16)
    text = data.decode('utf-8')

with open('somefile.bin', 'wb') as f:
    text = 'Hello World'
    f.write(text.encode('utf-8'))
```

### 很多对象还允许通过使用文件对象的 readinto() 方法直接读取二进制数据到其底层的内存中去
```python
>>> import array
>>> a = array.array('i', [0, 0, 0, 0, 0, 0, 0, 0])
>>> with open('data.bin', 'rb') as f:
...     f.readinto(a)
...
16
>>> a
array('i', [1, 2, 3, 4, 0, 0, 0, 0])
>>>
```

## 5.file,文件不存在才能写入

**不允许覆盖已存在的文件内容**
### open() 函数中使用 x 模式来代替 w 模式
```python
>>> with open('somefile', 'wt') as f:
...     f.write('Hello\n')
...
>>> with open('somefile', 'xt') as f:
...     f.write('Hello\n')
...
Traceback (most recent call last):
File "<stdin>", line 1, in <module>
FileExistsError: [Errno 17] File exists: 'somefile'
>>>
```

## 6.字符串的I/O操作

### io.StringIO() 和 io.BytesIO() 类来创建类文件对象操作字符串数据

**io.StringIO 只能用于文本。要操作二进制数据，要使用 io.BytesIO 类来代替**
```python
>>> s = io.StringIO()
>>> s.write('Hello World\n')
12
>>> print('This is a test', file=s)
15
>>> # Get all of the data written so far
>>> s.getvalue()
'Hello World\nThis is a test\n'
>>>

>>> # Wrap a file interface around an existing string
>>> s = io.StringIO('Hello\nWorld\n')
>>> s.read(4)
'Hell'
>>> s.read()
'o\nWorld\n'
>>>
```

需要注意的是， StringIO 和 BytesIO 实例并没有正确的整数类型的文件描述符。 因此，它们不能在那些需要使用真实的系统级文件如文件，管道或者是套接字的程序中使用。


## 7.file,读写压缩文件(gzip 和 bz2)

### 以文本形式读取压缩文件
```python
# gzip compression
import gzip
with gzip.open('somefile.gz', 'rt') as f:
    text = f.read()

# bz2 compression
import bz2
with bz2.open('somefile.bz2', 'rt') as f:
    text = f.read()
```

### 写入压缩数据
```python
# gzip compression
import gzip
with gzip.open('somefile.gz', 'wt') as f:
    f.write(text)

# bz2 compression
import bz2
with bz2.open('somefile.bz2', 'wt') as f:
    f.write(text)
```

**要注意的是选择一个正确的文件模式是非常重要的**

### 当写入压缩数据时，可以使用 compresslevel 这个可选的关键字参数来指定一个压缩级别

**默认的等级是9，也是最高的压缩等级。等级越低性能越好，但是数据压缩程度也越低**

```python
with gzip.open('somefile.gz', 'wt', compresslevel=5) as f:
    f.write(text)
```

### gzip.open() 和 bz2.open()可以作用在一个已存在并以二进制模式打开的文件上
```python
import gzip
f = open('somefile.gz', 'rb')
with gzip.open(f, 'rt') as g:
    text = g.read()
```

## 固定大小记录的文件迭代

### 使用 iter 和 functools.partial() 函数
```python
from functools import partial

RECORD_SIZE = 32

with open('somefile.data', 'rb') as f:
    # records 对象是一个可迭代对象，它会不断的产生固定大小的数据块，直到文件末尾
    records = iter(partial(f.read, RECORD_SIZE), b'')
    for r in records:
        ...
```

**iter() 函数有一个鲜为人知的特性就是，如果你给它传递一个可调用对象和一个标记值，它会创建一个迭代器**

## 9.读取二进制数据到可变缓冲区中

### 读取数据到一个可变数组中，使用文件对象的 readinto()
```python
import os.path

def read_into_buffer(filename):
    buf = bytearray(os.path.getsize(filename))
    with open(filename, 'rb') as f:
        f.readinto(buf)
    return buf

>>> # Write a sample file
>>> with open('sample.bin', 'wb') as f:
...     f.write(b'Hello World')
...
>>> buf = read_into_buffer('sample.bin')
>>> buf
bytearray(b'Hello World')
>>> buf[0:5] = b'Hallo'
>>> buf
bytearray(b'Hallo World')
>>> with open('newsample.bin', 'wb') as f:
...     f.write(buf)
...
11
>>>
```

**readinto() 方法能被用来为预先分配内存的数组填充数据，甚至包括由 array 模块或 numpy 库创建的数组**

readinto() 填充已存在的缓冲区而不是为新对象重新分配内存再返回它们。 因此可以使用它来避免大量的内存分配操作

**使用 f.readinto() 时需要注意的是，你必须检查它的返回值，也就是实际读取的字节数**

### memoryview
可以通过零复制的方式对已存在的缓冲区执行切片操作，甚至还能修改它的内容

```python
>>> buf
bytearray(b'Hello World')
>>> m1 = memoryview(buf)
>>> m2 = m1[-5:]
>>> m2
<memory at 0x100681390>
>>> m2[:] = b'WORLD'
>>> buf
bytearray(b'Hello WORLD')
>>>
```

## 10.file,内存映射的二进制文件
想内存映射一个二进制文件到一个可变字节数组中，目的可能是为了随机访问它的内容或者是原地做些修改

### 使用 mmap 模块来内存映射文件
```python
import os
import mmap

def memory_map(filename, access=mmap.ACCESS_WRITE):
    size = os.path.getsize(filename)
    fd = os.open(filename, os.O_RDWR)
    return mmap.mmap(fd, size, access=access)
>>> size = 1000000
>>> with open('data', 'wb') as f:
...     f.seek(size-1)
...     f.write(b'\x00')
...
>>>

>>> m = memory_map('data')
>>> len(m)
1000000
>>> m[0:10]
b'\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00'
>>> m[0]
0
>>> # Reassign a slice
>>> m[0:11] = b'Hello World'
>>> m.close()

>>> # Verify that changes were made
>>> with open('data', 'rb') as f:
... print(f.read(11))
...
b'Hello World'
>>>
```

### mmap() 返回的 mmap 对象同样也可以作为一个上下文管理器
```python
>>> with memory_map('data') as m:
...     print(len(m))
...     print(m[0:10])
...
1000000
b'Hello World'
>>> m.closed
True
>>>
```

**默认情况下， memeory_map() 函数打开的文件同时支持读和写操作。 任何的修改内容都会复制回原来的文件中**

如果需要只读的访问模式，可以给参数 access 赋值为 mmap.ACCESS_READ

想在本地修改数据，但是又不想将修改写回到原始文件中，可以使用 mmap.ACCESS_COPY

内存映射一个文件并不会导致整个文件被读取到内存中。 也就是说，文件并没有被复制到内存缓存或数组中。相反，操作系统仅仅为文件内容保留了一段虚拟内存。 当你访问文件的不同区域时，这些区域的内容才根据需要被读取并映射到内存区域中。 而那些从没被访问到的部分还是留在磁盘上

## 11.file,文件路径名的操作

### os.path
```python
>>> import os
>>> path = '/Users/beazley/Data/data.csv'

>>> # Get the last component of the path
>>> os.path.basename(path)
'data.csv'

>>> # Get the directory name
>>> os.path.dirname(path)
'/Users/beazley/Data'

>>> # Join path components together
>>> os.path.join('tmp', 'data', os.path.basename(path))
'tmp/data/data.csv'

>>> # Expand the user's home directory
>>> path = '~/Data/data.csv'
>>> os.path.expanduser(path)
'/Users/beazley/Data/data.csv'

>>> # Split the file extension
>>> os.path.splitext(path)
('~/Data/data', '.csv')
>>>
```

## 12.file,测试文件是否存在

### os.path测试一个文件或目录是否存在
```python
>>> import os
>>> os.path.exists('/etc/passwd')
True
>>> os.path.exists('/tmp/spam')
False
>>>
```

### os.path测试这个文件是什么类型
```python
>>> # Is a regular file
>>> os.path.isfile('/etc/passwd')
True

>>> # Is a directory
>>> os.path.isdir('/etc/passwd')
False

>>> # Is a symbolic link
>>> os.path.islink('/usr/local/bin/python3')
True

>>> # Get the file linked to
>>> os.path.realpath('/usr/local/bin/python3')
'/usr/local/bin/python3.3'
>>>
```

### os.path获取元数据
```python
>>> os.path.getsize('/etc/passwd')
3669
>>> os.path.getmtime('/etc/passwd')
1272478234.0
>>> import time
>>> time.ctime(os.path.getmtime('/etc/passwd'))
'Wed Apr 28 13:10:34 2010'
>>>
```

## 13.file,获取文件夹中的文件列

### os.listdir() 函数来获取某个目录中的文件列表
```python
import os.path

# Get all regular files
names = [name for name in os.listdir('somedir')
        if os.path.isfile(os.path.join('somedir', name))]

# Get all dirs
dirnames = [name for name in os.listdir('somedir')
        if os.path.isdir(os.path.join('somedir', name))]
```

### 取其他的元信息os.path / os.stat()
```python
# Example of getting a directory listing

import os
import os.path
import glob

pyfiles = glob.glob('*.py')

# Get file sizes and modification dates
name_sz_date = [(name, os.path.getsize(name), os.path.getmtime(name))
                for name in pyfiles]
for name, size, mtime in name_sz_date:
    print(name, size, mtime)

# Alternative: Get file metadata
file_metadata = [(name, os.stat(name)) for name in pyfiles]
for name, meta in file_metadata:
    print(name, meta.st_size, meta.st_mtime)
```

## 14.file,忽略文件名编码

默认情况下，所有的文件名都会根据 sys.getfilesystemencoding() 返回的文本编码来编码或解码

```python
>>> sys.getfilesystemencoding()
'utf-8'
>>>
```
某种原因你想忽略这种编码，可以使用一个原始字节字符串来指定一个文件名即可
```python
>>> # Wrte a file using a unicode filename
>>> with open('jalape\xf1o.txt', 'w') as f:
...     f.write('Spicy!')
...
6
>>> # Directory listing (decoded)
>>> import os
>>> os.listdir('.')
['jalapeño.txt']

>>> # Directory listing (raw)
>>> os.listdir(b'.') # Note: byte string
[b'jalapen\xcc\x83o.txt']

>>> # Open file with raw filename
>>> with open(b'jalapen\xcc\x83o.txt') as f:
...     print(f.read())
...
Spicy!
>>>
```

## 15.file,打印不合法的文件名

程序获取了一个目录中的文件名列表，但是当它试着去打印文件名的时候程序崩溃， 出现了 UnicodeEncodeError 异常和一条奇怪的消息—— surrogates not allowed

### 避免
```python
def bad_filename(filename):
    return repr(filename)[1:-1]

try:
    print(filename)
except UnicodeEncodeError:
    print(bad_filename(filename))
```

## 16.file,增加或改变已打开文件的编码

### io.TextIOWrapper()
```python
import urllib.request
import io

u = urllib.request.urlopen('http://www.python.org')
f = io.TextIOWrapper(u, encoding='utf-8')
text = f.read()
```

### detach()
```python
>>> import sys
>>> sys.stdout.encoding
'UTF-8'
>>> sys.stdout = io.TextIOWrapper(sys.stdout.detach(), encoding='latin-1')
>>> sys.stdout.encoding
'latin-1'
>>>
```
### I/O系统由一系列的层次构建 
```python
>>> f = open('sample.txt','w')
>>> f
<_io.TextIOWrapper name='sample.txt' mode='w' encoding='UTF-8'>
>>> f.buffer
<_io.BufferedWriter name='sample.txt'>
>>> f.buffer.raw
<_io.FileIO name='sample.txt' mode='wb'>
>>>
```

io.TextIOWrapper 是一个编码和解码Unicode的文本处理层   

io.BufferedWriter 是一个处理二进制数据的带缓冲的I/O层  

io.FileIO 是一个表示操作系统底层文件描述符的原始文件

## 17.file,将字节写入文本文件

### 将字节数据直接写入文件的缓冲区
```python
>>> import sys
>>> sys.stdout.write(b'Hello\n')
Traceback (most recent call last):
    File "<stdin>", line 1, in <module>
TypeError: must be str, not bytes
>>> sys.stdout.buffer.write(b'Hello\n')
Hello
5
>>>
```

文本文件是通过在一个拥有缓冲的二进制模式文件上增加一个Unicode编码/解码层来创建。 buffer 属性指向对应的底层文件。如果你直接访问它的话就会绕过文本编码/解码层

## 18.file,将文件描述符包装成文件对象

一个文件描述符和一个打开的普通文件是不一样的。 文件描述符仅仅是一个由操作系统指定的整数，用来指代某个系统的I/O通道。 

### 文件描述符可以使用 open() 函数来将其包装为一个Python的文件对象
```python
# Open a low-level file descriptor
import os
fd = os.open('somefile.txt', os.O_WRONLY | os.O_CREAT)

# Turn into a proper file
f = open(fd, 'wt')
f.write('hello world\n')
f.close()

# 当高层的文件对象被关闭或者破坏的时候，底层的文件描述符也会被关闭。 

# 可以给 open() 函数传递一个可选的 colsefd=False 阻止这种行为

# Create a file object, but don't close underlying fd when done
f = open(fd, 'wt', closefd=False)
...

```

## 19.file,创建临时文件和文件夹

### tempfile 模块
```python
from tempfile import TemporaryFile

with TemporaryFile('w+t') as f:
    # Read/write to the file
    f.write('Hello World\n')
    f.write('Testing\n')

    # Seek back to beginning and read the data
    f.seek(0)
    data = f.read()

# Temporary file is destroyed
```

```python
# 第二种用法
f = TemporaryFile('w+t')
# Use the temporary file
...
f.close()
# File is destroyed
```

### NamedTemporaryFile()
在大多数Unix系统上，通过 TemporaryFile() 创建的文件都是匿名的，甚至连目录都没有。 如果你想打破这个限制，可以使用 NamedTemporaryFile() 来代替

```python
from tempfile import NamedTemporaryFile

with NamedTemporaryFile('w+t') as f:
    print('filename is:', f.name)
    ...

# File automatically destroyed
```

### tempfile.TemporaryDirectory()创建一个临时目录
```python
from tempfile import TemporaryDirectory

with TemporaryDirectory() as dirname:
    print('dirname is:', dirname)
    # Use the directory
    ...
# Directory and all contents destroyed
```

## 20.与串行端口的数据通信

###  pySerial包
典型场景就是和一些硬件设备打交道(比如一个机器人或传感器)
```python

import serial
ser = serial.Serial('/dev/tty.usbmodem641', # Device name varies
                    baudrate=9600,
                    bytesize=8,
                    parity='N',
                    stopbits=1)
```

## 21.序列化Python对象

### pickle
```python
import pickle

data = ... # Some Python object
f = open('somefile', 'wb')
pickle.dump(data, f)

# 转存成字符串 pickle.dumps()
s = pickle.dumps(data)

# 从字节流中恢复一个对象，使用 picle.load() 或 pickle.loads() 函数
# Restore from a file
f = open('somefile', 'rb')
data = pickle.load(f)

# Restore from a string
data = pickle.loads(s)

```

**千万不要对不信任的数据使用pickle.load()**

pickle 对于大型的数据结构比如使用 array 或 numpy 模块创建的二进制数组效率并不是一个高效的编码方式。 如果你需要移动大量的数组数据，你最好是先在一个文件中将其保存为数组数据块或使用更高级的标准编码方式如HDF5 (需要第三方库的支持)

由于 pickle 是Python特有的并且附着在源码上，所有如果需要长期存储数据的时候不应该选用它。

