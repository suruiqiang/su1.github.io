---
layout: post
title: ApacheHAWQ_Running_a_HAWQ_Cluster_04
categories: ApacheHAWQ翻译
description: ApacheHAWQ_Running_a_HAWQ_Cluster_04
keywords: ApacheHAWQ翻译
---
-------

# Overview



> In this topic:
>
> i. [HAWQ Users](http://hawq.apache.org/docs/userguide/2.3.0.0-incubating/admin/RunningHAWQ.html#hawq_users)
>
> ii.[HAWQ Deployment Systems](http://hawq.apache.org/docs/userguide/2.3.0.0-incubating/admin/RunningHAWQ.html#hawq_systems)
>
> iii.[HAWQ Databases](http://hawq.apache.org/docs/userguide/2.3.0.0-incubating/admin/RunningHAWQ.html#hawq_env_databases)
>
> iv.[HAWQ Data](http://hawq.apache.org/docs/userguide/2.3.0.0-incubating/admin/RunningHAWQ.html#hawq_env_data)
>
> v.[HAWQ Operating Environment](http://hawq.apache.org/docs/userguide/2.3.0.0-incubating/admin/RunningHAWQ.html#hawq_env_setup)



本部分为负责管理HAWQ部署的系统管理员提供信息。

您应该具有Linux / UNIX系统管理，数据库管理系统，数据库管理和结构化查询语言（SQL）的一些知识，以管理HAWQ群集。<br />因为HAWQ基于PostgreSQL，所以您还应该对PostgreSQL有一定的了解。<br />HAWQ文档指出了整个HAWQ和PostgreSQL功能之间的相似之处。

## HAWQ Users

HAWQ支持具有管理和操作特权的用户。<br />HAWQ管理员可以选择使用Ambari或命令行来管理HAWQ群集。<br /> [使用Ambari管理HAWQ](http://hawq.apache.org/docs/userguide/2.3.0.0-incubating/admin/ambari-admin.html) 提供了特定于Ambari的HAWQ群集管理过程。<br /> [启动和停止HAWQ](http://hawq.apache.org/docs/userguide/2.3.0.0-incubating/admin/startstop.html), [扩展群集](http://hawq.apache.org/docs/userguide/2.3.0.0-incubating/admin/ClusterExpansion.html), 以及 [删除节点](http://hawq.apache.org/docs/userguide/2.3.0.0-incubating/admin/ClusterShrink.html)描述了特定的命令行管理的HAWQ群集管理过程。<br />本指南中的其他主题适用于Ambari和命令行管理的HAWQ群集。

默认的HAWQ管理员用户名为gpadmin。 <br />HAWQ管理员可以选择将管理或操作HAWQ特权分配给其他用户。<br />有关HAWQ用户配置的其他信息，请参考[配置客户端身份验证](http://hawq.apache.org/docs/userguide/2.3.0.0-incubating/clientaccess/client_auth.html)以及[管理角色和特权](http://hawq.apache.org/docs/userguide/2.3.0.0-incubating/clientaccess/roles_privs.html)。



## HAWQ Deployment Systems

典型HAWQ部署包括一个HDFS和HAWQ Master和Standby节点和多个HAWQ segment和HDFS数据节点。 <br/>HAWQ集群还可以包括运行HAWQ扩展框架(PXF)和其他Hadoop服务的系统。<br />请参阅[HAWQ架构](http://hawq.apache.org/docs/userguide/2.3.0.0-incubating/overview/HAWQArchitecture.html)和[选择HAWQ主机](http://hawq.apache.org/docs/userguide/2.3.0.0-incubating/install/select-hosts.html)解答HAWQ部署中不同系统以及如何配置的信息。。



## HAWQ Databases

[创建和管理数据库](http://hawq.apache.org/docs/userguide/2.3.0.0-incubating/ddl/ddl-database.html)以及[创建和管理表](http://hawq.apache.org/docs/userguide/2.3.0.0-incubating/ddl/ddl-table.html)描述了HAWQ数据库和表创建命令。<br />

您可以使用[psql](http://hawq.apache.org/docs/userguide/2.3.0.0-incubating/reference/cli/client_utilities/psql.html)实用程序（HAWQ数据库的交互式前端）在命令行上管理HAWQ数据库。<br />配置客户端对HAWQ数据库和表的访问可能需要与[建立数据库会话](http://hawq.apache.org/docs/userguide/2.3.0.0-incubating/clientaccess/g-establishing-a-database-session.html)有关的信息。<br />

[HAWQ数据库驱动程序和API](http://hawq.apache.org/docs/userguide/2.3.0.0-incubating/clientaccess/g-database-application-interfaces.html)会为其他客户端访问方法标识受支持的HAWQ数据库驱动程序和API。



## HAWQ Data

HAWQ内部数据驻留在HDFS中。<br />您可能需要访问数据湖中不同格式和位置的数据。<br />您可以使用HAWQ和HAWQ扩展框架（PXF）来访问和管理内部数据以及此外部数据：<br />

- 使用[HAWQ管理数据](http://hawq.apache.org/docs/userguide/2.3.0.0-incubating/datamgmt/dml.html)讨论了基本数据操作以及有关HAWQ内部表的加载和卸载语义的详细信息。

- 将[PXF与非托管数据](http://hawq.apache.org/docs/userguide/2.3.0.0-incubating/pxf/HawqExtensionFrameworkPXF.html)描述PXF，它是可用于查询HAWQ外部数据的可扩展框架。



## HAWQ Operating Environment

有关[HAWQ操作环境](http://hawq.apache.org/docs/userguide/2.3.0.0-incubating/admin/setuphawqopenv.html)的讨论，请参见介绍HAWQ操作环境，包括设置HAWQ环境的过程。<br />本节还介绍了HAWQ安装中的重要文件和目录。



-----

# Introducing the HAWQ Operating Environment

> In this topic:
>
> i. [Procedure: Setting Up Your HAWQ Operating Environment](http://hawq.apache.org/docs/userguide/2.3.0.0-incubating/admin/setuphawqopenv.html#hawq_setupenv)
>
> ii.[HAWQ Files and Directories](http://hawq.apache.org/docs/userguide/2.3.0.0-incubating/admin/setuphawqopenv.html#hawq_env_files_and_dirs)



## Procedure: Setting Up Your HAWQ Operating Environment



## HAWQ Files and Directories



----------------------------------------------------- 未完待续  ---------------------------------------------------- 

http://hawq.apache.org/docs/userguide/2.3.0.0-incubating/admin/RunningHAWQ.html

