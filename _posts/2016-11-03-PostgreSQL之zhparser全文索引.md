---
layout: post
title: PostgreSQL之zhparser全文索引 
categories: PostgreSQL
description: PostgreSQL之zhparser全文索引
keywords: PostgreSQL
---
# 参考
[ PostgreSQL10实时全文检索和分词,相似搜索,模糊匹配实现类似Google搜索自动提示](https://www.debugger.wiki/article/html/1562855079393888)
<br />这个帖子的方法比较不错,值得试试,被吸引的点如下:
```sql
CREATE INDEX name_idx ON goods USING GIN(to_tsvector('English',name));
```

# 实例(已过时)
## 加载全文索引插件zhparser (需要具有superuser权限加载)
```sql
drop extension if exists zhparser cascade; 
create extension zhparser; 
create text search configuration chinesecfg (parser = zhparser); 
alter text search configuration chinesecfg add mapping for n,v,a,i,e,l with simple;
```
 
## 建表
```sql
CREATE TABLE tb_staticsite (
	siteid varchar(64),
	sitedesc varchar(4000),
	sitetype bigint,
	longitude varchar(64),
	latitude varchar(64),
	siteaddress varchar(500)
);
```

## 加全文索引字段
```sql
ALTER TABLE tb_staticsite add column sitedesc_ts tsvector;
ALTER TABLE tb_staticsite add column siteaddress_ts tsvector;
```

## 建全文索引
```sql
CREATE INDEX idx_staticsite_sitedesc ON tb_staticsite USING gin(sitedesc_ts);
CREATE INDEX idx_staticsite_siteaddress ON tb_staticsite USING gin(siteaddress_ts);
```
 
## 指定词库
```sql
UPDATE tb_staticsite SET sitedesc_ts =to_tsvector('chinesecfg', sitedesc);
UPDATE tb_staticsite SET siteaddress_ts =to_tsvector('chinesecfg', siteaddress);
/*触发器性能不佳,建议使用job定时执行*/
```

## 查询
```sql
SELECT * FROM tb_staticsite WHERE sitedesc_ts @@ to_tsquery('南京市');
SELECT * FROM tb_staticsite WHERE siteaddress_ts @@ to_tsquery('江苏省');
``` 

