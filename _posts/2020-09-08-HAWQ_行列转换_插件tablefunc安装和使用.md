---
layout: post
title: HAWQ_行列转换_插件tablefunc安装和使用 
categories: HAWQ
description: HAWQ_行列转换_插件tablefunc安装和使用
keywords: HAWQ
---


# 说明
```
此功能只在测试环境论证过，没有在生产环境论证，如需使用，请自行评估风险
```

# HAWQ版本
```
PostgreSQL 8.2.15 (Greenplum Database 4.2.0 build 1) (HAWQ 2.4.0.0 build dev) on x86_64-unknown-linux-gnu, compiled by GCC gcc (G
CC) 4.8.5 20150623 (Red Hat 4.8.5-39) compiled on Mar 28 2020 17:33:50
```

# 获取和编译tablefunc
```
>$ mkdir -p /home/demo/tablefunc
>$ cd /home/demo/tablefunc
>$ wget https://ftp.postgresql.org/pub/source/v8.2.15/postgresql-8.2.15.tar.gz 
>$ tar zxvf postgresql-8.2.15.tar.gz
```

# tablefunc安装
```
>$ source /usr/local/hawq/greenplum_path.sh
>$ cd /home/demo/tablefunc/postgresql-8.2.15/contrib/tablefunc
>$ make USE_PGXS=1 install
```

```
gcc -O3 -std=gnu99  -Wall -Wmissing-prototypes -Wpointer-arith  -Wendif-labels -Wformat-security -fno-strict-aliasing -fwrapv -fno-aggressive-loop-optimizations  -I/usr/include/libxml2 -fpic -I. -I/usr/local/hawq/include/postgresql/server -I/usr/local/hawq/include/postgresql/internal -D_GNU_SOURCE  -I/opt/script/hawq-9c7ca3c2ce0b825c7236eaf208abf2475d9c992f/depends/libhdfs3/build/install/include -I/opt/script/hawq-9c7ca3c2ce0b825c7236eaf208abf2475d9c992f/depends/libyarn/build/install/include  -c -o tablefunc.o tablefunc.c
gcc -O3 -std=gnu99  -Wall -Wmissing-prototypes -Wpointer-arith  -Wendif-labels -Wformat-security -fno-strict-aliasing -fwrapv -fno-aggressive-loop-optimizations  -I/usr/include/libxml2 -fpic -L/usr/local/hawq/lib -L/usr/local/hawq/lib -Wl,--as-needed -L/opt/script/hawq-9c7ca3c2ce0b825c7236eaf208abf2475d9c992f/depends/libhdfs3/build/install/lib -L/opt/script/hawq-9c7ca3c2ce0b825c7236eaf208abf2475d9c992f/depends/libyarn/build/install/lib -Wl,-rpath,'/usr/local/hawq/lib',--enable-new-dtags  -shared -o tablefunc.so tablefunc.o
/bin/sh /usr/local/hawq/lib/postgresql/pgxs/src/makefiles/../../config/install-sh -c -m 644 ./uninstall_tablefunc.sql '/usr/local/hawq/share/postgresql/contrib'
/bin/sh /usr/local/hawq/lib/postgresql/pgxs/src/makefiles/../../config/install-sh -c -m 644 tablefunc.sql '/usr/local/hawq/share/postgresql/contrib'
 /bin/sh /usr/local/hawq/lib/postgresql/pgxs/src/makefiles/../../config/install-sh -c -m 755  tablefunc.so '/usr/local/hawq/lib/postgresql'
/bin/sh /usr/local/hawq/lib/postgresql/pgxs/src/makefiles/../../config/install-sh -c -m 644 ./README.tablefunc '/usr/local/hawq/docs/contrib'
rm tablefunc.o
```

# tablefunc.so包发送到其他机器
```
>$ gpscp -f all_nomaster  /usr/local/hawq/lib/postgresql/tablefunc.so =:/usr/local/hawq/lib/postgresql/
>$ gpssh -f all_nomaster "chmod 755 /usr/local/hawq/lib/postgresql/tablefunc.so"
>$ psql postgres -f  /usr/local/hawq/share/postgresql/contrib/tablefunc.sql
```


# 行列转换
## 测试表
```
create table score(
name varchar,
subject varchar,
score bigint
);
```

## 测试数据
```
insert into  score values
('Lucy','English',100),
('Lucy','Physics',90),
('Lucy','Math',85),
('Lily','English',76),
('Lily','Physics',57),
('Lily','Math',86),
('David','English',57),
('David','Physics',86),
('David','Math',100),
('Simon','English',88),
('Simon','Physics',99),
('Simon','Math',65);
```
## 原数据查询
```
select * from score;
 name  | subject | score 
-------+---------+-------
 Lucy  | English |   100
 Lucy  | Physics |    90
 Lucy  | Math    |    85
 Lily  | English |    76
 Lily  | Physics |    57
 Lily  | Math    |    86
 David | English |    57
 David | Physics |    86
 David | Math    |   100
 Simon | English |    88
 Simon | Physics |    99
 Simon | Math    |    65
(12 rows)
```
## sql标准实现
```
select name,
	sum(case when subject='English' then score else 0 end) as "English",
	sum(case when subject='Physics' then score else 0 end) as "Physics",
	sum(case when subject='Math' then score else 0 end) as "Math"
	from score
	group by name order by name desc;
 name  | English | Physics | Math 
-------+---------+---------+------
 Simon |      88 |      99 |   65
 Lucy  |     100 |      90 |   85
 Lily  |      76 |      57 |   86
 David |      57 |      86 |  100
(4 rows)
```
## postgres内嵌函数实现
```
select * from 
	crosstab('select name,subject,score from score order by name desc',   --name:分组标准,subject:聚合标准,score:聚合标准下经过计算的值
	$$values('English'::text),('Physics'::text),('Math'::text)$$           
	)
	as score(name text,English bigint,Physics bigint,Math bigint);        --显示字段name,English,Physics,Math   [name是分组标准;English,Physics,Math是聚合标准产生的字段名]
 name  | english | physics | math 
-------+---------+---------+------
 Simon |      88 |      99 |   65
 Lucy  |     100 |      90 |   85
 Lily  |      76 |      57 |   86
 David |      57 |      86 |  100
(4 rows)
```
# 报错汇总
## 报错
```
/usr/local/hawq/include/postgresql/server/storage/fd.h:61:23: 致命错误：hdfs/hdfs.h：没有那个文件或目录
 #include "hdfs/hdfs.h"
                       ^
编译中断。
make: *** [tablefunc.o] 错误 1
```
## 解决方法
```
文件中/usr/local/hawq/include/postgresql/server/storage/fd.h
将
#include "hdfs/hdfs.h" 
替换成
#include "/usr/local/hawq/include/hdfs/hdfs.h"
```
# 参考:
http://www.bubuko.com/infodetail-2159755.html

