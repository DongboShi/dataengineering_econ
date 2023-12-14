\mainmatter

# Python基础 {#py}

> 你总是给自己设置障碍，因为你不敢。*---邪不压正*

## Python简介

本章我们介绍`Python`的基础知识。在这之前，读者可能会好奇，为什么又多了一个语言？我们已经学习了`R语言`和`SQL语言`，接触了`bash`，在未来还要学习`html语言`和`正则表达式`。之所以学习这么多语言是因为数据工程中涵盖了多种多样的任务，针对不同的任务往往有不同的最佳工具。与其追求在一个语言中费劲完成

正如在第三章介绍的，尽管`R语言`非常强大，但是它毕竟不是一个通用语言，在很多任务上，效率并不高。例如，绝大多数的`API`都使用的是`Python`语法，这就极大限制了`R语言`的应用场景。因此，我们需要学习一门通用语言，

`Python`也是一门解释性语言，对于编译型语言如`C/C++`它更容易调试。`Python`的语法如`R语言`一般简洁易懂。`Python`是一个通用语言可，在生活中的方方面面都可以使用。

`Python`的软件库丰富，可以完成非常多其他的功能。`Python`可以将跨语言的程序粘合在一起，是天生的“胶水语言”，非常适合跨语言协作。

由于以上优点，`Python`成为当前最流行的编程语言。`Python`的出现大大降低了编程的门槛，程序员供给数量指数增多。

更重要的是，学习编程语言在总收益不变的情况下，边际成本是断崖式下降的，假设大家在学习`R语言`时候用了100%的努力，在学习`Python`时只用付出25%的努力，就可以快速掌握`Python`，但是编程能力和视野会翻倍。在未来深入学习`bash`时，只用付出10%的努力即可。不同的编程语言更是代表了不同的世界观，在增加技能的同时，让我们的用于更多看待世界的角度。

## Python安装与Pycharm

### 安装
`Python`存在两个版本主要版本，`Python2`和`Python3`。`Python3`是针对`Python2`的一次大规模改进，以至于很多`Python2`的程序无法在`Python3`中运行，因此可以将两者看做是不同的语言，以避免混淆出错。

Windows用户在WSL环境中使用`apt`命令安装`python3`。

```
apt install python3
```

R用户可以通过`brew`或者在`Gentoo prefix`中的`merge`命令安装。

```
brew install python3
```

### 编辑器

`Python`有一个增强式编译环境，`Ipython`可用于交互式编程。还提供了`Jupyter Notebook`，是`IPython`发展而来的可以在网页上进行交互式编程的环境。它的交互性更强，可以规避掉命令行，上手容易，适合写教程。


但是，网页并不能替代编辑器，不适合写大段程序，也没有批量处理能力。如果是只是学会了`Jupyter`就以为自己学会了编程，到处招摇过市，会暴露自己的无知与傲慢。因此，不推荐大家将其作为编辑器使用。


### Pycharm的安装与教育许可证

我们推荐`Pycharm Professional`作为`Python`的编辑器（[下载地址](https://www.jetbrains.com/pycharm/download/?section=mac)），这是一款不逊色于`Rstudio`的编辑器，且为教育机构用户提供免费的权限。`Pycharm`不仅继承了语法高亮、自动补全以及版本控制等基本功能，更是集合了远程服务器编译功能，让远程调试非常便捷，这是一个`Rstudio`都没有的功能。同时，`Pycharm`也支持跨语言编辑。

安装之后请在<https://www.jetbrains.com/shop/eform/students>申请教育许可证。建议使用“官方文件”认证的方式。

<img src="images/pybasic/pycharm.png" width="90%" />

打开`Pycharm`的第一步是新建项目，新建项目是要确定项目路径与编译代码的`Python`路径。Windows系统读者注意要使用WSL环境中的`python`，具体配置方法见视频。

<img src="images/pybasic/pycharm2.png" width="90%" />

这里对应的习惯是一个研究项目对应一个路径，其实这样的习惯在`R`中也应该学习。

`Pycharm`的窗口环境如下：

<img src="images/pybasic/pycharm3.png" width="90%" />

此外`Rstudio`也是可以兼容python的，使用`reticulate::repl_python()`即可将`Console`的编译环境变成`python`环境。在做一些小命令尝试的时候，可以直接在Rtudio中编程，而不需要特别转换工作环境。毕竟，任何环境的转换都是需要成本的。

## 变量

`Python`变量使用`=`赋值与修改，变量既可以是最基本的数据类型，也可以是复杂的数据结构，甚至是新的类实体。`print`函数可以将变量输出到屏幕。


```python
a = "hello world"
print(a)
```

```
## hello world
```

## 数据类型

`Python`有五种数据类型：整型（精度无限的整数）、浮点型（64位高精度小数）、字符串和`None`。字符串引号（单引号或双引号）标注、布尔型（`Ture`和`False`）。

`None`是`Python`中一个特别的值，即不是整数也不是浮点数，属于一种独有的类型。可以代表很多含义：“空”或者“没有”，或者“无法表达”，或者“出错了”、“非法”。转换为布尔类型时，None 的赋值为“假”。

`type`函数可以查看的数据类型。


```python
type(1)
```

```
## <class 'int'>
```

```python
type(3.14)
```

```
## <class 'float'>
```

```python
type("hello world")
```

```
## <class 'str'>
```

```python
type(True)
```

```
## <class 'bool'>
```

```python
type(None)
```

```
## <class 'NoneType'>
```

### 类型转换

`int`、`float`、`str`和`bool`函数可以转换数据类型。与`R`一样，不是所有类型都可以互相转换的，转换不及会报错。

### 算术运算

`+-*/`用于四则运算，幂运算使用两个星号，`//`表示整除，`%`为取余数。


```python
3 + 3
```

```
## 6
```

```python
3 - 3
```

```
## 0
```

```python
3 * 3
```

```
## 9
```

```python
3 / 2
```

```
## 1.5
```

```python
3 ** 6
```

```
## 729
```

```python
3 // 2
```

```
## 1
```

```python
4 % 2
```

```
## 0
```

更复杂的运算，可以导入`math`模块，例如阶乘。


```python
import math
math.factorial(10)
```

```
## 3628800
```

注意，在`Python`里面整数是高精度的，具体多少精度，取决于计算机的硬件水平。`Python`会自行做出判断，用计算效率损失来换取人类在编程时候的方便。这样的恰恰适合经济学家，如果能够节省人类的时间，不惜浪费计算机的时间；通过升级硬件来节约计算机的时间。

### 布尔运算

`and`，`or`，`not`是`Python`中的“与或非”运算。


```python
True and False
```

```
## False
```

```python
True or False
```

```
## True
```

```python
not True
```

```
## False
```
`<`、`<=`、`>`、`>=`、`==`，`!=`用于数值比较，生成布尔型。


```python
3 > 2
```

```
## True
```

```python
2 != 2
```

```
## False
```

在`Python`内部，布尔型被储存成整数型的0和1，因此可进行算术运算。


```python
2 + True
```

```
## 3
```

```python
2 > False
```

```
## True
```

### 字符串

`Python`有非常强大的字符串工具库，针对单个字符串，`Python`开发了非常强大的属性与操作。很多操作让R用户眼睛瞪得像铜铃。例如，用加法和乘法，定义字符串的拼接与重复。


```python
"3.14" + "15926"
```

```
## '3.1415926'
```

```python
"重要的话说三次！"*3
```

```
## '重要的话说三次！重要的话说三次！重要的话说三次！'
```
`len`函数可以得到字符串的长度。


```python
len("3.1415")
```

```
## 6
```

### 字符串的替换

`.format`函数可以实现灵活的字符串替换，例如，


```python
"我感觉{}还需要{}，你们毕竟还是{}，你明白这意思吧？我告诉你们我是{}了，见得多了，西方哪一个{}我没有去过？你们要知道，美国的{}，比你们不知道要{}到哪里去了，我跟他{}。".format("你们新闻界","学习","too young","身经百战","国家","华莱士","高","谈笑风生")
```

```
## '我感觉你们新闻界还需要学习，你们毕竟还是too young，你明白这意思吧？我告诉你们我是身经百战了，见得多了，西方哪一个国家我没有去过？你们要知道，美国的华莱士，比你们不知道要高到哪里去了，我跟他谈笑风生。'
```

其中，变的部分用`{}`表示，`.format`中的参数与前面的`{}`一一对应。

`f-string`可以实现同样的功能，在数据输出时经常使用。


```python
a = 3
b = 5
f"{a}乘以{b}等于{a*b}"
```

```
## '3乘以5等于15'
```


### 字符串的切片

直接用`[]`就可以取出字符串的子集，注意`Python`从0开始编号，且使用左闭右开区间，跨语言使用时别出错。


```python
a = "hello world"
a[0],a[2:4]
```

```
## ('h', 'll')
```

### 标准字符串操作

`Python`的官方文档详尽介绍了标准库中字符串的进阶操作，推荐大家自己学一遍。我们介绍一些常用操作。

|操作|作用|
|:----|:------------|
|`.count`|统计字符出现的次数|
|`.startwith`|判断字符串是否由某个子串开头|
|`.split`|将字符串按照给定的分隔符分成列表|
|`.replace`|一一替换字符|


```python
seed = bin(2324)
print(seed)
```

```
## 0b100100010100
```

```python
print(seed.replace('0',"奥").replace('1',"利"))
```

```
## 奥b利奥奥利奥奥奥利奥利奥奥
```

## 标准输入输出

### 标准输入

`input`函数可以键盘得到输入。


```python
x = input()
```
标准输入默认输入数据类型为字符串，因此，当需要数字时，需要进行类型转换。标准输入使程序可与外界交互，输入信息被赋给了变量。输入可以由人给出，也可以由其它程序给出。

### 命令执行

`Python`默认一个命令独立成行，如果需要多个命令写在一行中，需要用分号连接不同的命令。

## 数据结构

把基本数据类型组合起来可以构成复杂的数据结构。`Python`数据结构包括列表、元组和字典。

### 列表

列表用`[]`表达，其中的元素用`,`分割。

```python
lt = ["a","c",2]
```

#### 添加

生成空列表，使用`.append()`方法逐步加入元素，注意每次只能加一个


```python
lt.append(1)
print(lt)
```

```
## ['a', 'c', 2, 1]
```

#### 拼接

`+`，`+=`可用于拼接列表；`*`用于重复。


```python
lt + [1,2]
```

```
## ['a', 'c', 2, 1, 1, 2]
```

```python
lt * 3
```

```
## ['a', 'c', 2, 1, 'a', 'c', 2, 1, 'a', 'c', 2, 1]
```

```python
lt+=[1,2]
print(lt)
```

```
## ['a', 'c', 2, 1, 1, 2]
```

#### 切片

与字符串一致，可以用下标取出列表特定的元素，同样是左闭右开。


```python
lt[0:1]
```

```
## ['a']
```

#### 判断

`in`命令可以判断元素的归属。


```python
"b" in lt, 2 in lt
```

```
## (False, True)
```
#### 删除

`remove`用于为删除特定元素；`pop`用于删除指定索引对应的元素；`clear`用于清空列表中所有元素，得到空列表；`del`删除变量。


```python
lt.remove(1)
print(lt)
```

```
## ['a', 'c', 2, 1, 2]
```

```python
lt.pop(0)
```

```
## 'a'
```

```python
print(lt)
```

```
## ['c', 2, 1, 2]
```

```python
lt.clear()
print(lt)
```

```
## []
```

### 元组

元组是一类特殊的列表，不同之处在于元组的元素不能修改。使用`()`生成。`+`与`+=`可以用于拼接元组，`*`用于重复，`[]`用于切片。

由于元组是不能修改的，因此只能全部删除元组。


```python
tup1 = (1,23,4,'a')
tup2 = ("x","y")
tup = tup1 + tup2
tup[0:3]
```

```
## (1, 23, 4)
```

```python
del tup
```

注意一个细节，`("x")`一个元素时，会被认为是字符串而不是元组。但是`["x"]`却是一个列表。


```python
type(("x"))
```

```
## <class 'str'>
```

```python
type(["x"])
```

```
## <class 'list'>
```

### 字典

字典是`Python`的经典数据结构，与`JSON`数据结构类似。字典用`{}`定义，以`key:value`这样的键值对定义一组词，词与词之间用`,`分隔。

可以通过`.keys()`和`.values()`取出字典的键和值，`.items()`在则可以构建迭代器。

字典的索引是通过`keys`实现的，是无序的，但仍可以通过对keys的索引进行元素的删除，只是不支持切片操作。


```python
dc = {"女": 25}
dc["男"]=18
print(dc["女"])
```

```
## 25
```

```python
print('男' in dc)
```

```
## True
```
`dict`和`enumerate`函数可以把任何序列直接生成字典。这在后续的循环迭代器构建中有妙用。


```python
dict(enumerate("abcd"))
```

```
## {0: 'a', 1: 'b', 2: 'c', 3: 'd'}
```
#### 字典的键

字典的原理要求键是不可变类型。因此，元组可以作为键，例如，


```python
{(0, 0): "a", (0, 1): "b", (1, 0): "c"}
```

```
## {(0, 0): 'a', (0, 1): 'b', (1, 0): 'c'}
```
这样的数据结构适合地理数据。

#### 删除

`pop`的作用为删除指定索引对应的键值对，使用方式为 `dict.pop(keys)`。`clear`与`del`函数的作用与对列表一致。


```python
dc.pop("女")
```

```
## 25
```

```python
print(dc)
```

```
## {'男': 18}
```

### 数据结构之间的转化

列表与元组之间可以无缝转换，使用`tuple`函数和`list`函数。字典中的词或者值都可以转化为列表和元组。

## 流程控制

### 条件判断

`Python`中条件判断的语法如下：


```python
x = 10
if x % 2:
    print(f"{x}是奇数")
else:
    print(f"{x}是偶数")
```

```
## 10是偶数
```

上述代码构建了一个奇偶数判断器。特别要注意，`Python`强制要求代码缩进来表达程序的结构。如果缩进错误会直接报错。这么做的好处是可以省略掉其他语言中用于显示结构关系的标记符，例如`{}`。其哲学是，与其希望用户自觉缩进，不如要求强制缩进。

对于有良好编程习惯的程序员，这个约定不会带来额外的负担。

当有三重选择时，可以使用`else if（或elif）`，搬出我们的泰山代码。


```python
m = 100
n = 50
if m > n:
  print(m,"is lager than", n)
elif m ==n:
  print(m,"is equal to", n)
else:
  print(m,"is smaller than", n)
```

```
## 100 is lager than 50
```
### 循环

Python的循环结构有两种，`for`语句和`while`语句。例如，求和代码可以写成。


```python
a = 0
n = 0
while a < 101:
  n = n + a
  a += 1
print(n)
```

```
## 5050
```

```python
n = 0
for a in range(101):
  n = n + a
print(n)
```

```
## 5050
```

此处的`range`函数是生成了一个从0到100的**迭代器**（iterator）。在每次 for 循环时，都从迭代器中取出一个值。迭代器是一般概念，Python 中的多数多个元素组成的数据结构都可以看作迭代器。

字符串可以当成天然的迭代器，下面的写法就比`R`里面方便的多。


```python
for a in "hello world":
  print(a)
```

```
## h
## e
## l
## l
## o
##  
## w
## o
## r
## l
## d
```

`enumerate`函数生成索引和迭代器，


```python
for i, a in enumerate("hello world"):
  print(f"第{i}个字符是{a}")
```

```
## 第0个字符是h
## 第1个字符是e
## 第2个字符是l
## 第3个字符是l
## 第4个字符是o
## 第5个字符是 
## 第6个字符是w
## 第7个字符是o
## 第8个字符是r
## 第9个字符是l
## 第10个字符是d
```
字典也是天生的迭代器，


```python
for k, v in {"女": 25,"男":18}.items():
  print(f"班上有{v}个{k}生")
```

```
## 班上有25个女生
## 班上有18个男生
```

由此看来，构造迭代器是`for`循环的关键。

在循环里执行`continue`，可以跳过本次循环进入下一步。执行`break`则终止循环，直接跳出循环体。这与R的用法类似。

## 函数与模块

`Python`定义函数的方式如下：


```python
def add(x,y):
  print(f"x is {x} and y is {y}")
  return x + y  # Return values with a return statement

add(2,3)
```

```
## x is 2 and y is 3
## 5
```
### 批量调用

`map`可以将函数作用到每个元素后返回值组成的迭代器。


```python
def squared(x):
  return x*x

list(map(squared,range(5)))
```

```
## [0, 1, 4, 9, 16]
```
### 无名函数

当函数只是调用一次时，可以使用无名函数的方法来定义。


```python
list(map(lambda x:x*x,range(5)))
```

```
## [0, 1, 4, 9, 16]
```

### 函数文档

为了更好地解释函数的使用方法，可以在函数定义时，输入一段字符串，这样的字符串可以用`help`函数读出。


```python
def squared(x):
    "计算平方的函数"
    # 具体实现省略
    return x*x

help(squared)
```

```
## Help on function squared in module __main__:
## 
## squared(x)
##     计算平方的函数
```

如果一大段的说明，可以使用`Python`多行字符串。多行字符串以三个引号开始，三个引号结束，单引号双引号皆可。

### 模块

模块是把函数等聚集起来的名字空间，由目录或者文件划定。使用`import`方法可以导入模块，模块都具有详实的在线帮助，可以使用`help`函数查看。


```python
import math
help(math)
```

```
## Help on module math:
## 
## NAME
##     math
## 
## DESCRIPTION
##     This module provides access to the mathematical functions
##     defined by the C standard.
## 
## FUNCTIONS
##     acos(x, /)
##         Return the arc cosine (measured in radians) of x.
##         
##         The result is between 0 and pi.
##     
##     acosh(x, /)
##         Return the inverse hyperbolic cosine of x.
##     
##     asin(x, /)
##         Return the arc sine (measured in radians) of x.
##         
##         The result is between -pi/2 and pi/2.
##     
##     asinh(x, /)
##         Return the inverse hyperbolic sine of x.
##     
##     atan(x, /)
##         Return the arc tangent (measured in radians) of x.
##         
##         The result is between -pi/2 and pi/2.
##     
##     atan2(y, x, /)
##         Return the arc tangent (measured in radians) of y/x.
##         
##         Unlike atan(y/x), the signs of both x and y are considered.
##     
##     atanh(x, /)
##         Return the inverse hyperbolic tangent of x.
##     
##     cbrt(x, /)
##         Return the cube root of x.
##     
##     ceil(x, /)
##         Return the ceiling of x as an Integral.
##         
##         This is the smallest integer >= x.
##     
##     comb(n, k, /)
##         Number of ways to choose k items from n items without repetition and without order.
##         
##         Evaluates to n! / (k! * (n - k)!) when k <= n and evaluates
##         to zero when k > n.
##         
##         Also called the binomial coefficient because it is equivalent
##         to the coefficient of k-th term in polynomial expansion of the
##         expression (1 + x)**n.
##         
##         Raises TypeError if either of the arguments are not integers.
##         Raises ValueError if either of the arguments are negative.
##     
##     copysign(x, y, /)
##         Return a float with the magnitude (absolute value) of x but the sign of y.
##         
##         On platforms that support signed zeros, copysign(1.0, -0.0)
##         returns -1.0.
##     
##     cos(x, /)
##         Return the cosine of x (measured in radians).
##     
##     cosh(x, /)
##         Return the hyperbolic cosine of x.
##     
##     degrees(x, /)
##         Convert angle x from radians to degrees.
##     
##     dist(p, q, /)
##         Return the Euclidean distance between two points p and q.
##         
##         The points should be specified as sequences (or iterables) of
##         coordinates.  Both inputs must have the same dimension.
##         
##         Roughly equivalent to:
##             sqrt(sum((px - qx) ** 2.0 for px, qx in zip(p, q)))
##     
##     erf(x, /)
##         Error function at x.
##     
##     erfc(x, /)
##         Complementary error function at x.
##     
##     exp(x, /)
##         Return e raised to the power of x.
##     
##     exp2(x, /)
##         Return 2 raised to the power of x.
##     
##     expm1(x, /)
##         Return exp(x)-1.
##         
##         This function avoids the loss of precision involved in the direct evaluation of exp(x)-1 for small x.
##     
##     fabs(x, /)
##         Return the absolute value of the float x.
##     
##     factorial(n, /)
##         Find n!.
##         
##         Raise a ValueError if x is negative or non-integral.
##     
##     floor(x, /)
##         Return the floor of x as an Integral.
##         
##         This is the largest integer <= x.
##     
##     fmod(x, y, /)
##         Return fmod(x, y), according to platform C.
##         
##         x % y may differ.
##     
##     frexp(x, /)
##         Return the mantissa and exponent of x, as pair (m, e).
##         
##         m is a float and e is an int, such that x = m * 2.**e.
##         If x is 0, m and e are both 0.  Else 0.5 <= abs(m) < 1.0.
##     
##     fsum(seq, /)
##         Return an accurate floating point sum of values in the iterable seq.
##         
##         Assumes IEEE-754 floating point arithmetic.
##     
##     gamma(x, /)
##         Gamma function at x.
##     
##     gcd(*integers)
##         Greatest Common Divisor.
##     
##     hypot(...)
##         hypot(*coordinates) -> value
##         
##         Multidimensional Euclidean distance from the origin to a point.
##         
##         Roughly equivalent to:
##             sqrt(sum(x**2 for x in coordinates))
##         
##         For a two dimensional point (x, y), gives the hypotenuse
##         using the Pythagorean theorem:  sqrt(x*x + y*y).
##         
##         For example, the hypotenuse of a 3/4/5 right triangle is:
##         
##             >>> hypot(3.0, 4.0)
##             5.0
##     
##     isclose(a, b, *, rel_tol=1e-09, abs_tol=0.0)
##         Determine whether two floating point numbers are close in value.
##         
##           rel_tol
##             maximum difference for being considered "close", relative to the
##             magnitude of the input values
##           abs_tol
##             maximum difference for being considered "close", regardless of the
##             magnitude of the input values
##         
##         Return True if a is close in value to b, and False otherwise.
##         
##         For the values to be considered close, the difference between them
##         must be smaller than at least one of the tolerances.
##         
##         -inf, inf and NaN behave similarly to the IEEE 754 Standard.  That
##         is, NaN is not close to anything, even itself.  inf and -inf are
##         only close to themselves.
##     
##     isfinite(x, /)
##         Return True if x is neither an infinity nor a NaN, and False otherwise.
##     
##     isinf(x, /)
##         Return True if x is a positive or negative infinity, and False otherwise.
##     
##     isnan(x, /)
##         Return True if x is a NaN (not a number), and False otherwise.
##     
##     isqrt(n, /)
##         Return the integer part of the square root of the input.
##     
##     lcm(*integers)
##         Least Common Multiple.
##     
##     ldexp(x, i, /)
##         Return x * (2**i).
##         
##         This is essentially the inverse of frexp().
##     
##     lgamma(x, /)
##         Natural logarithm of absolute value of Gamma function at x.
##     
##     log(...)
##         log(x, [base=math.e])
##         Return the logarithm of x to the given base.
##         
##         If the base not specified, returns the natural logarithm (base e) of x.
##     
##     log10(x, /)
##         Return the base 10 logarithm of x.
##     
##     log1p(x, /)
##         Return the natural logarithm of 1+x (base e).
##         
##         The result is computed in a way which is accurate for x near zero.
##     
##     log2(x, /)
##         Return the base 2 logarithm of x.
##     
##     modf(x, /)
##         Return the fractional and integer parts of x.
##         
##         Both results carry the sign of x and are floats.
##     
##     nextafter(x, y, /)
##         Return the next floating-point value after x towards y.
##     
##     perm(n, k=None, /)
##         Number of ways to choose k items from n items without repetition and with order.
##         
##         Evaluates to n! / (n - k)! when k <= n and evaluates
##         to zero when k > n.
##         
##         If k is not specified or is None, then k defaults to n
##         and the function returns n!.
##         
##         Raises TypeError if either of the arguments are not integers.
##         Raises ValueError if either of the arguments are negative.
##     
##     pow(x, y, /)
##         Return x**y (x to the power of y).
##     
##     prod(iterable, /, *, start=1)
##         Calculate the product of all the elements in the input iterable.
##         
##         The default start value for the product is 1.
##         
##         When the iterable is empty, return the start value.  This function is
##         intended specifically for use with numeric values and may reject
##         non-numeric types.
##     
##     radians(x, /)
##         Convert angle x from degrees to radians.
##     
##     remainder(x, y, /)
##         Difference between x and the closest integer multiple of y.
##         
##         Return x - n*y where n*y is the closest integer multiple of y.
##         In the case where x is exactly halfway between two multiples of
##         y, the nearest even value of n is used. The result is always exact.
##     
##     sin(x, /)
##         Return the sine of x (measured in radians).
##     
##     sinh(x, /)
##         Return the hyperbolic sine of x.
##     
##     sqrt(x, /)
##         Return the square root of x.
##     
##     tan(x, /)
##         Return the tangent of x (measured in radians).
##     
##     tanh(x, /)
##         Return the hyperbolic tangent of x.
##     
##     trunc(x, /)
##         Truncates the Real x to the nearest Integral toward 0.
##         
##         Uses the __trunc__ magic method.
##     
##     ulp(x, /)
##         Return the value of the least significant bit of the float x.
## 
## DATA
##     e = 2.718281828459045
##     inf = inf
##     nan = nan
##     pi = 3.141592653589793
##     tau = 6.283185307179586
## 
## FILE
##     /usr/local/Cellar/python@3.11/3.11.5/Frameworks/Python.framework/Versions/3.11/lib/python3.11/lib-dynload/math.cpython-311-darwin.so
```

当模块中的内容很多时，会被安排在不同层次的名字空间中，可以使用`from`来简化调用，`as`可以指定模块的别名。


```python
import os
from os.path import abspath
from os.path import abspath as absp
```

## 数据读写与文件操作

之前我们介绍了通过`input`从键盘输入和`print`输出到屏幕。除此之外，还可以直接从文件读入和输出到文件。`Python`内置了`file`类，来完成文件操作。

### 读入文件

`open`函数用于打开文件，创建一个`file`对象，第一个参数是文件名，第二参数为打开模式，默认值`r`表示只读，`w`表示写出，`a`表示添加。

`open`打开文件之后，用迭代器逐行读取。




```python
for l in open("log.txt"):
  print(l,end = "") # 读入的字符串带有换行符，与print 叠加会有空行，end参数表示去掉空行。
```

```
## This is a record of my progress in learning Python
## Day 1: Introduction
```

### 写出文件

`open`以写模式打开，可以输出到文件。


```python
f = open("log.txt", "w")
f.write("Day 2: Basics\n")
```

```
## 14
```

```python
f.write("Day 3: Numpy\n")
```

```
## 13
```

```python
f.close()
```
注意文件写出完成后，需要关闭文件，否则会一直建立关联，占用资源。

写出模式下，之前的内容会会被覆盖，如果希望添加到已有内容中，需要使用添加模式。


```python
f = open("log.txt", "a")
f.write("Day 4: Scrapy\n")
```

```
## 14
```

```python
f.close()
```

### with关键字

使用`with`关键字系统会自动调用`f.close`方法。


```python
with open("log.txt","r") as f:
  for l in f.readlines():
    print(l,end = "")
```

```
## Day 2: Basics
## Day 3: Numpy
## Day 4: Scrapy
```
`readlines`方法是逐行读入文件，是一个天然的迭代器。


```python
with open("log.txt","a") as f:
  f.write("Day 5: Machine Learning\n")
```

```
## 24
```
## os模块

`os`提供了帮你执行文件处理操作的方法，比如重命名和删除文件。

|方法|作用|
|:------|:-------------------|
|`remove`|删除文件|
|`rename`|重名命文件|
|`mkdir`|创建新目录|
|`chdir`|改变当前目录|
|`getcwd`|查看当前目录|
|`rmdir`|删除目录|


## 案例：获取高德地图地址信息

## 面向对象单独成一章
