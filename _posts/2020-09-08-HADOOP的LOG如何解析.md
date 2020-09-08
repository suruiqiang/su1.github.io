---
layout: post
title: HADOOP的LOG如何解析
categories: HADOOP
description: HADOOP的LOG如何解析
keywords: HADOOP
topmost: true
---

# HDFS
## Zookeeper
### 获取LOG位置
```
ps -ef | grep -i zookeeper | awk -F '-Dzookeeper.log.dir=|-Dzookeeper.log.file=|-Dzookeeper.root.logger=' '{print $2"/"$3}' | sed 's/[[:space:]]//g' | grep -vE 'grep|\|'
```
### LOG关键字
```
INFO
WARN

java.net.SocketException
java.net.ConnectException
java.lang.InterruptedException
java.lang.Exception
java.io.EOFException
...
```
### 如何阅读
```
## 获取除了INFO外的其他信息的方向
grep -v INFO  xxx.log | awk -F '] -' '{print $2}' | grep -v '^$' | sort -rn | uniq


## 获取大概报错信息的方向
grep -v INFO  xxx.log | grep '^java' | sort -rn | uniq


## 获取详细信息
grep -v INFO  xxx.log | less
```

## NAMENODE
### 获取LOG位置
```
ps -ef | grep -i namenode | awk -F '-Dhadoop.log.dir=|-Dhadoop.log.file=|-Dhadoop.home.dir=' '{print $2"/"$3}' | sed 's/[[:space:]]//g' | grep -vE 'grep|\|'
```
### LOG关键字
```
INFO
WARN

java.io.IOException
...
```
### 如何阅读
```
## 获取除了INFO外的其他信息的方向
grep -v INFO xxx.log | awk '{print $3" "$4}' | grep -v '^$'|  grep -v "^=" | sort -rn | uniq



## 获取大概报错信息的方向
grep -v INFO  xxx.log | grep '^java' | sort -rn | uniq


## 获取详细信息
grep -v INFO  xxx.log | less
```

## DATANODE
### 获取LOG位置
```
ps -ef | grep -i datanode | awk -F '-Dhadoop.log.dir=|-Dhadoop.log.file=|-Dhadoop.home.dir=' '{print $2"/"$3}' | sed 's/[[:space:]]//g' | grep -vE 'grep|\|'
```
### LOG关键字
```
INFO
ERROR
WARN

java.io.IOException
...
```
### 如何阅读
```
## 获取除了INFO外的其他信息的方向
grep -v INFO xxx.log | awk '{print $3" "$4}' | grep -v '^$'|  grep -v "^=" | sort -rn | uniq



## 获取大概报错信息的方向
grep -v INFO  xxx.log | grep '^java' | sort -rn | uniq


## 获取详细信息
grep -v INFO  xxx.log | less
```

## JournalNode
### 获取LOG位置
```
ps -ef | grep -i JournalNode | awk -F '-Dhadoop.log.dir=|-Dhadoop.log.file=|-Dhadoop.home.dir=' '{print $2"/"$3}' | sed 's/[[:space:]]//g' | grep -vE 'grep|\|'
```
### LOG关键字
```
INFO
ERROR
WARN

java.io.IOException
...
```
### 如何阅读
```
## 获取除了INFO外的其他信息的方向
grep -v INFO xxx.log | awk '{print $3" "$4}' | grep -v '^$'|  grep -v "^=" | sort -rn | uniq



## 获取大概报错信息的方向
grep -v INFO  xxx.log | grep '^java' | sort -rn | uniq


## 获取详细信息
grep -v INFO  xxx.log | less
```


