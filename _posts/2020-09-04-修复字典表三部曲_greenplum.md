---
layout: post
title: 修复字典表三部曲\_greenplum
categories: GPDB
description:  修复字典表三部曲\_greenplum
keywords: GPDB,GreenPlum
---

# 说明
```
该三种方案只是在测试环境操作过，如需生产操作，请自行评估风险
```

# 方案一.
## utility模式下删除所有的状态为u的segment和正在使用的master上的对应的表
### 1.拼出语句
```sql
select E'PGOPTIONS\=\'-c gp_session_role\=utility\' psql -h '||hostname||' -p '||port||' -Atc "drop table  SCHEMA.TABLENAME;" -d databse' from gp_segment_configuration where  role='p';
```
### 2.执行删除语句
```sql
 PGOPTIONS='-c gp_session_role=utility' psql -h mdw -p 5432   DATABASE    -c "drop table SCHEMA.TABLENAME;"
 PGOPTIONS='-c gp_session_role=utility' psql -h sdw1 -p 40000 DATABASE   -c "drop table SCHEMA.TABLENAME;"
 PGOPTIONS='-c gp_session_role=utility' psql -h sdw1 -p 40001 DATABASE   -c "drop table SCHEMA.TABLENAME;"
 PGOPTIONS='-c gp_session_role=utility' psql -h sdw2 -p 40000 DATABASE   -c "drop table SCHEMA.TABLENAME;"
 PGOPTIONS='-c gp_session_role=utility' psql -h sdw2 -p 40001 DATABASE   -c "drop table SCHEMA.TABLENAME;"
 PGOPTIONS='-c gp_session_role=utility' psql -h sdw3 -p 40000 DATABASE   -c "drop table SCHEMA.TABLENAME;"
 PGOPTIONS='-c gp_session_role=utility' psql -h sdw3 -p 40001 DATABASE   -c "drop table SCHEMA.TABLENAME;"
```
### 3.检查(需要在数据库空闲状态下执行) 
```sql
gpcheckcat -g /home/gpadmin/20161012/ -p 5432 test
```
# 方案二.
## 1.用utility模式从一个正常的segment将对应的表导出,并插入异常的segment中
```sql
\copy (select oid,* from pg_type where oid=17727) to '/home/gpadmin/20161012/pg_type.out';
\copy pg_type from '/home/gpadmin/20161012/pg_type.out' with oids;

\copy (select oid,* from pg_class where oid=20751) to '/home/gpadmin/20161012/pg_class.out';
\copy pg_class from '/home/gpadmin/20161012/pg_class.out' with oids;

\copy (select * from pg_attribute where attrelid=17726) to '/home/gpadmin/20161012/pg_attribute.out';
\copy pg_attribute from '/home/gpadmin/20161012/pg_attribute.out';
```
## 2.检查(需要在数据库空闲状态下执行) 
```sql
gpcheckcat -g /home/gpadmin/20161012/ -p 5432 test
```

# 方案三.
## 1.出现persistent问题,可用下面工具修复[pivotal内部工具,建议不得到原厂的支持不要修复]
```sql
/usr/local/greenplum-db/sbin/gppersistentrebuild  -c 5  -D /home/gpadmin/20161012 
```

## 2.检查(需要在数据库空闲状态下执行) 
```sql
gpcheckcat -g /home/gpadmin/20161012/ -p 5432 test
```

**注意：修改字典表风险很大，操作需要谨慎再谨慎**
