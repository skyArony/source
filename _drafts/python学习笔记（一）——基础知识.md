---
title: python学习笔记（一）
date: 2018-02-13 00:13:04
tags:
---

初学编程的时候就学过 Python 了，当时写了几个模拟登陆获取成绩的接口，后来一直用的 PHP，过了这么久现在回想基本记不起了，可能当时写几个接口，学的是在太浅了。最近开始学习人工智能，需要用到 Python，就复习复习然后深入吧。本篇不能当做 python 学习的教程，只能用于本人快速翻阅。

# 一、学习准备
## 资料
- [廖雪峰老师的Python教程](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000)     很多代码样例参考自此
- [菜鸟教程-Python](http://www.runoob.com/python/python-tutorial.html)

## 环境
- 系统：Linux-[deepin](https://www.deepin.org/)
- Python环境配置：[Anaconda](https://www.anaconda.com/download/#linux)

注：deepin 中自带了 python2.7 和 python3.5 但是如果贸然修改或升级系统自带的 python 版本可能会导致系统问题，所以建议用 `Anaconda` 安装 python 的开发环境。

### 1. Anaconda 安装
[官方安装指导](https://docs.anaconda.com/anaconda/install/linux)

最后输入 `anaconda-navigator` 进入 [Anaconda Navigator](https://docs.anaconda.com/anaconda/navigator/)。

### 2. 设置国内镜像
```
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
conda config --set show_channel_urls yes
```

### 3. 新建一个环境
打开 `Anaconda Navigator`，在 `environments` 一栏中已经有一个 `root` 环境，这个环境是你下载是选择的 `Anaconda` 的版本，如果你选择的是 2.7 的版本那个这个环境的 python 版本就是 python 2.7 。

> 新建一个环境：点击下面 create ————> 输入环境名字 ————> 选择 python 版本 ————> 确定

**用命令行的方式新建一个环境**
```
# 创建一个名为python34的环境，指定Python版本是3.4（不用管是3.4.x，conda会为我们自动寻找3.4.x中的最新版本）
conda create --name python34 python=3.4

# 安装好后，使用activate激活某个环境
activate python34 # for Windows
source activate python34 # for Linux & Mac
# 激活后，会发现terminal输入的地方多了python34的字样，实际上，此时系统做的事情就是把默认2.7环境从PATH中去除，再把3.4对应的命令加入PATH

# 此时，再次输入
python --version
# 可以得到`Python 3.4.5 :: Anaconda 4.1.1 (64-bit)`，即系统已经切换到了3.4的环境

# 如果想返回默认的python 2.7环境，运行
deactivate python34 # for Windows
source deactivate python34 # for Linux & Mac

# 删除一个已有的环境
conda remove --name python34 --all
```

### 4. conda 的包管理命令
```
# 安装scipy
conda install scipy
# conda会从从远程搜索scipy的相关信息和依赖项目，对于python 3.4，conda会同时安装numpy和mkl（运算加速的库）

# 查看已经安装的packages
conda list
# 最新版的conda是从site-packages文件夹中搜索已经安装的包，不依赖于pip，因此可以显示出通过各种方式安装的包

# 查看当前环境下已安装的包
conda list

# 查看某个指定环境的已安装包
conda list -n python34

# 查找package信息
conda search numpy

# 安装package
conda install -n python34 numpy
# 如果不用-n指定环境名称，则被安装在当前活跃环境
# 也可以通过-c指定通过某个channel安装

# 更新package
conda update -n python34 numpy

# 删除package
conda remove -n python34 numpy

# 更新conda，保持conda最新
conda update conda

# 更新anaconda
conda update anaconda

# 更新python
conda update python
# 假设当前环境是python 3.4, conda会将python升级为3.4.x系列的当前最新版本
```

### 5. Pycharm 中使用 Anaconda
> File ——> settings ————> Project Interpreter ————> Add Local ————> 添加 Anaconda 环境（这里你命名一个，就会自动在 Anaconda 中新建一个环境）

### 6. VS code 中使用 Anaconda
- 安装扩展中安装量最高的扩展
- 输入命令 `Python: Select Interpreter` 选择 Anaconda 配置的环境

### 7. 激活环境
```
// 激活环境
source activate py364

// 进入 python 交互模式
python
```

### 8. [详细教程](https://www.zhihu.com/question/58033789)


# 二、如何运行一个程序
## 交互模式
交互模式下，每一行变成如下的样子：`>>> `
```
// 交互模式下简单运行
>>> 100 + 200
300
```

## 命令行执行文件
和其他脚本一样
```
// cd 到该目录下
$ python hello.py
```

# 三、代码编写

## a) 语法表示
1. 使用 `#` 作为注释符，且可以不用分号来结束一行；
2. 使用 `:` 作为代码块开始，并以后面缩进的内容为代码块内容；
3. 浮点数的科学计数法： $1.23\times10^9$ 就是 `1.23e9` ，或者 `12.3e8`，0.000012 可以写成 `1.2e-5`;
4. 可以在字符串前面加上 `r`, 表示默认不转义,例如 `print(r'\\\t\\')`,输出 `\\\t\\`;
5. 多行字符串: 可以用 `'''第一行内容[换行]第二行内容[换行]第三行内容'''` 来表示多行内容;
6. 布尔值: `True` 、`False` (注意首字母大写);
7. 布尔运算: `and` (与: 全真为真)、`or` (或: 存真为真) 和 `not` (非);
8. 空值: `None`;
9. 常量: 全大写为常量,比如 `PI = 3.1415926535`, 这并不像其他语言一样是锁死的,你可以改变;
10. 除法: python 中有两种除法,**除法`/`**和**地板除`//`**，除法无论能不能整除都会取浮点数，地板除无论能不能整除都会取整数；
11. python 文件的字符编码
    
    ```python
    #!/usr/bin/env python3
    # -*- coding: utf-8 -*-
    ```
    
    第一行告诉Linux/OS X系统，这是一个Python可执行程序，Windows系统会忽略这个注释；
    第二行告诉Python解释器，按照UTF-8编码读取源代码，否则，你在源代码中写的中文输出可能会有乱码；
12. 占位符：和C语言类似，格式为：`'Hi, %s, you have $%d.' % ('Michael', 1000000)`，一个占位符时，括号可以省略，另外可以两个 `%%` ，表示一个 `%`；
13. 用 `elif` 而不是 `else if`；
14. 用 `input()` 请求和读取用户的输入，返回字符串，python 中不能把整型和字符串进行比较；
15. 循环语句：

    ```python
    # 例1
    names = ['Michael', 'Bob', 'Tracy']
    for name in names:
        print(name)
    
    #例2
    sum = 0
    for x in range(101):
        sum = sum + x
    print(sum)
    
    >>> list(range(5))
    [0, 1, 2, 3, 4]

    # 例3
    n = 1
    while n <= 100:
        if n > 10:          # 当n = 11时，条件满足，执行break语句
            break           # break语句会结束当前循环
        if n % 2 == 0:      # 如果n是偶数，执行continue语句
            continue        # continue语句会直接继续下一轮循环，后续的print()语句不会执行
        print(n)
        n = n + 1
    print('END')
    ```
16. `str` 是不可变对象

    ```
    >>> a = 'abc'
    >>> b = a.replace('a', 'A')
    >>> b
    'Abc'
    >>> a
    'abc'
    ```
17. 可以把函数名赋给一个变量

    ```
    >>> a = abs     # 变量a指向abs函数
    >>> a(-1)       # 所以也可以通过a调用abs函数
    1
    ```
18. 函数的定义：需要用到 `def`：

    ```
    def my_abs(x):
        return abs(x)           # return None 可以简写为 return，若不写 return 则默认当做 return None
        return x, x+1           # 可以同时返回多个值，实际上是返回了一个 tuple
    
    from abstest import my_abs  # 从 abstest.py 文件中导入 my_abs 函数
    
    if age >= 18:
        pass                    # 可以用 pass 作为空占位符而避免代码出错
    ```
19. 函数的可变参数：

    ```
    def calc(*numbers):     # 在参数前面加上一个 * 号，表示可以传入任意个参数
        sum = 0
        for n in numbers:
            sum = sum + n * n
        return sum
    
    numbers = [1, 2, 3]     # 就算 numbers 是一个 list 或 tuple
    cacl(*numbers)          # 也可以这么调用
    ```

20. 关键字参数：

    ```
    def person(name, age, **kw):
        print('name:', name, 'age:', age, 'other:', kw)
        
    >>> extra = {'city': 'Beijing', 'job': 'Engineer'}
    >>> person('Jack', 24, city=extra['city'], job=extra['job'])
    name: Jack age: 24 other: {'city': 'Beijing', 'job': 'Engineer'}
    
    >>> extra = {'city': 'Beijing', 'job': 'Engineer'}
    >>> person('Jack', 24, **extra)
    name: Jack age: 24 other: {'city': 'Beijing', 'job': 'Engineer'}
    ```
21. 命名关键字参数

## b) 数据类型

### 1. python 中的“数组”

**list：**
```python
p = ['asp', 'php']
p[-1]               # 倒着取元素
L = []              # 空list
len()               # 长度
pop([i])            # 取末尾元素，或指定元素
append()            # 插入到末尾
insert(1, 'Jack')   # 插入到指定位置
```

**tuple：**
相当于赋值后就无法改变值的 list
```python
t = (1, 2)                  # 赋值用括号
t = (1,)                    # 单个元素时，要这么写
t = ('a', 'b', ['A', 'B'])  # 可变的 tuple
```

**dict：**
相当于键值数组
```python
d = {'M': 95, 'B': 75, 'T': 85}     # 定义一个 dict
d['M']                              # 取对应的值，注意 d[0] 会报错
d['Thomas']                         # 取不存在的会报错
'Thomas' in d                       # 判断是否存在，返回布尔值
d.get('Thomas', [-1])               # 存在则返回值，不存在则返回None(并不显示)，或指定的值
```

**set：**
一组key的集合，但不存储value，且key不能重复
```
>>> s = set([1, 2, 3])
>>> s
{1, 2, 3}
```
```python
add(4)      # 添加
remove(4)    # 移除
```

## c) 高级特性

### 1. 切片
可以用来截取 `list` 或 `tuple` 的元素。

```python
L = ['Michael', 'Sarah', 'Tracy', 'Bob', 'Jack']
L[0:3]      # 索引 0 开始，到索引 3 为止，但不包括索引 3
L[:3]       # 如果第一个索引是 0 ，可以省略 0
L[-2:]      # 取倒数两个元素
L[-2:-1]    # 取倒数一个元素
L[:10:2]    # 前十个数，每两个取一个
L[::5]      # 所有数，每五个取一个
L[:]        # 原样复制一个
(0, 1, 2, 3, 4, 5)[:3]      # tuple 的截取
```

### 2. 迭代
可以用 Collection 模块的 Iterable 类型来判断该数据类型是否可以用 `for ... in ...` 迭代。

```
>>> from collections import Iterable
>>> isinstance('abc', Iterable)         # str是否可迭代
true
>>> isinstance(x, str)                  # 判断 x 是不是字符串
```

可以用内置函数 enumerate 把一个list变成索引-元素对
```
for i, value in enumerate(['A', 'B', 'C']):     # i 为索引，value 为值
```

### 3. 列表生成式
可以非常方便的用一行代码生成列表

```
>>> [x * x for x in range(1, 11)]
[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]

>>> [x * x for x in range(1, 11) if x % 2 == 0]     # 还可以加上 if 判断
[4, 16, 36, 64, 100]

>>> [m + n for m in 'ABC' for n in 'XYZ']           # 两层循环
['AX', 'AY', 'AZ', 'BX', 'BY', 'BZ', 'CX', 'CY', 'CZ']
```

### 4. 生成器 Generator
把列表生成器的 `[]` 改成 `()` 即可，调用可以用 `next(g)` 或循环，建议用循环，因为 next 到了最后一个会报错。

```
>>> g = (x * x for x in range(10))
>>> g
<generator object <genexpr> at 0x1022ef630>
```

还可以用函数的方式构造生成器

```python
def fib(max):
    n, a, b = 0, 0, 1
    while n < max:
        yield b             # 一个函数中如果含有 yield ，那么它就是一个生成器
        a, b = b, a + b
        n = n + 1
    return 'done'
    
f = fib(6)                  # 这样得到一个生成器对象
```

### 5. 迭代器 Iterator
可以被 `next()` 函数不断调用并返回下一个值的对象,判断一个对象是不是迭代器：

```
>>> isinstance((x for x in range(10)), Iterator)    # 生成器都是迭代器对象
True
```

可以用 `iter()` 函数把`list`、`dict`、`str`等 `Iterable` 变成`Iterator`：


```
>>> isinstance(iter([]), Iterator)
True
```

## d) 函数

### 1. 函数名
函数名其实是个变量，可以赋值给其他的变量，甚至让函数名这个变量指向其他值，二使函数失效。可以通过 `__name__` 属性获得函数名字。

### 2. map/ reduce / filter / sorted
**map**
```python
r = map(函数名, Iterable)   # 循环 Iterable 中的值让函数名所指的函数处理并返回一个 Iterator（惰性）
list(r)                     # 最后会把 Iterator 处理完并返回一个 list

# 例子
>>> list(map(str, [1, 2, 3, 4, 5, 6, 7, 8, 9]))
['1', '2', '3', '4', '5', '6', '7', '8', '9']
```

**reduce**
```python
reduce(f, [x1, x2, x3, x4]) = f(f(f(x1, x2), x3), x4)   # 这里的等于符号是“等价于”的意思

# 一个样例，看懂就理解了，lambda 可以理解成 python 中匿名函数的写法 
from functools import reduce

DIGITS = {'0': 0, '1': 1, '2': 2, '3': 3, '4': 4, '5': 5, '6': 6, '7': 7, '8': 8, '9': 9}

def char2num(s):
    return DIGITS[s]

def str2int(s):
    return reduce(lambda x, y: x * 10 + y, map(char2num, s))
```

**filter**
用于过滤序列，参数和返回都与 map 类似，不同之处在于：filter 根据返回值布尔属性来决定元素的去留。

```python
def is_odd(n):
    return n % 2 == 1

list(filter(is_odd, [1, 2, 4, 5, 6, 9, 10, 15]))
# 结果: [1, 5, 9, 15]
```
注意到filter()函数返回的是一个Iterator，也就是一个惰性序列，所以要强迫filter()完成计算结果，需要用list()函数获得所有结果并返回list。

**sorted**
sorted 能够让你用非常简单的代码实现较为复杂的逻辑

```
>>> sorted([36, 5, -12, 9, -21])            # 普通排序
[-21, -12, 5, 9, 36]

>>> sorted([36, 5, -12, 9, -21], key=abs)   # 添加函数处理
[5, 9, -12, -21, 36]

>>> sorted(['bob', 'about', 'Zoo', 'Credit'], key=str.lower)    # 添加函数处理2
['about', 'bob', 'Credit', 'Zoo']

>>> sorted(['bob', 'about', 'Zoo', 'Credit'], key=str.lower, reverse=True)  # 逆序
['Zoo', 'Credit', 'bob', 'about']
```

### 3. 返回函数
可以把函数作为返回值，在需要的时候再执行：

```python
def lazy_sum(*args):
    def sum():
        ax = 0
        for n in args:
            ax = ax + n
        return ax
    return sum
    
>>> f = lazy_sum(1, 3, 5, 7, 9)
>>> f
<function lazy_sum.<locals>.sum at 0x101c6ed90>

>>> f()
25
```

**闭包**
返回一个函数时，牢记该函数并未执行，返回函数中不要引用任何可能会变化的变量。

### 4. 装饰器 Decorator
在代码运行期间动态增加功能的方式。理解有点复杂，建议看[原文](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/0014318435599930270c0381a3b44db991cd6d858064ac0000)。

### 5. 偏函数
可以设定参数的默认值，降低函数调用的难度。[原文](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/00143184474383175eeea92a8b0439fab7b392a8a32f8fa000)

## e) 模块
模块第一个需要解决的问题是冲突问题，python 中用模块和顶层包名的方法来防止冲突。一个顶层报名和模块的结构如下：

```
mycompany
 ├─ web
 │  ├─ __init__.py
 │  ├─ utils.py
 │  └─ www.py
 ├─ __init__.py  # 这个是必须存在的文件，否则这个目录就会被当做普通目录，且其本身为一个模块，名为mycompany
 ├─ abc.py
 └─ xyz.py
```

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-

' a test module '       # 任何模块的第一个字符串为模块注释，可用 __doc__ 访问

__author__ = 'Michael Liao'

import sys              # 导入一个模块

def test():
    args = sys.argv
    if len(args)==1:
        print('Hello, world!')
    elif len(args)==2:
        print('Hello, %s!' % args[1])
    else:
        print('Too many arguments!')

if __name__=='__main__':    # 这一行如果直接执行本文件则会运行，如果当做模块被导入不会运行
    test()
```

**作用域**
private 变量命名方式：`_xxx` 和 `__xxx` ，但这并不是限定死的，你完全可以应用而不报错，但最好不要。

**第三方模块**
python 用 `pip` 进行包管理：

```
pip install Pillow  # 第三方包一般会在官网上注册，这样就可以这用直接引用
```

## d) 面向对象编程
**class的定义**
`Class` 的定义：括号中的 `object` 表示该类是从哪个类继承下来的，如果没有合适的类，就继承 `object`。

```python
class Student(object):
    def __init__(self, name, score):    # 可以这样定义必须填写的属性
        self.name = name
        self.__score = score            # 可以在前面加上 __ ，这样就是一个 private 变量， 外部无法访问
    
    def print_score(self):              # 数据封装
        print('%s: %s' % (self.name, self.__score))
        
s = Student()
s.age = 18          # python 可以这样自由定义属性
```

**python**语言的“[鸭子类型](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/001431865288798deef438d865e4c2985acff7e9fad15e3000#0)”特性。

**type()**
可以用这个函数来获取数据对象的类型：

```
>>> type(123)
<class 'int'>
>>> type('str')
<class 'str'>
>>> type(None)
<type(None) 'NoneType'>
```

**isinstance()**
也可以用这个函数来判断数据对象的类型
```
object -> Animal -> Dog -> Husky
>>> a = Animal()
>>> d = Dog()
>>> h = Husky()

>>> isinstance(h, Husky)
True

>>> isinstance([1, 2, 3], (list, tuple))
True
>>> isinstance((1, 2, 3), (list, tuple))
True
```

**dir() \ getattr() \ setattr() \ hasattr()**
可以用 `dir()` 函数返回一个 list ，包含一个数据对象的所有属性和方法：

```
>>> dir('ABC')
['__add__', '__class__',..., '__subclasshook__', 'capitalize', 'casefold',..., 'zfill']
```

其他三个方法可以用于判断和设置数据对象的属性：

```
>>> hasattr(obj, 'x') # 有属性'x'吗？
True
>>> obj.x
9
>>> hasattr(obj, 'y') # 有属性'y'吗？
False
>>> setattr(obj, 'y', 19) # 设置一个属性'y'
>>> hasattr(obj, 'y') # 有属性'y'吗？
True
>>> getattr(obj, 'y') # 获取属性'y'
19
>>> obj.y # 获取属性'y'
19
>>> del obj.y # 删除实例的属性

# 可以传入一个default参数，如果属性不存在，就返回默认值
>>> getattr(obj, 'z', 404) # 获取属性'z'，如果不存在，返回默认值404
404
```

**类属性**
定义在方法之外，class 之内的叫做类属性：

```
>>> class Student(object):
...     name = 'Student'
...
>>> s = Student() # 创建实例s
>>> print(s.name) # 打印name属性，因为实例并没有name属性，所以会继续查找class的name属性
Student
>>> print(Student.name) # 打印类的name属性
Student
>>> s.name = 'Michael' # 给实例绑定name属性
>>> print(s.name) # 由于实例属性优先级比类属性高，因此，它会屏蔽掉类的name属性
Michael
>>> print(Student.name) # 但是类属性并未消失，用Student.name仍然可以访问
Student
>>> del s.name # 如果删除实例的name属性
>>> print(s.name) # 再次调用s.name，由于实例的name属性没有找到，类的name属性就显示出来了
Student
```

## e) 高级面向对象编程

### 1. 给实例或类动态绑定方法

```
>>> def set_age(self, age): # 定义一个函数作为实例方法
...     self.age = age
...
>>> from types import MethodType
>>> s.set_age = MethodType(set_age, s) # 给实例绑定一个方法
>>> s.set_age(25) # 调用实例方法
>>> s.age # 测试结果
25

>>> def set_score(self, score):
...     self.score = score
...
>>> Student.set_score = set_score
```

### 2. __slots__
限制实例的属性：

```
class Student(object):
    __slots__ = ('name', 'age') # 用tuple定义允许绑定的属性名称，这是一个白名单
    
# 以上定义后 Student 的实例只能拥有 tuple 中的属性，其他属性在定义时会报错
# 子类可以定义自己的 __slots__ ，这样实例同时可以定义子类加上父类开放的属性
```

### 3. @property 装饰器
可以简化 `setter` 和 `getter` ，具体看[这](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/00143186781871161bc8d6497004764b398401a401d4cce000)

简单理解，值在属性上面放一个 `@property` 就是只读。

### 4. 多重继承
写法：

```
class Bat(Mammal, Flyable):
    pass
```

### 5. 定制类
[具体看这](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/0014319098638265527beb24f7840aa97de564ccc7f20f6000#0)，定制类可以让 python 的函数具有极大的可定制性，你可以用定制类重新定义函数的基本底层。

### 6. 枚举类
[具体看这](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/00143191235886950998592cd3e426e91687cdae696e64b000)
便于一些关系常量的统一定义。

### 7. [元类](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/0014319106919344c4ef8b1e04c48778bb45796e0335839000)
可以改变类创建时的行为。

## 四、其他

### 1. 错误、调试和测试

**try...except...finally...错误处理机制**
`try` 中的代码块会在发生错误的一行停止执行；
`except` 中的代码块会在错误发生时执行，因为错误可能有多种，所以这个代码块也可能有多个；
`else` 会在没有任何错误发生时执行；
`finally` 可写可不写，但是其中的代码块一定会执行；

```python
import logging

try:
    print('try...')
    r = 10 / int('2')
    print('result:', r)
except ValueError as e:
    print('ValueError:', e)
except ZeroDivisionError as e:
    print('ZeroDivisionError:', e)
except Exception as e:
    logging.exception(e)    # 记录错误到日志
else:
    print('no error!')
finally:
    print('finally...')
print('END')
```

[常见错误及其继承关系](https://docs.python.org/3/library/exceptions.html#exception-hierarchy)

**调试**
调试的方式主要有以下几种：
- print
- 断言（assert）
- logging：输出到日志 ———— 推荐
- pdb：单步调试
- pdb.set_trace()：相当于调试中的断点

**[单元测试](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/00143191629979802b566644aa84656b50cd484ec4a7838000)**
对一个模块、一个函数或者一个类来进行正确性检验的测试工作。我个人理解是，可以把一些特殊的样例较为周全的写到单元测试的代码里面，以减少每一次修改代码后需要手动输入进行测试的数据。通过测试的代码不一定没有bug。

**[文档测试](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/0014319170285543a4d04751f8846908770660de849f285000)**
以注释的形式写在代码里，作用和单元测试类似，有一个很重要的作用就是可以用来自动生成代码。

### 2. I\O编程————磁盘文件读写

**文件读**

基本操作

```python
f = open('/Users/michael/test.txt', 'r')    # 得到一个流，默认编码 UTF-8
f.read()                                    # 读到内存
f.close()                                   # 关闭（这是必须的）

read(size)                                  # 每次最多读取 size 字节的内容
readline()                                  # 每次读一行
readlines()                                 # 一次读取所有内容并按行返回 list

f = open('/Users/michael/test.jpg', 'rb')   # 图片、视频等二进制文件，用'rb'模式打开文件即可
f = open('/Users/michael/gbk.txt', 'r', encoding='gbk') # 其他编码的文件
f = open('/Users/michael/gbk.txt', 'r', encoding='gbk', errors='ignore')    # 忽略读取文件中的编码错误
```

错误处理

```python
try:
    f = open('/path/to/file', 'r')
    print(f.read())
finally:
    if f:
        f.close()
```    

错误处理的一种简便写法

```python
with open('/path/to/file', 'r') as f:
    print(f.read())
```

**文件写**

基本操作

```python
>>> f = open('/Users/michael/test.txt', 'w')
>>> f.write('Hello, world!')
>>> f.close()
```

简便写法

```python
with open('/Users/michael/test.txt', 'w') as f:
    f.write('Hello, world!')
```

[完整文档](https://docs.python.org/3/library/functions.html#open)

### 3. I\O编程————内存读写

**StringIO 和 BytesIO**
```
>>> from io import StringIO
>>> f = StringIO()
>>> f.write('hello')
5
>>> f.write(' ')
1
>>> f.write('world!')
6
>>> print(f.getvalue())
hello world!

>>> from io import StringIO
>>> f = StringIO('Hello!\nHi!\nGoodbye!')
>>> while True:
...     s = f.readline()
...     if s == '':
...         break
...     print(s.strip())
...
Hello!
Hi!
Goodbye!
```

### 3. 操作文件或目录

```
# 查看当前目录的绝对路径:
>>> os.path.abspath('.')
'/Users/michael'

# 在某个目录下创建一个新目录，首先把新目录的完整路径表示出来:
>>> os.path.join('/Users/michael', 'testdir')
'/Users/michael/testdir'

# 然后创建一个目录:
>>> os.mkdir('/Users/michael/testdir')

# 删掉一个目录:
>>> os.rmdir('/Users/michael/testdir')

---

# 两个路径的合并，这样可以正确处理不同系统下的路径
os.path.join()

# 路径拆解
>>> os.path.split('/Users/michael/testdir/file.txt')
('/Users/michael/testdir', 'file.txt')

# 拆解扩展名
>>> os.path.splitext('/path/to/file.txt')
('/path/to/file', '.txt')

---

# 对文件重命名:
>>> os.rename('test.txt', 'test.py')
# 删掉文件:
>>> os.remove('test.py')
# 但是 os 中没有复制函数，可 shutil 模块提供了copyfile()的函数，你还可以在shutil模块中找到很多实用函数，它们可以看做是os模块的补充

---

# 过滤
>>> [x for x in os.listdir('.') if os.path.isdir(x)]
['.lein', '.local', '.m2', '.npm', '.ssh', '.Trash', '.vim', 'Applications', 'Desktop', ...]

>>> [x for x in os.listdir('.') if os.path.isfile(x) and os.path.splitext(x)[1]=='.py']
['apis.py', 'config.py', 'models.py', 'pymonitor.py', 'test_db.py', 'urls.py', 'wsgiapp.py']
```

### 4. Json 序列化

**序列化**

```
>>> import json
>>> d = dict(name='Bob', age=20, score=88)
>>> json.dumps(d)
'{"age": 20, "score": 88, "name": "Bob"}'

# dump()方法可以直接把JSON写入一个file-like Object
```

**反序列化**

```
>>> json_str = '{"age": 20, "score": 88, "name": "Bob"}'
>>> json.loads(json_str)
{'age': 20, 'score': 88, 'name': 'Bob'}

# JSON反序列化为Python对象，用loads()或者对应的load()方法，前者把JSON的字符串反序列化，后者从file-like Object中读取字符串并反序列化
```

**对象的 Json 化**

```
def student2dict(std):
    return {
        'name': std.name,
        'age': std.age,
        'score': std.score
    }
    
>>> print(json.dumps(s, default=student2dict))
{"age": 20, "name": "Bob", "score": 88}
```

**Json 的对象化**

```
def dict2student(d):
    return Student(d['name'], d['age'], d['score'])
    
>>> json_str = '{"age": 20, "score": 88, "name": "Bob"}'
>>> print(json.loads(json_str, object_hook=dict2student))
<__main__.Student object at 0x10cd3c190>
```

---

## 五、其他特性
后面的特性我只需要知道有这个东西和它在其他语言中不同的地方，等到需要用的时候再来具体看。大概看遍，这里只记标题和觉得值得记得地方。

## 1. 进程和线程
- 多核多线程在python中不可用（当然也不是没有办法）

## 2. 协程
异步I\O

## 3. 正则
可以先预编译，提高效率。

## 4. 常用内建模块
- datatime

- collections
    - deque
        增删效率高于 list

    - defaultdict 
        使用dict时，如果引用的Key不存在，就会抛出KeyError。如果希望key不存在时，返回一个默认值，就可以用defaultdict

    - OrderedDict
        使用dict时，Key是无序的。在对dict做迭代时，我们无法确定Key的顺序。如果要保持Key的顺序，可以用OrderedDict。

    - Counter
        Counter是一个简单的计数器，例如可以统计字符出现的个数。

- base64
    处理字节。

- hashlib
    常见的摘要算法。

- hmac
    可以理解为加盐哈希。

- itertools
    提供处理迭代功能的函数。

- contextlib
    上下文管理，在一段代码之前先执行什么，在一段代码执行完后执行什么。

- urllib
    一系列用于操作URL的功能。可以用于抓取网页内容，模拟请求。

- XML

- HTMLParser
    解析HTML。

## 5. 常用第三方模块

- Pillow
    图像处理。

- requests
    相当于第三方的urllib，有更多高级功能，更好用。

- chardet
    检测bytes的编码，并转化成str。

- psutil
    运维好伙伴，用于获取系统信息

## 6. virtualenv
隔离不用版本环境的 python。

## 7. 图形界面
- Tk
- wxWidgets
- Qt
- GTK

## 8. 网络编程

## 9. 电子邮件

## 10. 数据库访问

## 11. Web开发
WCGI

## 12. 异步IO