---
title: 'Oracle的存储过程——匿名块、函数、过程、包'
date: 2018-01-30 15:02:00
categories:
- 数据库
tags:
- Oracle
- 存储过程
---

对于 Oracle 数据库开发的入门玩家，很可能对“存储过程”的概念感到困惑。

PL/SQL 作为一种数据库编程语言，以数据表作为工作对象，其主要实现途径就是存储过程。

存储过程是一个通俗的统称，它可以细分为匿名块（Anonymous Block）、函数（Function）、过程（Procedure）、包（Package）。

<!-- more -->

# 一、匿名块（Anonymous Block）

匿名块是能够动态地创建和执行的语句块，不会以持久化的方式将代码作为数据库对象储存在数据库中。以下是一个简单的匿名块示例：

![Oracle](/post-images/1561792434340.png)

在只需要使用一次的场景下，可以使用**匿名块**这种简单便捷的方式。

但在代码需要复用的场景中，则需要使用**函数**、**过程**或**包**，把语句编译到数据库中。

PL/SQL 的函数（Function）和存储过程（Procedure），可以理解为匿名块（Anonymous Block）的升级版，函数和存储过程就是编译成数据库对象的匿名块。它们相对于匿名块，具有以下优点：

1. 编译为数据库对象，可以多次重复使用

2. 可以处理输入和输出参数

3. 可以逐条语句执行进行测试

# 二、函数（Function）

函数的编写格式如下：

![Oracle](/post-images/1561792707553.png)

## 函数的编写

有几个需要注意的地方：

1. 定义函数输入参数时，可以省略“in”关键字，即：fnPlusAB(paramA integer,paramB integer,……)。

2. 定义函数输出参数时，必须带有“out”关键字，即：fnPlusAB(……,paramC out varchar2)。

3. 函数必须定义返回值类型，并且，在 begin 和 end 之间的语句，必须有返回语句，即 return xxx。

4. 如果 begin 和 end 之间的语句包含分支等，每一个分支必须有一个返回值，即 return xxx。

## 函数的调用

需要注意，必须有一个变量来接收函数的返回值，或用表达式调用，如下两种格式：

1. 变量赋值

在这段语句中，函数返回值是 3，因此 l_C 接收返回值，变为 3，且 p_Msg 输出参数值为'A+B=3'。

`l_C:=fnPlusAB(1,2,p_Msg);`

2. 条件表达式

在这段语句中，函数返回值是 3，因此 if 条件语句通过判断，且 p_Msg 输出参数值为'A+B=3'。

```sql
if fnPlusAB(1,2,p_Msg)=3 then
	......
end if;
```

如果没有一个变量接收函数的返回值，那么是无法调用函数的，例如如下的错误示例：

`fnPlusAB(1,2,p_Msg);`

# 三、过程（Procedure）

过程的编写格式类似于函数，但也有一些不同，示例如下：

![Oracle](/post-images/1561793006177.png)

## 过程的编写

有几个需要注意的地方：

1. 定义过程参数时，与函数类似，可以省略“in”关键字，不能省略“out”关键字。

2. 过程不能定义返回值类型，但在 begin 和 end 之间的语句可以使用 return 来跳出本过程。

## 过程的调用

由于没有返回值，可以直接传入参数调用即可：

`pcPlusAB(1,2,p_Msg);`

由于没有返回值，该过程执行完成后，只是将 p_Msg 输出参数值改为'A+B=3'。

经过以上的对比，函数和存储过程唯一的区别就是有没有返回值，因此在选择的时候可以根据实际情况，需要返回值则选择函数，不需要则选择过程。

# 四、包

在学习了函数和过程的概念之后，PL/SQL 的包（Package）就不难理解了。

简单的说，包就是函数和过程的集合。

## 包的意义

既然已经有了函数和过程来执行各种数据操作，为什么还非要用包把函数和过程“包起来”呢？

这涉及到系统的复杂度，

在一个简单的系统中，三四个乃至十几个函数或过程就可以实现业务逻辑。

这些代码可以不必打包，直接以函数或过程编译存储即可。

而在比较复杂的系统中，上百个甚至几百个存储过程也是很常见的。

在这种情况下，维护系统代码就会碰到找不到在哪里的情况，为了避免这种情况，Oracle 设计了包（Package）来帮助管理函数和存储过程。

PL/SQL 的包类似于 C#的命名空间或 JAVA 的包，可以把同一类功能或具有相似特性的函数或过程放到一起，方便查找和维护。

## 包的创建

包有两部分，包头（Package Spec）和包体（Package Body）。

包头用于声明类型、常量、变量和函数、存储过程，示例如下：

![Oracle](/post-images/1561793241703.png)

包体用于定义函数和存储过程的具体实现代码，示例如下：

![Oracle](/post-images/1561793246941.png)

需要注意的几点：

1. 在包头中定义的函数和过程声明，包体中必须有具体的实现，且参数个数、类型保持一致。

2. 包头中定义的类型、常量、变量可以在包体中直接原名调用。

3. 包体中的函数或过程可以直接以原名互相调用。

## 包的调用

包在创建完成后，就可以在其他函数或过程中调用了。调用示例如下：

![Oracle](/post-images/1561793371925.png)

这段代码涉及到包的变量调用、包的函数调用和包的存储过程调用。

运行结果为：l_C 为 3，l_msg 为'A+B=3'。

包的创建和调用到此简单介绍完成，还有一些具体细节没有展开，例如包头和包体定义变量的作用域、利用包减轻数据库负载、包的程序设计模块化等等，请自行探索。

# 五、存储过程测试方法

## 获得输入参数

PL/SQL 的存储过程，是服务于应用系统的，可能是 JAVA、C#或其他任何语言编写的。

应用系统调用存储过程，传入参数，存储过程操作数据库，向应用系统返回值，实现应用系统的某项功能。

如果是 WEB 应用，可以在前端用浏览器 F12 获取输入参数，或者在后台用 DEBUG 打印输入参数。

## 开始测试

在 PL/SQL 中找到名为 fnbtngetDAH 的存储过程，右键测试（Test）：

![Oracle](/post-images/1561794187547.png)

![Oracle](/post-images/1561794198786.png)

打开测试窗口，输入获取的参数（没有的参数留空即可）：

![Oracle](/post-images/1561794258524.png)

点击绿三角放大镜按钮

![Oracle](/post-images/1561794272567.png)

进入存储过程测试：

![Oracle](/post-images/1561794265683.png)

在图中我们可以看到一系列功能，具体功能简述如下：

正常执行存储过程，不停止：

![Oracle](/post-images/1561794332863.png)

单步进入，按存储过程顺序，单句执行，如果调用其他存储过程或循环，则进入存储过程或循环：

![Oracle](/post-images/1561794361710.png)

单步跳过，按存储过程顺序，单句执行，如果调用其他存储过程或循环，跳过不进入：

![Oracle](/post-images/1561794371476.png)

单步跳出，从当前存储过程或循环中跳出：

![Oracle](/post-images/1561794396719.png)

跳到光标位置：

![Oracle](/post-images/1561794407347.png)

跳到下一个错误或异常：

![Oracle](/post-images/1561794428443.png)

在 Variable 输入变量名，可以实时监控该变量的值（Value）：

![Oracle](/post-images/1561794436138.png)

## 存储过程常见错误

存储过程的错误，千奇百怪，例如如下语句：

![Oracle](/post-images/1561794496401.png)

将一个字符串赋值给一个整数，出现以下错误：

![Oracle](/post-images/1561794506378.png)

根据错误提示一般都可以发现问题，如果提示不明显，也大多可以通过搜索类似“ORA-06502”的错误号，在网上找到已有的解决方案。

最常见的一些错误：

1. 给变量或字段赋予不同类型的值，例如给 integer 赋 varchar2（但是可以给字符串赋整数。。。），给 date 赋 number 等等。

2. select into 变量语句，没有找到或找到多个值，这种情况下一般加 max 或 min 就可以解决。

3. 语法错误，例如句末缺失分号，FUNCTION 没有返回值，别名（ALIAS）指向混乱等等。

4. insert into 语句，没有插入不为空的字段，或者主键的值发生重复的冲突。

在一些复杂的存储过程中，可能不容易找到错误，可以用直接跳到错误语句，把语句单独摘出来，替换相关变量来执行，从而定位错误。

总之，无论是编写、阅读还是测试，都是一个抽丝剥茧的过程，只要找到头绪，剩下的就是耐心。
