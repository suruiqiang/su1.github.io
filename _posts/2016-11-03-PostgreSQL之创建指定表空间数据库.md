---
layout: post
title: PostgreSQL之创建指定表空间数据库
categories: PostgreSQL
description: PostgreSQL之创建指定表空间数据库
keywords: PostgreSQL
---

# 方案一:
## a. 创建表空间
```
># mkdir -p /data/pgsqldata/test_tbsp
># chown -R postgres:postgres  /data/pgsqldata/test_tbsp
psql=#create tablespace test_tbsp location '/data/pgsqldata/test_tbsp';
```
## b. 创建用户
```
create user test with password 'test';
```
## c.创建指定表空间，指定用户的数据库
```	
create database test owner=test tablespace=test_tbsp;
```
## d.写函数需要的语言
```
su - postgres -c"createlang plpgsql saservice"
```
 
# 方案二:
## a.建用户(linux环境)
```
># createuser test –s
```
## b.建表空间实际存在的文件夹,并给权限(linux环境)
```
># mkdir /data/pgsqldata/test_tbs
># chown postgres:postgres /data/pgsqldata/test_tbs
```
## c.创建表空间(psql环境)
```
DB=# create tablespace test_tbs location '/data/pgsqldata/test_tbs';
```
## d.创建数据库(linux环境)
```
># createdb test -D test_tbs -O test
```
 
