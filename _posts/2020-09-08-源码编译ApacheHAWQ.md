---
layout: post
title: 源码编译ApacheHAWQ
categories: HAWQ
description: 源码编译ApacheHAWQ
keywords: HAWQ
---

# 参考
https://cwiki.apache.org/confluence/display/HAWQ/Build+and+Install



# 源码编译hawq
## 下载
```
# https://github.com/apache/hawq
```

## 添加用户gpadmin

```shell
># groupadd -g 3030 gpadmin
># useradd -u 3030 -g gpadmin -m -s /bin/bash gpadmin 
># echo gpadmin | passwd gpadmin --stdin
```

## Install dependencies on Rad Hat/CentOS 7.X

## Dependencies

```
wget https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
# For CentOs 7 the link is https://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-9.noarch.rpm
rpm -ivh epel-release-latest-7.noarch.rpm
yum makecache
# On redhat7, make sure enabled rhel-7-server-extras-rpms and rhel-7-server-optional-rpms channel in /etc/yum.repos.d/redhat.repo
# Otherwise yum will prompt some packages(e.g. gperf) not be found
yum install -y man passwd sudo tar which git mlocate links make bzip2 net-tools \
  autoconf automake libtool m4 gcc gcc-c++ gdb bison flex gperf maven indent \
  libuuid-devel krb5-devel libgsasl-devel expat-devel libxml2-devel \
  perl-ExtUtils-Embed pam-devel python-devel libcurl-devel snappy-devel \
  thrift-devel libyaml-devel libevent-devel bzip2-devel openssl-devel \
  openldap-devel protobuf-devel readline-devel net-snmp-devel apr-devel \
  libesmtp-devel python-pip json-c-devel \
  java-1.7.0-openjdk-devel lcov cmake3 \
  openssh-clients openssh-server perl-JSON perl-Env
 
# need tomcat6 if enable-rps
# download from http://archive.apache.org/dist/tomcat/tomcat-6/v6.0.44/
 
ln -s /usr/bin/cmake3 /usr/bin/cmake
pip --retries=50 --timeout=300 install pycrypto
```


## install hawq on Rad Hat/CentOS 7.X
```

make clean 
make distclean 
./configure 
make 
make install
```


# 问题汇总
## 问题
```
unzip 找不到命令
```
## 解决方法
```
yum install unzip
```

## 问题
```
/bin/python: No module named cogapp
```
## 解决方法
```
pip install cogapp
```

## 问题
```
**/opt/script/hawq/depends/dbcommon/src/dbcommon/log/logger.h:34:26:** **致命错误：**glog/logging.h：没有那个文件或目录
```
## 解决方法
```
yum install glog glog-devel
```

## 问题
```
**/usr/include/glog/logging.h:85:27:** **致命错误：**gflags/gflags.h：没有那个文件或目录
```
## 解决方法
```
yum install gflags gflags-devel
```

## 问题
```
**/opt/script/hawq/depends/dbcommon/src/dbcommon/utils/comp/lz4-compressor.h:23:17:** **致命错误：**lz4.h：没有那个文件或目录 \#include <lz4.h>
```
## 解决方法
```
yum install lz4 lz4-devel
```

## 问题
```
# This is due to flex bug around unused variable yyg in <= 2.5.35
```
## 解决方法
```
yum install flex flex-devel
```

## 问题
```
make clean 时候pxf报错

/opt/script/hawq-9c7ca3c2ce0b825c7236eaf208abf2475d9c992f/pxf
```
## 解决方法
```
[root@hawqm1 pxf]# cat gradlew
 curl --insecure -L -o "$zip_path/$file_name" "$distributionUrl"

替换成

 wget -P $zip_path https://downloads.gradle.org/distributions/$file_name
```

## 问题
```
**gram.c:1370:41:** **错误：**‘**yyscanner**’未声明(在此函数内第一次使用)
  yychar = yylex (&yylval, &yylloc, yyscanner);
```
## 解决方法
```
yum remove bison bison-devel
yum install http://rpmfind.net/linux/remi/enterprise/4/remi/x86_64/bison-2.3-2.x86_64.rpm
yum install http://rpmfind.net/linux/remi/enterprise/4/remi/x86_64/bison-devel-2.3-2.x86_64.rpm
```
