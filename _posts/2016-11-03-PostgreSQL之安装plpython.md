---
layout: post
title: PostgreSQL之安装plpython 
categories: PostgreSQL
description: PostgreSQL之安装plpython 
keywords: PostgreSQL
---
# 参考
[nstalling-plpython](https://stackoverflow.com/questions/12010344/postgres-database-crash-when-installing-plpython)

# 实例
## 方法一
### 1.在安装的时候,在步骤
```shell
./configure 改为      ./configure --with-python
## 然后正常安装即可
```

### 2.登陆用户
```shell
psql -U postgres -d postgres
```
 
### 3.创建语言
```sql
postgres=# create extension plpython2u;
postgres=# create extension plpythonu;
```
 
### 4.确认plpython是否添加成功
```sql
postgres=# select * from pg_language
```

## 方法二
在安装的时候没有加参数--with-python的安装方法
### 1.将源码包放入你的工作目录/work
### 2.解压源码包
### 3.进入源码包中
```shell
cd /work/postgresql-x.x.x
```
### 4.编译源码包
```shell
./configure --with-python --prefix=${source_dir}
make
su
make install
#其中目录source_dir有自己指定
```
 
### 5.将编译后获得的extension/plpython放入$PGHOME/share/extension/
```shell
cp ${source_dir}/share/postgresql/extension/plpython* $PGHOME/share/extension/
```
### 6.将编译后获得的plpython2.so放入$PGHOME/share/extension/
```shell
cp ${source_dir}/lib/postgresql/plpython2.so ${PGHOME}/lib/
```
 
### 7.登陆用户
```sql
postgres=# psql -U postgres -d postgres
```

### 8.创建语言
```sql
postgres=# create extension plpython2u;
postgres=# create extension plpythonu;
```

### 9.确认plpython是否添加成功
```sql
select * from pg_language;
```
