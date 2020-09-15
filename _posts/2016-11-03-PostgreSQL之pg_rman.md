---
layout: post
title: PostgreSQL之pg_rman 
categories: PostgreSQL
description: PostgreSQL之pg_rman
keywords: PostgreSQL
---
# 参考
[postgresql-pgrman](https://www.cnblogs.com/xibuhaohao/p/11114653.html)

# 实例
## 安装以及初始化pg_rman
### 安装pg_rman的rpm包
```shell
rpm -ivh --nodeps --force pg_rman-1.2.11-1.pg94.rhel6.x86_64.rpm
```
 
### 建备份目录
```shell
mkdir -p /pgdata/backup/{pg_rman,archived_log,bin}
chown -R postgres:postgres /pgdata
```
 
### 建立软链接
```shell
su - postgres
ln -s /usr/pgsql-9.4/bin/pg_rman  /pgdata/backup/bin/pg_rman
```
 
### 备份postgresql.conf
```shell
cd $PGDATA
cp postgresql.conf postgresql.conf.`date +%Y%m%d`
```

### 修改postgresql.conf
```shell
wal_level = archive
archive_mode =on
archive_command ='test ! -f /pgdata/backup/archived_log/%f && cp -f %p /pgdata/backup/archived_log/%f'
archive_timeout = 160
```
 
### 重启数据库
```shell
pg_ctl stop
pg_ctl start
pg_ctl status
```

### 初始化pg_rman
```shell
pg_rman init -B /pgdata/backup/pg_rman -D /data/pgsqldata -A /pgdata/backup/archived_log -S /data/pgsqldata/pg_log
```

## rman备份
### 全量备份
```shell
pg_rman backup --backup-mode=full --with-serverlog -B /pgdata/backup/pg_rman -D /data/pgsqldata -U postgres -d postgres
```

 
### 打印备份后的信息
```shell
pg_rman validate -B /pgdata/backup/pg_rman
pg_rman show -B /pgdata/backup/pg_rman 
pg_rman show timeline -B /pgdata/backup/pg_rman
pg_rman show -B /pgdata/backup/pg_rman
pg_rman show timeline -B /pgdata/backup/pg_rman 
```

### 增量备份
```shell
pg_rman backup --backup-mode=incremental --with-serverlog -B /pgdata/backup/pg_rman -D /data/pgsqldata -U postgres -d postgres
```
### 打印备份后的信息
```shell
pg_rman validate -B /pgdata/backup/pg_rman
pg_rman show -B /pgdata/backup/pg_rman 
pg_rman show timeline -B /pgdata/backup/pg_rman
pg_rman show -B /pgdata/backup/pg_rman
pg_rman show timeline -B /pgdata/backup/pg_rman 
```
 
## 恢复
### 获取备份的xid
```shell
pg_rman show  -B /pgdata/backup/pg_rman

pg_rman show '2016-04-18 19:01:23' -B /pgdata/backup/pg_rman
```
 
### xid恢复 (数据库需要处于关闭状态)
```shell
pg_rman restore  -B /pgdata/backup/pg_rman -D /data/pgsqldata -A /pgdata/backup/archived_log -S /data/pgsqldata/pg_log --recovery-target-xid 1871
## 1871 是 RECOVERY_XID
```
 
 

