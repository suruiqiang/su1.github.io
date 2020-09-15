---
layout: post
title: PostgreSQL之pgscript使用方法
categories: PostgreSQL
description: PostgreSQL之pgscript使用方法
keywords: PostgreSQL
---
# 参考
[pgscript](https://documentation.help/pgAdmin-III/pgscript.html)

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

