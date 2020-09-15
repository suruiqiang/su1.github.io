---
layout: post
title: PostgreSQL之递归查询 
categories: PostgreSQL
description: PostgreSQL之递归查询 
keywords: PostgreSQL
---
# 参考
[postgresql-recursive-query](https://www.postgresqltutorial.com/postgresql-recursive-query/)

# 实例
## 递归查询1
```sql
with recursive tbls as (
	select parentid,
           id::text,
           name 
    from base_dd_tab t 
           where t.type='XXXX' and parentid ='-1'  /*确定父集*/
    union all
    select t.parentid,
           tbls.id::text || '->' || t.id::text,
           t.name 
    from base_dd_tab t,
         tbls 
         where t.type='XXXX'  
         and t.parentid=tbls.id                   /*tbls.id是select上一次查询的结果*/
)
select * from tbls
```

## 递归查询2
```sql
with recursive tbls as
 (select 'father' father_parentsourceid,
	     'father' father_sourceid,
	     sourceid ::text as aa,
         parentsourceid,
         sourceid,
         sourcetype,
         sourcename
    from base_source
   where parentsourceid = -1
     and sourceid = XX                                                      /*确定第一次查询的结果,既父集*/
  union all
  select tbls.parentsourceid::text father_parentsourceid,                   /*上一次查询结果集的parentsourceid*/
	     tbls.sourceid::text father_sourceid,                               /*上一次查询结果集的sourceid*/ 
	     l.parentsourceid ::text || '->' || l.sourceid ::text as aa,  
         l.parentsourceid,                                                  /*本次查询结果集的parentsourceid*/
         l.sourceid,                                                        /*本次查询结果集的sourceid*/
         l.sourcetype,
         l.sourcename
    from base_source l, tbls
   where l.parentsourceid = tbls.sourceid                                   /*递归查询*/
)
select * from tbls order by parentsourceid;
```
