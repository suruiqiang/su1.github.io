---
layout: post
title: PostgreSQL之正则表达式
categories: PostgreSQL
description: PostgreSQL之正则表达式 
keywords: PostgreSQL
---
# 参考
[functions-matching](https://www.postgresql.org/docs/9.3/functions-matching.html)

# 实例
## 1.匹配以q开头的数据
```sql
Select * from test where a ~ '^q';
q,qwer,qasd
```
## 2.匹配任何单个字符 (.表示任意一个字符)
```sql
Select * from test where a ~ 'q.a';
```
 
## 3.匹配任意个字符(*表示零个或多个字符)
```sql
Select * from test where a ~ 'q*a';
qwa,qwwa,aqa,aqwa,aqwwwwaxx
```
 
## 4.匹配包含指定字符串的的文本
```sql
Select * from test where a ~ 'qa'
Aqa,aqas,aaqas,aaqaws
```
 
## 5.匹配包含字符集合中的任何一个字符
```sql
Select * from test where a ~ 'q[0-9]a';
q1a,q2a
```
 
## 6.匹配不在括号中
```sql
Select * from test where a ~ '[^0-9]'
asdf
```
 
## 7.匹配至少出现n次的字符串
```sql
Select * from test where a ~ 'a{3,}'
aaa,aaaa,aaaaaa
```
 
## 8.匹配至少出现n次，至多出现m次,如果n为0,表示为可选参数
```sql
Select * from test where a ~ 'a{1,3}';
a,aa,aaa
```
