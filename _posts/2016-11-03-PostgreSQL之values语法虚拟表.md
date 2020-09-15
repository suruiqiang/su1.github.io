---
layout: post
title: PostgreSQL之values语法虚拟表
categories: PostgreSQL
description: PostgreSQL之values语法虚拟表 
keywords: PostgreSQL
---
# 参考
[values-lists](https://www.postgresql.org/docs/9.5/queries-values.html)

# 实例
```sql
select * from 
 (
 values 
 ('1' ,'2' ,3 ,4 ,5 ),
 ('11','12',13,14,15)
 ) as l (a,b,c,d,e);
 
--注释 ：
--将values子句当做一个虚拟表来用时,需要为该表指定字段名,并将那些无法隐式转换的字段显示地进行类型转换
```

