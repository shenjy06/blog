---
title: Linux常用命令
date: 2022-12-25 09:04:24
tags:
  - Linux
  - network command

category:
  - 运维
---

## Linux 命令学习方法

Linux 命令都可以通过 Linux 提供的手册自己学习，一般来讲有三种方式查看命令的用法，如下：

就拿最常用的 `ls` 命令来举例子吧

第一种：

```sh
ls --help
```

第二种：

```sh
info ls
```

第三种：

```sh
man ls
```

大家可以使用 `命令 --help`, `info + 命令`, `man + 命令` 三种方式来学习新的命令，大家也可以使用 [The Linux man-pages project](https://www.kernel.org/doc/man-pages/) 来查找对应的命令。说到这里，让我们来学习下 Linux 的常用网路命令吧

同时，大家还是可以学习下 [The Missing Semester of Your CS Education](https://missing.csail.mit.edu/) 这门课程，这门课程的讲解非常好，大家可以跟着学习下。中文版本是 [计算机教育中缺失的一课](https://missing-semester-cn.github.io/)，B 站上的视频是 [计算机教育中缺失的一课](https://www.bilibili.com/video/BV1w7411477L/)，大家可以跟着学习下。

### Linux 常用网络命令

今天主要跟大家分享下 Linux 系统常用网络的命令:

- ifconfig
- ifup
- ifdown
- ip
- route
- ip route
- ping
- traceroute
- netstat
- nslookup

#### ifconfig

查看所有网卡信息

```sh
shenjy@DESKTOP-MCQT724:~$ ifconfig -a
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.3.2  netmask 255.255.255.0  broadcast 192.168.3.255
        ether b4:b6:86:fc:12:07  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.56.1  netmask 255.255.255.0  broadcast 192.168.56.255
        inet6 fe80::6715:52aa:2c80:112f  prefixlen 64  scopeid 0xfd<compat,link,site,host>
        ether 0a:00:27:00:00:03  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

eth2: flags=64<RUNNING>  mtu 1500
        inet 169.254.202.32  netmask 255.255.0.0
        inet6 fe80::971b:12ac:b88c:961d  prefixlen 64  scopeid 0xfd<compat,link,site,host>
        ether 7c:76:35:43:62:9f  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

eth3: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.22.96.1  netmask 255.255.240.0  broadcast 172.22.111.255
        inet6 fe80::584c:ccd0:e077:634c  prefixlen 64  scopeid 0xfd<compat,link,site,host>
        ether 00:15:5d:41:e6:ee  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 1500
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0xfe<compat,link,site,host>
        loop  (Local Loopback)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

wifi0: flags=64<RUNNING>  mtu 1500
        inet 169.254.169.148  netmask 255.255.0.0
        ether 7c:76:35:43:62:9b  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

wifi1: flags=64<RUNNING>  mtu 1500
        inet 169.254.112.16  netmask 255.255.0.0
        ether 7c:76:35:43:62:9c  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

wifi2: flags=64<RUNNING>  mtu 1500
        inet 169.254.241.4  netmask 255.255.0.0
        ether 7e:76:35:43:62:9b  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

其余，参数大家可以通过手册自行学习，这里要大家注意下 [MTU](https://www.cloudflare.com/learning/network-layer/what-is-mtu/) 这个关键参数，大家可以根据着篇 blog 进行学习。

大家也可以参照[这篇 Blog](https://www.geeksforgeeks.org/ifconfig-command-in-linux-with-examples/) 学习这个命令。

#### ifup

激活网络接口`eth0`

```sh
shenjy@DESKTOP-MCQT724:~$ ifup eth0
```

大家也可以参照[这篇 Blog](https://www.geeksforgeeks.org/ifup-command-in-linux-with-examples/) 学习这个命令。

#### ifdown

禁用网络端口`eth0`

```sh
shenjy@DESKTOP-MCQT724:~$ ifdown eth0
```

大家也可以参照[这篇 Blog](https://www.geeksforgeeks.org/ifdown-command-in-linux-with-examples/) 学习这个命令。

#### ip

查看机器所有网卡的 ip

```sh
shenjy@DESKTOP-MCQT724:~$ ip a
```

大家也可以参照[这篇 Blog](https://www.geeksforgeeks.org/ip-command-in-linux-with-examples/) 学习这个命令。

#### route

查看 Linux 内核路由表

```sh
shenjy@DESKTOP-MCQT724:~$ route
```

大家也可以参照[这篇 Blog](https://www.geeksforgeeks.org/route-command-in-linux-with-examples/) 学习这个命令。

#### ip route

查看默认路由表（main）路由

```sh
shenjy@DESKTOP-MCQT724:~$ ip route
```

大家也可以参照[这篇 Blog](https://blog.csdn.net/zhongmushu/article/details/108220232) 学习这个命令。

#### ping

查看网络链路是否通

```sh
shenjy@DESKTOP-MCQT724:~$ ping www.baidu.com
shenjy@DESKTOP-MCQT724:~$ ping localhost
shenjy@DESKTOP-MCQT724:~$ ping 127.0.0.1
```

大家也可以参照[这篇 Blog](https://www.geeksforgeeks.org/ping-command-in-linux-with-examples/) 学习这个命令。

#### traceroute

追踪网络数据包的路由途径，也可以顺道学习下 `tracert` 这个命令

```sh
shenjy@DESKTOP-MCQT724:~$ traceroute www.baidu.com
```

大家也可以参照[这篇 Blog](https://www.geeksforgeeks.org/traceroute-command-in-linux-with-examples/) 学习这个命令。

#### netstat

Print network connections, routing tables, interface statistics, masquerade connections, and multicast member‐
ships

```sh
shenjy@DESKTOP-MCQT724:~$ netstat -i
Kernel Interface table
Iface      MTU    RX-OK RX-ERR RX-DRP RX-OVR    TX-OK TX-ERR TX-DRP TX-OVR Flg
eth0      1500        0      0      0 0             0      0      0      0 BMRU
eth1      1500        0      0      0 0             0      0      0      0 BMRU
eth3      1500        0      0      0 0             0      0      0      0 BMRU
lo        1500        0      0      0 0             0      0      0      0 LRU
```

大家也可以参照[这篇 Blog](https://www.geeksforgeeks.org/netstat-command-linux/) 学习这个命令。

#### nslookup

Nslookup is a program to query Internet domain name servers. Nslookup has two modes: interactive and non-interactive.
Interactive mode allows the user to query name servers for information about various hosts and domains or to print a list
of hosts in a domain. Non-interactive mode is used to print just the name and requested information for a host or domain.

```sh
shenjy@DESKTOP-MCQT724:~$ nslookup baidu.com
Server:         8.8.8.8
Address:        8.8.8.8#53

Non-authoritative answer:
Name:   baidu.com
Address: 39.156.66.10
```

大家也可以参照[这篇 Blog](https://www.geeksforgeeks.org/nslookup-command-in-linux-with-examples/) 学习这个命令。

### 防火墙相关

#### firewall-cmd

- 安装 `firewalld` 软件

```sh
apt install firewalld
```

- 查看防火墙状态

```sh
firewall-cmd --state
```

- 查看开放了哪些端口

```sh
firewall-cmd --list-ports
```

- 开放端口

```sh
firewall-cmd --zone=public --add-port=80/tcp --permanent
```

- 关闭开放端口

```sh
firewall-cmd --zone=public --remove-port=80/tcp --permanent
```

- 重启防火墙

```sh
firewall-cmd --reload
```

- 停止防火墙

```sh
systemctl stop firewalld.service
```

- 启动/关闭开机启动功能

```sh
systemctl enable/disable firewalld.service
```

#### iptables

- 查看防火墙规则

```sh
iptables -L -n -v
```

- 查看开放了哪些端口

```sh
# 查看接受规则
iptables -L INPUT -n -v

# 查看正在监听的端口
netstat -tuln
ss -tuln
```

- 开放端口

```sh
# 开放 TCP 端口 80
iptables -A INPUT -p tcp --dport 80 -j ACCEPT

# 开放 UPD 端口
iptables -A INPUT -p udp --dport 53 -j ACCEPT

# 开放端口并限定源IP
iptables -A INPUT -p tcp -s 192.168.1.0/24 --dport 22 -j ACCEPT
```

> 注意：使用 iptables 命令添加的规则默认是临时的，系统重启后会丢失。
>
> 要使规则永久生效，需要将规则保存到配置文件中（不同的 Linux 发行版有不同的保存工具和方法，例如 iptables-save > /etc/sysconfig/iptables 或使用 iptables-persistent 包）。

- 关闭开放端口

```sh
# 查看规则编号
iptables -L INPUT -n --line-numbers

# 删除指定规则
iptables -D INPUT <number>
```

- 重启防火墙

```sh
systemctl restart iptables
# 或者
service iptables restart
```

- 停止防火墙

```sh
systemctl stop iptables
# 或者
service iptables stop
```

- 启动/关闭开机启动功能

```sh
systemctl enable/disable iptables
# 或者
chkconfig iptables on/off
# 或者
update-rc.d iptables enable/disable
```
