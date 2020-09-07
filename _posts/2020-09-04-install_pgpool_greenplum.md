---
layout: post
title: install_pgpool_greenplum
categories: GPDB
description:  install_pgpool_greenplum
keywords: pgpool,GPDB,GreenPlum
---

# 源码编译
```shell
># ./configure --prefix=/usr/local/pgpool
># make
># make install
```

# 发送必须文件到所有节点
```shell
># cd /usr/local
># chown -R demo.demo pgpool/

># su - demo
>$ cd /usr/local/pgpool/sql/pgpool-regclass
>$ make install
>$ vi all_nomaster
>$ vi all_hosts
>$ gpscp -f all_nomaster pgpool-regclass.so =:/usr/local/greenplum-db-4.3.25.1/lib/postgresql/
>$ gpscp -f all_nomaster pgpool-regclass.sql =:/usr/local/greenplum-db-4.3.25.1/share/postgresql/contrib/
>$ gpssh -f all_hosts  "cd /usr/local/greenplum-db-4.3.25.1/lib/postgresql/;chmod 755 pgpool-regclass.so;chown demo.demo pgpool-regclass.so"
>$ gpssh -f all_hosts  "cd /usr/local/greenplum-db-4.3.25.1/share/postgresql/contrib;chmod 644 pgpool-regclass.sql;chown demo.demo pgpool-regclass.sql"
```

# 将pgpool的信息载入GPDB
```shell
>$ psql 数据库 -f pgpool-regclass.sql
```

# 配置环境变量
```shell
>$ cat ~/.bashrc
export PATH=/usr/local/pgpool/bin:$PATH        
```
