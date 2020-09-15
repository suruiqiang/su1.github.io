---
layout: post
title: PostgreSQL之pgscript使用方法
categories: PostgreSQL
description: PostgreSQL之pgscript使用方法
keywords: PostgreSQL
---
# 参考
[pgscript](https://wiki.postgresql.org/wiki/Gsoc08-pgscript)

# pgScript作用
```
pgScript是一种客户端脚本语言，而pl/PgSQL在服务器上运行。这意味着它们有完全不同的用例
```

# 实例
## pgscript小例
```sql
declare @i , @labels , @tdef; 
set @i=0;
 
set @tdef='test(';
 
while @i<5
begin
	set @tdef = @tdef + 'id' +cast(@i as string)+ ' text,';
	set @i = @i + 1;
end
set @tdef=@tdef+'id'+cast(@i as string)+' text);';
 
print @tdef;
 
create table @tdef;

--上述脚本会创建表create table test(id0 text,id1 text,id2 text,id3 text,id4 text,id5 text);
```

## 调用方法
![pgscript_png](/images/posts/pgscript.jpg)

