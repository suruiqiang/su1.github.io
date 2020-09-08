---
layout: post
title: postgres或greenplum下psql使用方法 
categories: PostgreSQL
description: postgres或greenplum下psql使用方法
keywords: PostgreSQL,GPDB,HAWQ
---
# 说明
由于本人英语水平有限，翻译仅供参考

# 测试环境：
```
cpu : 2核
mem : 2G
disk: 5G
```
## 数据库情况:
```
1 master + 1 primary + 1 mirror
```
## 数据库版本：
```
PostgreSQL 8.2.15 (Greenplum Database 4.3.25.1 build 1)
```
# psql帮助手册指南：
```
testdb=# \?
General
  \copyright             show PostgreSQL usage and distribution terms
  \g [FILE] or ;         execute query (and send results to file or |pipe)
  \h [NAME]              help on syntax of SQL commands, * for all commands
  \q                     quit psql

Query Buffer
  \e [FILE]              edit the query buffer (or file) with external editor
  \ef [FUNCNAME]         edit function definition with external editor
  \p                     show the contents of the query buffer
  \r                     reset (clear) the query buffer
  \s [FILE]              display history or save it to file
  \w FILE                write query buffer to file

Input/Output
  \copy ...              perform SQL COPY with data stream to the client host
  \echo [STRING]         write string to standard output
  \i FILE                execute commands from file
  \o [FILE]              send all query results to file or |pipe
  \qecho [STRING]        write string to query output stream (see \o)

Informational
  (options: S = show system objects, + = additional detail)
  \d[S+]                 list tables, views, and sequences
  \d[S+]  NAME           describe table, view, sequence, or index
  \da[S]  [PATTERN]      list aggregates
  \db[+]  [PATTERN]      list tablespaces
  \dc[S]  [PATTERN]      list conversions
  \dC     [PATTERN]      list casts
  \dd[S]  [PATTERN]      show comments on objects
  \ddp    [PATTERN]      list default privileges
  \dD[S]  [PATTERN]      list domains
  \des[+] [PATTERN]      list foreign servers
  \deu[+] [PATTERN]      list user mappings
  \dew[+] [PATTERN]      list foreign-data wrappers
  \df[antw][S+] [PATRN]  list [only agg/normal/trigger/window] functions
  \dF[+]  [PATTERN]      list text search configurations
  \dFd[+] [PATTERN]      list text search dictionaries
  \dFp[+] [PATTERN]      list text search parsers
  \dFt[+] [PATTERN]      list text search templates
  \dg[+]  [PATTERN]      list roles (groups)
  \dx[+]  [PATTERN]      list extensions
  \di[S+] [PATTERN]      list indexes
  \dl                    list large objects, same as \lo_list
  \dn[+]  [PATTERN]      list schemas
  \do[S]  [PATTERN]      list operators
  \dp     [PATTERN]      list table, view, and sequence access privileges
  \dr[S+] [PATTERN]      list foreign tables
  \drds [PATRN1 [PATRN2]] list per-database role settings
  \ds[S+] [PATTERN]      list sequences
  \dt[S+] [PATTERN]      list tables
  \dT[S+] [PATTERN]      list data types
  \du[+]  [PATTERN]      list roles (users)
  \dv[S+] [PATTERN]      list views
  \dE     [PATTERN]      list external tables
  \l[+]                  list all databases
  \z      [PATTERN]      same as \dp

Formatting
  \a                     toggle between unaligned and aligned output mode
  \C [STRING]            set table title, or unset if none
  \f [STRING]            show or set field separator for unaligned query output
  \H                     toggle HTML output mode (currently off)
  \pset NAME [VALUE]     set table output option
                         (NAME := {format|border|expanded|fieldsep|footer|null|
                         numericlocale|recordsep|tuples_only|title|tableattr|pager})
  \t [on|off]            show only rows (currently off)
  \T [STRING]            set HTML <table> tag attributes, or unset if none
  \x [on|off]            toggle expanded output (currently off)

Connection
  \c[onnect] [DBNAME|- USER|- HOST|- PORT|-]
                         connect to new database (currently "testdb")
  \encoding [ENCODING]   show or set client encoding
  \password [USERNAME]   securely change the password for a user
  \conninfo              display information about current connection

Operating System
  \cd [DIR]              change the current working directory
  \timing [on|off]       toggle timing of commands (currently off)
  \! [COMMAND]           execute command in shell or start interactive shell

Variables
  \prompt [TEXT] NAME    prompt user to set internal variable
  \set [NAME [VALUE]]    set internal variable, or list all if no parameters
  \unset NAME            unset (delete) internal variable

Large Objects
  \lo_export LOBOID FILE
  \lo_import FILE [COMMENT]
  \lo_list
  \lo_unlink LOBOID      large object operations
```

# 部分指令测试：

## 命令: \copyright 

### 功能介绍: 显示 PostgreSQL 的版权和版本信息
```sql
testdb=#  \copyright
Greenplum Database version of PostgreSQL Database Management System
(formerly known as Postgres, then as Postgres95)

Portions Copyright (c) 2014-Present Pivotal Software, Inc.

Portions Copyright (c) 2011-2014 EMC

Portions Copyright (c) 1996-2011, PostgreSQL Global Development Group

Portions Copyright (c) 1994, The Regents of the University of California

Permission to use, copy, modify, and distribute this software and its
documentation for any purpose, without fee, and without a written agreement
is hereby granted, provided that the above copyright notice and this paragraph
and the following two paragraphs appear in all copies.

IN NO EVENT SHALL THE UNIVERSITY OF CALIFORNIA BE LIABLE TO ANY PARTY FOR
DIRECT, INDIRECT, SPECIAL, INCIDENTAL, OR CONSEQUENTIAL DAMAGES, INCLUDING LOST
PROFITS, ARISING OUT OF THE USE OF THIS SOFTWARE AND ITS DOCUMENTATION, EVEN IF
THE UNIVERSITY OF CALIFORNIA HAS BEEN ADVISED OF THE POSSIBILITY OF SUCH
DAMAGE.

THE UNIVERSITY OF CALIFORNIA SPECIFICALLY DISCLAIMS ANY WARRANTIES, INCLUDING,
BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A
PARTICULAR PURPOSE.THE SOFTWARE PROVIDED HEREUNDER IS ON AN "AS IS" BASIS,
AND THE UNIVERSITY OF CALIFORNIA HAS NO OBLIGATIONS TO PROVIDE MAINTENANCE,
SUPPORT, UPDATES, ENHANCEMENTS, OR MODIFICATIONS.
```
## 命令: \g  

### 功能介绍: 执行查询（并将结果发送到文件或管道）
#### 文件模式:
```sql
>$ vi g.sql 
\g res.sql  
select 'select 1234567890'; 

>$ psql -f g.sql 
>$ cat res.sql 
     ?column?      
-------------------
 select 1234567890
(1 row)
```
#### 管道模式1：
```sql
>$ vi g.sql
\g  | psql
select 'select 1234567890';

>$ psql -Atf g.sql
  ?column?  
------------
 1234567890
(1 row)
```
#### 管道模式2：
```sql
>$ vi g.sql
\g | xargs  
select 'select 1234567890'; 

>$ psql -f g.sql 
?column? ------------------- select 1234567890 (1 row)
```

## 命令: \h  

### 功能介绍: 帮助SQL命令的语法，*用于所有命令

#### 查看所有的命令
```sql
testdb=# \h *
Command:     ABORT
Description: abort the current transaction
Syntax:
ABORT [ WORK | TRANSACTION ]
......
```
#### 查看建表语句
```sql
testdb=# \h create table
Command:     CREATE TABLE
Description: define a new table
Syntax:
CREATE [[GLOBAL | LOCAL] {TEMPORARY | TEMP}] TABLE table_name ( 
[ { column_name data_type [ DEFAULT default_expr ]     [column_constraint [ ... ]
[ ENCODING ( storage_directive [,...] ) ]
......
```
## 命令: \q 

### 功能介绍: 退出psql
```sql
>$ psql
psql (8.3.23)
Type "help" for help.

testdb=# \q
>$
>$ psql
psql (8.3.23)
Type "help" for help.

testdb=#
```
 
## 命令: \p 

### 功能介绍: 显示查询缓冲区的内容
```sql
testdb=# select 6;
 ?column? 
----------
        6
(1 row)

testdb=# \e
 ?column? 
----------
        9
(1 row)

testdb=# \p
select 9;
testdb=#
``` 
## 命令: \e 

### 功能介绍: 使用外部编辑器编辑查询缓冲区（或文件）
#### 查询缓冲区模式：
```
testdb=# select 1;
 ?column? 
----------
        1
(1 row)

#编辑查询缓冲区同时执行sql并返回结果       
testdb=# \e 
 ?column? 
----------
        2
(1 row)


#展示查询缓冲区
testdb=# \p
select 2;
testdb=# 
```

#### 文件模式：
```sql
testdb=# select 7;
 ?column? 
----------
        7
(1 row)

testdb=# \p
select 7;

#编辑文件 保存退出后会执行buffer.sql中的内容
testdb=# \e buffer.sql
 ?column? 
----------
        7
(1 row)

testdb=# \p
select 7;
testdb=# 
```

## 命令: \ef 

### 功能介绍: 使用外部编辑器编辑函数定义
#### postgres 8.3环境测试   
```sql
testdb=# \ef mini_toolkit.func_slice_tablehe server (version 8.3) 
does not support editing function source.
```

#### postgres 9.6环境测试
```
postgres=# 
postgres=# 
#编辑函数fun_getdistribution，并保存退出
postgres=# \ef fun_getdistribution
postgres-# ;
CREATE FUNCTION
postgres=#\x
postgres=#\df+ fun_getdistribution
List of functions
-[ RECORD 1 ]-------+--------------------
Schema              | public
Name                | fun_getdistribution
Result data type    | record
Argument data types | tab_name text
Type                | normal
Volatility          | volatile
Parallel            | unsafe
Owner               | pg96
Security            | invoker
Access privileges   | 
Language            | sql
Source code         |                    +
                    |         select 6;  +
                    | 
Description         | 

postgres=# 
```

## 命令: \r 

### 功能介绍: 重置（清除）查询缓冲区绍:
```sql
testdb=#select 1;
testdb=# \p
select 1;
testdb=# \r
Query buffer reset (cleared).
testdb=# \p
Query buffer is empty.
testdb=# 
```

## 命令: \s 

### 功能介绍: 显示历史记录或将其保存到文件
```sql
testdb=# select 1;
 ?column? 
----------
        1
(1 row)

testdb=# select 2;
 ?column? 
----------
        2
(1 row)

testdb=# select 3;
 ?column? 
----------
        3
(1 row)

testdb=# \s history_records.sql
Wrote history to file "./history_records.sql".
testdb=#
```
 
## 命令: \w 

### 功能介绍: 将查询缓冲区写入文件
```sql
testdb=# \p
select 'heelo';
testdb=# \w buffer_save.sql
testdb=# \! cat buffer_save.sql
select 'heelo';
testdb=# 
```
## 命令: \copy 

### 功能介绍: 执行copy SQL将数据流发送到客户端主机
```sql
前提: 使用pgadminIII工具登陆数据库，打开PSQL console组件，并执行下列语句
testdb=# --导出数据到客户端
testdb=# \copy t to 'C:\Users\lenovo\Desktop\ppt模板\t.data' ;

testdb=#--将客户端数据导入数据库
testdb=# truncate table t;
TRUNCATE TABLE
testdb=# select count(*) from t;
 count
-------
     0
(1 row)


testdb=# \copy t from 'C:\Users\lenovo\Desktop\ppt模板\t.data' ;
testdb=# select count(*) from t;
 count
-------
   100
(1 row)
testdb=#
```

## 命令: \echo [STRING]  

### 功能介绍: 将字符串标准输出
```sql
--单引号与双引号的区别
testdb=# \echo "'hello world'"
"'hello world'"
testdb=# \echo '"hello world"'
"hello world"
testdb=# \echo "hello world"
"hello world"
testdb=# \echo 'hello world'
hello world
testdb=#
``` 

## 命令: \i FILE  

### 功能介绍: 执行文件中的命令
```sql
>$ cat test.sql 
select 'iiiii';

>$ psql
psql (8.3.23)
Type "help" for help.

testdb=# \i test.sql
 ?column? 
----------
 iiiii
(1 row)

testdb=#
``` 
## 命令: \o [FILE]  

### 功能介绍: 发送所有的查询结果到文件或管道

#### 将结果写入文件1：
```sql
>$ >res.sql 

>$ psql
psql (8.3.23)
Type "help" for help.

testdb=# \o res.sql
testdb=# select 123456;
testdb=# \q
>$ cat res.sql 
 ?column? 
----------
   123456
(1 row) 
```
#### 将结果写入文件2：
```sql
>$ vi o.sql 
\o res.sql
select 'select 1';  
>$ cat res.sql 
 ?column? 
----------
 select 1
(1 row)
```
#### 管道模式1:
```sql
>$ vi o.sql 
\o  | psql
select 'select 1';  

>$ psql -Atf o.sql 
 ?column? 
----------
        1
(1 row)
```
#### 管道模式2:
```sql
>$ vi o.sql 
\o | xargs  
select 'select 1234567890';
>$ psql -f o.sql 
?column? ------------------- select 1234567890 (1 row)
```
## 命令: \qecho [STRING]  

### 功能介绍: 写入字符串以查询输出流
```sql
testdb=# \o t.data
testdb=# \qecho 123456
testdb=# \q
[gpadmin@test cmd]$ cat t.data 
123456
```
## 命令: \d[S+]  

### 功能介绍: 列出表、视图和序列.
```sql
teb=# \dS+ 
                                     List of relations
   Schema   |              Name               |   Type   |  Owner  | Storage | Description 
------------+---------------------------------+----------+---------+---------+-------------
 pg_catalog | gp_configuration                | table    | gpadmin | heap    | 
 pg_catalog | gp_configuration_history        | table    | gpadmin | heap    | 
......
```
## 命令: \d[S+] NAME  

### 功能介绍: 描述表、视图、序列或索引
```sql
testdb=# \dS+ public.test_seq
                 Sequence "public.test_seq"
    Column     |  Type   |  Value   | Storage | Description 
---------------+---------+----------+---------+-------------
 sequence_name | name    | test_seq | plain   | 
 last_value    | bigint  | 2        | plain   | 
 increment_by  | bigint  | 2        | plain   | 
 max_value     | bigint  | 10       | plain   | 
 min_value     | bigint  | 0        | plain   | 
 cache_value   | bigint  | 2        | plain   | 
 log_cnt       | bigint  | 4        | plain   | 
 is_cycled     | boolean | t        | plain   | 
 is_called     | boolean | t        | plain   | 

testdb=# \dS+ t
                   Table "public.t"
 Column |  Type   | Modifiers | Storage | Description 
--------+---------+-----------+---------+-------------
 id     | integer |           | plain   | 
Has OIDs: no
Distributed by: (id)

testdb=#
``` 
## 命令: \da[S] [PATTERN]  

### 功能介绍: 列出聚合函数
```sql
testdb=# \daS
                                                                List of aggregate functions
   Schema  | Name    | Result data type  | Argument data types      |     Description                        
-----------+---------+-------------------+--------------------------+---------------------------
 pg_catalog|array_agg| anyarray          | anyelement               | concatenate aggregate input into an array
 pg_catalog|array_sum| integer[]         | integer[]                | 
 pg_catalog|avg      | numeric           | bigint                   | 
......

testdb=# \daS corr
                               List of aggregate functions
   Schema   | Name | Result data type |        Argument data types         | Description 
------------+------+------------------+------------------------------------+-------------
 pg_catalog | corr | double precision | double precision, double precision | 
(1 row)
```
## 命令: \db[+] [PATTERN]  

### 功能介绍: 列出表空间
```sql
testdb=# \db+
                           List of tablespaces
    Name    |  Owner  | Filespace Name | Access privileges | Description 
------------+---------+----------------+-------------------+-------------
 pg_default | gpadmin | pg_system      |                   | 
 pg_global  | gpadmin | pg_system      |                   | 
(2 rows)
```
## 命令: \dc[S] [PATTERN]  

### 功能介绍: 列出字符集转换函数
```sql
testdb=# \dcS
                                   List of conversions
   Schema   |              Name              |     Source     |  Destination   | Default? 
------------+--------------------------------+----------------+----------------+----------
 pg_catalog | ascii_to_mic                   | SQL_ASCII      | MULE_INTERNAL  | yes
 pg_catalog | ascii_to_utf8                  | SQL_ASCII      | UTF8           | yes
......

testdb=# \dcS+ windows_866_to_windows_1251
                            List of conversions
   Schema   |            Name             | Source | Destination | Default? 
------------+-----------------------------+--------+-------------+----------
 pg_catalog | windows_866_to_windows_1251 | WIN866 | WIN1251     | yes
(1 row)
```
## 命令: \dC [PATTERN]  

### 功能介绍: 列出类型转换函数
```sql
testdb=# \dC 
                                         List of casts
         Source type         |         Target type         |      Function      |   Implicit?   
-----------------------------+-----------------------------+--------------------+---------------
 abstime                     | date                        | date               | in assignment
 abstime                     | integer                     | (binary coercible) | no
......

testdb=# \dC bit
                       List of casts
 Source type | Target type |      Function      | Implicit? 
-------------+-------------+--------------------+-----------
 bigint      | bit         | bit                | no
 bit         | bigint      | int8               | no
 bit         | bit         | bit                | yes
 bit         | bit varying | (binary coercible) | yes
 bit         | integer     | int4               | no
 bit varying | bit         | (binary coercible) | yes
 integer     | bit         | bit                | no
(7 rows)
```
## 命令: \dd[S] [PATTERN]  

### 功能介绍: 显示对象的注释
```sql
testdb=# \ddS+ com
         Object descriptions
 Schema | Name | Object | Description 
--------+------+--------+-------------
 public | com  | table  | com表注释
(1 row)
```

## 命令: \ddp [PATTERN]  

### 功能介绍: 列出默认权限
#### PostgreSQL 8.3.23 (Greenplum Database 5.0.0 build dev) on x86_64-pc-linux-gnu环境测试
```sql
testdb=# \ddp
The server (version 8.3) does not support altering default privileges.
testdb=# 
```
#### PostgreSQL 10.3 on x86_64-pc-linux-gnu环境测试
```sql
postgres=> create user d_a password '123456';
postgres=> create user d_b password '123456';
postgres=> \c postgres d_a
postgres=> create table tab (id int);
postgres=> insert into tab select 1;
postgres=> ALTER DEFAULT PRIVILEGES for user d_a  IN SCHEMA public grant select on  tables to d_b;
postgres=> \ddp
         Default access privileges
 Owner | Schema | Type  | Access privileges 
-------+--------+-------+-------------------
 d_a   | public | table | d_b=r/d_a
(1 row)

postgres=> create table t(id int);
CREATE TABLE
postgres=> \dp t
                            Access privileges
 Schema | Name | Type  | Access privileges | Column privileges | Policies 
--------+------+-------+-------------------+-------------------+----------
 public | t    | table | d_a=arwdDxt/d_a  +|                   | 
        |      |       | d_b=r/d_a         |                   | 
(1 row)
```
## 命令: \dD[S] [PATTERN]  

### 功能介绍: 显示所有共同值域
```sql
--未测试通过
```

## 命令: \des[+] [PATTERN]   list foreign servers

### 功能介绍: 显示外部服务
```sql
--未测试通过
参考: https://yq.aliyun.com/articles/3074
```

## 命令: \deu[+] [PATTERN]  
### 功能介绍: 显示用户映射
```sql
--未测试通过
参考: https://yq.aliyun.com/articles/3074
```
## 命令: \dew[+] [PATTERN]  

### 功能介绍: 显示外部数据包装器
```sql
--未测试通过 https://yq.aliyun.com/articles/3074
```

## 命令: \df[antw][S+] [PATRN]  

### 功能介绍: 列表[聚合函数/正常的/触发器/窗口]函数
```sql
--前提先写一个函数
test=# \df
                         List of functions
 Schema |  Name  | Result data type | Argument data types |  Type  
--------+--------+------------------+---------------------+--------
 public | add_em | integer          | integer, integer    | normal
(1 row)

test=# \x
Expanded display is on.
test=# \df+ add_em
List of functions
-[ RECORD 1 ]-------+-----------------
Schema              | public
Name                | add_em
Result data type    | integer
Argument data types | integer, integer
Type                | normal
Data access         | contains sql
Volatility          | volatile
Owner               | gpadmin
Language            | sql
Source code         | 
                    | SELECT $1 + $2;
                    | 
Description         | 

test=#
``` 
## 命令: \dF[+] [PATTERN]  

### 功能介绍: 显示文本搜索配置
```sql
test=# \dF+
The server (version 8.2) does not support full text search.
test=#
```
 
## 命令: \dFd[+] [PATTERN]   
### 功能介绍: 显示文本搜索字典
```sql
test=# \dFd+
The server (version 8.2) does not support full text search.
test=#
```
 
## 命令: \dFp[+] [PATTERN]  

### 功能介绍: 显示文本搜索解析器
```sql
test=# \dFp+ The server (version 8.2) does not support full text search. 
test=#
```

## 命令: \dFt[+] [PATTERN]  

### 功能介绍: 显示文本搜索模板
```sql
test=# \dFt+ The server (version 8.2) does not support full text search. 
test=#
```

## 命令: \dg[+] [PATTERN]  

### 功能介绍: 显示用户角色(用户组 )
```sql
test=# \dg+
                                List of roles
 Role name  |            Attributes             |  Member of   | Description 
------------+-----------------------------------+--------------+-------------
 dataload   |                                   | {rightnow}   | 
 gpadmin    | Superuser, Create role, Create DB |              | 
 gpmon      | Superuser, Create DB              |              | 
 group_user |                                   |              | 
 rightnow   |                                   |              | 
 test_user  |                                   | {group_user} | 
```
## 命令: \di[S+] [PATTERN]  

### 功能介绍: 显示索引
```sql
test=# \di
                   List of relations
 Schema |   Name   | Type  |  Owner  | Storage | Table 
--------+----------+-------+---------+---------+-------
 public | idx_y_id | index | gpadmin | heap    | y
(1 row)

test=# \di+ idx_y_id
                          List of relations
 Schema |   Name   | Type  |  Owner  | Storage | Table | Description 
--------+----------+-------+---------+---------+-------+-------------
 public | idx_y_id | index | gpadmin | heap    | y     | 
(1 row)

test=#
``` 
## 命令: \dl  

### 功能介绍: 列出大对象，与\lo_list]相同
```sql
--目前基于gpdb测试失败，各位可以基于postgres版本测试看看
参考: https://blog.csdn.net/zutsoft/article/details/78847559
```

## 命令: \dn[+] [PATTERN]  

### 功能介绍: 显示schema
```
test=# \dn
       List of schemas
        Name        |  Owner  
--------------------+---------
 gp_toolkit         | gpadmin
 information_schema | gpadmin
 mini_toolkit       | gpadmin
 pg_aoseg           | gpadmin
 pg_bitmapindex     | gpadmin
 pg_catalog         | gpadmin
 pg_toast           | gpadmin
 public             | gpadmin
(8 rows)

test=# \dn+
                                                 List of schemas
        Name        |  Owner  | Access privileges  |                         Description                         
--------------------+---------+--------------------+-------------------------------------------------------------
 gp_toolkit         | gpadmin | gpadmin=UC/gpadmin | 
                              : =U/gpadmin           
 information_schema | gpadmin | gpadmin=UC/gpadmin | 
                              : =U/gpadmin           
 mini_toolkit       | gpadmin | gpadmin=UC/gpadmin | 
                              : =UC/gpadmin          
 pg_aoseg           | gpadmin |                    | Reserved schema for Append Only segment list and eof tables
 pg_bitmapindex     | gpadmin |                    | Reserved schema for internal relations of bitmap indexes
 pg_catalog         | gpadmin | gpadmin=UC/gpadmin | System catalog schema
                              : =U/gpadmin           
 pg_toast           | gpadmin |                    | Reserved schema for TOAST tables
 public             | gpadmin | gpadmin=UC/gpadmin | Standard public schema
                              : =UC/gpadmin          
(8 rows)

test=#
```
 
## 命令: \do[S] [PATTERN]  

### 功能介绍: 显示操作符
```sql
test=# \do !
                               List of operators
   Schema   | Name | Left arg type | Right arg type | Result type | Description 
------------+------+---------------+----------------+-------------+-------------
 pg_catalog | !    | bigint        |                | numeric     | 
(1 row)

test=# \do !*
                                                  List of operators
   Schema   | Name | Left arg type | Right arg type | Result type |                   Description                    
------------+------+---------------+----------------+-------------+--------------------------------------------------
 pg_catalog | !    | bigint        |                | numeric     | 
 pg_catalog | !!   |               | bigint         | numeric     | 
 pg_catalog | !!=  | integer       | text           | boolean     | not in
 pg_catalog | !!=  | oid           | text           | boolean     | not in
 pg_catalog | !~   | character     | text           | boolean     | does not match regex., case-sensitive
 pg_catalog | !~   | name          | text           | boolean     | does not match regex., case-sensitive
 pg_catalog | !~   | text          | text           | boolean     | does not match regex., case-sensitive
 pg_catalog | !~*  | character     | text           | boolean     | does not match regex., case-insensitive
 pg_catalog | !~*  | name          | text           | boolean     | does not match regex., case-insensitive
 pg_catalog | !~*  | text          | text           | boolean     | does not match regex., case-insensitive
 pg_catalog | !~~  | bytea         | bytea          | boolean     | does not match LIKE expression
 pg_catalog | !~~  | character     | text           | boolean     | does not match LIKE expression
 pg_catalog | !~~  | name          | text           | boolean     | does not match LIKE expression
 pg_catalog | !~~  | text          | text           | boolean     | does not match LIKE expression
 pg_catalog | !~~* | character     | text           | boolean     | does not match LIKE expression, case-insensitive
 pg_catalog | !~~* | name          | text           | boolean     | does not match LIKE expression, case-insensitive
 pg_catalog | !~~* | text          | text           | boolean     | does not match LIKE expression, case-insensitive
(17 rows)

test=#
```
 
## 命令: \dp [PATTERN]  

### 功能介绍: 显示表、视图和顺序访问权限
```sql
test=# \dp
                     Access privileges for database "test"
 Schema |       Name       | Type  |             Access privileges              
--------+------------------+-------+--------------------------------------------
 public | dest_test        | table | 
 public | largeobject_test | table | 
 public | lobject_test     | table | 
 public | source_test      | table | 
 public | t                | table | {gpadmin=arwdDxt/gpadmin,=arwdDxt/gpadmin}
 public | test_tamplate    | table | 
 public | y                | table | 
(7 rows)

test=#
``` 
## 命令: \dr[S+] [PATTERN]  

### 功能介绍: 显示外部表
```sql
--未测试通过
```
## 命令: \drds [PATRN1 [PATRN2]]  

### 功能介绍: 列出每个数据库角色设置
```sql
test=# \drds 
No per-database role settings support in this server version.
test=#
```
 
## 命令: \ds[S+] [PATTERN]  

### 功能介绍: 显示序列
```sql
test=# create SEQUENCE seq_test INCREMENT 1;
CREATE SEQUENCE
test=# \ds
                List of relations
 Schema |   Name   |   Type   |  Owner  | Storage 
--------+----------+----------+---------+---------
 public | seq_test | sequence | gpadmin | heap
(1 row)

test=# \ds+ seq_test
                       List of relations
 Schema |   Name   |   Type   |  Owner  | Storage | Description 
--------+----------+----------+---------+---------+-------------
 public | seq_test | sequence | gpadmin | heap    | 
(1 row)

test=# 
```

## 命令: \dt[S+] [PATTERN]  

### 功能介绍: 显示表
```sql
mydatabase=# \dtS+ 
                                  List of relations
   Schema   |             Name              | Type  |  Owner  | Storage | Description 
------------+-------------------------------+-------+---------+---------+-------------
 pg_catalog | gp_configuration              | table | gpadmin | heap    | 
 pg_catalog | gp_configuration_history      | table | gpadmin | heap    | 
 pg_catalog | gp_db_interfaces              | table | gpadmin | heap    | 
 pg_catalog | gp_distribution_policy        | table | gpadmin | heap    | 

mydatabase=# \dtS+ cities 
                     List of relations
 Schema |  Name  | Type  |  Owner  | Storage | Description 
--------+--------+-------+---------+---------+-------------
 public | cities | table | gpadmin | heap    | 
(1 row)

mydatabase=# 
```
## 命令: \dT[S+] [PATTERN]  

### 功能介绍: 显示数据类型
```
 Schema     |  Name        |   Internal name  | Size  | Elements | Type Options | Description                                
------------+--------------+------------------+-------+----------+--------------+------------------------------------------
 pg_catalog | abstime      | abstime          | 4     |          |              | absolute, limited-range date and ... 
 pg_catalog | aclitem      | aclitem          | 12    |          |              | access control list
 pg_catalog | "any"        | any              | 4     |          |              | 
 pg_catalog | anyarray     | anyarray         | var   |          |              | 
 pg_catalog | anyelement   | anyelement       | 4     |          |              | 
 pg_catalog | anyenum      | anyenum          | 4     |          |              | 
 pg_catalog | anynonarray  | anynonarray      | 4     |          |              | 
 pg_catalog | anytable     | anytable         | var   |          |              | Represents a generic TABLE value expression

mydatabase=# \dTS+ oid
                                               List of data types
   Schema   | Name | Internal name | Size | Elements | Type Options |                Description                
------------+------+---------------+------+----------+--------------+-------------------------------------------
 pg_catalog | oid  | oid           | 4    |          |              | object identifier(oid), maximum 4 billion
(1 row)

mydatabase=# \dTS+ bytea
                                                 List of data types
   Schema   | Name  | Internal name | Size | Elements | Type Options |                  Description                  
------------+-------+---------------+------+----------+--------------+-----------------------------------------------
 pg_catalog | bytea | bytea         | var  |          |              | variable-length string, binary values escaped
(1 row)

mydatabase=#
```

## 命令: \du[+] [PATTERN]  

### 功能介绍: 显示角色(用户)
```sql
mydatabase=# \du
                                                                      List of roles
 Role name |                      Attributes                                                            | Member of 
-----------+--------------------------------------------------------------------------------------------+-----------
 gpadmin   | Superuser, Create role, Create DB, Ext gpfdist Table, Wri Ext gpfdist Table, ...           | {}
 gpmon     | Superuser, Create DB                                                                       | {}
mydatabase=# \du+
                                                                             List of roles
 Role name |                      Attributes                                                   | Member of | Description 
-----------+-----------------------------------------------------------------------------------+-----------+-------------
 gpadmin   | Superuser, Create role, Create DB, Ext gpfdist Table, Wri Ext gpfdist Table, ...  | {}        | 
 gpmon     | Superuser, Create DB                                                              | {}        | 

mydatabase=#
``` 
## 命令: \dv[S+] [PATTERN] 

### 功能介绍: 显示视图
```
mydatabase=# \dv
                   List of relations
 Schema |       Name        | Type |  Owner  | Storage 
--------+-------------------+------+---------+---------
 public | geography_columns | view | gpadmin | none
 public | geometry_columns  | view | gpadmin | none
 public | raster_columns    | view | gpadmin | none
 public | raster_overviews  | view | gpadmin | none
(4 rows)

mydatabase=# \dvS+
                                   List of relations
   Schema   |              Name               | Type |  Owner  | Storage | Description 
------------+---------------------------------+------+---------+---------+-------------
 pg_catalog | gp_distributed_log              | view | gpadmin | none    | 
 pg_catalog | gp_distributed_xacts            | view | gpadmin | none    | 
 pg_catalog | gp_pgdatabase                   | view | gpadmin | none    | 
 pg_catalog | gp_transaction_log              | view | gpadmin | none    | 
 pg_catalog | pg_available_extension_versions | view | gpadmin | none    | 
 pg_catalog | pg_available_extensions         | view | gpadmin | none    | 
```
## 命令: \dE [PATTERN] 

### 功能介绍: 显示外部表
```sql
testdb=# \dE test_cmd_web 
                 List of relations
 Schema |     Name     | Type  |  Owner  | Storage  
--------+--------------+-------+---------+----------
 public | test_cmd_web | table | gpadmin | external
(1 row)

testdb=# \d test_cmd_web 
    External table "public.test_cmd_web"
 Column |          Type          | Modifiers 
--------+------------------------+-----------
 id     | bigint                 | 
 name   | character varying(128) | 
Type: readable
Encoding: GB18030
Format type: text
Format options: delimiter '|' null '' escape 'OFF'
External options: {}
Command: sh /home/gpadmin/20160907/get_data.sh
Execute on: host 'sdw01'
```
## 命令: \l[+]  

### 功能介绍: 显示所有数据库
```
 testdb=# \l
                   List of databases
    Name    |  Owner  | Encoding |  Access privileges  
------------+---------+----------+---------------------
 gpperfmon  | gpadmin | UTF8     | gpadmin=CTc/gpadmin 
                                 : =c/gpadmin
 mydatabase | gpadmin | UTF8     | 
 postgres   | gpadmin | UTF8     | 
 template0  | gpadmin | UTF8     | =c/gpadmin          
                                 : gpadmin=CTc/gpadmin
 template1  | gpadmin | UTF8     | =c/gpadmin          
                                 : gpadmin=CTc/gpadmin
 testdb     | gpadmin | UTF8     | 
(6 rows)

testdb=# \l+
                                           List of databases
    Name    |  Owner  | Encoding |  Access privileges  | Size  | Tablespace |        Description        
------------+---------+----------+---------------------+-------+------------+---------------------------
 gpperfmon  | gpadmin | UTF8     | gpadmin=CTc/gpadmin | 24 MB | pg_default | 
                                 : =c/gpadmin                                 
 mydatabase | gpadmin | UTF8     |                     | 27 MB | pg_default | 
 postgres   | gpadmin | UTF8     |                     | 21 MB | pg_default | 
 template0  | gpadmin | UTF8     | =c/gpadmin          | 20 MB | pg_default | 
                                 : gpadmin=CTc/gpadmin                        
 template1  | gpadmin | UTF8     | =c/gpadmin          | 21 MB | pg_default | default template database
                                 : gpadmin=CTc/gpadmin                        
 testdb     | gpadmin | UTF8     |                     | 27 MB | pg_default | 
(6 rows)
```
## 命令: \a  

### 功能介绍: 在未对齐和对齐的输出模式之间切换
```sql
mydatabase=#  SELECT p1.name,p2.name,
					ST_Distance_Sphere(p1.the_geom,p2.the_geom) 
			  FROM cities AS p1, 
                    cities AS p2 
              WHERE p1.id > Domain Premium: p2.id;
      name       |      name       | st_distance_sphere 
-----------------+-----------------+--------------------
 London, Ontario | London, England |   5875787.03777356
 East London,SA  | London, England |   9789680.59961472
 East London,SA  | London, Ontario |   13892208.6782928
(3 rows)

mydatabase=# \a
Output format is unaligned.
mydatabase=#  SELECT p1.name,p2.name,
                    ST_Distance_Sphere(p1.the_geom,p2.the_geom) 
              FROM cities AS p1, 
                   cities AS p2 
              WHERE p1.id > Domain Premium: p2.id;
name|name|st_distance_sphere
London, Ontario|London, England|5875787.03777356
East London,SA|London, England|9789680.59961472
East London,SA|London, Ontario|13892208.6782928
(3 rows)

mydatabase=# \a
Output format is aligned.
mydatabase=#  SELECT p1.name,p2.name,
                    ST_Distance_Sphere(p1.the_geom,p2.the_geom) 
              FROM cities AS p1, 
                   cities AS p2 
              WHERE p1.id > Domain Premium: p2.id;
      name       |      name       | st_distance_sphere 
-----------------+-----------------+--------------------
 London, Ontario | London, England |   5875787.03777356
 East London,SA  | London, England |   9789680.59961472
 East London,SA  | London, Ontario |   13892208.6782928
(3 rows)
```
## 命令: \C [STRING]  

### 功能介绍: 设置表标题，如果没有则取消设置
```sql
mydatabase=# \C 'test'
Title is "test".
mydatabase=# select 1,2,3,4,5,6;
                              test
 ?column? | ?column? | ?column? | ?column? | ?column? | ?column? 
----------+----------+----------+----------+----------+----------
        1 |        2 |        3 |        4 |        5 |        6
(1 row)

mydatabase=# \C
Title is unset.
mydatabase=# select 1,2,3,4,5,6;
 ?column? | ?column? | ?column? | ?column? | ?column? | ?column? 
----------+----------+----------+----------+----------+----------
        1 |        2 |        3 |        4 |        5 |        6
(1 row)

mydatabase=#
``` 
## 命令: \f [STRING]   （GPDB中验证失败）

### 功能介绍: 为未对齐的查询输出显示或设置字段分隔符
```sql
mydatabase=# \f ,,,,,
Field separator is ",,,,,".
mydatabase=# select * from test ;
 a | b 
---+---
 1 | 
   | 2
(2 rows)

mydatabase=# 
```
## 命令: \H  

### 功能介绍: 切换HTML输出模式
```sql
mydatabase=# \H
Output format is aligned.
mydatabase=# select * from test ;
 a | b 
---+---
 1 | 
   | 2
(2 rows)

mydatabase=# \H
Output format is html.
mydatabase=# select * from test ;
<table border="1">
  <tr>
    <th align="center">a</th>
    <th align="center">b</th>
  </tr>
  <tr valign="top">
    <td align="left">1</td>
    <td align="left">&nbsp; </td>
  </tr>
  <tr valign="top">
    <td align="left">&nbsp; </td>
    <td align="left">2</td>
  </tr>
</table>
<p>(2 rows)<br />
</p>
mydatabase=#
```
 
## 命令: \pset NAME [VALUE]  

### 功能介绍: 这条命令设置影响查询结果表输出的选项(详细请参考postgres手册)
```sql
set table output option

(NAME := {format|border|expanded|fieldsep|footer|null|

numericlocale|recordsep|tuples_only|title|tableattr|pager})

mydatabase=# \pset expanded on
Expanded display is on.
mydatabase=# select * from test ;
[ RECORD 1 ]
| a | 1 |
| b |   |
[ RECORD 2 ]
| a |   |
| b | 2 |
-+--

mydatabase=# \pset expanded off
Expanded display is off.
mydatabase=# select * from test ;
+---+---+
| a | b |
+---+---+
| 1 |   |
|   | 2 |
+---+---+
(2 rows)

mydatabase=# \pset expanded 
Expanded display is on.
mydatabase=# \pset expanded 
Expanded display is off.
mydatabase=# \pset border
Border style is 6.
mydatabase=# \pset border 9
Border style is 9.
mydatabase=# \pset border
Border style is 9.
```
## 命令: \t [on|off]  

### 功能介绍: 仅显示行
```sql
mydatabase=# select * from test ;
+---+---+
| a | b |
+---+---+
| 1 |   |
|   | 2 |
+---+---+
(2 rows)

mydatabase=# \t
Showing only tuples.
mydatabase=# select * from test ;
| 1 |   |
|   | 2 |
+---+---+

mydatabase=#
``` 
## 命令: \T [STRING]  

### 功能介绍: 允许你在使用 HTML 输出模式时声明放在 table 标记里的属性utes, or unset if none
```
mydatabase=# \H
mydatabase=# \T
Table attributes unset.
mydatabase=# select * from test ;
<table border="9">
  <tr>
    <th align="center">a</th>
    <th align="center">b</th>
  </tr>
  <tr valign="top">
    <td align="left">1</td>
    <td align="left">&nbsp; </td>
  </tr>
  <tr valign="top">
    <td align="left">&nbsp; </td>
    <td align="left">2</td>
  </tr>
</table>
<p>(2 rows)<br />
</p>
mydatabase=# \T 123456789
Table attribute is "123456789".
mydatabase=# select * from test ;
<table border="9" 123456789>
  <tr>
    <th align="center">a</th>
    <th align="center">b</th>
  </tr>
  <tr valign="top">
    <td align="left">1</td>
    <td align="left">&nbsp; </td>
  </tr>
  <tr valign="top">
    <td align="left">&nbsp; </td>
    <td align="left">2</td>
  </tr>
</table>
<p>(2 rows)<br />
</p>
mydatabase=#
``` 
## 命令: \x [on|off]  

### 功能介绍: 切换扩展输出
```sql
mydatabase=# select * from test ;
 a | b 
---+---
 1 | 
   | 2
(2 rows)

mydatabase=# \x
Expanded display is on.
mydatabase=# select * from test ;
-[ RECORD 1 ]
a | 1
b | 
-[ RECORD 2 ]
a | 
b | 2

mydatabase=#
``` 
## 命令: \c[onnect] [DBNAME|- USER|- HOST|- PORT|-]  

### 功能介绍: 连接到新的数据库
```sql
mydatabase=# \c
You are now connected to database "mydatabase" as user "gpadmin".
mydatabase=# \c testdb gpadmin 127.0.0.1 5432
You are now connected to database "testdb" as user "gpadmin" on host "127.0.0.1" at port "5432".
testdb=# \c
You are now connected to database "testdb" as user "gpadmin".
testdb=#
``` 
## 命令: \encoding [ENCODING]  

### 功能介绍: 显示或设置客户端字符集
```sql
mydatabase=# \encoding
UTF8
mydatabase=# \encoding GB18030
mydatabase=# \encoding
GB18030
mydatabase=#
``` 
## 命令: \password [USERNAME]  

### 功能介绍: 安全地更改用户的密码
```sql
mydatabase=# create user test_user PASSWORD '123456';
NOTICE:  resource queue required -- using default resource queue "pg_default"
CREATE ROLE
mydatabase=# \password test_user
Enter new password: 
Enter it again: 
mydatabase=#
```
 
## 命令: \conninfo 

### 功能介绍: 显示有关当前连接的信息
```sql
mydatabase=# \c testdb gpadmin 127.0.0.1 5432
You are now connected to database "testdb" as user "gpadmin" on host "127.0.0.1" at port "5432".
testdb=# \conninfo
You are connected to database "testdb" as user "gpadmin" on host "127.0.0.1" at port "5432".
testdb=#
``` 
## 命令: \cd [DIR] 

### 功能介绍: 更改当前工作目录
```sql
testdb=# \! pwd
/home/gpadmin
testdb=# \cd /home/gpadmin/data
testdb=# \! pwd
/home/gpadmin/data
testdb=#
``` 
## 命令: \timing [on|off]  

### 功能介绍: 以毫秒为单位显示每条 SQL 语句的耗时
```sql
testdb=# \timing
Timing is on.
testdb=# select 1;
 ?column? 
----------
        1
(1 row)

Time: 1.379 ms
```
## 命令: \! [COMMAND]  

### 功能介绍: 在shell中执行命令或启动交互式shell
```sql
testdb=# \! cat /etc/redhat-release
CentOS Linux release 7.3.1611 (Core) 
testdb=# \! hostname
test
testdb=#
``` 
## 命令: \prompt [TEXT] NAME  

### 功能介绍: 提示用户设置内部变量(没有理解透，各位可以自行研究)
```sql
mydatabase=# \prompt 'tishi' var
tishi
```

## 命令: \set [NAME [VALUE]]  

### 功能介绍: 设置内部变量，如果没有参数，则列出所有
```sql
mydatabase=# \set a 1;
mydatabase=# select :a
 ?column? 
----------
        1
(1 row)

mydatabase=# \set 
AUTOCOMMIT = 'on'
PROMPT1 = '%/%R%# '
PROMPT2 = '%/%R%# '
PROMPT3 = '>> '
VERBOSITY = 'default'
VERSION = 'PostgreSQL 8.3.23 (Greenplum Database 5.0.0 build dev) on x86_64-pc-linux-gnu, compiled by GCC gcc (GCC) 4.8.5 20150623 (Red Hat 4.8.5-28), 64-bit'
DBNAME = 'mydatabase'
USER = 'gpadmin'
PORT = '5432'
ENCODING = 'UTF8'
var = ''
a = '1;'
```

## 命令: \unset NAME  

### 功能介绍: 取消设置（删除）内部变量
```sql
mydatabase=# \set a 1
mydatabase=# select :a;
 ?column? 
----------
        1
(1 row)

mydatabase=# \unset a 
mydatabase=# select :a;
ERROR:  syntax error at or near ":"
LINE 1: select :a;
               ^
mydatabase=# 
```

## 大对象操作
```sql
## 没有找到测试方法
Large Objects

\lo_export LOBOID FILE

\lo_import FILE [COMMENT]

\lo_list

\lo_unlink LOBOID large object operations
```


## 参考:
        关于PostgreSQL 10下psql元命令的使用方法及示例
        postgresql官方手册
