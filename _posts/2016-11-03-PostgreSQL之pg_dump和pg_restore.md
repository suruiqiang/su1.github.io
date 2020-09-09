---
layout: post
title: PostgreSQL之pg_dump和pg_restore
categories: PostgreSQL
description: PostgreSQL之pg_dump和pg_restore 
keywords: PostgreSQL
---

# 帮助文档
```shell
pg_dump --help

pg_restore --help
```

# tar格式的dmp包的制作
## 备份
```shell
pg_dump  -Ft -t 表 -U 用户 -d 库 -f 文件.pgdmp
```
## 恢复
```shell
pg_restore -U 用户 -d 库 -O --format=t  文件.pgdmp 
pg_restore -U 用户 -d 库 -O -Ft  文件.pgdmp 
```
 
# 自定义格式压缩dmp的制作
## 备份
```shell
pg_dump -Fc -Z 3 -t 表 -U 用户 -d 库 -f 文件.pgdmp
```
## 恢复
```shell
pg_restore -U 用户 -d 库 -O -Fc  文件.pgdmp 
```

 

