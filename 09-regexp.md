\mainmatter

# 正则表达式 {#regexp}

正则表达式是对字符串类型数据进行匹配判断，提取等操作的一套逻辑公式。可以看做是一整套约定俗成的黑话，对特定符号的约定俗成。当然，正则表达式可以广泛被采纳，是因为其背后有一套完备的公理体系。正则表达式是独立于编程语言之外的一个迷你语法，是自然语言处理的最佳工具。

更重要的是，在数据工程中，所有的数据都是文件，而所有的文件都是字符串。自动化处理字符串可以让我们避免大量的重复劳动，也可以让我们的科研充满乐趣。

## 语法要素

正则表达式默认所有字符与自己匹配，通过约定一组语法要素来表达人类语言难以精准描述的字符串模式，从而达到更加灵活与精确识别自然语言的目的。

### 元字符

大部分字符会简单匹配自身。除此之外，正则表达式约定一类特殊的不匹配自身的元字符（metacharacters），它们表示匹配一些非常规的内容，或者通过重复它们或改变它们的含义来影响正则的其他部分。

元字符含义如下：

|符号|含义|
|-----|-------------|
|`.`|代表任意字符|
|`^`、`$`|开始与结束|
|`*`|重复任意次|
|`+`|至少重复一次|
|`?`|0或1次|
|`()`|字符组合|
|`|`|或|
|`[]`|字符集合|

### 重复

除去用元字符表达简单重复外，正则表达式还约定了重复m至n（包含）次的表达方式为`{m,n}`。其中m和n可缺省一个，`{m,}`表示至少重复m次，而`{,n}`表示至多重复n次，`{m}`表示恰好重复m次。

总结如下，

|符号|含义|
|:-----|:-------------|
|`{m,n}`|重复m至n（包含）次|
|`{m,}`|至少重复m次|
|`{m}`|重复m次|

*思考下，如何用正则表达式表达至多n次？*

### 常用字符集

常用字符集有：

|符号|含义|
|:-----|:-------------|
|`[0-9]`|数字字符集|
|`[a-zA-Z]`|英文字符集|
|`\<`、`\>`|单词开始或结束位置的空白符|
|`\b`|单词两边的空白字符|
|`\B`|非单词两边的空白字符|

除此之外，还约定了一组有特殊含义的字符集合，这些字符集合在使用时需要再嵌套一组`[]`。

|符号|含义|
|:-----|:-------------|
|`[:alpha:]`|字母|
|`[:digit:]`|数字|
|`[:alnum:]`|字母或数字；`\w`与`[[:alnum:]]`同义，`\W` 与`[^[:alnum:]]`同义|
|`[:graph:]`|可见字符，不包括空白|
|`[:lower:]`|小写字母|
|`[:upper:]`|大写字母|
|`[:print:]`|可见的字符和空白|
|`[:punct:]`|标点|
|`[:space:]`|空白符，包括`\t`,`\r`,`\n`；`\s`与`[[:space:]]`同义，`\S`与`[^[:space:]]` 同义|
|`[:xdigit:]`|十六进制数|
|`[\u4e00-\u9fa5]`|中文字符|

由于上述特殊字符被占用，需要转义操作来表达特殊字符自身，有三种约定：基础正则表达式、扩展正则表达式与`Perl`正则表达式。在基础正则表达式中，特殊字符（`?+*{[`等）是字符本身，需要特殊含义得加上`\`来表达其正则表达式含义（例如`\?`表示重复0次或1次，而`?`表示问号本身）。扩展正则表达式相反，用特殊字符默认表达正则含义，而加上`\`后表示字符本身。Perl正则表达式则把扩展正则表达式扩展到更大的范围，与`Perl5`语言正则表达式有同样功能。

**练习题**

1. 请构造一个正则表达式来表示交大的邮箱地址，要求不能以数字开头，必须包含字母。

2. 数据表[qje2014_2023.txt]()包含了2014-2023年发表在Quarterly Journal of Economics杂志上的所有论文。可以看到，论文的作者与地址信息是以如下的模式表示的`[Gopinath, Gita; Stein, Jeremy C.] Harvard Univ, Cambridge, MA 02138 USA; [Gopinath, Gita; Stein, Jeremy C.] NBER, Cambridge, MA 02138 USA`，请构造一个正则表达式表述这样的模式。

### 分组

正则表达式可以分为不同的小组来解析字符串，这些小组匹配不同组件。分组是用`(`，`)`元字符来标记，子组从左到右编号，从1向上编号。组可以嵌套；要确定编号只需计算从左到右的左括号字符。使用`\n`表示第n组的内容。例如，`(\w+)\s\1`可用于表示任意两组重复的字符组，`the the`。

### 非捕获组与断言

精心设计的正则可以使用许多组，既可以捕获感兴趣的子串，也可以对正则本身进行分组和构建。为避免混淆，`Perl5`中使用 `(?...)`作为扩展语法。

如果我们对匹配的内容不感兴趣，可以使用`(?:...)`表示的非捕获组，`...`表示被匹配的正则表达式。在修改现有模式时特别有用，可以添加新组而不更改所有其他组的编号方式。

断言（assertion）用于找到的对应的位置，同样不匹配内容，因此也被称为零宽断言（zero-width assertion）。匹配到位置后，可以向后（向右）也可以向前（向左）继续搜索。

断言如下四种：

|符号|含义|
|:-----|:-------------------|
|`(?<=...)`|肯定型后视断言，匹配成功时向后搜索，`(?<=[\u4e00-\u9fa5])\s`表示所有中文字符之后的空格|
|`(?<!...)`|否定型后视断言，匹配成功时向后搜索，`(?<=[\u4e00-\u9fa5])\s`表示所有非中文字符之后的空格|
|`(?=...)`|肯定型前视断言，匹配成功时向前搜索，`\s(?=[\u4e00-\u9fa5])`表示所有中文字符之前的空格|
|`(?!...)`|否定型前视断言，匹配成功时向前搜索，`\s(?![\u4e00-\u9fa5])`表示所有非中文字符之前的空格|

断言可以让我们的正则表达式非常灵活，避免了很多复杂的情形。例如，如果我们要求，在注册用户的时候，需要验证用户的密码必须包含英文大小写字母以及数字，可以使用下面的正则表达式。

`(?=.*[a-z])(?=.*[A-Z])(?=.*[0-9]).*`

其中，`(?=.*[a-z])`是一个肯定型向前断言，用于判断在任意字符（包括没有字符的情况）后面是否出现了小写英文字母。这个断言不匹配具体内容，所以后面可以继续使用其他断言。

设想一下如果没有断言，这里的情况会有多么复杂。


## R语言stringr包

R语言中的正则表达式转义符使用两个`\`，这一点值得特别注意。`tidyverse`宇宙为我们提供了`stringr`包来对处理字符数据，其常用函数列举如下：

### stringr包

| 字符            | 含义                         |
|:-----------------|:------------------------------|
| `str_sub`         | 提取指定位置的字符    |
| `str_dup`      | 重复字符串                |
| `str_length`     | 返回字符的长度          |
| `str_pad`        | 填补字符                   |
| `str_trim`       | 丢弃填充，如去掉字符前后的空格 |
| `str_c`         | 连接字符                   |
| `str_extract`     | 提取首个匹配模式的字符 |
| `str_extract_all` | 提取所有匹配模式的字符 |
| `str_locate`      | 返回首个匹配模式的字符的位置 |
| `str_locate_all`  | 返回所有匹配模式的字符的位置 |
| `str_replace`     | 替换首个匹配模式       |
| `str_replace_all` | 替换所有匹配模式       |
| `str_split`     | 按照模式分割字符串    |
| `str_split_fixed` | 按照模式将字符串分割成指定个数 |
| `str_detect`    | 检测字符是否存在某些指定模式 |
| `str_count`       | 返回指定模式出现的次数 |

这些函数的是用并不复杂，下面我们用案例来说明如何在研究实践应用正则表达式。

### 应用案例：wos数据分析

我们想知道再过去十年哪个研究机构曾经在Quarterly Journal of Economics（QJE）杂志上面发表论文，哪些作者是发表论文最多的？

首先，从[web of science](https://webofscience.clarivate.cn/wos/woscc/summary/3fe2f324-5bc1-4b62-8171-2e44e3f46bd6-b216654f/relevance/1)下载QJE的论文数据到文件。

其次，读入文件，可以看到论文的作者地址在哪一列？

然后，解析论文地址列，分别制作“论文-作者-地址-地址顺序”表格以及“论文-作者-作者顺序”，统计相关数据。

### 应用案例：数据合并与校对

在研究中，我们进程需要合并来自不同数据源头的同一个数据。

现在，我们需要比较同一个科学家在不同数据库中解锁得到的科学家论文发表清单，来判断不同数据库的准确性。来自[Aminer](https://www.aminer.org/)数据库的论文清单储存为[aminer.csv](https://drive.google.com/file/d/18CF3QhHwTfRG3u39MvwFl4zpan8R10zR/view?usp=drive_link)，科学家在其个人主页自我披露的论文清单存在
[gt.csv](https://drive.google.com/file/d/1nipKSFlQZmzhmpUdKxeEhzNw7c0kJhxx/view?usp=drive_link)。

可以的信息包括：论文发表年份、期刊、doi、论文题目。

## Python的re模块

`Python`的`re`模块提供了与Perl语言类似的正则表达式匹配操作。正则表达式在`Python`中被定义为`模式（pattern）`对象，字符串先被编译为模式对象，然后在进行匹配。

### 原生字符串

`Python`约定使用关键字`r`来区分原生字符串与正则表达式字符串。`r`置于字符串前时，表示该字符串为原生字符串（基本正则表达式），否则默认字符串为扩展正则表达式。


```python
print(r"hello\nworld")
```

```
## hello\nworld
```

```python
print("hello\nworld")
```

```
## hello
## world
```
上例中，第一个字符串中`\n`被当成字符本身来处理而第二个字符串则用于表示换行符。

在`Python`re模块中，`'\n'`，`'\\n'`以及`r'\n'`，含义相同，均可以识别换行符；但是`r'\\n'`无法识别换行符。这是因为，`re`模块会首先将字符串编译成正则表达式，`'\\n'`的第二个`\`被转义，从而`\\`等同于`\`，与`n`放在一起时，re看到的仍然一个`\n`，最后编译为换行符。


```python
import re
s = "hello\nworld"
print(re.findall('\n', s))
```

```
## ['\n']
```

```python
print(re.findall(r'\n', s))
```

```
## ['\n']
```

```python
print(re.findall('\\n', s))
```

```
## ['\n']
```

```python
print(re.findall(r'\\n', s))
```

```
## []
```

### 编译

`re.complile`函数可以将字符串编译为正则表达式


```python
import re
p = re.compile('ab*')
p
```

```
## re.compile('ab*')
```

编译标志被用于修改正则表达式的工作方式。例如`re.I`（等价于`re.IGNORECASE`）表示忽略字母大小写；例如`re.M`（等价于`re.MULTILINE`）表示多行匹配，此时`^`与`$`只匹配第一行的开始与最后一行的结尾。

### 匹配

编译成模式后，正则表达式可用于匹配字符串，常用函数如下：

| 函数     | 作用     |
|:----------|:--------------------------------------- |
|`match`    | 确定正则是否从字符串的开头匹配。|
|`fullmatch`    | 确定正则是整个字符串匹配。|
|`search`   | 扫描字符串，查找此正则匹配的任何位置。 |
|`findall`  | 找到正则匹配的所有子字符串，并将它们作为列表返回。 |
|`finditer`| 找到正则匹配的所有子字符串，并将它们返回为一个 iterator。 |

如果没有找到匹配，`match`和`search`返回None。如果它们成功，一个匹配对象实例将被返回，包含匹配相关的信息：起始和终结位置、匹配的子串以及其它。

| 函数     | 作用     |
|:----------|:--------------------------------------- |
|`group`    | 返回正则匹配的字符串          |
|`start`   | 返回匹配的开始位置|
|`end`  | 返回匹配的结束位置 |
|`span`|返回包含匹配 (start, end) 位置的元组|



```python
m = p.match('abcd->')
m.group()
```

```
## 'ab'
```
`search`会扫描整个字符串，直到找到一个匹配的结果。


```python
s = p.search('bbabcdab->')
m.group()
```

```
## 'ab'
```
`findall`会返回所有满足匹配的字符串的列表，`finditer`则直接将`findall`的匹配对象返回为一个迭代器。


```python
fd = p.findall('bbabcdab->')
fd
```

```
## ['ab', 'ab']
```

```python
iterator = p.finditer('bbabcdab->')
for match in iterator:
  print(match.span())
```

```
## (2, 4)
## (6, 8)
```

在实际使用时，匹配函数采用与相应模式方法相同的参数，并将正则字符串作为第一个参数添加，并仍然返回`None`或匹配对象实例。

### 分割

`re.split`在正则匹配的任何地方拆分字符串，返回一个片段列表，用法接近字符串的`split`方法，但是在分隔符中提供了更多的通用性。`maxsplit`参数用于指定分割次数。


```python
re.split(';','Northwestern Univ, Evanston, IL 60208 USA	University of London; London School Economics & Political Science; Innovations for Poverty Action (IPA); Massachusetts Institute of Technology (MIT); Centre for Economic Policy Research - UK; Yale University; National Bureau of Economic Research; Northwestern University')
```

```
## ['Northwestern Univ, Evanston, IL 60208 USA\tUniversity of London', ' London School Economics & Political Science', ' Innovations for Poverty Action (IPA)', ' Massachusetts Institute of Technology (MIT)', ' Centre for Economic Policy Research - UK', ' Yale University', ' National Bureau of Economic Research', ' Northwestern University']
```

`[Bryan, Gharad; Karlan, Dean] London Sch Econ, London, England; [Karlan, Dean] Innovat Poverty Act, London, England; [Karlan, Dean] MIT, Jameel Poverty Act Lab, Cambridge, MA 02139 USA; [Karlan, Dean] Ctr Econ Policy Res, London, England; [Choi, James J.] Yale Univ, New Haven, CT 06520 USA; [Choi, James J.; Karlan, Dean] NBER, Cambridge, MA 02138 USA; [Karlan, Dean] Northwestern Univ, Evanston, IL 60208 USA	University of London; London School Economics & Political Science; Innovations for Poverty Action (IPA); Massachusetts Institute of Technology (MIT); Centre for Economic Policy Research - UK; Yale University; National Bureau of Economic Research; Northwestern University`

**回到案例！**

### 替换

`re.sub(pattern, repl, string, count=0, flags=0)`返回通过使用`repl` 替换在`string`最左边非重叠出现的`pattern`而获得的字符串。如果样式没有找到，则不加改变地返回`string`。可选参数`count`是要替换的最大次数。


```python
re.sub(r'\sAND\s', ' & ', 'Baked Beans And Spam', flags=re.IGNORECASE)
```

```
## 'Baked Beans & Spam'
```
当`repl`是一个函数时，则它会针对每次`pattern`的非重叠出现的情况被调用。例如下面的代码可以随机化除了首位和末尾字符的每个字符的位置。


```python
import random
def repl(m):
    inner_word = list(m.group(2))
    random.shuffle(inner_word)
    return m.group(1) + "".join(inner_word) + m.group(3)

text = "Professor Abdolmalek, please report your absences promptly."
re.sub(r"(\w)(\w+)(\w)", repl, text)
```

```
## 'Preofossr Amleabldok, pseale rpeort yuor abesnces potmplry.'
```
`re.subn`的行为与`re.sub`相同，但是返回一个元组 (字符串, 替换次数)。

### 命名组

`Python`添加的拓展正则表达式用`(?P...)`表示。其中最常见的是命名组`(?P<name>...)`，其中`name`是改组的名称，可以被用于后续调用。


```python
p = re.compile(r'(?P<word>\b\w+\b)')
m = p.search( '(((( Lots of punctuation )))' )
m.group('word')
```

```
## 'Lots'
```
`groupdict`将命名分组提取为一个字典。

```python
m = re.match(r'(?P<first>\w+) (?P<last>\w+)', 'Jane Doe')
m.groupdict()
```

```
## {'first': 'Jane', 'last': 'Doe'}
```
`(?P=name)`表示在当前点再次匹配名为`name`的组的内容。


```python
p = re.compile(r'\b(?P<word>\w+)\s+(?P=word)\b')
p.search('Paris in the the spring').group()
```

```
## 'the the'
```

去除所有中文字符间的空格例子，在这个案例中，我们要得到什么呢？来处理处



