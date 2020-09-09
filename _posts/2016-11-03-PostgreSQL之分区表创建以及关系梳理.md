---
layout: post
title: PostgreSQL之分区表创建以及关系梳理
categories: PostgreSQL
description: PostgreSQL之分区表创建以及关系梳理
keywords: PostgreSQL
---

# 分区的操作
## 实验条件:
```
存在表tr_g001
```

## a.创建分区
```sql
Create table tr_g001_1
  (
	Check(taskid>’1’ and taskid <’10’)
  ) inherits (tr_g001);
```

## b.创建规则
```sql
CREATE OR REPLACE RULE TR_G0001_1_rule AS
ON INSERT TO TR_G0001 WHERE (TASKID>'1' AND TASKID<'100')
DO INSTEAD
INSERT INTO TR_G0001_1 VALUES(NEW.*);
```

## c.删除规则
### 方案一：
```sql
DROP RULE TR_G0001_1_rule ON TR_G0001
```

### 方案二：
```sql
DROP RULE IF EXISTS TR_G0001_1_rule ON TR_G0001
```
## d.删除子表
```sql
DROP TABLE IF EXISTS TR_G0001_1  
```

# 查父表子表的继承各种关系
## 查父表
```sql
select distinct pi.inhparent,
                pc.relname parent_name 
from  pg_inherits pi 
left join pg_class pc 
    on pi.inhparent=pc.oid
```
 
## 查子表
```sql
select distinct pi.inhrelid,
                pc.relname child_name 
from  pg_inherits pi 
left join pg_class pc 
    on pi.inhrelid=pc.oid 
```

## 查父表子表的继承关系
```sql
select  pi.inhrelid child_oid,
        pc.relname child_name,
        pi.inhparent parent_oid,
        pc2.relname parent_name 
from  pg_inherits pi 
left join pg_class pc 
    on pi.inhrelid=pc.oid
left join pg_class pc2
    on  pi.inhparent=pc2.oid
```

## 根据父表找出子表的名称
```sql
select child_name from 
(
select  pi.inhrelid child_oid,
        pc.relname child_name,
        pi.inhparent parent_oid,
        pc2.relname parent_name 
from  pg_inherits pi 
left join pg_class pc 
    on pi.inhrelid=pc.oid
left join pg_class pc2
    on pi.inhparent=pc2.oid
) as t where parent_name ='父表名'
```
 

