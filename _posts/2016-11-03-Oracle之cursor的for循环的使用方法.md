---
layout: post
title: Oracle之cursor的for循环的使用方法
categories: Oracle
description: Oracle之cursor的for循环的使用方法
keywords: Oracle
---

```sql
create or replace procedure proc_del_samehead_table(tab_head varchar2)
is
  cursor  c_tab is select t.TABLE_NAME from
       user_tables t
       where lower(t.TABLE_NAME) like trim(tab_head)||'%'
       and length(t.TABLE_NAME) > length(trim(tab_head));
begin
  for v_tmp in c_tab  loop
      dbms_output.put_line('clear '||v_tmp.table_name);
  end loop;
end;
```
