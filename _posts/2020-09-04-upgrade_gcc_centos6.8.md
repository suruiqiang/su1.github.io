---
layout: post
title: upgrade_gcc_centos6.8
categories: Linux
description: upgrade_gcc_centos6.8
keywords: gcc,Linux
---

# 参考
[源码安装GCC-4.9.2](https://www.cnblogs.com/succeed/p/6204438.html)

# 查看原始gcc版本
```shell
># gcc --version
gcc (GCC) 4.4.7 20120313 (Red Hat 4.4.7-18)
```

# 下载对应的版本的gcc包
```shell
># wget  http://ftp.gnu.org/gnu/gcc/gcc-4.9.2/gcc-4.9.2.tar.gz
```

# 解压
```shell
># tar zxvf gcc-4.9.2.tar.gz
```

# 下载编译所需的依赖包
```shell
># cd gcc-4.9.2
># ./contrib/download_prerequisites 
```

# 确认原始gcc的安装路径
```shell
># yum install -y  gcc-c++  glibc-static gcc       ##为避免出错建议安装此包
># type gcc
gcc is /usr/bin/gcc
```

# 编译安装gcc
```shell
>#  ./configure --prefix=/home/demo/gcc  --enable-bootstrap  --enable-checking=release --enable-languages=c,c++ --disable-multilib
># make
># make install 
```

# 配置环境变量
```shell
># su - demo
>$ vi ~/.bashrc
export PATH=/home/demo/gcc/bin:$PATH
```

# 查看gcc版本
```shell
>$ gcc (GCC) 4.9.2
Copyright © 2014 Free Software Foundation, Inc.
本程序是自由软件；请参看源代码的版权声明。本软件没有任何担保；
包括没有适销性和某一专用目的下的适用性担保。
```

