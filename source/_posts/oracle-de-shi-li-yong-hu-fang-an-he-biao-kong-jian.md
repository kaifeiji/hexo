---
title: 'Oracle的实例、用户、方案和表空间'
date: 2018-01-25 18:12:18
categories:
- 数据库
tags:
- Oracle
- 实例
- 用户
- 方案
- 表空间
---

![Oracle](/post-images/oracle-de-shi-li-yong-hu-fang-an-he-biao-kong-jian.jpg)

刚开始接触数据库时，对数据库的实例、用户、方案和表空间的概念和关系比较迷惑。

经过一段时间使用 Oracle，有了初步的认识，在这里记录一下。

<!-- more -->

# 实例（Instance）

实例（Instance），按 Oracle 的官方说法，就是内存结构和一系列进程结构。

这个概念很难以平白的语言去解释。

从不同的角度来说：

1. 从连接的角度，实例就是 IP 加斜杠后边的名字，例如 127.0.0.1/ORCL，ORCL 就是实例名。

2. 从运行的角度，实例就是 Services.msc 中，名为 OracleServicesORCL 的的服务，ORCL 就是实例名。

3. 从存储的角度，实例就是 Oracle 中 oradata 文件夹下的 ORCL 文件夹。

这三个角度都正确，但是加起来才是完整的实例。一个 ORCL 实例，实际数据存储在 oradata 下的表空间文件中，通过运行 OracleServicesORCL，为连接 127.0.0.1/ORCL 的用户提供数据服务。

# 表空间（Tablespace）

在一些业务库中，为了便于记忆和对应，常将用户和表空间创建为同名。这样做对于新手而言造成了概念理解上的混淆，分不清用户和表空间的区别。

上文所说，实例的表空间都存储在“oradata\实例名\”文件夹下（这是表空间的默认存储位置，也可以存在其他文件夹），创建表空间的语句示例：

```sql
create tablespace test_ts logging datafile 'C:\Oracle\oradata\orcl\test_ts.dbf' size 50m;
```

从上述语句可见，创建表空间是需要指定表空间的路径的，也就是说表空间实际上对应的是文件，是数据的物理存储位置。

## 用户（User）

相对应的，用户代表了数据的逻辑存储位置，接着来看用户的创建示例：

```sql
create user test_user identified by 123 default tablespace test_ts;
```

创建用户的语句，指定了默认表空间为 test_ts，把数据的逻辑存储和物理存储关联起来，也就是用户 test_user 在创建表、函数、存储过程、包等数据库对象时，会把数据存储在默认的表空间 test_ts。

所谓用户是数据的逻辑存储位置，也可以理解为，用户是这些表、函数、存储过程、包等数据库对象的所有者。

因此，当其他用户的数据库对象需要调用 test_user 的数据库对象时，需要加上“test_user.”前缀，例如：

user_A 需要调用 test_user 的表 test_table，那么 user_A 是不能直接使用表名 test_table 的，而需要使用 test_user.test_table。

# 导入导出

在系统部署和迁移场景中，使用 expdp 导出和 impdp 导入 Oracle 数据库是是一个常见的操作。

导入命令结构如下：

```bash
impdp 用户名/密码@IP/实例名 directory=DATA_DUMP_DIR dumpfile=dmp文件名 logfile=日志文件名 schemas=dmp中的用户名
```

在 cmd、bat 或 sqlplus 中执行该语句时：

1. 如果数据库中已经存在 dmp 中的用户名，那么 dmp 的数据将导入到相同用户名下的表空间，例如 dmp 中的用户是 userA，dmp 中的表空间是 tsA，数据库中已有 userA，且 userA 默认的表空间是 tsA，那么 dmp 中的数据将导入到 userA 中，数据的物理存储位置是表空间 tsA。

2. 如果数据库中不存在 dmp 中的用户名，那么在导入时，Oracle 会创建 userA，并试图将数据存储到表空间 tsA，但如果表空间 tsA 不存在，就会报错。

因此，执行该命令时，可以不必在数据库中建立和 dmp 中相同的用户名，但是必须要建立和 dmp 中相同的表空间名。

如果我想要用不同的用户名和不同的表空间怎么办？这就需要用到 impdp 命令的 remap_schema 和 remap_tablespace：

```bash
impdp system/123@127.0.0.1/orcl directory=DATA_DUMP_DIR dumpfile=xxx.dmp logfile=xxx.log schemas=userA remap_schema=userA:userB remap_tablespace=tsA:tsB
```

这样就可以把数据导入到用户 userB 和表空间 tsB 中了，当然前提是，必须要事先创建用户 userB 和表空间 tsB。

扩展知识：

在 impdp 命令中，有一个 directory=DATA_DUMP_DIR 的参数，这个参数指定的是导入 dmp 所在的文件夹。“DATA_DUMP_DIR”指向的是 Oracle 数据库中的文件夹（Directory）对象，是数据库默认自带的导入导出文件夹。

这个文件夹（Directory）对象默认的文件夹是：C:\Oracle\admin\orcl\dpdump

在使用 impdp 命令时，directory=DATA_DUMP_DIR 的参数是可以省略的，在不写该参数的情况下，系统默认到该文件夹下查找 dmp 进行导入。如果需要从其他文件夹导入 dmp，必须在 Oracle 中创建新的 Directory 对象，并在 impdp 中加入 directory=新对象名的参数。

Directory 对象存在的意义是，控制 Oracle 对文件目录的访问权限。只有定义了 Directory 对象，Oracle 才能对该目录下的文件进行操作。

# 方案（Schema）

在导入导出时，使用 expdp 和 impdp 命令，指定了一个 schema 参数，参数值等于用户名。

那为什么这个参数是 schema 不是 user 呢？

原因就是：实际导入导出的是方案，而不是用户（当然导出的方案中包含了用户及权限）。

通俗的讲，用户和方案的关系是父子关系，一个用户可以有多个方案。

创建用户的时候，系统会默认创建一个与用户同名的方案，造成很多人误认为用户（User）和方案（Schema）是一回事。

可以把方案理解成用户下的容器，用户有一个默认容器和用户同名，还可以建其他容器放其他的数据对象。

在用户量少，资源压力不大的情况，更常见的是在一台服务器建多个实例。

在资源压力较大而又存在数据对象逻辑分隔的需求，可以使用一个用户多个方案的方式来实现。

# 实例、用户、表空间、数据对象关系

如图：

![Oracle](/post-images/1561173578697.jpg)
