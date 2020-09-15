---
layout: post
title: PostgreSQL之自定义聚合函数 
categories: PostgreSQL
description: PostgreSQL之自定义聚合函数
keywords: PostgreSQL
---
# 参考
[CREATEAGGREGATE](https://gpdb.docs.pivotal.io/43180/ref_guide/sql_commands/CREATE_AGGREGATE.html)

# 实例
## 测试表
```sql
drop table if exists test cascade;
create table test(
id bigint, 
name text
);
```

## 测试数据
```sql
insert into test
values(1,'my'),
(1,'name'),
(1,'is'),
(1,'pt');
```
 
 
## 状态转换函数 
```sql
/*
--参数说明:
--pre      上一次被调用后生成的计算结果
--curr_str 本次待处理的记录
*/
create or replace function my_str_conn2(pre text,curr_str text)
returns text as
$$
	select 
	case 
		when coalesce($2,'0') = '0' then $1 
		else $1 || ' ' || $2
	end;
$$
language sql immutable;
```
 
## 最终处理函数[可选]    对sfunc函数的输出结果做最后的加工
```sql
create or replace function my_str_conn2_final(text)
returns text as
$$
	select trim(replace($1,'init,',''));
$$
language sql;
```
 
## 聚合函数
```sql
drop aggregate my_str_conn_cnt2(text);
create aggregate my_str_conn_cnt2(text)(
sfunc=my_str_conn2,            --聚合运算的逻辑主体
stype=text,                    --聚集的状态值的数据类型
finalfunc=my_str_conn2_final,  --负责最终加工步骤的函数      
initcond='init'                --状态值的初始设置
)
```
 
## 验证
```sql
select id ,my_str_conn_cnt2(name) from test group by id;
```
 
# Example
## 状态转换函数    
```sql
--prev记录上一次 计算结果的作为本次输入
--curr_need是本次待处理记录
create or replace function my_sum(prev bigint[2],curr_need bigint)
returns bigint[] as
$$
	select 
		case when $2 is null or $2=0 then $1
		else array[coalesce($1[1],0),$1[2]+$2]
		end;        
$$
language sql immutable;
```
 
## 最终处理函数[可选]    对sfunc函数的输出结果做最后的加工
```sql
create or replace function my_sum_final(prev bigint[])
returns bigint as
$$
select  ($1[1]+$1[2]);
$$
language sql immutable;
```
 
## 聚合函数
```sql
create aggregate my_sum_cnt(bigint)(
sfunc    =my_sum,             --
stype    =bigint[],           -- The data type for the aggregate state value.
finalfunc=my_sum_final,       -- 
initcond ='{0,0}'             -- The initial setting for the state value
);
```
 
## 验证
```sql
create table a( id int);
insert into a select generate_series(1,100);

select  my_sum_cnt(id::bigint),sum(id) from a
```
