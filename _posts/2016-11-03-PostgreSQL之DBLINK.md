---
layout: post
title: PostgreSQL之DBLINK
categories: PostgreSQL
description: PostgreSQL之DBLINK
keywords: PostgreSQL
---
# 参考
[dblink-connect](https://www.postgresql.org/docs/10/contrib-dblink-function.html)
[dblink-disconnect](https://www.postgresql.org/docs/10/contrib-dblink-disconnect.html)

# 实例
## 1.连接其他库
```sql
select dblink_connect('myconn','hostaddr=10.0.227.73 port=5432 dbname=pointer user=pointer password=pointer');
```
 
## 2.建立视图
```sql
CREATE VIEW tt AS
SELECT *
    FROM dblink('myconn', 'select datadate from test')
    AS t1(S              bigint);
```
 
## 3.获取dblink的名称
```sql
select dblink_get_connections();

--展示如下:
DB=# select dblink_get_connections();
  dblink_get_connections
--------------------------
 {myconn}
```


## 4.断开连接
```sql
SELECT dblink_disconnect('myconn');
```
