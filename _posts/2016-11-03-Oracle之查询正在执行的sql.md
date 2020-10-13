---
layout: post
title: Oracle之查询正在执行的sql 
categories: Oracle
description: Oracle之查询正在执行的sql
keywords: Oracle
---


```sql
select sql_text, 
		spid, 
		v$session.program, 
		process
  from 	v$sqlarea, 
		v$session, 
		v$process
where v$session.PADDR = v$process.addr
		and v$sqlarea.SQL_TEXT not like '%select%' -- 可以修改
		and v$process.spid in (9027);              -- 可以修改
```
