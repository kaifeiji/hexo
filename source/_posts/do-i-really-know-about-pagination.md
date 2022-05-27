---
title: 我真的会分页吗
date: 2022-03-11 14:16:59
categories:
- 数据库
tags:
- postgreSQL
- 分页
- pagination
- LIMIT
- OFFSET
---

用户反馈，列表翻页，越往后越慢，到最后要20多秒才出来。看了看表，数量千万级，分页排序字段有索引，不应该啊。

测试、查资料，一通折腾，最终解决了问题，总结一番，形成此文。

本文探讨的仅限PostgreSQL数据库。

<!-- more -->

## 缘起

之前一直把`LIMIT OFFSET`当作分页的默认选项，当作数据库的基础设施。

客户反馈，翻页一页20多秒，我的信仰崩塌了。

谷歌、Stackworkflow、知乎一顿整。

## LIMIT OFFSET 分页原理

一个学校的毕业典礼：1000个学生，分成一批次10个，共100批次上台领毕业证。

校长说：请按学号排序，第98批次上台领奖。

``` sql
SELECT * FROM students ORDER BY id LIMIT 10 OFFSET (98 - 1) * 10;
```

1. 学生按学号乱哄哄**排序**（`order by`）
2. **第一次数人头**（`OFFSET`）：导员从第1个人，数到第970个人
3. **第二次数人头**（`LIMIT`）：第971个到第980个学生上台

抛开全表、扫描、索引、并行等专业词汇，`LIMIT OFFSET`就是这样做的分页。

从日常生活经验出发，这件事儿的瓶颈在排序和第一次数人头。

### 排序（ORDER BY）

一般分页的排序字段和正序倒序是确定的，那么排序的耗时应该也是不变的。

给排序字段加上索引，是一个非常好的实践，索引能大大减少排序耗时。

当然，不加`ORDER BY`排序，一样能分页，不过就是数据库引擎内部排序，一般是按存储顺序读取。

### 数人头（LIMIT OFFSET）

分页的目的就是不让一页有太多数据，所以第二次数人头（`LIMIT`）数量不会太大，因此也不会耗时太久。

问题在于第一次数人头（`OFFSET`），要找到指定批次的学生，是要一个一个数过去的。

随着批次越大，要数的人头也越来越多，也就越来越慢。

## 如何实现更快的分页

经过查阅资料案例，总结如下：

### 基于键（Keyset）/无限滚动（Infinite Scroll）

获取第一页：

``` sql
SELECT * FROM students ORDER BY id LIMIT 10;
```

记录第一页最后一个的id，翻页/向下滚动时，前一页最后一个的id作为参数传入第二页：

``` sql
select * from students WHERE id > ? LIMIT 10;
```

**优点**：

1、规避了OFFSET耗时的线性增长

**缺点**：

2、无法直接跳转到指定的页码

**评价**：

数据量较大时，分页的常规操作，除了id，更常见的是按时间排序。

### 基于事务ID（xmin）

`xmin`是PostgreSQL的内置字段，代表的是事务ID。一次INSERT/UPDATE即为一次事务。

获取第一页：

``` sql
SELECT xmin::text::int as xmin, * FROM students ORDER BY xmin::text::int LIMIT 10;
```

记录第一页最后一个的xmin，翻页/向下滚动时，前一页最后一个的xmin作为参数传入第二页：

``` sql
SELECT * FROM students WHERE xmin::text::int > ? LIMIT 10 offset 10;
```

**优点**：

1、规避了OFFSET耗时的线性增长

2、内置字段无需额外建立和维护索引

**缺点**：

1、无法直接跳转到指定的页码

2、可能会出现数据缺失

**评价**：

按事务ID排序分页，实际是按时间排序，因为事务ID是递增的。

如果存在一次事务INSERT/UPDATE多条数据，例如一次事务更新了20条数据，每页10条，翻到第二页的时候，后10条就被遗漏了。

所以这种排序，只适合没有批量插入更新事务的业务表。

### 基于存储位置（ctid）

`ctid`是PostgreSQL另一个内置字段，代表的是一条数据所存储的位置。一次INSERT/UPDATE即为一次事务。

获取第p页：

``` sql
SELECT * FROM users WHERE ctid = ANY (ARRAY
  (SELECT ('(' || p || ',' || s.n || ')')::tid
     FROM generate_series(0,current_setting('block_size')::int / 4) AS s(n)
  )
);
```

**优点**：

1、规避了OFFSET耗时的线性增长

2、内置字段无需额外建立和维护索引

3、能够跳转到指定的页码

4、按存储位置读取，速度比较快

**缺点**：

1、但每页的数量不确定

2、如果进行过DELETE操作，且没有VACCUM，有的页码可能没有记录

3、存储排序没有任何业务上的意义

4、不支持WHERE过滤条件

**评价**：

经常更新的表，不定时VACCUM，分页会有“空洞”。

所以适合数据量大、不常更新的表，且没有过滤需求（WHERE）。

按存储读取，相对会快一些。

### 基于表统计数据（pg_stats）

根据数据表的统计数据，可以

获取第p页：

``` sql
WITH bucket AS (
    SELECT (histogram_bounds::text::int[])[10 * p / 1000 + 1] AS lower_bound,
           (histogram_bounds::text::int[])[10 * p / 1000 + 2] AS upper_bound
    FROM pg_stats
    WHERE tablename = 'students'
    AND attname = 'id'
    LIMIT 1
  )
SELECT *
FROM users
WHERE id >= (select lower_bound from bucket)
AND id < (select upper_bound from bucket)
ORDER BY id ASC
LIMIT 1
OFFSET (10 * p % b);
```

**优点**：

1、规避了OFFSET耗时的线性增长

2、可以指定排序的字段

3、可以跳转到指定的页码

4、按统计数据读取，速度比较快

**缺点**：

1、统计数据不精确，第10页返回的结果并非第91-100条数据

2、表频繁变动时，统计数据存在滞后性

3、不支持WHERE过滤条件

**评价**：

比`ctid`更好的一点，是能指定排序字段。

但同样不支持过滤（WHERE），而且由于统计数据的特性，适合不常更新的表。

## 总结

以上几种分页的奇技淫巧，没有细讲原理，有兴趣的可以看看参考文献。

大多数读者应该只想知道这个：

|分页方法|耗时|业务排序|业务过滤|跳转指定页|是否精确|
|-|-|-|-|-|-|
|LIMIT-OFFSET|线性增加|√|√|√|√|
|Keyset|一般|√|√|×|√|
|xmin|快|×|√|√|×|
|ctid|很快|×|×|√|√|
|pg_stats|快|√|×|√|×|

没有找到完美的分页方案。

数据量小的时候，`LIMIT-OFFSET`是最佳选择。

数据量大，不需要跳转指定页，一般选择`Keyset`。

如果对于耗时有更高要求，可以根据需求选择其他非常规的分页。

## P.S

最终，发现问题的根源是索引损坏，导致分页时排序太慢。

修复索引后，耗时1秒以内——千万级的数据分页，LIMIT-OFFSET还是HOLD住的。

也就是说，我前边整的活儿算是白折腾了。

## 参考

- [1] [Fast pagination on PostgreSQL](https://chrisdone.com/posts/postgresql-pagination/)
- [2] [Pagination Done the PostgreSQL Way](https://leopard.in.ua/2014/10/11/postgresql-paginattion)
- [3] [General and Unconventional Pagination Techniques in Postgres](https://dev.to/tariqabughofa/general-and-unconventional-pagination-techniques-postgres-24fh)
- [4] [Five ways to paginate in Postgres, from the basic to the exotic](https://www.citusdata.com/blog/2016/03/30/five-ways-to-paginate/)
