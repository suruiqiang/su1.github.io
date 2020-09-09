---
layout: post
title: PostgreSQL之分页查询 
categories: PostgreSQL
description: PostgreSQL之分页查询
keywords: PostgreSQL
---
# 参考
[queries-limit](https://www.postgresql.org/docs/8.1/queries-limit.html)

# 语法
```sql
select * from 表order by 字段 limit 每页条数 offset 开始的位置;
```

# 例子
```sql
select * from ARGOS_DICT_CONFIG_MANAGER order by id limit 10 offset 10;
```

