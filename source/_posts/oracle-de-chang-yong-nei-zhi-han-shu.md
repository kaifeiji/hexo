---
title: 'Oracle的常用内置函数'
date: 2018-01-31 16:10:12
categories:
- 数据库
tags:
- Oracle
- 内置函数
---

Oracle常用内置函数总结

<!-- more -->

# 转换函数

to_char(d|n,fmt):把日期和数字转换为指定格式的字符串;

to_number(x,fmt):把一个字符串转换为一个指定格式的数字;

to_date():将一个代表时间的字符串按指定格式转换为 date 类型;

# 条件取值函数

nvl(x,value):如果 x 为 null，返回 value，否则返回 x;

nvl2(x,value1,value2):若果 x 非空返回 value1，否则返回 value2;

decode(x,m,1,n,2,3):如果 x 为 m 返回 1，x 为 n 返回 2，其他返回 3;

# 聚合函数

avg():求平均值;

sum():求和;

min():求最小值;

max():求最大值;

count():统计数量;

# 数值函数

abs(n):返回数字 n 的绝对值;

mod(n,m):返回数字 n/m 的余数，如果 m=0 则返回 n;

ceil(n):向上取整;

floor(n):向下取整;

round(n,[m]):四舍五入，m 表示保留小数点后的多少位，可以用负数表示小数点前的位数;缺省则到整数位;

trunc(n,[m]):截取数字，m 表示小数点后的多少位，可用负数截取小数点前的位数;缺省则到整数位;

# 幂指函数

sqrt(n):返回数字 n 的平方根，n 必须大于 0;

power(n,m):返回数字 n 的 m 次方;

exp(n):返回 e 的 n 次幂

ln(n):返回数字 n(n 必须大于 0)的自然对数;

log(n,m):返回底数为 n(除 0 及 1 的正整数)的 m 对数;

# 三角函数

cos(n):返回数字 n(弧度)的余弦值;

cosh(n):返回数字 n 的双曲余弦值;

acos(n):返回数字 n 的反余弦值，结果的单位为弧度，n 的取值范围为(-1,1);

sin(n):返回数字 n 的正弦值;

tan(n):返回数字 n 的正切值;

tanh(n):返回数字 n 的双曲正切值;

atan(n):返回数字 n 的反正切值;

atan(n,m):返回数字 n/m 的反正切值;

# 字符函数

ascii(c):返回字符 c 的 ascii 值;

chr(n):将 ascii 值转换为对应的字符;

initcap(s):将字符串 s 的所有单词的首字母大写，其余小写;

lower(s):将字符串 s 中所有的字符小写;

upper(s):将字符串 s 中所有的字符大写;

concat(s1,s2):将字符串 s2 连在 s1 后面，等同于操作符||;

length(s):返回字符串 s 的长度;

lpad(s1,n,s2):在字符串 s1 的左端填充字符串 s2，直至填充后的 s1 的总长度为 n，不指定 s2 则默认为空格，如果 s1 的长度大于 n，则直接返回 s1 左端的 n 个字符;

rpad(s1,n,s2):在字符串 s1 的右端填充字符串 s2，用法同上;

instr(s1,s2,n,m):取得 s2 在 s1 中的位置，n 表示查找的起始位置，m 表示匹配到的第几个;

substr(s1,m,n):取得字符串 s1 中从 m 开始，长度为 n 的子字符串，m 可为负数;

ltrim(s1,set):从 s1 的左端开始逐一截掉字符，截掉的字符为 set 包含的字符，若在执行过程中遇到 set 中不存在的字符则函数停止，并返回被截后的 s1;

rtrim(s2,set):原理和 ltrim 相同，从 s2 的右边开始;

trim(c FROM s1):从 s1 的两端截去字符 c(只能是一个字符)，原理同上;

replace(s1,s2,s3):将字符串 s1 中的子串 s2 用 s3 替代;

translate(s1,forms,tos):将字符串 s1 按照 froms 和 tos 的对应关系进行转换;froms 表示原字符的集合，tos 表示将要转换成的字符的集合，froms 和 tos 按顺序进行对应，例：

```sql
translate('about','abcd','1234') ;--12out
```

# 正则函数

regexp_substr(s1,pattern,position,occurrence,match_parameter):按照正则表达式 pattern 从 s1 字符串中的 position 位置开始，截取第 occurrence 次出现的匹配 pattern 的字符串，matche_parameter 为匹配参数;

regexp_replace(s1,pattern,str,position,occurrence,match_parameter):按照正则表达式 pattern 从 s1 字符串中的 position 位置开始，替换掉第 occurrence(可以为 0，表示全文匹配)次出现的满足正则的字符串为 str，matche_parameter 为匹配参数;

regexp_instr(s1,pattern,position,ocurrence,return_option,match_parameter):和 instr 类似，用于返回匹配正则表达式的字符串的开始位置，其中的参数 return_option 指定返回的位置，0 表示返回出现的第一个字符的位置，1 表示返回字符之后发生的位置;

regexp_like(s1,pattern):和 like 类似，只用于条件表达式;

# 日期函数

sysdate:返回系统当前时间;

systimestamp:返回系统当前日期时间和时区;

current_date:返回当前时区所对应的日期和时间;

current_timestamp:返回当前时区所对应的日期时间;

localtimestamp:返回当前时区所对应的日期时间;

systimestamp:返回系统当前日期时间和时区;

sessiontimezone:返回当前所在的时区;

dbtimezone:返回资料库所在的时区;

add_months(d,n):返回指定日期 d 之后(或之前)的 n 个月所对应的日期时间;n 大于 0 表示之后;

extract():从指定的日期里取出所需要的特定数据，如日期、月、时间等，例：

```sql
extract(month from sysdate)
```

last_day(d):返回日期 d 所在月份的最后一天;

next_day(d,day):返回日期 d 之后的下一个 day 例：

```sql
next_day(sysdate,'星期五');--表示今天之后的下一个星期五
```

round(d,fmt):返回日期时间的四舍五入结果，fmt 指定四舍五入的维度，可为 year，month，day，例：

```sql
round(to_date('2018-05-17','yyyy-mm-dd'),'month');--2018/6/1
```

trunc(d,fmt):用于截断日期数据，fmt 指定截断的维度;

to_timestamp(chr,nls_param):将指定格式的日期时间字符串转为 timestamp 类型;例：

```sql
to_timestamp('2018-05-17 15:18:00','yyyy-mm-dd hh24:mi:ss')
```

month_between(d1,d2):返回两个日期间相差的月份数;

# 数据库系统相关函数

sys_context(context,attribyte):返回应用上下文特定属性;

uid:当前用户的 id 号;

user:返回当前会话对应的数据库名;

userenv(parameter):返回当前上下文指定的属性信息;

sys_guid():生成类型为 raw 的 16 字节的唯一标识符;每次调用会有不同的值;
