---
title: 'Oracle和PostgreSQL的自动备份及定期删除脚本'
date: 2019-07-02 08:27:28
categories:
- 运维
tags:
- Oracle
- PostgreSQL
- 自动备份
- 定时清理
---

数据库备份是系统安全的重要保障。

但持续的备份会使磁盘空间越来越小，若运维不及时，可能导致磁盘写满、服务器宕机的事故。

因此在备份脚本中加入了代码，删除 7 天前的老备份。

<!-- more -->

# PostgreSQL

```powershell
@ECHO  OFF
echo ================================================
echo Windows环境下PostgreSQL数据库的自动备份脚本
echo 1. 使用当前日期命名备份文件。
echo 2. 自动删除7天前的备份。
echo ================================================
::PGPATH - PostgreSQL路径
SET PGPATH=C:\"Program Files"\PostgreSQL\9.1\bin\
::SVPATH - 备份文件路径
SET SVPATH=f:\
::PRJDB - 要备份的数据库名
SET PRJDB=demo
::DBUSR - 数据库用户名
SET DBUSR=postgres
FOR /F "TOKENS=1,2,3 DELIMS=/ " %%i IN ('DATE /T') DO SET d=%%i-%%j-%%k
FOR /F "TOKENS=1,2,3 DELIMS=: " %%i IN ('TIME /T') DO SET t=%%i%%j%%k
SET DBDUMP=%PRJDB%_%d%_%t%.sql
@ECHO  OFF
%PGPATH%pg_dump -h localhost -p 5432 -U postgres %PRJDB% > %SVPATH%%DBDUMP%
echo Backup Taken Complete %SVPATH%%DBDUMP%
::删除7天前的备份。
forfiles /p "%DATADIR%" /m *.sql /d -7 /c "cmd /c del @path"
```

# Oracle

```powershell
@echo off
echo ================================================
echo Windows环境下Oracle数据库的自动备份脚本
echo 1. 使用当前日期命名备份文件。
echo 2. 自动删除7天前的备份。
echo ================================================
::以“YYYYMMDD”格式取出当前时间。
set BACKUPDATE=%date:~0,4%%date:~5,2%%date:~8,2%
::设置用户名、密码和要备份的数据库。
set USER=usr1
set PASSWORD=psw1
set DATABASE=orcl
set DATADIR=E:\app\Administrator\admin\ORCL\dpdump
::删除7天前的备份。
forfiles /p "%DATADIR%" /m *.dmp /d -7 /c "cmd /c del @path"
expdp %USER%/%PASSWORD%@%DATABASE% dumpfile=%BACKUPDATE%_%USER%.dmp logfile=%BACKUPDATE%_%USER%.log schemas=%USER%
exit
```
