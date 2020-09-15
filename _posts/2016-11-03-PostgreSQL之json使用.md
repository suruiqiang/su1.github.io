---
layout: post
title: PostgreSQL之json使用 
categories: PostgreSQL
description: PostgreSQL之json使用 
keywords: PostgreSQL
---
# 参考
[postgresqlJson](https://www.postgresql.org/docs/9.5/functions-json.html)

# 实例
## 建表以及插入测试数据
```sql
drop table justjson;
CREATE TABLE justjson(id INTEGER, doc json);---建立测试表
INSERT INTO justjson VALUES ( 1, '{
    "name":"fred",
    "address":{
        "line1":"52 The Elms",
        "line2":"Elmstreet",
        "postcode":{"pp":{"nihao":"zg"}}
        }
    }');
```
 
## ->和->>操作符
```sql
--测试查询， ->> 操作符，查内容， -> 操作符做导航
```
### 第一层显示(含含双引号,这是导航符号,不建议使用)
```sql
select id,doc->'name',doc->'address' from justjson
```

### 第一层显示
```sql
select id,doc->>'name',doc->>'address' from justjson
```

### 第二层显示
```sql
select id,doc->>'name',
	doc -> 'address'->>'line1',
	doc -> 'address'->>'line2',
	doc->'address'->>'postcode' from justjson
```
 
### 第三层显示
```sql
select id,doc->>'name',
	doc->'address'->'postcode'->>'pp'
	 from justjson
```
 
### 第四层显示
```sql
select id,doc->>'name',
	doc->'address'->'postcode'->'pp'->>'nihao'
	 from justjson     
```
## #>> 操作符
### 第一层显示
```sql
select id,doc#>>'{name}',doc#>>'{address}' from justjson 
```
 
### 第二层显示
```sql
select id,doc#>>'{name}',
	doc#>>'{address,postcode}' from justjson 
```
 
### 第三层显示
```sql
select id,doc#>>'{name}',
	doc#>>'{address,postcode,pp}' from justjson 
```
 
### 第四层显示
```sql
select id,doc#>>'{name}',
	doc#>>'{address,postcode,pp,nihao}' from justjson
```

## json的包含操作
### @>是左边内容包含右边，包含的话就是真
```sql
SELECT '"foo11"'::jsonb @> '"foo11"'::jsonb;
SELECT '[1, 2, 3]'::jsonb @> '[1, 3]'::jsonb;
SELECT '[1, 2, 3]'::jsonb @> '[3, 1]'::jsonb;
SELECT '[1, 2, 3]'::jsonb @> '[1, 2, 2]'::jsonb;
SELECT '{"product": "PostgreSQL", "version": 9.4, "jsonb":true}'::jsonb @> '{"version":9.4}';
```

### <@是左边内容包含右边，包含的话就是真
```sql
SELECT '{"jsonb":true}'  <@  '{"product": "PostgreSQL", "version": 9.4, "jsonb":true}'::jsonb
```

## 扩展
```sql
JSONB和JSON操作是一样的
```
 
