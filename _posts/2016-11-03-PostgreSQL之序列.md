---
layout: post
title: PostgreSQL之序列 
categories: PostgreSQL
description: PostgreSQL之序列 
keywords: PostgreSQL
---
# 参考
[createsequence](https://www.postgresql.org/docs/9.5/sql-createsequence.html)

# 创建序列
```sql
create sequence  序列名 increment by n    --步长
                 start with n             --开始的值
                 maxvalue n | nomaxvalue  --最大的值
                 minvalue n | nominvalue  --最小的值
                 cycle      | nocycle     --是否循环
                 cache n    | nocache     --缓存n个
```

# 使用方法
```sql
select currval('序列名')；
select nextval('序列名')；
```

# 删除序列
```sql
drop sequence序列名
```

# 与oracle区别
```
oracle版  :        
    select   序列名.nextval from dual;

postgres版:      
    select nextval('序列名')；
```
