---
layout: post
title: PostgreSQL之匿名块 
categories: PostgreSQL
description: PostgreSQL之匿名块 
keywords: PostgreSQL
---
# 参考
[sql-do](https://www.postgresql.org/docs/9.6/sql-do.html)

# 实例
```sql
do language plpgsql
$$
declare
  v_text text default null;
begin
  v_text := 'Chinese';
  raise notice 'hello %',v_text;
end;
$$;

```
