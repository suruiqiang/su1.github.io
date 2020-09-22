---
layout: post
title: Oracle之SqlLoader数据加载工具 
categories: Oracle
description: Oracle之SqlLoader数据加载工具
keywords: Oracle
---

# 控制文件的编写
```shell
LOAD DATA
INFILE 带路径的bcp文件
append into INTO TABLE 表名
FIELDS TERMINATED BY “\t”  TRAILING NULLCOLS
(字段1,…,字段n)
```
 
## 例子
```shell
load data
infile './2.csv'
append into table TBL_TEST
fields terminated by '\t' TRAILING NULLCOLS
(ID,NAME,AGE)
```
 
# SQL文件编写
在里面写上sql语句,导出的数据会根据这个sql语句来导出
 
## sqlldr入库
```shell
sqlldr 用户名/密码@IP:PORT/SID control=控制文件 bad=失败文件记录文件.csv 
```

### 例子
```shell
sqlldr test/test@10.0.0.1:1521/ora11g control=TBL_TEST.ctl 
```
 
## 参数说明
```
userid                             -- ORACLE 用户名/密码
sql                                -- 记录sql语句的文件名 
query                              -- select语句
control                            -- 控制文件名 
log                                -- 日志文件名                       
bad                                -- 错误文件名                       
data                               -- 数据文件名 
discard                            -- 废弃文件名
discardmax                         -- 允许废弃的文件的数目 (全部默认)               
skip                               -- 要允许跳过的物理文件行数  (Default 0)
load                               -- 要加载的物理文件的行数    (Default all)
errors                             -- 可以被允许的错误行数      (Default 50)
rows                               -- 常规路径绑定数组中或直接路径保存数据间的行数(常规路径默认64,直接路径默认全部)
Bindsize                           -- 常规路径绑定数据的大小
slient                             -- 禁止输出信息
direct                             -- 使用直接路径(默认 FALSE)
parfile                            -- 参数文件:包含参数说明的文件的名称
parallel                           -- 执行并行加载(默认 FALSE)
file                               -- 要从以下对象中分配区的文件
skip__unusable_index               -- 不允许/允许使用无用的索引(默认 FALSE)
skip_index_maintenance             -- 不维护索引,将受到影响的索引标记为失效(默认 FALSE)
commit_discontinued                -- 提交加载中断时已加载的行(默认 FALSE)
readsize                           -- 读取缓冲区的大小(默认 1048579)
external_table                     -- 使用外部表进行加载;NOT_USED,GENERATE_ONLY,RXECUTE(默认NOT_USED )
colimnarrayrows                    -- 直接路径列数组的行数(默认 5000)
streamsize                         -- 直接路径流缓冲区的大小(默认 5000)
multithreading                     -- 在直接路径中使用多线程
resumable                          -- 启用或禁用当前的可恢复会话(默认 FALSE)
resumable_name                     -- 有助于标识可恢复语句的文本字符串
resumable_timeout                  -- RESUMABLE的等待时间(以秒计) (默认 7200)
date_cache                         -- 日期转换高速缓存的大小
charset                            -- 设置目标数据库的charset
ncharset                           -- 设置目标数据库的ncharset
text                               -- 输出文件的类型
head                               -- yes表示要表头 no表示不要表头
field                              -- 一条记录中字段与字段的分隔符
record                             -- 每条记录结尾的标记符
```

## 备注
### 分隔符的表示方法
```shell
\r=0x0d
\n=0x0a
| =0x07
, =0x2c
\t=0x09
: =0x3a
# =0x23
" =0x22
' =0x27
```
