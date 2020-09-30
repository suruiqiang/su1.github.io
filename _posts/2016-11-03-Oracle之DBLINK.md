---
layout: post
title: Oracle之DBLINK 
categories: Oracle
description: Oracle之DBLINK
keywords: Oracle
---

# 1.赋予创建dblink的权限
```sql
grant create database link,drop public database link to ayee;
```

## 权限列表:
```sql
CREATE DATABASE LINK (只能创建者使用)
DROP  DATABASE LINK
CREATE PUBLIC DATABASE LINK
DROP  PUBLIC DATABASE LINK
```
 
# 2.建立dblink
## 方法一：(经过试验可以从ora11g连接到ora10g)
```sql
create database link test_dblink  
	connect to 用户名 identified by 密码 
	using 'IP地址:端口/实例名';
```
## 方法二：(靠谱点)
```sql
create public database link dblink名称 
	connect to 用户 identified by 密码 
	using
	'(DESCRIPTION=
	     (ADDRESS_LIST=
	         (ADDRESS=(PROTOCOL=TCP)
	                  (HOST=192.168.x.x)
                      (PORT=1521)
             )
	     )
	     (CONNECT_DATA=
	         (SERVER=DEDICATED)
	         (SERVICE_NAME=实例名)
	     )
	)'; 
	 
	或者
	create database link test_bdlink
	  connect to TRAPPERSDBN identified by TRAPPERSDBN
	  using 
	  '(description=(address=(protocol=TCP) (host=10.0.xxx.xxx)(port=1521))(connect_data=(sid=ora11g)))';
```

# 3.查询方法
```sql
select * from 用户.表名@dblink名称 ;
```
 
# 4.删除dblink
```sql
drop database link dblink名称;
```
 
