---
layout: post
title: PostgrSQL之启动与关闭
categories: PostgrSQL
description: PostgrSQL之启动与关闭
keywords: PostgrSQL
---


# pg的启动
## a.切换用户
```
su – postgres
```
## b.启动psotgresql服务
```
pg_ctl start
```

# pg 的关闭
## a.切换用户
```
su – postgres
```
## b.关闭postgresql服务
```
pg_ctl stop (或pg_ctl stop -m immediate)
```
## 选项说明:
```
-m 
smart      等待所有客户端中断连接   
fast       不等待所有客户端中断连接，所有事务都被回卷并且客户端强制断开
immediate  在没有干净关闭的情况下退出,这么做将导致在重新启动的时候恢复
```



