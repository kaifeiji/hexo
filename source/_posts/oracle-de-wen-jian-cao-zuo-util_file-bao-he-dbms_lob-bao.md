---
title: 'Oracle的文件操作——UTIL_FILE包和DBMS_LOB包'
date: 2018-03-22 21:02:22
categories:
- 数据库
tags:
- Oracle
- 文件操作
---

![Oracle](/post-images/oracle-de-wen-jian-cao-zuo-util_file-bao-he-dbms_lob-bao.jpg)

最近客户要求从老数据库迁移数据，其中涉及到把 Blob 格式的图片转为文件格式，通过研究编写了一个简单的存储过程，输入 Blob 可以向指定文件夹输出文件，供参考。

<!-- more -->

# 源代码

```sql
create or replace procedure fnBlobtoFile(inBlob blob, outDIR  varchar2,	outFile varchar2) is
  --参数inBlob是输入的Blob，outDIR是服务器的绝对路径，outFILE是文件名（含扩展名）
  l_sql varchar2(200);
  l_len integer;
  l_dstfile utl_file.file_type;
  l_buffer raw(32767);
  l_amount binary_integer := 32767;
  l_pos integer := 1;
begin
  --创建directory对象，指向输出路径
  l_sql:='create or replace directory OUT_DIR as '''||outDIR||'''';
  execute immediate l_sql;
	--用dbms_lob包的getlength方法获取inBlob的长度
  l_len:=dbms_lob.getlength(inBlob);
	--用utl_file的fopen方法在OUT_DIR文件夹下创建名为outFile的文件
  l_dstfile:=utl_file.fopen('OUT_DIR',outFile,'wb',32767);
	--以l_amount为基数，分段循环将inBlob的内容写入l_dstfile文件
  while l_pos<l_len loop
    begin
    dbms_lob.read (inBlob, l_amount, l_pos, l_buffer);
    utl_file.put_raw(l_dstfile, l_buffer, TRUE);
    l_pos := l_pos + l_amount;
    exception when others then
      exit;
    end;
  end loop;
	--关闭l_dstfile文件
  utl_file.fclose(l_dstfile);
end;
```

# UTIL_FILE 包是否支持远程文件操作？

答案是肯定的，但需要解决权限问题。

在 Windows 环境下，Oracle 的服务是通过本地系统登录的。

而本地系统账户是没有远程文件或映射磁盘的访问权限。

这里我们需要使用管理员账户登录 Oracle 服务：

1. 关闭 OracleServiceORCL 和 OracleOraDb11g_home1TNSListener 服务；

2. 设置两个服务的登录账户，以管理员身份登录；

![Oracle](/post-images/1561901241937.jpg)

3. 重启两个服务，可以看到服务右侧显示为 Administrator 账户；

4. 使用 Administrator 账户登录需要操作的远程文件夹：

`\\192.168.0.102\d$\files`

5. 输入远程主机的用户名、密码，并保存凭证；

6. 修改输出文件夹 directory 为完整的 UNC 地址：

```sql
l_sql:='create or replace directory out_DIR as '''\\192.168.0.102\d$\files';''';
```

这样，就可以通过 UTIL_FILE 进行远程文件操作了！
