---
layout: post
title: PostgreSQL之触发器 
categories: PostgreSQL
description: PostgreSQL之触发器 
keywords: PostgreSQL
---
# 参考
[postgresql_triggers](https://www.tutorialspoint.com/postgresql/postgresql_triggers.htm)

# 特殊变量介绍
```sql
用CREATE FUNCTION命令创建
系统会在顶层的声明段里自动创建几个特殊变量。
NEW:
		数据类型是RECORD；该变量为INSERT/UPDATE 操作在行级(row-level)触发器中保存一个新的数据库行。这个变量在语句级
(statement-level)触发器中为NULL。
OLD:
    数据类型是RECORD;该变量为INSERT/UPDATE 操作在行级(row-level)触发器中保存一个旧的数据库行。这个变量在语句级
(statement-level)触发器中为NULL。
TG_WHEN:
    数据类型是text；是一个由触发器定义决定的字符串， 要么是BEFORE 要么是AFTER。
TG_LEVEL:
    数据类型是text；是一个由触发器定义决定的字符串， 要么是ROW 要么是STATEMENT。
TG_OP:
数据类型是text；是一个说明触发触发器的操作的字符串， 可以是INSERT，UPDATE 或者DELETE。
TG_RELNAME:
    数据类型是name；是激活触发器调用的表的名称。
TG_NARGS:
    数据类型是integer； 是在CREATE TRIGGER 语句里面赋予触发器过程的参数的个数。
```

# 例子
```sql
CREATE OR REPLACE function emp_sal_trig_function() RETEURNMS trigger AS $$
DECLARE
		sal_diff numeric(7,2);
BEGIN
		IF TG_OP = 'INSERT' THEN
						RAISE NOTICE 'Inserting employee %', NEW.empno;
						RAISE NOTICE '..New salary: %', NEW.sal;
		END IF;
		IF TG_OP = 'UPDATE' THEN
						sal_diff := NEW.sal - OLD.sal;
						RAISE NOTICE 'Updating employee %', OLD.empno;
						RAISE NOTICE '..Old salary: %', OLD.sal;
						RAISE NOTICE '..New salary: %', NEW.sal;
						RAISE NOTICE '..Raise : %', sal_diff;
		END IF;
		IF TG_OP = 'DELETE' THEN
						RAISE NOTICE 'Deleting employee %', OLD.empno;
						RAISE NOTICE '..Old salary: %', OLD.sal;
		END IF;
RETURN NEW; --如果返回NULL修改将被回滚
END;
$$ LANGUAGE plpgsql;
 
CREATE TRIGGER emp_sal_trig BEFORE INSERT OR UPDATE OR DELETE ON emp
FOR EACH ROW EXECUTE PROCEDURE emp_sal_trig_function();
```
 
# 返回值说明
`
它可以返回NULL以忽略对当前行的操作。 
这就指示执行器不要执行调用该触发器的行级别操作(对特定行的插入或者更改)。 

只用于INSERT和UPDATE行触发器： 
    返回的行将成为被插入的行或者是成为将要更新的行。 这样就允许触发器函数修改将要被插入或者更新的行。 

一个无意导致任何这类行为的在操作之前触发的行级触发器必须仔细返回那个被 当作新行传进来的行。
也就是说，对于INSERT 和 UPDATE触发器而言， 是NEW行，对于DELETE触发器而言，是OLD行。 
对于在操作之后触发的行级触发器，其返回值会被忽略，因此可以返回NULL。 
` 

