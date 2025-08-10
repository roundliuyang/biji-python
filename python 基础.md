# python 基础



## 字典(Dictionary)

字典是另一种可变容器模型，且可存储任意类型对象。

字典的每个键值 **key:value** 对用冒号 **:** 分割，每个键值对之间用逗号 **,** 分割，整个字典包括在花括号 **{}** 中 ,格式如下所示：

```py
d = {key1 : value1, key2 : value2 }
```

注意：**dict** 作为 Python 的关键字和内置函数，变量名不建议命名为 dict。

键一般是唯一的，如果重复最后的一个键值对会替换前面的，值不需要唯一。

```python
\>>> tinydict = {'a': 1, 'b': 2, 'b': '3'}
\>>> tinydict['b']
'3'
\>>> tinydict
{'a': 1, 'b': '3'}
```

值可以取任何数据类型，但键必须是不可变的，如字符串，数字或元组。

一个简单的字典实例：

```
tinydict = {'Alice': '2341', 'Beth': '9102', 'Cecil': '3258'}
```

也可如此创建字典：

```
tinydict1 = { 'abc': 456 } tinydict2 = { 'abc': 123, 98.6: 37 }
```



### 访问字典里的值

把相应的键放入熟悉的方括弧，如下实例:

```python
#!/usr/bin/python
 
tinydict = {'Name': 'Zara', 'Age': 7, 'Class': 'First'}
 
print "tinydict['Name']: ", tinydict['Name']
print "tinydict['Age']: ", tinydict['Age']
```

以上实例输出结果：

```python
tinydict['Name']:  Zara
tinydict['Age']:  7
```



### 字典内置函数&方法

Python字典包含了以下内置方法：

| 序号 | 函数及描述                                                   |
| :--- | :----------------------------------------------------------- |
| 1    | [dict.clear()](https://www.runoob.com/python/att-dictionary-clear.html) 删除字典内所有元素 |
| 2    | [dict.copy()](https://www.runoob.com/python/att-dictionary-copy.html) 返回一个字典的浅复制 |
| 3    | [dict.fromkeys(seq[, val\])](https://www.runoob.com/python/att-dictionary-fromkeys.html) 创建一个新字典，以序列 seq 中元素做字典的键，val 为字典所有键对应的初始值 |
| 4    | [dict.get(key, default=None)](https://www.runoob.com/python/att-dictionary-get.html) 返回指定键的值，如果值不在字典中返回default值 |



## Python 使用继承创建一个子类

在 Python 中，继承是面向对象编程的一个重要特性。通过继承，子类可以继承父类的属性和方法，并且可以在子类中添加新的属性和方法，或者重写父类的方法。下面是一个简单的例子，展示了如何使用继承创建一个子类。

```python
# 定义一个父类
class Animal:
    def __init__(self, name):
        self.name = name

    def speak(self):
        return f"{self.name} makes a sound."

# 定义一个子类，继承自 Animal
class Dog(Animal):
    def speak(self):
        return f"{self.name} barks."

# 创建父类的实例
animal = Animal("Generic Animal")
print(animal.speak())

# 创建子类的实例
dog = Dog("Buddy")
print(dog.speak())
```

代码解析：

1. `Animal` 是一个父类，它有一个构造函数 `__init__`，用于初始化 `name` 属性。`speak` 方法返回一个字符串，表示动物发出的声音。
2. `Dog` 是一个子类，继承自 `Animal`。它重写了 `speak` 方法，返回一个表示狗叫的字符串。
3. 创建了一个 `Animal` 类的实例 `animal`，并调用 `speak` 方法，输出 "Generic Animal makes a sound."。
4. 创建了一个 `Dog` 类的实例 `dog`，并调用 `speak` 方法，输出 "Buddy barks."。

输出结果：

```python
Generic Animal makes a sound.
Buddy barks.
```



## 字符串

### Python endswith()方法

#### 描述

Python endswith() 方法用于判断字符串是否以指定后缀结尾，如果以指定后缀结尾返回True，否则返回False。可选参数"start"与"end"为检索字符串的开始与结束位置。

#### 语法

endswith()方法语法：

```python
str.endswith(suffix[, start[, end]])
```

#### 参数

- suffix -- 该参数可以是一个字符串或者是一个元素。
- start -- 字符串中的开始位置。
- end -- 字符中结束位置。

#### 返回值

如果字符串含有指定的后缀返回True，否则返回False。



## f-string

从 Python 3.6 开始，`f-strings` 是一种很好的格式化字符串的新方法。它们不仅比其他格式化方式更易读、更简洁、更不容易出错，而且速度也更快！

### f-Strings

**一种在 Python 中格式化字符串的新方法和改进方法**

**f-string亦称为格式化字符串常量（\*formatted string literals\*）**

是Python3.6引入的一种字符串格式化方法，主要目的是使格式化字符串的操作更加简便。

f-string在功能方面不逊于传统的 %-formatting 语句和 str.format() 函数

同时性能又优于二者，且使用起来也更加简洁明了，因此对于Python3.6及以后的版本，推荐使用f-string进行字符串格式化。

#### 简单语法

语法类似于使用 str.format() 的语法，但不那么冗长。

```python
>>> name = "Eric"
>>> age = 74
>>> f"Hello, {name}. You are {age}."
'Hello, Eric. You are 74.'
```

使用大写字母 F 也是有效的：

```python
>>> F"Hello, {name}. You are {age}."
'Hello, Eric. You are 74.'
```





## Python 关键字

### is 关键字











# IO 编程

## 文件读写

读写文件是最常见的IO操作。Python内置了读写文件的函数，用法和C是兼容的。

读写文件前，我们先必须了解一下，在磁盘上读写文件的功能都是由操作系统提供的，现代操作系统不允许普通的程序直接操作磁盘，所以，读写文件就是请求操作系统打开一个文件对象（通常称为文件描述符），然后，通过操作系统提供的接口从这个文件对象中读取数据（读文件），或者把数据写入这个文件对象（写文件）。

### 读文件

要以读文件的模式打开一个文件对象，使用Python内置的`open()`函数，传入文件名和标示符：

```plain
>>> f = open('/Users/michael/test.txt', 'r')
```



标示符`'r'`表示读，这样，我们就成功地打开了一个文件。

如果文件不存在，`open()`函数就会抛出一个`IOError`的错误，并且给出错误码和详细的信息告诉你文件不存在：

```plain
>>> f=open('/Users/michael/notfound.txt', 'r')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
FileNotFoundError: [Errno 2] No such file or directory: '/Users/michael/notfound.txt'
```



如果文件打开成功，接下来，调用`read()`方法可以一次读取文件的全部内容，Python把内容读到内存，用一个`str`对象表示：

```plain
>>> f.read()
'Hello, world!'
```



最后一步是调用`close()`方法关闭文件。文件使用完毕后必须关闭，因为文件对象会占用操作系统的资源，并且操作系统同一时间能打开的文件数量也是有限的：

```plain
>>> f.close()
```



由于文件读写时都有可能产生`IOError`，一旦出错，后面的`f.close()`就不会调用。所以，为了保证无论是否出错都能正确地关闭文件，我们可以使用`try ... finally`来实现：

```python
try:
    f = open('/path/to/file', 'r')
    print(f.read())
finally:
    if f:
        f.close()
```



但是每次都这么写实在太繁琐，所以，Python引入了`with`语句来自动帮我们调用`close()`方法：

```python
with open('/path/to/file', 'r') as f:
    print(f.read())
```



这和前面的`try ... finally`是一样的，但是代码更佳简洁，并且不必调用`f.close()`方法。

调用`read()`会一次性读取文件的全部内容，如果文件有10G，内存就爆了，所以，要保险起见，可以反复调用`read(size)`方法，每次最多读取size个字节的内容。另外，调用`readline()`可以每次读取一行内容，调用`readlines()`一次读取所有内容并按行返回`list`。因此，要根据需要决定怎么调用。

如果文件很小，`read()`一次性读取最方便；如果不能确定文件大小，反复调用`read(size)`比较保险；如果是配置文件，调用`readlines()`最方便：

```python
for line in f.readlines():
    print(line.strip()) # 把末尾的'\n'删掉
```



### file-like Object

像`open()`函数返回的这种有个`read()`方法的对象，在Python中统称为file-like Object。除了file外，还可以是内存的字节流，网络流，自定义流等等。file-like Object不要求从特定类继承，只要写个`read()`方法就行。

`StringIO`就是在内存中创建的file-like Object，常用作临时缓冲。

### 二进制文件

前面讲的默认都是读取文本文件，并且是UTF-8编码的文本文件。要读取二进制文件，比如图片、视频等等，用`'rb'`模式打开文件即可：

```plain
>>> f = open('/Users/michael/test.jpg', 'rb')
>>> f.read()
b'\xff\xd8\xff\xe1\x00\x18Exif\x00\x00...' # 十六进制表示的字节
```



### 字符编码

要读取非UTF-8编码的文本文件，需要给`open()`函数传入`encoding`参数，例如，读取GBK编码的文件：

```plain
>>> f = open('/Users/michael/gbk.txt', 'r', encoding='gbk')
>>> f.read()
'测试'
```



遇到有些编码不规范的文件，你可能会遇到`UnicodeDecodeError`，因为在文本文件中可能夹杂了一些非法编码的字符。遇到这种情况，`open()`函数还接收一个`errors`参数，表示如果遇到编码错误后如何处理。最简单的方式是直接忽略：

```plain
>>> f = open('/Users/michael/gbk.txt', 'r', encoding='gbk', errors='ignore')
```



### 写文件

写文件和读文件是一样的，唯一区别是调用`open()`函数时，传入标识符`'w'`或者`'wb'`表示写文本文件或写二进制文件：

```plain
>>> f = open('/Users/michael/test.txt', 'w')
>>> f.write('Hello, world!')
>>> f.close()
```



你可以反复调用`write()`来写入文件，但是务必要调用`f.close()`来关闭文件。当我们写文件时，操作系统往往不会立刻把数据写入磁盘，而是放到内存缓存起来，空闲的时候再慢慢写入。只有调用`close()`方法时，操作系统才保证把没有写入的数据全部写入磁盘。忘记调用`close()`的后果是数据可能只写了一部分到磁盘，剩下的丢失了。所以，还是用`with`语句来得保险：

```python
with open('/Users/michael/test.txt', 'w') as f:
    f.write('Hello, world!')
```



要写入特定编码的文本文件，请给`open()`函数传入`encoding`参数，将字符串自动转换成指定编码。

细心的童鞋会发现，以`'w'`模式写入文件时，如果文件已存在，会直接覆盖（相当于删掉后新写入一个文件）。如果我们希望追加到文件末尾怎么办？可以传入`'a'`以追加（append）模式写入。

所有模式的定义及含义可以参考Python的[官方文档](https://docs.python.org/3/library/functions.html#open)。

### 练习

请将本地一个文本文件读为一个str并打印出来：

```python
fpath = '/etc/timezone'

with open(fpath, 'r') as f:
    s = f.read()
    print(s)

# 运行代码观察结果
```

### 小结

在Python中，文件读写是通过`open()`函数打开的文件对象完成的。使用`with`语句操作文件IO是个好习惯。





# 包管理

## pip 安装

```
C:\Windows\system32> C:\ProgramData\anaconda3\python.exe -m pip install --upgrade pip
```

这个命令是用于升级 pip 包管理器的。让我详细解释每个部分：
命令解析

- C:\ProgramData\anaconda3\python.exe - 指定使用 Anaconda 安装的 Python 解释器
- -m pip - 运行 pip 模块作为脚本
- install --upgrade pip - 安装并升级 pip 包到最新版本

为什么要用这种方式

1. 避免路径冲突：当系统中有多个 Python 安装时，确保使用正确的 pip
2. 权限问题：Anaconda 通常安装在系统目录中，需要明确指定路径
3. 环境一致性：确保 pip 和 Python 版本匹配



## 镜像源

问题原因
这是由于网络连接问题导致无法访问 PyPI 官方仓库，常见于某些网络环境下。

解决方案
**方法一：使用国内镜像源（推荐）**
根据你 README.md 中提供的地址，使用清华大学镜像源：

```py
pip3 install django==2.0.1 -i https://pypi.tuna.tsinghua.edu.cn/simple
```

或者配置默认镜像源：

```
pip3 config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
```

配置完成后，正常安装：

**方法二：临时跳过 SSL 验证（不推荐）**

```
pip3 install django==2.0.1 --trusted-host pypi.org --trusted-host pypi.python.org --trusted-host files.pythonhosted.org
```





然后可以通过以下命令安装所有依赖：

```
pip3 install -r requirements.txt
```

使用命令行参数指定镜像源

```
pip install -r requirements.txt -i https://pypi.tuna.tsinghua.edu.cn/simple
```



