# [Parboiled](https://blog.csdn.net/MaseratiD/article/details/102824691)

## **Parboiled概述**

Parboiled是一个混合的Java/ Scala库，提供了基于解析表达文法（PEGs）的轻量级、易用、功能强大的任意输入文本解析。

图形数据库Neo4j使用Parboiled解析查询语言Cypher。

- 特点

  1.  用户可以以某种方式指定解析语法，并使它快速，轻松地工作。
  2.  解析表达式语法的强大表达能力
  3.  支持强大而灵活的解析器操作
  4.  出色的解析错误报告和恢复
  5.  良好的性能
  6.  易于集成
  7.  轻量级，易于使用

  Parboiled提供了递归下降PEG解析器的实现，该实现可对用户指定的PEG规则进行操作。



## 解析表达文法（PEG）

以纯公式的形式展现递归下降解析器的基础语法，对这个具体的解析器采用的实现方法没有限定。

与上下文无关文法（CFG）很像，但存在区别：

1. PEG不存在二义性，只产生一个确定的语法分析树。
2. PEG的选择操作符是有序的。如果第一个可能成功了，那么第二个可能就忽略。

### 文法的组成部分：

-    一个有限的非终结符的集合![N](https://private.codecogs.com/gif.latex?N)
-    一个有限的终结符的集合![\sum](https://private.codecogs.com/gif.latex?%5Cdpi%7B80%7D%20%5Csum)，和![N](https://private.codecogs.com/gif.latex?N)没有交集
-    一个有限的解析规则的集合![P](https://private.codecogs.com/gif.latex?P)
-    一个被称为起点表达式的解析表达式![e_{S}](https://private.codecogs.com/gif.latex?e_%7BS%7D)

   ![P](https://private.codecogs.com/gif.latex?P)中每一个解析规则以![A\leftarrow e](https://private.codecogs.com/gif.latex?A%5Cleftarrow%20e)的形式出现，这里![A](https://private.codecogs.com/gif.latex?A)是一个非终结符，![e](https://private.codecogs.com/gif.latex?e)是一个解析表达式。解析表达式是类似正则表达式的层次表达式。

### 原子解析表达式

-    任何的非终结符
-    任何的终结符
-    空字符串![\varepsilon](https://private.codecogs.com/gif.latex?%5Cvarepsilon)

### 解析表达式操作符

![](E:\doc\学习积累\md\java\lib\Parboiled\parboided-1.png)

### 优点

1.    PEG更加严格更加强大，可以很好地成为正则表达式的替代品。
2.    PEG不存在二义性。

###  缺点

1.    PEG不能表达左递归的解析规则。
2.    未能被广泛应用。

### 使用Parboiled的两个阶段

