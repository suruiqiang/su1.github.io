---
layout: post
title: PostgreSQL之自定义类型 
categories: PostgreSQL
description: PostgreSQL之自定义类型 
keywords: PostgreSQL
---
# 参考
[createType](https://www.postgresql.org/docs/9.5/sql-createtype.html)

# 实例
## 方法一
```sql
create table mytype (i bigint,j bigint);
--注意:建表的同时系统会默认建一个同名的自定义类型
```
## 方法二
```sql
create type mytype as (i bigint,j bigint);
```

## 建表
```sql
create table mytest (id bigint,val mytype);
```
## 插入数据
```sql
insert into mytest(id,val) values(1,(2,3)::mytype)
```
## 查询
```sql
select * from mytest
 
select id,(val).* from mytest
 
select id,(val).i,(val).j from mytest
```
