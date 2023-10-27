---
title: Zookeeper学习笔记
date: 2023-10-26 20:10:14
tags:
  - Zookeeper
category:
  - 大数据
---

## 软件简介

- Apache ZooKeeper is an open-source server for highly reliable distributed coordination of cloud applications.[2] It is a project of the Apache Software Foundation.
- ZooKeeper is essentially a service for distributed systems offering a hierarchical key-value store, which is used to provide a distributed configuration service, synchronization service, and naming registry for large distributed systems (see Use cases).[3] ZooKeeper was a sub-project of Hadoop but is now a top-level Apache project in its own right.
- 如果你想了解更多信息，可以访问软件的官网:
  - https://zookeeper.apache.org/doc/r3.9.1/index.html

## 软件安装

### 安装前准备

- 在软件安装前，要先下载软件，可以到官网的下载页面下载软件，我们使用的软件版本是 3.6.4，请注意，选择这个版本仅是学习使用，如果要在线上环境使用，请选择非 EoL 版本。
  - https://zookeeper.apache.org/releases.html

```sh
# 软件下载
wget https://archive.apache.org/dist/zookeeper/zookeeper-3.6.4/apache-zookeeper-3.6.4-bin.tar.gz
```

- 在本软件安装教程中前置条件
  - 虚拟机软件 VMWare16 pro
  - 操作系统：CentOS 7
  - JDK 的安装可以参照之前的文章[在 Linux 系统上安装 Java 环境][1]一文

以上准备好之后，就可以进行软件安装了

### 软件安装

- 创建软件安装目录

```sh
mkdir -p /opt/zk
```

- 将下载好的软件拷贝到这个目录中

```sh
cp apache-zookeeper-3.6.4-bin.tar.gz /opt/zk
```

- 解压软件

```sh
tar xvf apache-zookeeper-3.6.4-bin.tar.gz
```

- 进入软件路径修改软件配置

```sh
cd apache-zookeeper-3.6.4-bin/conf
cp zoo_sample.cfg zoo.cfg
```

- 在配置文件中配置`ZK_HOME`

```sh
# 编辑 /etc/profile 文件
vim /etc/profile
# 添加如下代码
#ZK
export ZK_HOME=/opt/zk/apache-zookeeper-3.6.4-bin
export PATH=$ZK_HOME/bin:$PATH
# 重新生效
source|. /etc/profile
```

- 启动 ZK

```sh
zkServer.sh start|stop|restart|status
# 启动软件的话，直接使用下面的命令
zkServer.sh start
```

以上就可以在 CentOS7 上启动 ZK 了。

## ZK 集群环境搭建

对于集群环境，需要做的前置准备就是要准备三台虚拟机，现阶段给其做的规划如下表

| IP 地址       | ZK  |
| ------------- | --- |
| 192.168.32.10 | ZK1 |
| 192.168.32.11 | ZK2 |
| 192.168.32.12 | ZK3 |

11，12 这两台机器是基于 10 虚拟机的镜像版本，如果这样的话，这三台虚拟机上都有 ZK 软件了，接下来的操作就是搭建集群环境了

- 修改 10 机器上 ZK 的配置文件

```sh
cd /opt/zk/apache-zookeeper-3.6.4-bin
# 创建data文件夹
mkdir data

# 在进入存放配置的文件夹
cd conf
# 编辑配置文件
vim zoo.cfg
# 修改 `dataDir` 属性为下面的值
dataDir=/opt/zk/apache-zookeeper-3.6.4-bin/data
# 以下三行在配置文件末尾加
server.1=192.168.32.10:2888:3888
server.2=192.168.32.11:2888:3888
server.3=192.168.32.12:2888:3888
```

- 将配制好的内容分发到其他两台计算机，同时创建 data 数据目录，再在这个目录下创建一个 myid 文件

```sh
# 在这个目录，创建myid文件
/opt/zk/apache-zookeeper-3.6.4-bin/data
# 对于10那台机器:
echo 1 > myid
# 对于11那台机器
echo 2 > myid
# 对于12那台机器
echo 3 > myid

# 将11，12两台机器上的zk全删除
rm -rf /opt/zk/apache-zookeeper-3.6.4-bin
# 将10上的zk分发到11，12服务器上，如果配置免密的话，执行这句话的时候不需要输入用户名密码
scp -r /opt/zk/apache-zookeeper-3.6.4-bin/ root@192.168.32.11|12:/opt/zk
# 然后修改 /opt/zk/apache-zookeeper-3.6.4-bin/data 目录中的 myid 文件，将其该成对应的 2, 3
```

- 关闭系统防火墙

```sh
#开启命令：
systemctl start firewalld
#临时关闭命令：
systemctl stop firewalld
#永久关闭命令：
systemctl disable|enable firewalld
```

- 或者，开启指定的端口

```sh
sudo firewall-cmd --zone=public --add-port=3000/tcp --permanent
sudo firewall-cmd --reload

sudo firewall-cmd --remove-port=3000/tcp --permanent
```

- 启动服务，分别在三台机器上执行下面命令

```sh
zkServer.sh start
```

- 验证 ZK 是否启动成功以及服务具体角色，结果是一台机器上的 ZK 角色是 leader，其他两台角色是 follower

```sh
zkServer.sh status
```

## ZK 的使用场景

- [分布式锁][2]
- 服务注册发现，跟 [Dubbo][3] 结合使用

[1]: http://markshen1992.top/2023/10/23/Java%E5%9C%A8Linux%E4%B8%8A%E5%AE%89%E8%A3%85/
[2]: https://www.bilibili.com/video/BV1mw411d7Cc/?spm_id_from=333.337.search-card.all.click
[3]: https://cn.dubbo.apache.org/zh-cn/
