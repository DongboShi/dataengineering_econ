\mainmatter

# GUN Make的数据生产线{#make}
本章中我们将介绍如何使用GUN Make工具来帮助我们实现更为便捷的可复现数据分析。对于绝大多数数据分析人员，GNU Make是一个完全陌生的概念；但对于软件工程师而言，Make则是再熟悉不过的工具。Make起源于1976年，是软件工程开发人员为了将多文件组成的复杂软件工程搭建过程自动化的工具。本章中，我们要解决三个问题：**使用Make的必要性**，**Make的基本语法以及Make的高级语法**。

## 使用Make的必要性
当前，数据分析已经成为一个系统性工程。从工程的视角来看，一个数据分析项目至少包括数据获取，数据清理，数据分析，结果展示，以及最终开发数据产品等环节。设想一下有一个三人合作的经济学研究项目(如下图所示)，Alice负责从网页爬取数据，Bob负责原始数据的清理，Eve用机器学习的方法从数据中定义一个变量，然后Alice在用Bob和Eve的数据跑回归模型，Bob擅长分析结果的可视化展示，最后Alice写研究论文。该项目中的复杂性来自两个方面：第一是多人协同，三个合作者的工作是相互依赖的。例如，Bob需要Alice的原始数据作为输入，Alice的回归则依赖于Bob和Eve的数据清理结果，这种相互依赖性会意味着数据分析某一环节的变化（例如改变了对缺失值的处理方法）都会引起其后续步骤的变化；第二是多语言协同。之所以出现多语言协同，可能是因为不同合作者擅长的语言各异，更重要的是在这个项目中，Alice使用的是Python、Stata、Latex，Bob使用R，而Eve使用python，本来就没有一个语言是各方面都是最优的。实际上，“根据任务特性选择合适的语言”本就是数据工程的一个重要原则。多语言意味着没办法将所有的代码整合进一个文件中。传统地，我们以来**人工协同**来完成数据分析项目，但是人工协同不但非常耗时，还增加了出错的概率。


```{=html}
<div class="grViz html-widget html-fill-item-overflow-hidden html-fill-item" id="htmlwidget-7bf9a19ece20ad655458" style="width:672px;height:480px;"></div>
<script type="application/json" data-for="htmlwidget-7bf9a19ece20ad655458">{"x":{"diagram":"\ndigraph boxes_and_circles {\n\n  # a \"graph\" statement\n  graph [overlap = true, fontsize = 10,layout = circo]\n\n  # several \"node\" statements\n  node [shape = box,\n        fontname = Helvetica]\n        \n  WebPages; RawData;CleanData; Variables; Model; Figures; Articles\n\n  node [shape = circle,\n        fixedsize = false,\n        width = 1] // sets as circles\n  Alice_Crawler; Bob_DataClean; Eve_ML; Alice_Regression; Bob_Visualization; Alice_Writing;\n\n  # several \"edge\" statements\n  WebPages->Alice_Crawler Alice_Crawler->RawData\n  RawData->Bob_DataClean Bob_DataClean->CleanData\n  CleanData ->Eve_ML CleanData->Alice_Regression\n  Eve_ML -> Variables Variables->Alice_Regression\n  Alice_Regression -> Model Model->Bob_Visualization \n  CleanData ->Bob_Visualization Bob_Visualization -> Figures\n  Figures -> Alice_Writing Model -> Alice_Writing\n  Alice_Writing -> Articles\n}\n","config":{"engine":"dot","options":null}},"evals":[],"jsHooks":[]}</script>
```

Make为我们管理数据工程提供了自动化解决方案。首先，Make定义了数据分析的“菜单”，菜单上定义了所有数据之间的依赖关系与转换步骤（即数据工程中使用的各类程序）。良好定义的Make可以实现复现数据的分析流程。

之后，我们可以在命令行使用```make```命令，与项目相关的所有相关文件将会按照菜单顺序依次执行。其次，Make是一种**语言独立**（Language Agnostic）的工具，这使得Make可以将任何语言的代码加入数据流（Data Pipeline）当中，在管理数据工程时非常灵活。

*下面这写内容是从续本达课上学到的，所以需要和续本达确定是否可以在书稿中使用*
## 数据流水线的构造目标
对于任何数据工程项目，都可以抽象成三个要素：数据输入、数据输出和数据处理过程。在此基础上，我们还需要精确表达输入数据、输出数据与中间计算结果的依赖关系。同时，我们需要满足高效执行的要求包括并行处理与超级计算机上的运行。最后我们希望当时数据工程出现错误时，我们可以从最后一个正确的结果中恢复运算。

GNU make是我们构造数据工程流水线的最佳工具。*这是我的结论*

可以实现复现原则；管理程序运行，在超级计算机上运行；可以从错误中恢复。

## Make安装

Mac在命令行通过`brew install make`直接安装；Windows的安装参考[Make for windows](http://gnuwin32.sourceforge.net/packages/make.htm)。

安装完成后，可以在命令行通过`make --version`查看make的版本。

## 快速上手make
安装make之后，可以根据上面的例子，编辑一个Makefile文件，如下：


```base
targe

```

*把前面的示意图的例子直接写一个demo出来*
从上面的例子可以看出，Makefile的语法简单明了，其基本规则如下：

```bash
target : prerequisites
  command1
  command2
  ...
```

其中:
- ```target``` 是make的输出
- ```prerequisites```是生成target的所有输入，target与prerequisites使用冒号连接
- ```command```是生成target需要执行的命令，可以使任意的shell命令。command前使用制表符```\t```缩进。一般来说，make会以UNIX的标准Shell，也就是 /bin/sh 来执行命令。

多个上述基本单元放在一起形成一个完整的文件依赖系统，Make根据target与其prerequisites时间关系来判断是否需要执行command中的命令。只要prerequisites中至少有一个文件比target更新，command的命令就会被执行。

make更像是一个函数式编程语言，不再关注“执行什么操作”，而是关注“输入到输出的映射”


## make的工作方式

有了这个Makefile文件，我们在命令行中使用`make`来自动运行整个数据工程（是不是特别神奇）。实际上，GNU make工作时的执行逻辑为：

1. 读入Makefile
2. 为所有的输出文件创建输入文件依赖关系链
3. 根据依赖关系，决定哪些文件要重新生成
4. 执行生成命令

其中，1-2为第一阶段，3-4为第二阶段，5为第三阶段。Makefile支持`*`，`?`和`~`通配符，例如`*.csv`表示所有后缀为csv的文件。

## Makefile 的高级写法

### 特殊目标

`.PHONY`用于指明伪目标，伪目标指的是不需要生成输出文件的目标。在下面的代码中，定义了一个没有没有依赖的伪目标clean，此时不会生成名字为clean的文件，它的作用是删除所有文件名中包含temp的文件。当我们运行`make clean`即可删除相关文件。
```
.PHONY : clean
clean :
    rm *temp.*
```
其他比较重要的特殊目标为:
```
.DELETE_ON_ERROR:出错时删掉坏文件，避免make认为出错但是依然输出的文件已存在而不更新
.SECONDARY:保留中间结果，这样我们的数据运算更容易回溯与检查
```


*后文中要修改语言风格为自己的风格*
Makefile中， `#` 是注释符 

通常，make会把其要执行的命令行在命令执行前输出到屏幕上。当我们用 @ 字符在命令行前，那么，这个命令将不被make显示出来，最具代表性的例子是，我们用这个功能来向屏幕显示一些信息。如:
`@echo 正在编译XXX模块......`

make一般是使用环境变量SHELL中所定义的系统Shell来执行命令，默认情况下使用UNIX的标准Shell——/bin/sh来执行命令。

### 使用变量
Makefile中可以使用变量，变量的命名字可以包含字符、数字，下划线（可以是数字开头），但不应该含有 : 、 # 、 = 或是空字符（空格、回车等）。注意Makefile中的变量名是大小写敏感的。变量在声明时需要通过`:=`给予初值，而在使用时，需要给在变量名前加上`$` 符号，但最好用小括号`()`。在Makefile中，变量可以使用在“目标”，“依赖目标”， “命令”或是Makefile的其它部分中。

我们可以使用 += 操作符给变量追加值。
```
objects = main.o foo.o bar.o utils.o
objects += another.o
```

a:= 1
$(info $(a)) info是GNU make自带函数用于输出信息

$^表示所有的输入
$@表示所有的输出
$<表示第一个输入
$(word n, $^)表示第n个输入（此处，n是自然数）。

Makefile中可以调用Shell命令，将其标准输出作为值。例如

guile可以调用scheme语言

字符串替代的结果

`#`是makefile的注释标识符


### 分支与循环结构
使用条件判断，可以让make根据运行时的不同情况选择不同的执行分支。条件表达式可以是比较变量的值，或是比较变量和常量的值。下面的代码中，
```
libs_for_gcc = -lgnu
normal_libs =

foo: $(objects)
ifeq ($(CC),gcc)
    $(CC) -o foo $(objects) $(libs_for_gcc)
else
    $(CC) -o foo $(objects) $(normal_libs)
endif
```
可见，在上面示例的这个规则中，目标 foo 可以根据变量 $(CC) 值来选取不同的函数库来编译程序。

我们可以从上面的示例中看到三个关键字： ifeq 、 else 和 endif 。 ifeq 的意思表示条件语句的开始，并指定一个条件表达式，表达式包含两个参数，以逗号分隔，表达式以圆括号括起。 else 表示条件表达式为假的情况。 endif 表示一个条件语句的结束，任何一个条件表达式都应该以 endif 结束。


### 函数

在Makefile中可以使用函数来处理变量，从而让我们的命令或是规则更为的灵活和具有智能。make 所支持的函数也不算很多，不过已经足够我们的操作了。函数调用后，函数的返回值可以当做变量来使用。

函数调用，很像变量的使用，也是以 $ 来标识的，其语法如下：
`$(<function> <arguments>)`

例如，foreach函数可以用来做循环，
```
names := a b c d
files := $(foreach n,$(names),$(n).o)
```



make使用惰性赋值，只有变量所在依赖关系被使用时，变量才会展开。

之所以有一些语法看不懂，是因为对shell的命令不太清楚

- define something 
- dummy target

Make 语法是否执行的原则。
Make target 需要举一个例子作为不同的部分，这个地方需要画图加上去，然后要准备后续的部分，科学家的要处理起来
然后准备机器学习的课件

## Make的高级语法

SHEBANG符号```#!```位于Unix系统脚本的第一行，用于指明执行该脚本的解释程序。符合以下规则：

1. 脚本文件中没有```#!```这一行，那么它执行时会默认用当前Shell去解释这个脚本(即：$SHELL环境变量）；
2. ```#!```之后的解释程序是一个可执行文件，那么执行这个脚本时，它就会把文件名及其参数一起作为参数传给解释程序去执行。
3. ```#!```指定的解释程序没有可执行权限，则会报错“bad interpreter: Permission denied”。
4. ```#!```指定的解释程序不是一个可执行文件，那么指定的解释程序会被忽略，转而交给当前的SHELL去执行这个脚本。
5. ```#!```指定的解释程序不存在，那么会报错“bad interpreter: No such file or directory”。注意：```#!```之后的解释程序，需要写其绝对路径（如：#!/bin/bash），它是不会自动到$PATH中寻找解释器的。


定义变量
Automatic variables
- ```$@```:    目标的文件名（包括“路径/文件”）
- ```$<```:   第一个依赖
- ```$^```:    所有依赖
- ```$(@D)```:    目标的路径/文件夹部分
- ```$(@F)```:    目标的文件部分
- ```$(<D)```:    第一个依赖的路径/文件夹部分
- ```$(<F)```:    第一个依赖的文件部分


### 并行计算

`make -j n target`使用n个核并行运行


Make的工作原理可以参考John Graham-Cumming 的[The GNU Make Book](Graham-Cumming J. The GNU make book[M]. No Starch Press, 2015.
)


## 参考资料
http://zmjones.com/make

https://bost.ocks.org/mike/make/

http://matt.might.net/articles/intro-to-make/

http://stat545.com/automation04_make-activity.html

https://seisman.github.io/how-to-write-makefile/introduction.html

[Managing Projects with GNU Make, Third Edition](https://www.oreilly.com/openbook/make3/book/index.csp)
[Make Documents](https://www.gnu.org/software/make/manual/make.html)


*需要单独整理一期shell命令行的内容*
