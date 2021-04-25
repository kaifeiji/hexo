---
title: 'PostgreSQL的行转列和列转行'
date: 2019-07-03 11:41:41
categories:
- 数据库
tags:
- PostgreSQL
- 行转列
- 列转行
---

![PostgreSQL](/post-images/postgresql-de-xing-zhuan-lie-he-lie-zhuan-xing.png)

Oracle 中的行转列和列转行分别有 pivot 和 unpivot 方法。

在 PostgreSQL 中，行列互转的方法也有很多，在这里介绍常用的两种。

<!-- more -->

# crosstab 行转列

有某平均温度数据（data_avg_temp）：

| name | month | avg_temp |
| :--: | :---: | :------: |
| 康山 |  jan  |    5     |
| 康山 |  apr  |   16.3   |
| 康山 | july  |   28.8   |
| 康山 |  oct  |   19.2   |
| 棠荫 |  jan  |    6     |
| 棠荫 |  apr  |   17.6   |
| 棠荫 | july  |   29.7   |
| 棠荫 |  oct  |   20.3   |

要转换成如下格式：

| name | jan | apr  | july | oct  |
| :--: | :-: | :--: | :--: | :--: |
| 康山 |  5  | 16.3 | 28.8 | 19.2 |
| 棠荫 |  6  | 17.6 | 29.7 | 20.3 |

使用 crosstab 方法实现行转列：

1. 使用 crosstab 方法，需要安装扩展模块 tablefunc：

```sql
CREATE EXTENSION tablefunc;--第一次使用crosstab前执行，后续无需再执行
```

2. 实现代码：

```sql
SELECT * FROM crosstab
(
	'SELECT name, month, avg_temp FROM data_avg_temp ORDER  BY 1,2',
	$$values('jan'::text),('apr'::text),('july'::text),('oct'::text)$$
)
AS data_avg_temp_cross
(name text, jan numeric, apr numeric, july numeric, oct numeric);
```

或：

```sql
SELECT * FROM crosstab
(
	'SELECT name, month, avg_temp FROM data_avg_temp ORDER  BY 1,2',
	'SELECT DISTINCT month FROM data_avg_temp ORDER BY 1'
)
AS data_avg_temp_cross
(name text, jan numeric, apr numeric, july numeric, oct numeric);
```

3. 转换原理

crosstab 内的第一个参数，是行转列的源表数据：

```sql
'SELECT name, month, avg_temp FROM data_avg_temp ORDER BY 1,2',
```

crosstab 内的第二个参数，是行转列的那一列数据，有两种形式：

-   values 形式：

```sql
$$values('jan'::text),('apr'::text),('july'::text),('oct'::text)$$
```

-   DISTINCT 形式：

```sql
'SELECT DISTINCT month FROM data_avg_temp ORDER BY 1'
```

最后要定义转换后的表结构：

```sql
AS data_avg_temp_cross
(name text, jan numeric, apr numeric, july numeric, oct numeric)
```

# jsonb 列转行

有某平均温度数据（data_avg_temp2）：

| name | jan | apr  | july | oct  |
| :--: | :-: | :--: | :--: | :--: |
| 康山 |  5  | 16.3 | 28.8 | 19.2 |
| 棠荫 |  6  | 17.6 | 29.7 | 20.3 |

要转换成如下格式：

| name | month | avg_temp |
| :--: | :---: | :------: |
| 康山 |  jan  |    5     |
| 康山 |  apr  |   16.3   |
| 康山 | july  |   28.8   |
| 康山 |  oct  |   19.2   |
| 棠荫 |  jan  |    6     |
| 棠荫 |  apr  |   17.6   |
| 棠荫 | july  |   29.7   |
| 棠荫 |  oct  |   20.3   |

使用 jsonb 方法实现列转行：

1. 实现代码：

```sql
SELECT name, (b.rec).key as month, (b.rec).value FROM
(
	SELECT name, jsonb_each(row_to_json(a)::jsonb-'name'::varchar) as rec FROM
	(SELECT name, jan, apr, july, oct FROM data_avg_temp2) a
) b
```

2. 转换原理

关键点在于：

```sql
jsonb_each(row_to_json(a)::jsonb-'name'::varchar)
```

row_to_json(a)::jsonb 把行数据转为 JSON，例如：

| name | jan | apr  | july | oct  |
| :--: | :-: | :--: | :--: | :--: |
| 康山 |  5  | 16.3 | 28.8 | 19.2 |

转为：

`{"name":"康山","jan":"5","apr":"16.3","july":"28.8","oct":"19.2"}`

-'name'::varchar 把 JSON 中的 name 键值去掉：

`{"jan":"5","apr":"16.3","july":"28.8","oct":"19.2"}`

jsonb_each 再转换为 JSON 对象 rec，最后在外层 SELECT 中用(b.rec).key 和(b.rec).value 分别取出月份和平均温度。
