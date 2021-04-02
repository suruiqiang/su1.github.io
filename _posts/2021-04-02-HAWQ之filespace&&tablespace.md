---
layout: post
title: HAWQ之filespace&&tablespace
categories: HAWQ
description: HAWQ之filespace&&tablespace
keywords: HAWQ
---



# 创建filespace

## 配置filespace
```
># su - gpadmin
>$ hawqfilespace -o oushu_fpc 
Enter a name for this filespace
> oushu_fpc
Enter replica num for filespace. If 0, default replica num is used (default=3)
> 3
Please specify the DFS location for the filespace (for example: localhost:9000/fs)
location> nn
gpcheckhdfs hdfs nn off postgres 
gpcheckhdfs error code is 26880
20190622:09:33:18:003803 hawqfilespace:hawq01:gpadmin-[INFO]:-[created]
20190622:09:33:18:003803 hawqfilespace:hawq01:gpadmin-[INFO]:-
To add this filespace to the database please run the command:
   hawqfilespace --config /home/gpadmin/oushu_fpc

>$
```
## 确认filespace配置文件
```
>$ more /home/gpadmin/oushu_fpc
filespace:oushu_fpc
fsreplica:3
dfs_url::nn
```

## 创建所需目录
```
># su - hdfs
>$ hdfs dfs -mkdir hdfs://nn/oushu_fpc
>$ hdfs dfs -chown gpadmin:gpadmin hdfs://nn/oushu_fpc
>$ hdfs dfs -ls hdfs://nn/
Found 5 items
...
drwxr-xr-x   - gpadmin gpadmin             0 2019-06-22 09:34 hdfs://nn/oushu_fpc
>$ 
```
## 创建filespace
```
>$ hawqfilespace --config /home/gpadmin/oushu_fpc
Reading Configuration file: '/home/gpadmin/oushu_fpc'

CREATE FILESPACE oushu_fpc ON hdfs 
('nn/oushu_fpc') WITH (NUMREPLICA = 3);
20190622:09:36:24:004236 hawqfilespace:hawq01:gpadmin-[INFO]:-Connecting to database
20190622:09:36:24:004236 hawqfilespace:hawq01:gpadmin-[INFO]:-Filespace "oushu_fpc" successfully created
>$ 

postgres=# CREATE FILESPACE oushu_fpc ON hdfs ('nn/oushu_fpc') WITH (NUMREPLICA = 3);
CREATE FILESPACE
postgres=# 

postgres=# select * from pg_filespace_entry ;
 fsefsoid | fsedbid |              fselocation               
----------+---------+----------------------------------------
...
    24710 |       0 | hdfs://{replica=3}nn/oushu_fpc
(3 rows)
```

# 创建tablespace
```
postgres=# create TABLESPACE oushu_tpc FILESPACE oushu_fpc;
CREATE TABLESPACE
postgres=# select * from pg_tablespace ;
   spcname   | spcowner | spclocation | spcacl | spcprilocations | spcmirlocations | spcfsoid 
-------------+----------+-------------+--------+-----------------+-----------------+----------
...
 hawq_tpc    |       10 |             |        |                 |                 |    24710
(4 rows)
```

# 建表测试
```
postgres=# CREATE TABLE aoo(i int) TABLESPACE oushu_tpc;
CREATE TABLE
postgres=# \df generate_series 
postgres=# insert into aoo select 1;
INSERT 0 1
```