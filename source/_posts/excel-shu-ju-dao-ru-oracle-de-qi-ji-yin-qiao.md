---
title: 'Excel数据导入Oracle的奇技淫巧'
date: 2018-01-29 14:43:36
categories:
- 数据库
tags:
- Oracle
- Excel
- 导入
- 小窍门
---

![Excel导入Oracle](/post-images/excel-shu-ju-dao-ru-oracle-de-qi-ji-yin-qiao.jpg)

<!-- more -->

在工作中常遇到 EXCEL 数据导入 Oracle 的场景，例如：

![1](/post-images/1561791004337.png)

在只有几条记录的情况下，完全可以手动录入：

![2](/post-images/1561791019609.png)

但如果记录有几百条、几千条，显然手动录入就太慢了，可以用复制粘贴的方法：

1. 按照 EXCEL 的格式建立相应的数据表：

![3](/post-images/1561791042826.png)

2. 用 for update 或 rowid 开启编辑数据模式：

![4](/post-images/1561791048689.png)

3. 点击红色小锁解锁：

![5](/post-images/1561791056446.png)

4. 在 EXCEL 左侧插入一个空列：

![6](/post-images/1561791069111.png)

5. 选中包括空列在内的数据（不包含表头），复制（CTRL+C）：

![7](/post-images/1561791078908.png)

6. 用 for update 或 rowid 打开表编辑模式并选中第一行：

![8](/post-images/1561791087031.png)

7. 粘贴（CTRL+V）：

![9](/post-images/1561791095048.png)

8. 提交数据，可以看到，复制的数据完好无损的粘贴到 Oracle 的表中：

![10](/post-images/1561791108628.png)

需要注意的是第 2 步，要在 EXCEL 左侧插入一个空列，这个空列对应的是 SELECT 语句查询结果中的 rownum，也就是行号（1、2、3……）：

![11](/post-images/1561791115538.png)

最后，这种方法适用于数据量不是特别大的情况，如果几十万条甚至几百万条，电脑性能可能跟不上，PL/SQL DEVELOPER 就会卡死，导致导入失败。

当然，电脑性能超级强的可以试一试。
