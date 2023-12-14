\mainmatter

# 数据整理 {#manipulate}

> 整齐数据都是一样的，凌乱数据各有各的凌乱。*--- Hadley Wickham*

前面两章我们介绍了R语言基本语法以及文学式编程。本章开始我们正式进入数据处理环节，此处的数据整理对应了英文的data manipulation，也可以协议理解为数据清理。在一个经济学研究中，数据整理工作往往占到一个数据工程（除数据采集、论文修改与写作外）的50%-80%的工作量。

数据整理的目的是根据研究设计，通过清理、合并、变形等操作，把原始数据转变成可以被直接用于统计分析和可视化的数据，这样的数据，被称为整齐数据。

## 整齐数据tidydata

### 分析单位(unit of analysis)

为了理解整齐数据，需要先介绍一个新的概念-分析单位。分析单元是实证研究中至关重要的概念。毫不夸张的说，理解分析单位就是理解和设计实证论文的钥匙。然而遗憾的是，分析单位在国内的教学中并没有得到充分的重视。

分析单位是一个"单位"的概念，类似于*千克，公里*，是针对某种对象的"计量"方式。艾尔·巴比在《社会研究方法》中将分析单位定义为"用来考察和总结同类事物特征，解释其中差异的单位。"该定义的核心关键词是*差异*。在艾尔·巴比的基础上，我们可以给出一个更加直观的定义，即**分析单位是研究设计中用于比较研究对象差异的最小计量单位**。设想这样的一个研究教育回报的研究设计，研究对象为上海市的所有应届毕业生，研究者可以通过普查的方式得到所有人的受教育年限与收入水平，此时的分析单位是**个人**。如果研究问题转化为研究生教育对收入的影响，此时，分析单位就不再是**个人**，而成为*研究生学历*与*研究生以下学历*的两个群体，因为同群体内的个人在教育测度上并不存在差异。从这个角度来看，风笑天在其影响范围很广的教材《社会研究方法》中对分析单位的定义就有点草率了。风笑天将分析单位定义为*"一项社会科学的研究对象"*，这个定义直接将分析单位与研究对象等价。

与分析单位密切相关的一个概念是**观测单位(unit of observation)**，指的是观测数据中的计量单位。直观来说，就是数据每一行的单位。在上面的两个例子里面，数据都是在个人层面采集的，观测单位都是**个人**。这就看出了观测单位与分析单位之间的细微差别。在数据库中，观测单位对应了一张表格的`key`，后文中我们会介绍，确定`key`是数据整理中的核心概念。

单位与变量的关系

在操作层面，分析单位是由自变量的设计决定的，而观测单位是由因变量的设计决定的。在介绍理论与模型的章节中，我们提到当前的理论主要是关注两个变量之间的关系。因此，此处的自变量指的是我们关心的核心自变量(variable of interest)。所以才说，分析单位是实证研究的钥匙，当我们开始一项研究的时候，第一个要回答的问题便是这个研究的自变量与因变量分别是在什么"单位"上测量的。同样的道理，当我们去精读别人的论文时，首先要理解的便是他的回归中的分析单位与观测单位。如果我们看到识别模型的为 $$y_{i,j,t} = \alpha + \beta \times x_{j,t} + \epsilon_{i,j,t}$$

我们便可以确定此处的分析单位是{j,t}，而观测单位是{i,j,t}，在回归表中的观测值数量（样本大小N）应该等于$I \times J \times T$。

所以我们常说，看懂回归表格中的观测值数量是判断回归是否看懂的黄金标准。

## 整齐数据

了解完变量与分析单位后，我们就可以介绍整齐数据（tidy data）的定义及其背后深刻的哲学思考了。

### 整齐数据的缘起

正如Hadley所说，数据分析中超过80%的时间都被用于数据清理与变形等为数据分析做准备的工作。在实践中，我们往往要处理大量的不同类型与来源的数据，但是却没有一个理论告诉我们，数据清理的目标与终点是什么。在这篇文章中，Hadley把他创造plyr与ggplot2中总结出来的数据哲学概括为一个重要的思想，即整齐数据。整齐数据是可以支撑数据分析的结构化的数据集，是数据清理的终点，也是数据分析的起点。

### 整齐数据的定义

整齐数据是针对"表"这一类数据结构的定义。实际上，表是我们数据分析时使用到的最主要的数据结构。"表"是一个二维的数据结构，表的基本元素是单元格（cell），具有行（row）与列（volume）两个属性（大多数时候，行列是不可以互换的）。每个单元格中储存一个数据。

回忆一下，我们讨论过的变量与观测单元的概念。变量指的是对研究对象的某一个属性（概念）的测度，而一个观测是属于同一个观测个体的所有变量。当我们提到表格时，一般默认表格的"第零行"为表头，用于储存变量名，而非数据值。

当一个表格满足

**1. 每一列都储存同一个变量，且相同变量都储存在同一列**

**2. 每一行都储存对观测单元的一个观测，且同一个观测都储存于同一行**

**3. 一个表储存同一个观测单元**

三个条件时，该表便是一组整齐数据。违背以上三个条件中的任何一个的表格都不是整齐数据，我们将其称为凌乱数据（Messy data）。

我们看下面的三组数据，分别包含了

当我们使用表1计算xx变量的平均数时，需要通过复杂的数据提取与计算。如果我们使用表2，则可以轻松的通过mean(xx)来达到同样的目的。这一下子就看出了整齐数据蕴含的力量。

实际上，整齐数据直接对应了研究设计中使用的数据结构，因此可以直接用于回归分析。另一方面，R中的可视化工具ggplot2也是基于整齐数据而设计的，整齐数据在可视化中也有着天然的不可替代的优势。

### 数据的凌乱点

Hadley在他的论文中总结了5种最常见的凌乱点：

1.  表头不是变量名，而是数据值
2.  多个变量被储存在同一列
3.  变量同时被储存在行与列中
4.  存在两个及以上的观测单元
5.  同一组观测被存在多行中

当然，我们永远不能低估数据的凌乱程度，在实际数据分析时遇到超出想象的情形也无需大惊小怪

## 关系代数

在介绍如何整理数据之前，我们先介绍数据整理背后的，数据库查询语言数学基础，关系代数。

关系代数定义了使用表格组织数据所对应的数据运算。实际上，表格可以表示一切的数据，因此定义出一套简洁高效的基于表格的运算，就可以实现数据计算的正交分解，一部分计算机科学家可以专注于优化关系代数的实现效率，而其他人则只用了解最简单的约定来完成自己数据分析工作，可充分利用前者的集体智慧。因此，关系代数是表格数据处理的最佳工具。

关系代数将表格的行视作集合，列视作属性。经济学里面对表格的理解反而更直接，行对应于观测，列对应于变量。高度概括起来就是一句话：一切都是关系，关系就是表格。

关系代数运算包括：

- 集合运算：交集、并集与补集。

- 线性运算：笛卡尔积、（按列）投影与（按行）选取。

常用的非关系代数的扩展运算包括：

- 关系运算：两个表格之间连接，分为左（右）连接、内连接、外连接。

- 分组与聚集运算：一张大表聚集成小表，转换unit

现在看起来关系代数是如此简介明了，但实际上关系代数的发展为数据库技术的应用与普及扫清了障碍，也为当代大数据与人工智能奠定了基础。相关研究诞生了1981年（Edgar F. Codd）和2014年两届图灵奖（Michael Stonebraker）。

参考资料：Hellerstein, Joseph M. and Michael Stonebraker. Readings in Database Systems.


### SQL语言

SQL是Structured Query Language，是关系数据库的基本语言。关系型数据库是以关系代数为基础的数据管理程序，是对关系代数的程序实现。SQL是一种描述型语言，描述选取什么的数据出来。尽管SQL也有写入数据的语法，但是在使用中往往读的次数远大于写的次数。

当前主要的开源关系型数据库有三个：MariaDB (MySQL 的后继)、PostgreSQL以及SQLite。MySQL目前使用最广泛；PostgreSQL支持商用开发并包括了灵活的NoSQL语法与数据，被其拥趸认为是未来最主流的选择，SQLite是轻量级数据库用于教学与小型网络数据。

在`R`语言中的data.frame数据结构整合了SQL的思想，使其成为处理表格数据的最佳工具。`Python`社区受到`R`的影响，开发了`pandas`包来实现data.frame及其基本操作。

`MapReduce`分布式大数据算法也受到关系代数的影响，一般都会使用关系代数作为平台高级接口。例如，`Hadoop`生态圈和`Spark`等。

尽管本书更推荐PostgreSQL作为服务器数据库的选择，但是考虑到MySQL的应用范围, 本书也会花篇幅介绍MySQL。

## SQLite3

### 安装

Linux用户使用`apt install sqlite3`，macOS用户使用`brew install sqlite3`来安装。可以使用客户端`sqlitebrowser`提供的窗口界面浏览数据库，分别使用`apt`与`brew`安装。

### 数据类型

sqlite支持的数据类型如下：

|变量|类型|
|:-----|:----------|
|NULL|缺失值|
|INTEGER|整数|
|REAL|8字节的浮点数|
|TEXT|文本长度不限|
|BLOB|二进制对象|

### 新建数据库与表格

`sqlite`是轻量级数据库，每个数据库单独储存在一个`db`文件中。使用`sqlite3 testDB.db`来新建数据库。

一般约定数据库语言的关键字全部使用大写字母，命令使用`;`结束。

除去直接从外部读入数据外，还可以通过`CREATE`命令与`INSERT`命令创建表格并插入数据。

```
CREATE TABLE A (ID INTEGER, name TEXT, age INTEGER, gender TEXT);
INSERT INTO A VALUES(1, '张三', 23, 'M');
INSERT INTO A VALUES(2, '李四', 24, 'M');
INSERT INTO A VALUES(3, '王二', 22, 'F');
INSERT INTO A VALUES(4, '赵五', 23, 'M');
```

在SQL语言中，一切命令都是查询，使用`SELECT`，例如`SELECT * FROM A;`

### 集合运算

交运算是INTERSECT，并运算是UNION，补运算是EXCEPT。

```
CREATE TABLE B (ID INTEGER, name TEXT, age INTEGER, gender TEXT);
INSERT INTO B VALUES (2, '李四', 24, 'M'),(3, '王二', 22, 'F'),(4, '赵五', 23, 'M'),(5, '刘六', 21, 'F');
SELECT * FROM A INTERSECT SELECT * FROM B;
SELECT * FROM A UNION SELECT * FROM B;
SELECT * FROM A EXCEPT SELECT * FROM B;
```

### 线性运算

#### 投影

使用`SELECT`命令来选择做投影运算，选择特定的列，对于大表，查询时要加上LIMIT关键字来限制选出的行数，避免卡死进程。

```
SELECT ID FROM A LIMIT 1;
```

#### 选取

WHERE关键字配合差查询语句起到过滤的作用，可以搭配等式与不等式、IS NULL、BETWEEN、IN、LIKE等表达式使用。

```
SELECT * FROM A WHERE ID = 1;
SELECT * FROM A WHERE ID > 1;
SELECT * FROM A WHERE ID BETWEEN 1 AND 3;
SELECT * FROM A WHERE name LIKE '张%';
SELECT * FROM A WHERE age IN (21,24);
```

#### 笛卡尔积

笛卡尔积使用CROSS JOIN实现

```
SELECT A.ID,B.NAME FROM A CROSS JOIN B;
```

或者直接联合查询

```
SELECT * FROM A,B;
```

#### 扩张

ALTER TABLE 命令用于表格修改，`UPDATE`命令用于修改列的值。

```
ALTER TABLE A ADD COLUMN score real;
UPDATE A SET score = 0;
UPDATE A SET score = 60 WHERE ID = 1;
```

注意，数据库的思想是表格查询远大于表格修改，表格如果需要频繁修改，应该使用外部工具，例如R或者Python来处理。

### 数据连接

三种连接方式对应：INNER JOIN，LEFT JOIN，FULL OUTER JOIN，ON关键字表示匹配条件

```
SELECT A.ID, B.NAME FROM A INNER JOIN B ON A.ID = B.ID;
```

### 数据分组与汇总

分组使用GROUP B， 可以搭配函数使用来进行汇总

```
SELECT COUNT(*), gender FROM A GROUP BY gender;
```

### 其他

排序使用ORDRR BY，删除使用DROP


### 数据读写

csv数据读入命令如下

```
.mode csv
.import /Users/birdstone/dropbox/bigdata_econ_2023/data/pub_211.csv pub_211
```

调整模式使得，可以查询到表格的表头

```
.header on
.mode column
pragma table_info('pub_211');
SELECT * FROM pub_211 LIMIT 10;
```


使用output命令可以转存数据成为sql语句

```
.output /Users/birdstone/dropbox/bigdata_econ_2023/data/new.sql
.dump pub_211
.quit
```

### 索引与键

索引算法将数据的不重复的信息储存在一个B-tree当中，查询建立索引的列。如果不使用索引，每次查询都要遍历整个表，使用索引可以极大改善查询速度。因此建议每次查询之前都确认是否能使用索引。

```
CREATE INDEX inst_index ON pub_211(inst_cn, pubyear);
SELECT * FROM pub_211 WHERE inst_cn = "湖南大学" and pubyear = 2022;
```

## MySQL

#### 安装

macOS中自带了MySQL，可以在`settings`搜索`MySQL`来找到并开启。

也可以使用`brew`管理器来安装，具体命令如下：

```
brew update
brew install mysql
```

通过`brew`安装的mysql会在路径`/usr/local/Cellar`，启动文件`mysql.server`位于`/usr/local/Cellar/mysql/{版本号}/support-files`内。

在启动mysql之前，需要做如下初始化操作。

1. 进入`/usr/local/Cellar/mysql/{版本号}`，执行命令`bin/mysqld --initialize-insecure --user=mysql`初始化储存数据的路径，此时root账号没有设置密码。

2. 进入于`/usr/local/Cellar/mysql/{版本号}/support-files`，执行命令`mysql.server start`启动mysql（`mysql.server stop`为关闭mysql命令）。

3. 启动完成后，使用`mysql -u root --skip-password`登录mysql，并使用`ALTER USER 'root'@'localhost' IDENTIFIED BY 'root-password'`修改密码。

\BeginKnitrBlock{rmdnote}<div class="rmdnote">关于PID报错，“The server quit without updating PID file”，是一个指向不明确的报错。很多原因都可能指向这个错误，直接搜索该错误会得出完全不同的解决方案。在实际处理过程中，应该去看错误文件“username.local.err”，再根据文件内部的详细描述来搜索具体问题的解决方案、</div>\EndKnitrBlock{rmdnote}

### 基本命令

MySQL的绝大多数命令与SQLite相同，这里列举出来不同的地方。

第一个不同的地方是新建数据库与展示数据表格。

```
CREATE DATABASE testDB;
SHOW DATABASES;
USE testDB;
SHOW TABLES;
DESC A;
```

第二个不同地方是读写数据，读入数据使用LOAD DATA INFILE

```
LOAD DATA INFILE "~/pub_211.csv" INTO TABLE pub_211 FIELDS TERMINATED BY "," LINES TERMINATED BY "\n" IGNORE 1 lines;
```

`FIELDS TERMINATED BY``指定数据的分列符，` LINES TERMINATED BY`指定分行符，`IGNORE`表示导入时忽略表头。

导出数据使用`SELECT * INTO OUTFILE`

```
SELECT * INTO OUTFILE "~/pub_211new.csv" FROM  pub_211 FIELDS TERMINATED BY "," LINES TERMINATED BY "\n" IGNORE 1 lines;
```

转存数据使用mysqldump

```
mysqldump db_name > backup-file.sql
```

读入dump出的数据库使用mysql命令

```
mysql db_name < backup-file.sql
```

最后，MySQL是面向多用户的应用场景，因此需要管理用户权限。使用root用户登录后，可以使用如下命令创建新用户。

```
CREATE USER 'username'@'localhost' IDENTIFIED BY 'password';
```

上述命令中，username为用户名，'@'后面指定该用户可以使用的服务器地址，localhost表示该用户仅限于登录本机后使用，password是用户密码。

使用GRANT命令修改用户权限。

```
GRANT SELECT ON db.table TO 'username'@'localhost';
```

上述命令表示赋予username在数据库db总的table表的查询权限。


## Postgresql


## dplyr

关系代数思想在R中的直接对应了`data.frame`数据结构。R语言大神Hadley Wickham开发了`dplyr`和`tidyr`包来实现各类关系代数运算与其他表格运算。这两个包的语法非常直观、灵活的，是数据整理的最佳工具，无出其右。

### 集合运算
`intersect`，`union`, `setdiff`函数可以直接用于`data.frame`的交并补运算。


```r
library(dplyr)
stdnt_1 <- data.frame(ID = 1:4,
                      name = c("张三","李四","王二","赵五"),
                      age = c(23,24,22,23),
                      gender = c("M","M","F","M"))
stdnt_2 <- data.frame(ID = c(2,5), 
                      name = c("李四","刘六"),
                      age = c(24,21), 
                      gender = c("M","F"))

intersect(stdnt_1,stdnt_2)
```

```
##   ID name age gender
## 1  2 李四  24      M
```

```r
union(stdnt_1,stdnt_2)
```

```
##   ID name age gender
## 1  1 张三  23      M
## 2  2 李四  24      M
## 3  3 王二  22      F
## 4  4 赵五  23      M
## 5  5 刘六  21      F
```

```r
setdiff(stdnt_1,stdnt_2)
```

```
##   ID name age gender
## 1  1 张三  23      M
## 2  3 王二  22      F
## 3  4 赵五  23      M
```

### 线性运算

#### 投影（选择变量）

`select`函数用于投影操作，第一种用法是使用变量名组成的向量作为参数选出对应的变量，第二种办法是使用数字组成的向量选出对应位置的变量，例如：


```r
select(stdnt_1, c(name,age)) 
```

```
##   name age
## 1 张三  23
## 2 李四  24
## 3 王二  22
## 4 赵五  23
```

```r
select(stdnt_1, c(1,3))
```

```
##   ID age
## 1  1  23
## 2  2  24
## 3  3  22
## 4  4  23
```
当参数加入`-`时，删除对应的变量，例如


```r
select(stdnt_1, -c(name,age))
```

```
##   ID gender
## 1  1      M
## 2  2      M
## 3  3      F
## 4  4      M
```

`dplyr`提供了配套函数，让`select`更加灵活

|配套函数|功能|
|:-----|:-----------|
|`starts_with("a")`|选择名字以`a`开头的变量|
|`ends_with("a")`|选择名字以`a`结尾的变量|
|`contains("a")`|选择名字中含有`a`的变量|
|`matches(pattern)`|选择正则表达式匹配的变量|
|`num_range("x", 1:3)`|选择x1, x2, x3|

#### 选取（选择观测）

`slice`函数可以使用数值向量来选择对应的行，`-`表示删除对应行


```r
slice(stdnt_1, 1:3)
```

```
##   ID name age gender
## 1  1 张三  23      M
## 2  2 李四  24      M
## 3  3 王二  22      F
```

```r
slice(stdnt_1, -2)
```

```
##   ID name age gender
## 1  1 张三  23      M
## 2  3 王二  22      F
## 3  4 赵五  23      M
```

`slice_`扩展函数

|函数|功能|
|:-----|:-----------|
|`slice_head(n)`|提取前n行，等价于`head(n)`|
|`slice_tail(n=5)`|提取最后n行，等价于`tail(n)`|
|`slice_min(x, n)`|提取x值最小的n行|
|`slice_max(x, n=1)`|提取x值最大的n行|

`filter`函数则可以用逻辑判断来选取满足条件的行，不同条件用`,`分割，例如选出A中年龄大于23，且性别为男性的行，可以使用下面的代码。


```r
filter(stdnt_1, age > 23, gender == "M")
```

```
##   ID name age gender
## 1  2 李四  24      M
```

#### 笛卡尔积

笛卡尔积在研究设计中对应了面板数据，即每一个观测单位上都有观测值的数据结构。`tidyr::expand_grid`函数可以生成其参数的所有可能的组合。


```r
library(tidyr)
expand_grid(ID = 1:6, year = 1998:2000) 
```

```
## # A tibble: 18 × 2
##       ID  year
##    <int> <int>
##  1     1  1998
##  2     1  1999
##  3     1  2000
##  4     2  1998
##  5     2  1999
##  6     2  2000
##  7     3  1998
##  8     3  1999
##  9     3  2000
## 10     4  1998
## 11     4  1999
## 12     4  2000
## 13     5  1998
## 14     5  1999
## 15     5  2000
## 16     6  1998
## 17     6  1999
## 18     6  2000
```

```r
stdnt <- union(stdnt_1, stdnt_2)
expand_grid(stdnt, year = 1998:2000)
```

```
## # A tibble: 15 × 5
##       ID name    age gender  year
##    <dbl> <chr> <dbl> <chr>  <int>
##  1     1 张三     23 M       1998
##  2     1 张三     23 M       1999
##  3     1 张三     23 M       2000
##  4     2 李四     24 M       1998
##  5     2 李四     24 M       1999
##  6     2 李四     24 M       2000
##  7     3 王二     22 F       1998
##  8     3 王二     22 F       1999
##  9     3 王二     22 F       2000
## 10     4 赵五     23 M       1998
## 11     4 赵五     23 M       1999
## 12     4 赵五     23 M       2000
## 13     5 刘六     21 F       1998
## 14     5 刘六     21 F       1999
## 15     5 刘六     21 F       2000
```

`nesting`函数可以看作是`expand_grid`的反函数，可以从数据中提炼出不重复的组合。

```r
stdnt_panel <- expand_grid(stdnt, year = 1998:2000)
stdnt_panel_dup <- rbind(stdnt_panel,stdnt_panel)
nesting(stdnt_panel_dup)
```

```
## # A tibble: 15 × 5
##       ID name    age gender  year
##    <dbl> <chr> <dbl> <chr>  <int>
##  1     1 张三     23 M       1998
##  2     1 张三     23 M       1999
##  3     1 张三     23 M       2000
##  4     2 李四     24 M       1998
##  5     2 李四     24 M       1999
##  6     2 李四     24 M       2000
##  7     3 王二     22 F       1998
##  8     3 王二     22 F       1999
##  9     3 王二     22 F       2000
## 10     4 赵五     23 M       1998
## 11     4 赵五     23 M       1999
## 12     4 赵五     23 M       2000
## 13     5 刘六     21 F       1998
## 14     5 刘六     21 F       1999
## 15     5 刘六     21 F       2000
```


在实际的研究设计中，第一步就是确定最终的数据结构，这其中的关键是观测单位。根据观测单位，又可以将面板数据分为平衡面板数据与非平衡面板数据，这两种数据结构都可以通过`expand_grid`函数生成。


#### 扩张

线性空间的扩张对应于生成新的变量，`mutate`函数可以生成新的变量或对现有变量进行赋值，例如，在回归分析汇总我们经常会加入年龄的平方项


```r
mutate(stdnt, 
       age2 = age^2,
       name = factor(name),
       female = as.numeric(gender == "M"))
```

```
##   ID name age gender age2 female
## 1  1 张三  23      M  529      1
## 2  2 李四  24      M  576      1
## 3  3 王二  22      F  484      0
## 4  4 赵五  23      M  529      1
## 5  5 刘六  21      F  441      0
```

假设这些同学的平时和期末成绩如下表，平时成绩占比35%，期末成绩占比65%，那么可以计算每个同学最终的课程成绩。


```r
score <- data.frame(ID = 1:5, 
                    hw1 = c(90,87,99,80,100),
                    hw2 = c(95,0,88,98,98),
                    hw3 = c(80,0,90,85,95),
                    final = c(83,60,88,90,94))
mutate(score, total = 0.35*(hw1+hw2+hw3)/3+0.65*final)
```

```
##   ID hw1 hw2 hw3 final    total
## 1  1  90  95  80    83 84.86667
## 2  2  87   0   0    60 49.15000
## 3  3  99  88  90    88 89.51667
## 4  4  80  98  85    90 89.18333
## 5  5 100  98  95    94 95.28333
```

`transmute`函数可以在生成新的变量的同时，删除掉所有老的变量。


```r
transmute(score, total = 0.35*(hw1+hw2+hw3)/3+0.65*final)
```

```
##      total
## 1 84.86667
## 2 49.15000
## 3 89.51667
## 4 89.18333
## 5 95.28333
```

`mutate`可以搭配其他窗口函数[^1](窗口函数是SQL语言中在不改变行数的情况下返回极值、排序等结果的函数)使用，满足用户多样化的需求。

|函数                |功能                               |
|--------------------|-----------------------------------|
|`pmin`、`pmax`      |多个变量逐行对比，返回相应变量的最小、最大值|
|`cummin`、 `cummax` |单个变量的累计最小、最大值|
|`cumsum`、`cumprod` |累计求和、乘积|
|`between`           |判断是否介于两个值之间|
|`cume_dist`         |小于等于当前值的行数占总行数比重|
|`cumall`、`cumany`  |累计是否全部（任一）为TRUE      |
|`cummean`           |累计均值                    |
|`lead(n)`、`lag(n)` |当前行的前（后）n行的结果|
|`ntile(n)`          |将数据平分成n份后，返回当前行所属组别|
|`dense_rank`        |当前行的排名，排名不间断|
|`min_rank`        |当前行的排名，排名允许间断，最大排名数与行数相同|
|`percent_rank`        |当前行的百分比排名           |
|`row_numbers`        |当前行数|

**课堂练习**

1. 选出所有不及格同学的学号，并储存成一个向量（使用pull函数）

2. 选出所有不及格同学的姓名、性别

3. 选出班上成绩最好的两个同学的姓名与性别

### 管道

在实际的数据整理中，我们往往要对表格进行连续的计算，如果每一次计算都进行一次函数和赋值，不仅会让代码变得冗余，而且可读性差。`magrittr`包（`tidyverse`的组成部分）借鉴命令行中的管道操作符的概念，在R中引入了管道操作符`%>%`。从4.1.0版本以上的R的基本包中也引入了管道操作符`|>`。在大部分情况下，这两种管道操作符没有区别。由于`tidyverse`是我们推荐的最佳工具，因此本教程依然使用`%>%`。

`%>%`的作用方式是把管道符左边的结果传输过去成为管道符右边函数的第一个参数。例如，


```r
score %>% 
  mutate(total = 0.35*(hw1+hw2+hw3)/3+0.65*final) %>% 
  filter(total < 60) %>% 
  pull(ID)
```

```
## [1] 2
```
### 数据连接

在实践中，信息往往被存在不同的表格当中，这就要求我们把不同的表格连接起来。表格之间的连接运算可以分为内连接（反连接）、左连接（右连接）和全连接。

#### 表格的主键

连接运算依赖一个重要的概念，主键（primary key），即表格中不重复的列。理论上，任何表格都应该确定其主键，主键可以是一列也可以是多列的组合。可以使用count函数确定主键。


```r
stdnt %>% 
  count(ID) %>% 
  filter(n>1) %>% 
  nrow()
```

```
## [1] 0
```

#### 内连接 （inner_join）

内连接仅仅匹配两个表之间的键可以匹配的结果。`dplyr`提供了`inner_join`函数来实现内连接，`by`参数用于指定两边表格的键。例如，


```r
inner_join(score, stdnt_1, by = "ID")
```

```
##   ID hw1 hw2 hw3 final name age gender
## 1  1  90  95  80    83 张三  23      M
## 2  2  87   0   0    60 李四  24      M
## 3  3  99  88  90    88 王二  22      F
## 4  4  80  98  85    90 赵五  23      M
```

```r
# 等价于
score %>% inner_join(stdnt_1, by = "ID")
```

```
##   ID hw1 hw2 hw3 final name age gender
## 1  1  90  95  80    83 张三  23      M
## 2  2  87   0   0    60 李四  24      M
## 3  3  99  88  90    88 王二  22      F
## 4  4  80  98  85    90 赵五  23      M
```

为了保障研究设计执行的严谨性，可以设置参数为`multiple = "error"`，此时当两表一对多链接关系时，`inner_join`会报错。



```r
score %>% inner_join(stdnt_panel, by = "ID", multiple = "error")
```

`multiple`参数的缺省值为`all`，即程序正常运行，默认为一对多匹配。`multiple`参数还可以取值为`any`，`first`，`last`分别表示从多个匹配中选取任意或者第一个或者最后一个值来匹配。我们不推荐这样的参数设置，会让计算结果不可控。


```r
score %>% inner_join(stdnt_panel, by = "ID")
```

```
##    ID hw1 hw2 hw3 final name age gender year
## 1   1  90  95  80    83 张三  23      M 1998
## 2   1  90  95  80    83 张三  23      M 1999
## 3   1  90  95  80    83 张三  23      M 2000
## 4   2  87   0   0    60 李四  24      M 1998
## 5   2  87   0   0    60 李四  24      M 1999
## 6   2  87   0   0    60 李四  24      M 2000
## 7   3  99  88  90    88 王二  22      F 1998
## 8   3  99  88  90    88 王二  22      F 1999
## 9   3  99  88  90    88 王二  22      F 2000
## 10  4  80  98  85    90 赵五  23      M 1998
## 11  4  80  98  85    90 赵五  23      M 1999
## 12  4  80  98  85    90 赵五  23      M 2000
## 13  5 100  98  95    94 刘六  21      F 1998
## 14  5 100  98  95    94 刘六  21      F 1999
## 15  5 100  98  95    94 刘六  21      F 2000
```

反过来，如果左表与右表是多对一的关系，设置参数`multiple = "error"`时，也不会报错。


```r
stdnt_panel %>% inner_join(score, by = "ID",multiple = "error")
```

```
## Warning: Specifying `multiple = "error"` was deprecated in dplyr 1.1.1.
## ℹ Please use `relationship = "many-to-one"` instead.
## Call `lifecycle::last_lifecycle_warnings()` to see where this warning was
## generated.
```

```
## # A tibble: 15 × 9
##       ID name    age gender  year   hw1   hw2   hw3 final
##    <dbl> <chr> <dbl> <chr>  <int> <dbl> <dbl> <dbl> <dbl>
##  1     1 张三     23 M       1998    90    95    80    83
##  2     1 张三     23 M       1999    90    95    80    83
##  3     1 张三     23 M       2000    90    95    80    83
##  4     2 李四     24 M       1998    87     0     0    60
##  5     2 李四     24 M       1999    87     0     0    60
##  6     2 李四     24 M       2000    87     0     0    60
##  7     3 王二     22 F       1998    99    88    90    88
##  8     3 王二     22 F       1999    99    88    90    88
##  9     3 王二     22 F       2000    99    88    90    88
## 10     4 赵五     23 M       1998    80    98    85    90
## 11     4 赵五     23 M       1999    80    98    85    90
## 12     4 赵五     23 M       2000    80    98    85    90
## 13     5 刘六     21 F       1998   100    98    95    94
## 14     5 刘六     21 F       1999   100    98    95    94
## 15     5 刘六     21 F       2000   100    98    95    94
```

反连接是内连接的反向操作，即只保留第一个表格中无法匹配第二个表格的结果。


```r
score %>% anti_join(stdnt_1, by = "ID")
```

```
##   ID hw1 hw2 hw3 final
## 1  5 100  98  95    94
```

注意，内连接是对称运算，而反连接是非对称运算。

#### 左连接（left_join）、右连接（right_join）与全连接（full_join）

左连接是内连接的扩展运算，无论是否key匹配，左表的所有观测都会保留，并将缺失变量（下面的例子中是`name`、`age`、`gender`）填充为`NA`，左连接也是非对称运算。

当两边的变量名字不同时，可以使用`by=c("左名"="右名")`的格式指定key的对应关系。


```r
score %>% left_join(stdnt_1, by = "ID")
```

```
##   ID hw1 hw2 hw3 final name age gender
## 1  1  90  95  80    83 张三  23      M
## 2  2  87   0   0    60 李四  24      M
## 3  3  99  88  90    88 王二  22      F
## 4  4  80  98  85    90 赵五  23      M
## 5  5 100  98  95    94 <NA>  NA   <NA>
```

`dplyr`还提供了`right_join`函数来保留右表中所有的观测。在实践中，左连接就足够满足所有的需求了。

`full_join`可以保留左右两表的所有观测。`semi_join`类似于`inner_join`但是只保留左表的变量，实质上是一个筛选函数。

**课堂练习**

1. 使用连接函数，完成上一个练习的第2题与第3题


### 数据分组与汇总

可以使用`group_by`函数来对数据进行分组，`group_by`可以指定一个或多个变量作为分组依据。`ungroup`函数可以去除数据的分组。

本节的案例使用科学家的论文数据，[scientst_pub.csv](https://www.dropbox.com/t/iEGB5Ow4j7GkpeM3)。

#### 分组后生成新变量

数据分组后使用mutate生成新变量时，变量在每一组内进行计算赋值，不改变行数。搭配各种窗口函数可以实现多种目的。


```r
library(readr)
paper <- read_csv("scientist_pub.csv")
```

```
## Rows: 2535 Columns: 13
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (7): uniqueID, addr, item_type, wos, fullname, surname, givenname
## dbl (6): ut_char, pub_year, type, startyear, endyear, auseq
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```r
paper %>% group_by(uniqueID) %>% mutate(num_pub = n())
```

```
## # A tibble: 2,535 × 14
## # Groups:   uniqueID [10]
##    ut_char uniqueID addr  item_type pub_year  type startyear endyear wos   auseq
##      <dbl> <chr>    <chr> <chr>        <dbl> <dbl>     <dbl>   <dbl> <chr> <dbl>
##  1 2.08e11 1_91     natl… Meeting …     2009     2      2006    2010 natl…     5
##  2 2.08e11 1_91     natl… Meeting …     2009     1      2006    2010 natl…     1
##  3 2.08e11 1_449    univ… Meeting …     2010     1      2006    2009 univ…     2
##  4 2.08e11 1_449    univ… Meeting …     2010     1      2006    2009 univ…     4
##  5 2.09e11 1_449    univ… Meeting …     2012     1      2006    2009 univ…     7
##  6 2.09e11 1_449    univ… Meeting …     2012     1      2006    2009 univ…     3
##  7 2.52e11 1_91     natl… Article       2008     1      2006    2010 natl…     1
##  8 2.53e11 0_51     univ… Article       2008     1      2006    2015 univ…     2
##  9 2.53e11 1_91     peki… Article       2008     1      2006    2006 peki…     3
## 10 2.53e11 1_91     peki… Article       2008     1      2006    2006 peki…     3
## # ℹ 2,525 more rows
## # ℹ 4 more variables: fullname <chr>, surname <chr>, givenname <chr>,
## #   num_pub <int>
```

#### 分组后汇总

大部分情况下，`group_by`与`summarise`联合用于数据汇总。例如，下列代码可以用于计算科学家每年每种类型的论文数量。


```r
paper %>% 
  group_by(uniqueID,pub_year,item_type) %>% 
  summarise(num_pub = n())
```

```
## `summarise()` has grouped output by 'uniqueID', 'pub_year'. You can override
## using the `.groups` argument.
```

```
## # A tibble: 205 × 4
## # Groups:   uniqueID, pub_year [140]
##    uniqueID pub_year item_type        num_pub
##    <chr>       <dbl> <chr>              <int>
##  1 0_117        2006 Article                6
##  2 0_117        2006 Meeting Abstract       2
##  3 0_117        2007 Article                8
##  4 0_117        2008 Article                4
##  5 0_117        2008 Review                 4
##  6 0_117        2009 Article                8
##  7 0_117        2010 Article                4
##  8 0_117        2010 Review                 4
##  9 0_117        2011 Article                4
## 10 0_117        2012 Article               18
## # ℹ 195 more rows
```

常见的汇总函数总结如下：

|函数                |功能                               |
|--------------------|-----------------------------------|
|`min`、`max`      |最小、最大值|
|`mean`、 `median` |均值与中位数|
|`var`、`sd` |方差、标准差|
|`sum`|向量求和|
|`first`|向量的第一个值|
|`last`|向量的最后一个值|
|`nth`|向量的第n个值|
|`n`|行数|
|`n_distinct`|去重后的行数|

## 宽数据与长数据的转换

前面计算课程总分数的案例中，我们使用了一个宽数据，即不同变量被储存在不同的列中，宽数据的优势是与人类的阅读和熟悉习惯是一致，方便原始数据记录与采集，适合结果展示。但是并不适合进一步的数据分析，设想如果有十次平时作业的话，计算总成绩的代码会非常冗长。

`tidyr`新设计了`pivot_longer`和`pivot_wider`函数，代替之前的`gather`和`spread`函数，来实现长宽数据转换。新设计的函数语法更加直观，功能更全，是长宽数据转换的最佳工具。

### 宽数据转长数据

宽数据转长数据的最简单情形是，每一行是一个观测，但是同一变量储存在不同列中。


```r
score %>% 
  pivot_longer(cols = hw1:final,
               names_to = "test",
               values_to = "score")
```

```
## # A tibble: 20 × 3
##       ID test  score
##    <int> <chr> <dbl>
##  1     1 hw1      90
##  2     1 hw2      95
##  3     1 hw3      80
##  4     1 final    83
##  5     2 hw1      87
##  6     2 hw2       0
##  7     2 hw3       0
##  8     2 final    60
##  9     3 hw1      99
## 10     3 hw2      88
## 11     3 hw3      90
## 12     3 final    88
## 13     4 hw1      80
## 14     4 hw2      98
## 15     4 hw3      85
## 16     4 final    90
## 17     5 hw1     100
## 18     5 hw2      98
## 19     5 hw3      95
## 20     5 final    94
```
在上面的例子中，`cols`参数用于指定转换的变量，其用法与`select`函数中指定变量的方式相同；`names_to`参数用于指定一个新变量名（缺省值为`name`），储存宽数据中的列标题；`values_to`用于指定另一个新变量名（缺省值为`value`），储存宽数据中的变量取值；如果转换结果中不希望保留`NA`，可以设置参数`values_drop_na=TRUE`。

在这个例子中，平时作业成绩与期末作业成绩，也可以看做是两个不同的变量，此时只用将`hw`对应的变量转化为长数据。


```r
score %>% 
  pivot_longer(cols = starts_with("hw"),
               names_to = "homework",
               values_to = "score")
```

```
## # A tibble: 15 × 4
##       ID final homework score
##    <int> <dbl> <chr>    <dbl>
##  1     1    83 hw1         90
##  2     1    83 hw2         95
##  3     1    83 hw3         80
##  4     2    60 hw1         87
##  5     2    60 hw2          0
##  6     2    60 hw3          0
##  7     3    88 hw1         99
##  8     3    88 hw2         88
##  9     3    88 hw3         90
## 10     4    90 hw1         80
## 11     4    90 hw2         98
## 12     4    90 hw3         85
## 13     5    94 hw1        100
## 14     5    94 hw2         98
## 15     5    94 hw3         95
```

我们可以直接提取homework中的数字作为变量取值，参数`names_prefix`指定了变量名称的前缀，在提取取值时会被忽略，`names_transform`定义了数值类型的转换函数。


```r
score %>% 
  pivot_longer(cols = starts_with("hw"),
               names_to = "homework",
               names_prefix = "hw",
               names_transform =as.integer,
               values_to = "score")
```

```
## # A tibble: 15 × 4
##       ID final homework score
##    <int> <dbl>    <int> <dbl>
##  1     1    83        1    90
##  2     1    83        2    95
##  3     1    83        3    80
##  4     2    60        1    87
##  5     2    60        2     0
##  6     2    60        3     0
##  7     3    88        1    99
##  8     3    88        2    88
##  9     3    88        3    90
## 10     4    90        1    80
## 11     4    90        2    98
## 12     4    90        3    85
## 13     5    94        1   100
## 14     5    94        2    98
## 15     5    94        3    95
```

更加复杂的情形是，宽数据中的一行中包括了多个观测值。我们使用官方教程中的例子来介绍，


```r
library(data.table)
household
```

```
## # A tibble: 5 × 5
##   family dob_child1 dob_child2 name_child1 name_child2
##    <int> <date>     <date>     <chr>       <chr>      
## 1      1 1998-11-26 2000-01-29 Susan       Jose       
## 2      2 1996-06-22 NA         Mark        <NA>       
## 3      3 2002-07-11 2004-04-05 Sam         Seth       
## 4      4 2004-10-10 2009-08-27 Craig       Khai       
## 5      5 2000-12-05 2005-02-28 Parker      Gracie
```

数据`household`中，每一行是包括了一个家庭的两个孩子的出生日期和名字，如果以孩子作为观测单位，则该数据中的每一样行包括了多个观测值。可以使用下面的方式来将两个观测值拆开。


```r
household %>% 
  pivot_longer(
    cols = !family, 
    names_to = c(".value", "child"), 
    names_sep = "_", 
    values_drop_na = TRUE
  )
```

```
## # A tibble: 9 × 4
##   family child  dob        name  
##    <int> <chr>  <date>     <chr> 
## 1      1 child1 1998-11-26 Susan 
## 2      1 child2 2000-01-29 Jose  
## 3      2 child1 1996-06-22 Mark  
## 4      3 child1 2002-07-11 Sam   
## 5      3 child2 2004-04-05 Seth  
## 6      4 child1 2004-10-10 Craig 
## 7      4 child2 2009-08-27 Khai  
## 8      5 child1 2000-12-05 Parker
## 9      5 child2 2005-02-28 Gracie
```

上述代码中，`.value`表示将从变量名称中取出的值作为新数据值的变量名，`names_sep`表示分隔符，联合起来看，`dob_child1`会被拆分成为`dob`和`child1`，其中`dob`会被储存成新的变量名，而`child1`作为取值存入变量`child`。同时，无需指定`values_to`参数。

**课堂练习**

科学家简历上数据的变形

### 长数据转宽数据

在需要生成描述性统计表或者直观地展示数据时，往往需要将长数据转变为宽数据。`pivot_wider`函数，作为`pivot_longer`的反函数，可以实现此功能。


```r
score_long <- score %>% 
  pivot_longer(cols = -ID,
               names_to = "test",
               values_to = "score") %>% 
  slice(-2)

score_long %>% pivot_wider(names_from = test,
                           values_from = score,
                           values_fill = 0)
```

```
## # A tibble: 5 × 5
##      ID   hw1   hw3 final   hw2
##   <int> <dbl> <dbl> <dbl> <dbl>
## 1     1    90    80    83     0
## 2     2    87     0    60     0
## 3     3    99    90    88    88
## 4     4    80    85    90    98
## 5     5   100    95    94    98
```

此处我们只介绍了`pivot`函数的基本用法。函数的设计者结合实际应用情形（对应了问卷设计里面的情形），为其赋予了众多妙用，调用`vignette("pivot")`可以看到，强烈推荐读者阅读。

我们从中选择一个常用的情形做介绍。

### 联系人数据表的案例

使用`tribble`函数生成一个联系人表格，这个表格的挑战在于没有一个联系人的ID，但是我们人类知道相邻行表示的是同一个联系人的信息。此处的思路是配合使用`mutate`和`cumsum`函数，先生成ID，然后转变为宽数据。


```r
contacts <- tribble(
  ~field, ~value,
  "name", "Jiena McLellan",
  "company", "Toyota", 
  "name", "John Smith", 
  "company", "google", 
  "email", "john@google.com",
  "name", "Huxley Ratcliffe"
)
contacts <- contacts %>% 
  mutate(
    person_id = cumsum(field == "name")
  )
contacts %>% 
  pivot_wider(
    names_from = field, 
    values_from = value
  )
```

```
## # A tibble: 3 × 4
##   person_id name             company email          
##       <int> <chr>            <chr>   <chr>          
## 1         1 Jiena McLellan   Toyota  <NA>           
## 2         2 John Smith       google  john@google.com
## 3         3 Huxley Ratcliffe <NA>    <NA>
```

**课堂练习**

科学家的cv的长数据转变为宽数据

## 其他定制操作

### 排序
`arrange`函数用于变量的顺序排列，联合`desc`可用于逆序排列。


```r
paper %>% arrange(uniqueID, desc(pub_year))
```

```
## # A tibble: 2,535 × 13
##    ut_char uniqueID addr  item_type pub_year  type startyear endyear wos   auseq
##      <dbl> <chr>    <chr> <chr>        <dbl> <dbl>     <dbl>   <dbl> <chr> <dbl>
##  1 5.05e11 0_117    univ… Article       2020     1      2004    2021 univ…     6
##  2 5.05e11 0_117    univ… Article       2020     1      2004    2021 univ…     6
##  3 5.05e11 0_117    univ… Article       2020     1      2004    2021 univ…     6
##  4 5.05e11 0_117    univ… Article       2020     1      2004    2021 univ…     6
##  5 5.05e11 0_117    univ… Article       2020     1      2004    2021 univ…     6
##  6 5.05e11 0_117    univ… Article       2020     1      2004    2021 univ…     6
##  7 5.05e11 0_117    univ… Article       2020     1      2004    2021 univ…     6
##  8 5.05e11 0_117    univ… Article       2020     1      2004    2021 univ…     6
##  9 5.24e11 0_117    univ… Article       2020     1      2004    2021 univ…    10
## 10 5.24e11 0_117    univ… Article       2020     1      2004    2021 univ…    10
## # ℹ 2,525 more rows
## # ℹ 3 more variables: fullname <chr>, surname <chr>, givenname <chr>
```

### 抽样
`sample_n`函数可以从数据框中随机无放回抽样


```r
paper %>% sample(10)
```

```
## # A tibble: 2,535 × 10
##    item_type        addr    auseq ut_char startyear surname  type wos   fullname
##    <chr>            <chr>   <dbl>   <dbl>     <dbl> <chr>   <dbl> <chr> <chr>   
##  1 Meeting Abstract natl i…     5 2.08e11      2006 JIN         2 natl… jinc    
##  2 Meeting Abstract natl i…     1 2.08e11      2006 JIN         1 natl… jinchua…
##  3 Meeting Abstract univ c…     2 2.08e11      2006 ZHANG       1 univ… zhangjun
##  4 Meeting Abstract univ c…     4 2.08e11      2006 ZHANG       1 univ… zhangjun
##  5 Meeting Abstract univ c…     7 2.09e11      2006 ZHANG       1 univ… zhangjun
##  6 Meeting Abstract univ c…     3 2.09e11      2006 ZHANG       1 univ… zhangjun
##  7 Article          natl i…     1 2.52e11      2006 JIN         1 natl… jinchua…
##  8 Article          univ v…     2 2.53e11      2006 LU          1 univ… lujiwei 
##  9 Article          peking…     3 2.53e11      2006 JIN         1 peki… jinchua…
## 10 Article          peking…     3 2.53e11      2006 JIN         1 peki… jinchua…
## # ℹ 2,525 more rows
## # ℹ 1 more variable: givenname <chr>
```

### 去重复

`distinct`函数返回变量的去重结果，如果希望保留数据框中其它变量， 可以加选项`.keep_all=TRUE`。


```r
paper %>% select(uniqueID) %>% distinct()
```

```
## # A tibble: 10 × 1
##    uniqueID
##    <chr>   
##  1 1_91    
##  2 1_449   
##  3 0_51    
##  4 1_148   
##  5 1_226   
##  6 0_394   
##  7 0_117   
##  8 1_532   
##  9 1_587   
## 10 0_496
```


### 缺失值处理

`drop_na`函数用于删除指定变量有缺失值的行。


```r
paper %>% drop_na(wos)
```

```
## # A tibble: 2,535 × 13
##    ut_char uniqueID addr  item_type pub_year  type startyear endyear wos   auseq
##      <dbl> <chr>    <chr> <chr>        <dbl> <dbl>     <dbl>   <dbl> <chr> <dbl>
##  1 2.08e11 1_91     natl… Meeting …     2009     2      2006    2010 natl…     5
##  2 2.08e11 1_91     natl… Meeting …     2009     1      2006    2010 natl…     1
##  3 2.08e11 1_449    univ… Meeting …     2010     1      2006    2009 univ…     2
##  4 2.08e11 1_449    univ… Meeting …     2010     1      2006    2009 univ…     4
##  5 2.09e11 1_449    univ… Meeting …     2012     1      2006    2009 univ…     7
##  6 2.09e11 1_449    univ… Meeting …     2012     1      2006    2009 univ…     3
##  7 2.52e11 1_91     natl… Article       2008     1      2006    2010 natl…     1
##  8 2.53e11 0_51     univ… Article       2008     1      2006    2015 univ…     2
##  9 2.53e11 1_91     peki… Article       2008     1      2006    2006 peki…     3
## 10 2.53e11 1_91     peki… Article       2008     1      2006    2006 peki…     3
## # ℹ 2,525 more rows
## # ℹ 3 more variables: fullname <chr>, surname <chr>, givenname <chr>
```

`fill`函数用于按行填充缺失值


```r
df <- data.frame(ID = c(1,NA,NA,2,NA,3),
                 score = c(90,87,99,80,100,92),
                 year = c(2021,2022,2023,2022,2023,2023))
df %>% fill(ID)
```

```
##   ID score year
## 1  1    90 2021
## 2  1    87 2022
## 3  1    99 2023
## 4  2    80 2022
## 5  2   100 2023
## 6  3    92 2023
```

`coalesce`函数可以用于`NA`值的赋值，`na_if`函数是它的反函数。


```r
df %>% mutate(ID = coalesce(ID,0),IDnew = na_if(ID,0))
```

```
##   ID score year IDnew
## 1  1    90 2021     1
## 2  0    87 2022    NA
## 3  0    99 2023    NA
## 4  2    80 2022     2
## 5  0   100 2023    NA
## 6  3    92 2023     3
```

### 拆分与合并数据列

`seperate`函数可以利用分隔符将列拆分为各自的变量列。`unite`函数是它的反函数


```r
df_sep <- read_csv(
"testid, succ/total
1, 1/10
2, 3/5
3, 2/8")
```

```
## Rows: 3 Columns: 2
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (1): succ/total
## dbl (1): testid
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```r
df_sep %>% 
  separate(
    `succ/total`, 
    into=c("succ", "total"), 
    sep="/", 
    convert=TRUE) 
```

```
## # A tibble: 3 × 3
##   testid  succ total
##    <dbl> <int> <int>
## 1      1     1    10
## 2      2     3     5
## 3      3     2     8
```

### 合并数据
`bind_rows`函数可以对两个或多个数据框纵向合并。要求变量集合是相同的，但变量次序可以不同，这一点比`rbind`函数方便。`bind_rows`函数可以将两个行数相同的数据框按行号对齐合并。


```r
paper1 <- paper %>% slice(1:10)
paper2 <- paper %>% slice(11:20)
paper1 %>% bind_rows(paper2)
```

```
## # A tibble: 20 × 13
##    ut_char uniqueID addr  item_type pub_year  type startyear endyear wos   auseq
##      <dbl> <chr>    <chr> <chr>        <dbl> <dbl>     <dbl>   <dbl> <chr> <dbl>
##  1 2.08e11 1_91     natl… Meeting …     2009     2      2006    2010 natl…     5
##  2 2.08e11 1_91     natl… Meeting …     2009     1      2006    2010 natl…     1
##  3 2.08e11 1_449    univ… Meeting …     2010     1      2006    2009 univ…     2
##  4 2.08e11 1_449    univ… Meeting …     2010     1      2006    2009 univ…     4
##  5 2.09e11 1_449    univ… Meeting …     2012     1      2006    2009 univ…     7
##  6 2.09e11 1_449    univ… Meeting …     2012     1      2006    2009 univ…     3
##  7 2.52e11 1_91     natl… Article       2008     1      2006    2010 natl…     1
##  8 2.53e11 0_51     univ… Article       2008     1      2006    2015 univ…     2
##  9 2.53e11 1_91     peki… Article       2008     1      2006    2006 peki…     3
## 10 2.53e11 1_91     peki… Article       2008     1      2006    2006 peki…     3
## 11 2.53e11 1_91     peki… Article       2008     1      2006    2006 peki…     3
## 12 2.53e11 1_91     peki… Article       2008     1      2006    2006 peki…     3
## 13 2.53e11 0_51     univ… Article       2008     1      2006    2015 univ…     2
## 14 2.53e11 1_148    univ… Article       2008     1      2005    2008 univ…     2
## 15 2.53e11 1_148    univ… Article       2008     1      2005    2008 univ…     2
## 16 2.53e11 1_91     peki… Article       2008     2      2006    2006 peki…     4
## 17 2.53e11 1_91     peki… Article       2008     2      2006    2006 peki…     4
## 18 2.53e11 1_91     peki… Article       2008     2      2006    2006 peki…     4
## 19 2.53e11 1_91     peki… Article       2008     2      2006    2006 peki…     4
## 20 2.53e11 1_226    univ… Article       2008     1      2001    2007 univ…     1
## # ℹ 3 more variables: fullname <chr>, surname <chr>, givenname <chr>
```

**课堂练习题**

计算科学家每年发表的论文之后，对该数据滞后一年。

## 数据库的连接

虽然SQL中也可以定义变量，使用流程控制语句与函数，但是语法并不友好，因此不推荐。如果涉及到循环等复杂的程序结构，可以使用外部程序调用SQL的API。

### R与数据库

R语言中使用`DBI`（Database Interface）包以及对应的`RMySQL`包和`RPostgreSQL`来操纵SQL。

#### 数据库的连接与关闭

db.connect函数用于建立R与数据库之间的联系。`RSQLite`包是R内建的，因此可以直接新建SQLite数据库

```r
mydb <- dbConnect(RSQLite::SQLite(), dbname = "my-db.sqlite")
```

连接`MySQL`则需要加载`RMySQL`包，`dbname`用于指定数据库名称，`username`和`password`对应登录密码，`host`对应数据库主机地址，如果是本地则为localhost，`port`用于指定端口。


```r
mydb <- dbConnect(RMySQL::MySQL(), 
                  dbname = "test",
                  username = "user",
                  password = "pwd**",
                  host = "192.168.1.100",
                  port = 8881)
```

理解PostgreSQL方法类似使用`RPostgreSQL`包。


```r
drv <- dbDriver("PostgreSQL")
mydb <- dbConnect(drv,
                  dbname = "test",
                  username = "user",
                  password = "pwd**",
                  host = "192.168.1.100",
                  port = 551)
```

`dbDisconnect(mydb)`用于关闭与数据库的连接。

#### 查询数据

使用`dbGetQuery`函数可以将SELECT语句查询的语句载入内存


```r
dbGetQuery(mydb, statement = 'SELECT * FROM A LIMIT 5;')
```

#### 执行语句

`dbExecute`可以执行出查询之外的其他语句，例如`UPDATE`，`INSERT`和`DELETE`等。


```r
dbExecute(mydb, 'DROP TABLE A')
```

#### 写入数据

`dbWriteTable`可将内存中的数据写入数据库，例如，


```r
dbWriteTable(mydb, "A", A)
```

### Python与数据库

Python中分别使用`sqlite`，`MySQLdb`与`psycopg2`连接数据库。以psycopg2为例，WSL与macOS先安装psycopg2模块。

```
pip3 install python-psycopg2
```

或者
```
apt install python-psycopg2
```

#### 数据库的连接与关闭


```python
import psycopg2
# 连接数据库
conn = psycopg2.connect("dbname=test user=postgres")
# 建立执行指针
cur = conn.cursor()
# 关闭连接
conn.close()
```

#### 执行命令


```python
# 执行命令
cur.execute("SELECT * FROM my_data")

# 获取查询数据
records = cur.fetchall()
```


## data.table


