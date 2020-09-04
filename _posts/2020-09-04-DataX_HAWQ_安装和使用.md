---
layout: post
title: DataX_HAWQ_安装&&使用
categories: HAWQ
description: DataX_HAWQ_安装&&使用
keywords: HAWQ,apache hawq
---


# 参考
[DataX_HashData](https://github.com/HashDataInc/DataX/blob/v1.0.4/userGuid.md)


# OS 环境
```shell
CentOS Linux release 7.4.1708 (Core)
```
# HAWQ版本
```shell
PostgreSQL 8.2.15 (Greenplum Database 4.2.0 build 1) 
(HAWQ 2.4.0.0 build dev) on x86_64-unknown-linux-gnu, 
compiled by GCC gcc (GCC) 4.8.5 20150623 (Red Hat 4.8.5-39) 
compiled on Mar 28 2020 17:33:50
```

# 列出GpdbWriter针对PostgreSQL类型转换列表
| DataX 内部类型| PostgreSQL 数据类型    |
|-|-|
| Long     |bigint, bigserial, integer, smallint, serial |
| Double   |double precision, money, numeric, real |
| String   |varchar, char, text, bit|
| Date     |date, time, timestamp |
| Boolean  |bool|
| Bytes    |bytea|

# 安装dataX
```shell
mkdir -p /home/gpadmin/dataX/
cd /home/gpadmin/dataX/
wget https://github.com/HashDataInc/DataX/archive/v1.0.4.zip

unzip v1.0.4.zip
cd /home/gpadmin/dataX/DataX-1.0.4

# 源码编译
mvn -U clean package assembly:assembly -Dmaven.test.skip=true

cd /home/gpadmin/dataX/DataX-1.0.4/target/
mv datax-v1.0.4-hashdata /opt/
```
# DB中表准备
```shell
create table s(id int,name text);INSERT INTO S select generate_series(1,1000),'您好qwer!@#$%^&*()';
create table d(id int,name text);

## 如果是HAWQ请在DB中执行下列函数，如果是greenplum或hashdata请跳过该步骤
CREATE FUNCTION gp_truncate_error_log(name text,out res int) AS $$ SELECT 0 $$ LANGUAGE SQL;
```

# 找到datax
```shell
cd /opt/datax-v1.0.4-hashdata/datax/bin/
```

# 生成postgresql to hawq 的导数模版
## 对应参数说明文档
[postgresqlreader](https://github.com/alibaba/DataX/blob/master/postgresqlreader/doc/postgresqlreader.md)
[gpdbwriter](https://github.com/HashDataInc/DataX/blob/master/gpdbwriter/doc/gpdbwriter.md)
```python

python datax.py -r postgresqlreader -w gpdbwriter >pg2hawq.json.tamplate
```

# 制作导致json文件
```json
{
    "job": {
        "content": [
            {
                "reader": {
                    "name": "postgresqlreader",
                    "parameter": {
                        "column": [ "id","name" ],
                        "connection": [
                            {
                                "jdbcUrl": [ "jdbc:postgresql://127.0.0.1:5432/test" ],
                                "table":  [ "public.s" ]
                            }
                        ],
                        "password": "gpadmin",
                        "username": "gpadmin"
                    }
                },
                "writer": {
                    "name": "gpdbwriter",
                    "parameter": {
                        "column": [ "id","name" ],
                        "connection": [
                            {
                                "jdbcUrl": "jdbc:postgresql://127.0.0.1:5432/test",
                                "table": [ "public.d" ]
                            }
                        ],
                        "preSql": [ "select 'beginTime: '||now();truncate table d" ],
                        "postSql": [ "select 'endTime: '||now() " ],
                        "segment_reject_limit": 0,
                        "username": "gpadmin",
                        "password": "gpadmin"
                    }
                }
            }
        ],
        "setting": {
            "speed": {
                "channel": "1"
            }
        }
    }
}
```


# 执行job
```python
python datax.py pg2hawq.json
```






# 列出GpdbWriter针对PostgreSQL类型转换列表
| DataX 内部类型| PostgreSQL 数据类型    |
|-|-|
| Long     |bigint, bigserial, integer, smallint, serial |
| Double   |double precision, money, numeric, real |
| String   |varchar, char, text, bit|
| Date     |date, time, timestamp |
| Boolean  |bool|
| Bytes    |bytea|

# 
