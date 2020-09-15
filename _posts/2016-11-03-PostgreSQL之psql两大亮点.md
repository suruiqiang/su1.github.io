---
layout: post
title: PostgreSQL之psql两大亮点 
categories: PostgreSQL
description: PostgreSQL之psql两大亮点 
keywords: PostgreSQL
---
# 参考
[psql-tools](https://www.postgresql.org/docs/12/app-psql.html)

# 实例
## 1.SQL在线帮助
```sql
postgres=#\? 

postgres=#\h select
```

## 2.tab补齐
```sql
postgres=# select * from base_   (此时按下tab键会自动帮你补齐 )
```
