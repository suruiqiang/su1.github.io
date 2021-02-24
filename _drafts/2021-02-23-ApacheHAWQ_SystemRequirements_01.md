---
layout: post
title: ApacheHAWQ_SystemRequirements_01 
categories: ApacheHAWQ翻译
description: ApacheHAWQ_SystemRequirements_01.md
keywords: ApacheHAWQ翻译
---
[toc]

按照以下指导原则配置好即将在每台机器上运行Apache HAWQ或PXF服务。

# 主机内存配置
为了防止Apache HAWQ集群中的数据丢失或损坏，您必须在每台主机上配置内存，
以便Linux内存不足（OOM）killer进程不会由于OOM情况而终止HAWQ进程。
（HAWQ应用自己的规则来强制执行内存限制。）

**对于HAWQ的任务关键型部署，请在每台主机上执行以下步骤以配置内存：**
1. 设置操作系统`vm.overcommit_memory`内存参数设置为2。
通过此设置，OOM killer进程将报告错误，而不是终止正在运行的进程。要设置此参数：
  1. 打开并编辑`/etc/sysctl.conf`文件
  2. 添加或更改参数定义，使文件包含以下行：
  ```shell
  kernel.threads-max=798720
  vm.overcommit_memory=2
  ```  
  3. 保存并关闭文件，然后执行以下命令使参数生效
  ```shell
  $ sysctl -p
  ```
  4. 执行下列命令,查看当前配置`vm.overcommit_memory`:
  ```shell
  $ sysctl -a | grep overcommit_memory
  ```
  5. 执行下列命令,查看运行时的配置`vm.overcommit_memory`:
  ```shell
  $ cat /proc/meminfo | grep Commit
  ```

2.设置Linux swap 大小和`vm.overcommit_ratio`的值依据每台主机的可用内存情况来设定。
对于`2GB <= MEM < 8GB` 的主机，设置`swap space = physical RAM`        并设置`vm.overcommit_ratio=50`. 
对于`8GB <= MEM < 64GB`的主机，设置`swap space = physical RAM * 0.5`，并设置`vm.overcommit_ratio=50`
对于`64GB<= MEM` 的主机 ，     设置`swap space = 4GB`                 并设置`vm.overcommit_ratio=100`
设置`vm.overcommit_ratio`参数：
  1. 打开文件`/etc/sysctl.conf`并编辑
  2. 新增或修改参数定义在文件中
  ```shell
  vm.overcommit_ratio=50
  ```
  3.保存和关闭文件,并执行下列命令使配置更改生效:
  ```shell
  $ sysctl -p
  ```
  4. 查看当前系统中`vm.overcommit_ratio`的设置
  ```shell
  $ sysctl -a | grep overcommit_ratio
  ```
  你可以选择专有的交换分区,交换文件或者两种结合。
  查看当前swap设置的命令:
  ```shell
  $ cat /proc/meminfo | grep Swap
  ```
  5. 确保机器上运行的所有Java服务都使用-Xmx开关只用于分配它们所需的heap。
  6. 确保没有其他服务（如Puppet）或自动进程尝试重置群集主机上的`overcommit_ratio`设置。
  7. 在安装过程中，通过设置YARN或HAWQ配置参数来配置HAWQ内存，详见[HAWQ内存配置](# HAWQ内存配置)。

# HAWQ内存配置


