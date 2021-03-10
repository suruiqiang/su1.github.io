---
layout: post
title: ApacheHAWQ_SystemRequirements_01 
categories: ApacheHAWQ翻译
description: ApacheHAWQ_SystemRequirements_01
keywords: ApacheHAWQ翻译
---

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

当你使用YARN或者HAWQ管理系统资源,前提你必须配置MEM用于HAWQ。

当你根据[主机内存配置](# 主机内存配置)配置`vm.overcommit_ratio`和swap space,每台LInux系统总的可用内存可以采用下列计算公式表示:

```shell
TOTAL_MEMORY = RAM * overcommit_ratio_percentage + SWAP
```

`TOTAL_MEMORY` 包括`HAWQ_MEMORY`和`NON_HAWQ_MEMORY`, 后者是组件使用的MEM,例如:

- Operating system

- DataNode

- NodeManager

- PXF

- All other software you run on the host machine.

  

要为确定的主机配置HAWQ内存，请首先确定机器上的`NON_HAWQ_MEMORY`。

然后根据您是使用HAWQ默认资源管理器还是YARN来管理资源，

在通过设置正确的参数来配置HAWQ内存：

- 如果你使用 YARN资源管理, set `yarn.nodemanager.resource.memory-mb` = `MIN(TOTAL_MEMORY - NON_HAWQ_MEMORY , RAM)`.
- 如果你使用HAWQ默认资源管理, set `hawq_rm_memory_limit_perseg = RAM - NON_HAWQ_MEMORY`.

您可以在配置YARN时使用Ambari设置参数，也可以在使用Ambari安装HAWQ时使用Ambari设置参数。



**Example 1 - Large Host Machine**

大型主机使用内存配置的示例：

> RAM: 256GB
>
> SWAP: 4GB
>
> NON_HAWQ_MEMORY:
>
> > 2GB for Operating System
> >
> > 2GB for DataNode
> >
> > 2GB for NodeManager
> >
> > 1GB for PXF
>
> overcommit_ratio_percentage:1 (`vm.overcommit_ratio` = 100)

得出这台集群的 `TOTAL_MEMORY = 256GB * 1 + 4GB = 260GB`.

- 如果此系统使用YARN进行资源管理，则`yarn.nodemanager.resource.memory-mb` = `TOTAL_MEMORY - NON_HAWQ_MEMORY` = 260GB - 7GB = 253（因为253GB小于可用的RAM容量）。

- 如果此系统使用默认的HAWQ资源管理器，则`hawq_rm_memory_limit_perseg` = `RAM - NON_HAWQ_MEMORY` = 256 GB - 7GB = 249.



**Example 2 - Medium Host Machine**

示例中型主机使用内存配置：

> RAM: 64GB
>
> SWAP: 32GB
>
> NON_HAWQ_MEMORY:
>
> > 2GB for Operating System
> >
> > 2GB for DataNode
> >
> > 2GB for NodeManager
> >
> > 1GB for PXF
>
> overcommit_ratio_percentage: 0.5 (`vm.overcommit_ratio` = 50)

得出这台集群的  `TOTAL_MEMORY = 64GB * .5 + 32GB = 64GB`.

- 如果此系统使用YARN进行资源管理，则`yarn.nodemanager.resource.memory-mb` = `TOTAL_MEMORY - NON_HAWQ_MEMORY` = 64GB - 7GB = 57 （因为57GB小于可用的RAM容量）。

- 如果此系统使用默认的HAWQ资源管理器，则`hawq_rm_memory_limit_perseg` = `RAM - NON_HAWQ_MEMORY` = 64 GB - 11GB = 57.



**Example 3 - Small Host Machine (不建议用于生产)**

示例小型机器使用内存配置：

> RAM: 8GB
>
> SWAP: 8GB
>
> NON_HAWQ_MEMORY:
>
> > 2GB for Operating System
> >
> > 2GB for DataNode
> >
> > 2GB for NodeManager
> >
> > 1GB for PXF
>
> overcommit_ratio_percentage: 0.5 (`vm.overcommit_ratio` = 50)

得出这台集群的`TOTAL_MEMORY = 8GB * .5 + 8GB = 12GB`.

- 如果此系统使用YARN进行资源管理，则`yarn.nodemanager.resource.memory-mb` = `TOTAL_MEMORY - NON_HAWQ_MEMORY` =  12GB - 7GB = 5 （因为5GB小于可用的RAM容量）。

- 如果此系统使用默认的HAWQ资源管理器，则`hawq_rm_memory_limit_perseg` = `RAM - NON_HAWQ_MEMORY` = 8 GB - 7GB = 1.



# 无密码SSH配置

HAWQ主机将配置为在安装过程中使用无密码SSH进行集群内通信。

必须在每个HAWQ主机上启用基于密码的临时身份验证，以准备此配置。

1. 如果尚未在HAWQ系统上配置，请安装SSH服务器：

   ```shell
   $ yum list installed | grep openssh-server
   $ yum -y install openssh-server
   ```

2. 更新主机的SSH配置以允许基于密码的身份验证。编辑SSH config文件并将PasswordAuthentication配置值从no更改为yes：

   ```shell
   $ sudo vi /etc/ssh/sshd_config
   ```

   ```shell
   PasswordAuthentication yes
   ```

3. 重启ssh：

   ```shell
   $ sudo service sshd restart
   ```

*安装完成后，您可以选择关闭前面步骤中配置的基于密码的临时身份验证：*

1. 在文本编辑器中打开SSH`/etc/SSH/sshd_config`文件，并更新在上面步骤2中启用的配置选项：

   ```shell
   PasswordAuthentication no
   ```

2. 重启ssh:

   ```shell
   $ sudo service sshd restart
   ```

   

# 磁盘要求

- 每个主机至少预留2GB用于HAWQ安装
- 每个segment实例的元数据大约300MB。
- 对于HAWQ master and segment 的临时目录，建议使用多个大磁盘（2TB或更大）。<br />对于给定的查询，HAWQ将为每个virtual segment使用单独的temp目录（如果可用）来存储溢出文件。<br />多个HAWQ会话还将在可用的情况下使用单独的temp目录，以避免磁盘争用。<br />如果配置的临时目录太少，或者在同一磁盘上放置多个临时目录，则当多个virtual segment以同一磁盘为目标时，会增加磁盘争用或磁盘空间不足的风险。<br />每个HAWQ段节点可以有6个virtual segment。
- 适当的数据可用空间：磁盘应至少有30%的可用空间（容量不超过70%）。
- 高速本地存储



# 网络要求

- 阵列中的千兆以太网。对于生产集群，建议使用万兆以太网。
- 专用非阻塞开关。
- 具有多个NIC的系统需要NIC绑定以利用所有可用的网络带宽。
- HAWQ master和segments 之间的通信需要在群集网络中配置反向DNS查找。



# 端口要求

在添加HAWQ和PXF服务之后,安装的各个PXF插件需要在主机上安装Tomcat。<br />Tomcat保留端口8005、8080和8009。<br />
如果您在将运行PXF插件的主机上配置了Oozie JXM ，请确保报告服务使用的端口不是8005。<br />这有助于防止在启动PXF服务时发生端口冲突错误。



# Umask要求

在所有群集主机上将操作系统文件系统的umask设置为022。这样可以确保用户可以读取HDFS块文件。



