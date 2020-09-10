---
layout: post
title: PostgreSQL之模拟dual虚拟表 
categories: PostgreSQL
description: PostgreSQL之模拟dual虚拟表
keywords: PostgreSQL
---

# dual虚拟表创建方法
```
drop table if exists dual cascade;
create table dual
(
dummy varchar(1)
);

insert into dual values('X');
```
