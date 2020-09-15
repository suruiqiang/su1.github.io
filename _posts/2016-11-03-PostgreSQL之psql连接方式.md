---
layout: post
title: PostgreSQL之psql连接方式 
categories: PostgreSQL
description: PostgreSQL之psql连接方式 
keywords: PostgreSQL
---
# 参考
[psql-connect](https://www.jianshu.com/p/f246dc45e6dc)

# 实例
## 连接方式1
```shell
psql postgres://DB_USER:DB_PASS@IP:PORT/DB

```

## 连接方式2
```shell
psql -U DB_USER -d DB  -h IP -p PORT 
```
