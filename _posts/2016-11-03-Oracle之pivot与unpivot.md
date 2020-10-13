---
layout: post
title: Oracle之pivot与unpivot 
categories: Oracle
description: Oracle之pivot与unpivot
keywords: Oracle
---

# 描述
```
pivot就是行转换,unpivot就是列转换
```

# 测试环境准备
```sql
create table att(
owner varchar2(256),
type  varchar2(256),
value varchar2(256)
);

insert into att (OWNER, TYPE, VALUE) values ('sys', 'table', '20');
insert into att (OWNER, TYPE, VALUE) values ('sys', 'index', '46');
insert into att (OWNER, TYPE, VALUE) values ('sys', 'table', '28');
insert into att (OWNER, TYPE, VALUE) values ('sys', 'table', '15');

select * from att  pivot(max(value) for type in('table','index'))
```

# 语法
```
select * from att  
pivot(
	max(value)          -- 聚合函数
	for type            -- 行转列标准
	in('table','index') -- 行转列列取值和顺序
)
```
 

