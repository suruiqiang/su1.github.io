---
layout: post
title: ApacheHAWQ_SystemOverview_02
categories: ApacheHAWQ翻译
description: ApacheHAWQ_SystemOverview_02
keywords: ApacheHAWQ翻译
---

[HAWQ是什么?](# HAWQ是什么?)
[HAWQ 架构](# HAWQ 架构)

# HAWQ是什么?

HAWQ是一个Hadoop原生SQL查询引擎，它结合了MPP数据库的关键技术优势和Hadoop的可扩展性和方便性。<br />HAWQ从HDFS本地读取数据并将数据写入HDFS。



HAWQ提供业界领先的性能和线性可扩展性。<br />它为用户提供了简单和成功地与PB级数据集交互的工具。<br />HAWQ为用户提供了一个完整的、符合标准的SQL接口。<br />更具体地说，HAWQ具有以下特点：<br />

- 内部部署或云部署
- 健壮的ANSI SQL遵从性：SQL-92、SQL-99、SQL-2003、OLAP扩展
- 极高的性能—比其他Hadoop SQL引擎快很多倍
- 世界级并行优化器
- 完整的事务处理能力和一致性保证：ACID
- 基于高速UDP互连的动态数据流引擎
- 基于按需的virtual segments and data locality的弹性执行引擎
- 支持多级分区和基于 List/Range的分区表。
- 支持多种压缩方法：snappy、gzip
- 多语言用户自定义函数支持：Python、Perl、java、C/C++、R
- 通过MADLib实现高级机器学习和数据挖掘功能
- 动态节点扩展：秒
- 最先进的三级资源管理：与YARN和分层资源队列集成。
- 轻松访问所有HDFS数据和外部系统数据（例如，HBase）
- Hadoop原生：从存储（HDFS）、资源管理（YARN）到部署（Ambari）。
- 身份验证和细粒度授权：Kerberos、SSL和基于角色的访问
- 先进的C/C++访问库：HDBFS和YARN： libhdfs3 和libYARN
- 支持大多数第三方工具：Tableau、SAS等。
- 标准连接：JDBC/ODBC

HAWQ将复杂的查询分解为小任务，并将它们分发给MPP查询处理单元执行。



HAWQ的基本并行单元是segment实例。<br />商品服务器上的多个segment实例协同工作，形成一个单一的并行查询处理系统。<br />提交给HAWQ的查询经过优化，分解成更小的组件，并分派到segments上一起工作,每个segment返回单个结果集的各个部分。<br />所有关系操作（如table scans、joins、aggregations和sorts）都会同时跨segments并行执行。<br />动态管道中来自上游组件的数据通过User Datagram Protocol（UDP）互连传输到下游组件。



HAWQ基于Hadoop的分布式存储，无单点故障，支持全自动在线恢复。<br />系统状态被持续监视，因此如果某个segmemt出现故障，它将自动从集群中删除。<br />在此过程中，系统将继续为客户查询提供服务，必要时可以将这些segments添加回系统。

[HAWQ Architecture](# HAWQ Architecture)





--------------------------------


# HAWQ 架构

> in this topic:
>
> i.[HAWQ Master](## HAWQ Master)
>
> ii.[HAWQ Segment](## HAWQ Segment)
>
> iii.[HAWQ Interconnect](## HAWQ Interconnect)
>
> iv.[HAWQ Resource Manager](## HAWQ Resource Manager)
>
> v.[HAWQ Catalog Service](## HAWQ Catalog Service)
>
> vi.[HAWQ Fault Tolerance Service](## HAWQ Fault Tolerance Service)
>
> vii.[HAWQ Dispatcher](## HAWQ Dispatcher)
> 

## HAWQ Master



## HAWQ Segment



## HAWQ Interconnect



## HAWQ Resource Manager



## HAWQ Catalog Service



## HAWQ Fault Tolerance Service



## HAWQ Dispatcher