---
layout: post
title: Oracle之表空间和用户和kill会话 
categories: Oracle
description: Oracle之表空间和用户和kill会话
keywords: Oracle
---


# 创建表空间
```sql
create  bigfile tablespace 表空间名 datafile ‘/xxx/xxx/xxx.dbf’ size 100m autoextend on next 1024m nologging;
```

# 创建用户
```sql
create user 用户 identified by 密码
	default tablespace 表空间名
	temporary tablespace temp;
	profile default;
```

# 删除用户
```sql
drop user 用户名 cascade;
```

# 删除表空间以及文件
```sql
drop tablespace weishu_tbs including contents and datafiles;
```

# 删除正在连接的用户
## 查看status
```sql
select t.USERNAME,t.SID,t.SERIAL#,t.STATUS from v$session t where lower(username)='XXX';
```
## 杀掉sid，sersial#
```sql
alter system kill session '822,8993'
```
## 删除用户
```sql
drop user weishu cascade;
```
 
