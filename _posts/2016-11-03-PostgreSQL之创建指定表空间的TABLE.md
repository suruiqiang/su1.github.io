---
layout: post
title: PostgreSQL之创建指定表空间的TABLE
categories: PostgreSQL
description: PostgreSQL之创建指定表空间的TABLE
keywords: PostgreSQL
---

# a.建指定用户的表空间
```sql
create tablespace test_tbs owner "test_usr" location '/data/pgsqldata/test_tbs';
```

# b.建指定表空间的表
```sql
create table tt( in   bigint        not null,
	             name varchar(256)
               ) 
               tablespace test_tbs;
```
 
