---
title: Linux常用命令
date: 2022-12-25 09:04:24
tags:
  - Linux
  - network command

category:
  - 运维
---

> Linux Syscall Table: https://filippo.io/linux-syscall-table/

## 性能之巅

![性能之巅](https://github.com/user-attachments/assets/87e7789c-db16-47b0-af77-24b577094e7a)

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

**View manual pages for commands, programs, and system functions.**

#### Basic Usage

```sh
man [section] <command_name>
```

#### Common Sections

- **1**: Executable programs (default)
- **2**: System calls (e.g., `man 2 open`)
- **3**: Library functions (e.g., `man 3 printf`)
- **5**: File formats (e.g., `man 5 passwd`)
- **8**: Admin commands (e.g., `man 8 ifconfig`)

#### Key Options

| Short | Long        | Description                            |
| ----- | ----------- | -------------------------------------- |
| `-f`  | `--whatis`  | Show short description (`whatis`).     |
| `-k`  | `--apropos` | Search manuals by keyword (`apropos`). |
| `-a`  | `--all`     | Show all matching sections.            |

#### Navigation in `man`

- **↑/↓**: Scroll line by line.
- **Space**: Next page.
- **/pattern**: Search forward (e.g., `/options`).
- **n/N**: Next/previous match.
- **q**: Quit.

#### Examples

1. View the manual for `ls`:
   ```sh
   man ls
   ```
2. Search for manuals about "network":
   ```sh
   man -k network
   ```
3. Show _all_ sections for `open` (command + system call):
   ```sh
   man -a open
   ```

---

> ℹ️ **Pro Tip**: Use `tldr <command>` (if installed) for simplified, practical examples!

```sh
man ls
```

大家可以使用 `命令 --help`, `info + 命令`, `man + 命令` 三种方式来学习新的命令，大家也可以使用 [The Linux man-pages project](https://www.kernel.org/doc/man-pages/) 来查找对应的命令。说到这里，让我们来学习下 Linux 的常用网路命令吧

同时，大家还是可以学习下 [The Missing Semester of Your CS Education](https://missing.csail.mit.edu/) 这门课程，这门课程的讲解非常好，大家可以跟着学习下。中文版本是 [计算机教育中缺失的一课](https://missing-semester-cn.github.io/)，B 站上的视频是 [计算机教育中缺失的一课](https://www.bilibili.com/video/BV1pFk3B3Eey/?spm_id_from=333.1387.collection.video_card.click)，大家可以跟着学习下。

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

### 查看系统信息

- 查看系统信息脚本 `sysinfo.sh`, 使用 `chmod +x sysinfo.sh` 给脚本添加可执行权限

```sh
#!/usr/bin/env bash
echo "====== Host / Distro ======"
hostnamectl
echo; echo "====== CPU ======"
lscpu | grep -E 'Model name|Socket|Core|Thread'
echo; echo "====== Memory ======"
free -h
echo; echo "====== Disk ======"
lsblk -fp
echo; echo "====== Mount ======"
df -hT | grep -E '^/dev'
echo; echo "====== Network ======"
ip -br addr
echo; echo "====== Listen Ports ======"
ss -tunlp | grep LISTEN
echo; echo "====== Failed Services ======"
systemctl list-units --failed
```

我来为您整理 Linux 常用命令，按功能分类：

### 📁 文件和目录操作

- **ls** - 列出目录内容
- **cd** - 切换目录
- **pwd** - 显示当前目录
- **mkdir** - 创建目录
- **rmdir** - 删除空目录
- **rm** - 删除文件或目录
- **cp** - 复制文件或目录
- **mv** - 移动或重命名文件
- **touch** - 创建空文件或更新时间戳
- **find** - 查找文件
- **tree** - 以树状图显示目录结构

### 📄 文件查看和编辑

- **cat** - 查看文件内容
- **less/more** - 分页查看文件
- **head** - 查看文件开头
- **tail** - 查看文件末尾
- **vi/vim** - 文本编辑器
- **nano** - 简单文本编辑器
- **grep** - 搜索文本内容
- **sed** - 流编辑器
- **awk** - 文本处理工具

### 🔐 权限管理

- **chmod** - 修改文件权限
- **chown** - 修改文件所有者
- **chgrp** - 修改文件所属组
- **umask** - 设置默认权限

### 👤 用户和组管理

- **useradd** - 添加用户
- **userdel** - 删除用户
- **passwd** - 修改密码
- **su** - 切换用户
- **sudo** - 以管理员权限执行命令
- **whoami** - 显示当前用户
- **id** - 显示用户 ID 信息

### 💻 系统信息和监控

- **top/htop** - 实时显示进程状态
- **ps** - 查看进程
- **df** - 查看磁盘使用情况
- **du** - 查看目录大小
- **free** - 查看内存使用情况
- **uname** - 显示系统信息
- **uptime** - 系统运行时间
- **dmesg** - 查看系统日志
- **lscpu** - 显示 CPU 信息
- **lsblk** - 列出块设备

### 🔄 进程管理

- **kill** - 终止进程
- **killall** - 按名称终止进程
- **pkill** - 按模式终止进程
- **bg** - 后台运行任务
- **fg** - 前台运行任务
- **jobs** - 查看后台任务
- **nohup** - 不挂断运行命令

### 🌐 网络相关

- **ping** - 测试网络连通性
- **ifconfig/ip** - 配置网络接口
- **netstat** - 网络状态
- **ss** - 查看 socket 状态
- **curl** - 传输数据
- **wget** - 下载文件
- **ssh** - 远程登录
- **scp** - 远程复制文件
- **nslookup/dig** - DNS 查询

### 📦 软件包管理

- **apt/apt-get** - Debian/Ubuntu 包管理
- **yum/dnf** - RedHat/CentOS 包管理
- **rpm** - RPM 包管理
- **dpkg** - Debian 包管理
- **snap** - Snap 包管理

### 🗜️ 压缩和解压

- **tar** - 打包/解包
- **gzip/gunzip** - gzip 压缩/解压
- **zip/unzip** - zip 压缩/解压
- **bzip2/bunzip2** - bzip2 压缩/解压

### 🔍 系统管理

- **systemctl** - 管理系统服务（systemd）
- **service** - 管理服务（传统）
- **cron/crontab** - 定时任务
- **shutdown/reboot** - 关机/重启
- **mount/umount** - 挂载/卸载文件系统

### 📝 其他常用命令

- **echo** - 输出文本
- **clear** - 清屏
- **history** - 查看命令历史
- **date** - 显示/设置日期时间
- **man** - 查看命令手册
- **which** - 查找命令位置
- **ln** - 创建链接
- **wc** - 统计字数行数
- **sort** - 排序
- **uniq** - 去重

这些是 Linux 系统中最常用的命令。建议您从基础的文件操作命令开始学习，逐步掌握其他类别的命令。使用 `man 命令名` 可以查看每个命令的详细使用说明。

## 📊 综合性能监控

### top / htop

- **top** - 实时显示系统整体状态和进程信息
  ```bash
  top              # 基础使用
  top -u username  # 查看特定用户进程
  top -p PID       # 监控特定进程
  ```
- **htop** - top 的增强版，更直观（需安装）
  - 支持鼠标操作
  - 彩色显示
  - 更友好的界面

### atop

- 全面的系统性能监控工具
- 记录历史数据，可回溯分析
- 同时监控 CPU、内存、磁盘、网络

### glances

- 跨平台监控工具
- 自动高亮显示瓶颈
- 支持 Web 界面

---

## 💻 CPU 性能分析

### mpstat

```bash
mpstat           # 显示所有CPU统计信息
mpstat -P ALL 1  # 每秒显示每个CPU核心状态
```

### vmstat

```bash
vmstat 1 10      # 每秒输出一次，共10次
# 关注：
# - r: 运行队列长度（>CPU核心数表示过载）
# - us: 用户态CPU使用率
# - sy: 内核态CPU使用率
# - id: 空闲CPU
# - wa: IO等待
```

### sar

```bash
sar -u 1 10      # CPU使用率
sar -q 1 10      # 队列长度和负载
sar -P ALL 1 10  # 每个CPU的统计
```

### uptime

```bash
uptime           # 显示系统负载（1分钟、5分钟、15分钟平均值）
```

### pidstat

```bash
pidstat 1        # 每秒显示进程CPU使用情况
pidstat -p PID 1 # 监控特定进程
```

---

## 🧠 内存性能分析

### free

```bash
free -h          # 以人类可读格式显示内存
free -m          # 以MB为单位
free -s 1        # 每秒刷新
```

### vmstat

```bash
vmstat -s        # 内存统计摘要
vmstat -a        # 显示活跃/非活跃内存
# 关注：
# - swpd: 使用的swap
# - free: 空闲内存
# - buff: 缓冲区内存
# - cache: 缓存内存
```

### sar

```bash
sar -r 1 10      # 内存使用率
sar -B 1 10      # 分页统计
sar -W 1 10      # swap统计
```

### slabtop

```bash
slabtop          # 实时显示内核slab缓存信息
```

### pmap

```bash
pmap -x PID      # 查看进程内存映射
```

### smem

```bash
smem -r -s pss   # 按实际物理内存排序（需安装）
```

---

## 💾 磁盘 I/O 性能分析

### iostat

```bash
iostat -x 1      # 每秒显示详细磁盘统计
iostat -d 1      # 只显示设备统计
# 关键指标：
# - %util: 磁盘使用率（接近100%表示饱和）
# - await: 平均等待时间
# - svctm: 平均服务时间
# - r/s, w/s: 每秒读写次数
```

### iotop

```bash
iotop            # 实时显示进程I/O使用情况（需root）
iotop -o         # 只显示有I/O活动的进程
```

### df

```bash
df -h            # 查看磁盘空间使用
df -i            # 查看inode使用情况
```

### du

```bash
du -sh *         # 显示当前目录各文件/目录大小
du -h --max-depth=1  # 只显示一级目录
```

### lsof

```bash
lsof /path       # 查看哪些进程在使用某个文件/目录
lsof -p PID      # 查看进程打开的文件
lsof | grep deleted  # 查找已删除但仍被占用的文件
```

### fio

```bash
fio --name=test --rw=randread --bs=4k --size=1G  # I/O性能测试
```

---

## 🌐 网络性能分析

### netstat

```bash
netstat -tupln   # 查看监听端口和连接
netstat -s       # 网络统计信息
netstat -i       # 网络接口统计
```

### ss

```bash
ss -tupln        # 更快的netstat替代品
ss -s            # 统计摘要
ss -o state established  # 查看已建立的连接
```

### iftop

```bash
iftop -i eth0    # 实时网络流量监控（需安装）
```

### iptraf / iptraf-ng

```bash
iptraf-ng        # 交互式网络监控工具
```

### nethogs

```bash
nethogs eth0     # 按进程显示网络带宽使用
```

### tcpdump

```bash
tcpdump -i eth0  # 抓包分析
tcpdump -i eth0 port 80  # 抓取80端口的包
```

### ping / mtr

```bash
ping example.com     # 测试连通性和延迟
mtr example.com      # 结合ping和traceroute
```

### ethtool

```bash
ethtool eth0         # 查看网卡信息和统计
ethtool -S eth0      # 网卡详细统计
```

### sar

```bash
sar -n DEV 1 10      # 网络接口统计
sar -n TCP 1 10      # TCP统计
sar -n SOCK 1 10     # socket统计
```

---

## 🔍 进程性能分析

### ps

```bash
ps aux           # 显示所有进程
ps -ef           # 另一种格式
ps -eo pid,ppid,cmd,%mem,%cpu --sort=-%cpu  # 按CPU排序
```

### pstree

```bash
pstree -p        # 显示进程树及PID
```

### pidstat

```bash
pidstat -d 1     # 磁盘I/O统计
pidstat -r 1     # 内存统计
pidstat -u 1     # CPU统计
```

### strace

```bash
strace -p PID    # 跟踪进程系统调用
strace -c -p PID # 统计系统调用
strace ./program # 跟踪程序执行
```

### ltrace

```bash
ltrace -p PID    # 跟踪库函数调用
```

### perf

```bash
perf top         # 实时性能分析
perf stat ./program  # 程序性能统计
perf record -g -p PID  # 记录性能数据
perf report      # 查看性能报告
```

---

## 🔥 火焰图和高级分析

### perf + FlameGraph

```bash
# 生成CPU火焰图
perf record -F 99 -a -g -- sleep 60
perf script | ./stackcollapse-perf.pl | ./flamegraph.pl > cpu.svg
```

### bpftrace / bcc-tools

```bash
# eBPF性能分析工具
opensnoop        # 跟踪文件打开
execsnoop        # 跟踪进程执行
biolatency       # 块I/O延迟分布
tcplife          # TCP连接生命周期
```

---

## 📈 系统日志和诊断

### dmesg

```bash
dmesg            # 查看内核日志
dmesg -T         # 显示可读时间戳
dmesg | grep -i error  # 查找错误信息
```

### journalctl

```bash
journalctl -f    # 实时查看系统日志
journalctl -u service  # 查看特定服务日志
journalctl --since "1 hour ago"  # 查看最近1小时日志
```

### sysstat 包

- sar - 系统活动报告
- iostat - I/O 统计
- mpstat - CPU 统计
- pidstat - 进程统计
- sadf - 数据格式化工具

---

## 🛠️ 专项性能工具

### **CPU 相关**

- `lscpu` - CPU 架构信息
- `turbostat` - CPU 频率和功耗
- `cpupower` - CPU 频率管理

### **内存相关**

- `numactl` - NUMA 策略
- `numastat` - NUMA 统计
- `pcstat` - 页缓存统计

### **磁盘相关**

- `hdparm` - 硬盘参数
- `smartctl` - 硬盘健康状态
- `blktrace` - 块层 I/O 跟踪

### **网络相关**

- `nload` - 网络流量实时监控
- `bmon` - 带宽监控
- `speedtest-cli` - 网速测试
- `iperf3` - 网络性能测试

---

## 💡 性能分析方法论

### USE 方法（Utilization, Saturation, Errors）

针对每个资源检查：

1. **使用率**（Utilization）- 资源繁忙程度
2. **饱和度**（Saturation）- 等待队列长度
3. **错误**（Errors）- 错误计数

### 检查清单

```bash
# CPU
uptime                    # 负载
vmstat 1                  # 运行队列
mpstat -P ALL 1          # 每核使用率
pidstat 1                # 进程CPU

# 内存
free -m                   # 可用内存
vmstat 1                  # si/so swap
dmesg | grep -i oom      # OOM killer

# 磁盘I/O
iostat -x 1              # %util, await
iotop                    # 进程I/O

# 网络
sar -n DEV 1             # 接口吞吐量
sar -n TCP,ETCP 1        # TCP统计
netstat -s               # 错误统计
```

---

## 📦 推荐安装的工具包

```bash
# CentOS/RHEL
yum install sysstat iotop htop dstat nload iftop nethogs

# Ubuntu/Debian
apt install sysstat iotop htop dstat nload iftop nethogs

# 高级工具
# bcc-tools (eBPF)
# perf
# glances
```

---

## 🎯 快速诊断流程

遇到性能问题时，建议按此顺序检查：

1. **uptime** - 快速查看系统负载
2. **dmesg | tail** - 检查系统错误
3. **vmstat 1** - 整体资源使用
4. **mpstat -P ALL 1** - CPU 瓶颈？
5. **pidstat 1** - 哪个进程有问题？
6. **iostat -x 1** - 磁盘瓶颈？
7. **free -m** - 内存不足？
8. **sar -n DEV 1** - 网络瓶颈？
9. **top** - 深入查看

这些工具组合使用，可以全面诊断和解决 Linux 系统的性能问题！
