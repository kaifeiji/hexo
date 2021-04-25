---
title: 'SELECT详解——过滤条件的运算符'
date: 2018-01-28 13:10:17
categories:
- 数据库
tags:
- Select
- 过滤条件
- 运算符
---

![SELECT语句详解](/post-images/select-xiang-jie-guo-lu-tiao-jian-de-yun-suan-fu.jpg)

SELECT 中的过滤条件的运算符包括关系运算符、逻辑运算符、范围运算符。

关系运算符：=（等于）、>（大于）、>=（大于等于）、<（小于）、<=（小于等于）、<>（不等于）

逻辑运算符：AND（与）、OR（或）、NOT（非）

范围运算符：IS（是）、BETWEEN（介于）、IN（在...中）、EXISTS（存在）、LIKE（通配）

<!-- more -->

# 一、关系运算符

关系运算符很容易理解：判断关系运算符两侧的值是否满足条件。

```sql
SELECT * FROM student WHERE name='张三';
--过滤出姓名为张三的学生
```

```sql
SELECT * FROM student WHERE age>18;
--过滤出年龄大于18的学生
```

# 二、逻辑运算符

1. AND（与）

```sql
SELECT * FROM student WHERE sex='Male' AND age>18;
--过滤出年龄大于18的男学生
```

2. OR（或）

```sql
SELECT * FROM student WHERE age>18 OR sex='Female';
--过滤出年龄大于18或性别为女的学生
```

3. NOT（非）

```sql
SELECT * FROM student WHERE NOT age>18';
--过滤出年龄不大于18的学生
```

4. 逻辑运算符优先级

NOT>OR>AND，但为避免混乱，一般用()来明确优先级：

```sql
SELECT * FROM student WHERE sex='Female' AND (age>18 OR age<14);
--过滤出年龄大于18或小于14的女学生
```

# 三、范围运算符

1. IS（是）

```sql
SELECT * FROM student WHERE sex IS NULL';
--过滤出性别为空的学生
```

可以使用 NOT 关键字取 IS（是）的补集 IS NOT（不是），同理以下的范围运算符也可以使用：NOT BETWEEN、NOT IN、NOT EXISTS、NOT LIKE。

```sql
SELECT * FROM student WHERE sex IS NOT NULL';
--过滤出性别不为空的学生
```

3. BETWEEN（介于）

需要注意的是，BETWEEN 的范围是闭合全集，即包括 14 岁和 18 岁。

```sql
SELECT * FROM student WHERE age BETWEEN 14 and 18';
--过滤出年龄介于14和18的学生
```

5. IN（在...中）

```sql
SELECT * FROM student WHERE name in ('张三','李四')';
--过滤出姓名在括号中的学生
```

7. EXISTS（存在）

```sql
SELECT * FROM student WHERE EXISTS (SELECT 1 FROM score WHERE name=student.name)';
--过滤出在成绩表中有记录的学生
```

9. LIKE（通配）

```sql
SELECT * FROM student WHERE name like '张%';
--过滤出姓张的学生
```
