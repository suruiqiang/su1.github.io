---
layout: post
title: PostgreSQL之终止某用户下所有SQL 
categories: PostgreSQL
description: PostgreSQL之终止某用户下所有SQL
keywords: PostgreSQL
---

# 创建function
```sql
CREATE OR REPLACE FUNCTION toolkit.func_kill_process(in_username varchar, in_dbname varchar)
RETURNS boolean AS
$body$
DECLARE
  b_result boolean default false;
  cur_pid cursor for select pid from pg_stat_activity where usename = lower(in_username) and datname = lower(in_dbname) and pid<>pg_backend_pid();
BEGIN
  for tmp_pid in cur_pid loop
    b_result = pg_terminate_backend(tmp_pid.pid);
    if b_result = true then
      raise notice '%s', 'user ' || in_username || ' process ' || tmp_pid.pid || ' killed!';
    end if;
  end loop;
  return true;
END;
$body$
LANGUAGE PLPGSQL;
```

# 执行操作
```sql
select toolkit.func_kill_process('DB_USER','DB_NAME'); 
```

# 感谢
```
这里需要感谢我的第一位PostgreSQL师傅
```

