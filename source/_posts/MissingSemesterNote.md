---
title: Missing Semester of Your CS Education Note
date: 2025-12-10 19:54:30
tags:
  - Linux
---

> Linux command line tldr: https://tldr.sh

## Lecture 1 Course Overview + the Shell

how to use useful shell to do userful tools.

```shell
# 命令 date
shenjy@shenjy:~$ date
Wed Dec 10 09:07:47 CST 2025

# 命令 cal
shenjy@shenjy:~$ cal
  December 2025
Su Mo Tu We Th Fr Sa
    1  2  3  4  5  6
 7  8  9 10 11 12 13
14 15 16 17 18 19 20
21 22 23 24 25 26 27
28 29 30 31

# 命令 time ls 查看命令 ls 的执行时间
shenjy@shenjy:~$ time ls
anytls     c       ds             es      javademo  lua       nacos  pa         redis        skywalking  tldr   xxl-job
arthas     docker  easy-dataset   images  jyy_site  minikube  neo4j  python     sh           snap        video
bookstore  docs    elasticsearch  java    logs      mysql     os     qwen-code  simple-blog  soft        vue

real    0m0.010s # 实际执行时间
user    0m0.004s # 用户态执行时间
sys     0m0.006s # 内核态执行时间

# \ 的含义
shenjy@shenjy:~$ echo hello\ world
hello world
```

### 初始理解命令

首先，我看到这个命令是：

```bash
echo hello\ world
```

我知道 `echo` 是一个在终端中常用的命令，用于输出后面的参数。通常的用法是：

```bash
echo hello world
```

这会输出：

```
hello world
```

### 反斜杠 `\` 的出现

在给定的命令中，`hello` 和 `world` 之间有一个反斜杠 `\`，即 `hello\ world`。这与我平时看到的 `echo hello world` 有所不同。我开始思考这个反斜杠的作用。

### 反斜杠在 shell 中的作用

在 shell（如 bash）中，反斜杠 `\` 是一个**转义字符**。它的主要作用是：

1. **转义下一个字符**：反斜杠告诉 shell，紧接在它后面的字符应该被视为字面意义上的字符，而不是作为 shell 的特殊字符（如空格、制表符、换行符、美元符号 `$`、星号 `*` 等）。

   例如：

   - `\ `：表示一个实际的空间字符，而不是参数的分隔符。
   - `\$`：表示一个实际的美元符号 `$`，而不是变量的开始。

2. **换行续接**：如果反斜杠出现在行尾，表示命令延续到下一行（这在多行命令中很有用），但这不是我们这里讨论的情况。

### 应用到 `echo hello\ world`

在 `echo hello\ world` 中：

- `hello\ world`：这里的反斜杠转义了后面的空格。也就是说，`\ ` 表示一个实际的空间字符，而不是参数的分隔符。

  通常，shell 会将空格作为参数的分隔符。例如：

  ```bash
  echo hello world
  ```

  这里 `hello` 和 `world` 是两个分开的参数传递给 `echo`，`echo` 会将它们用空格连接输出。

  但在 `hello\ world` 中，`\ ` 使得空格被当作字面字符，因此 `hello\ world` 被视为一个整体的参数。

### 实验验证

为了验证我的理解，我可以在终端中尝试：

1. 直接运行 `echo hello\ world`：

   ```bash
   echo hello\ world
   ```

   输出：

   ```
   hello world
   ```

   看起来和 `echo hello world` 的输出一样。

2. 但是如果我尝试用引号来比较：

   ```bash
   echo "hello world"
   ```

   输出：

   ```
   hello world
   ```

   也是相同的。那区别在哪？

   区别在于参数的数量：

   - `echo hello world`：两个参数 `hello` 和 `world`，`echo` 会将它们用空格连接。
   - `echo hello\ world`：一个参数 `hello world`（因为空格被转义）。
   - `echo "hello world"`：一个参数 `hello world`。

   在 `echo` 的情况下，输出看起来是一样的，因为 `echo` 会将所有参数用空格连接输出。但如果是其他命令，参数的数量可能会影响行为。

3. 用 `printf` 或 `set` 来观察：

   例如，使用 `set` 可以查看位置参数：

   ```bash
   set -- hello\ world
   echo $#
   ```

   输出：

   ```
   1
   ```

   表示有一个参数。

   ```bash
   set -- hello world
   echo $#
   ```

   输出：

   ```
   2
   ```

   表示有两个参数。

   这说明 `hello\ world` 确实被视为一个单一参数。

### 反斜杠的其他用途

反斜杠不仅可以转义空格，还可以转义其他特殊字符：

- `echo \$PATH`：输出 `$PATH`，而不是展开变量 `PATH`。
- `echo \*`：输出 `*`，而不是进行文件名通配。
- `echo \"`：输出 `"`，而不是作为字符串的开始或结束。

### 特殊情况

如果反斜杠后面跟的不是特殊字符，反斜杠通常会被忽略：

- `echo hello\world`：`\w` 不是特殊字符，所以反斜杠无效，输出 `helloworld`。

### 结论

在 `echo hello\ world` 这个命令中：

- `\ `（反斜杠加空格）中的反斜杠是一个转义字符，它使得空格被当作字面意义上的空格，而不是参数分隔符。
- 因此，`hello\ world` 被视为一个单一的参数 `hello world`。
- `echo` 接收一个参数 `hello world` 并输出 `hello world`。
- 虽然在这种情况下输出和 `echo hello world` 相同，但背后的参数传递机制不同。

### 最终答案

在命令 `echo hello\ world` 中：

- **`\`（反斜杠）** 是一个 **转义字符**，它的作用是告诉 shell 将紧接在它后面的字符（这里是空格）视为字面意义上的字符，而不是 shell 的特殊字符（如参数分隔符）。

- 因此：
  - `hello\ world` 中的 `\ ` 表示一个实际的空间字符，使得整个 `hello\ world` 被当作一个单一的参数传递给 `echo`。
  - `echo` 输出这个参数，结果为 `hello world`。

虽然在这个例子中 `echo hello\ world` 和 `echo hello world` 的输出看起来相同，但前者是将 `hello world` 作为一个参数传递，而后者是将 `hello` 和 `world` 作为两个参数传递。对于 `echo` 来说，这两种情况的结果是相同的，但对于其他命令可能会有区别。

```shell
# 查看环境变量
shenjy@shenjy:~$ echo $PATH
/home/shenjy/java/jdk1.8.0_202/bin:/home/shenjy/.cargo/bin:/home/shenjy/.local/bin:/home/shenjy/.nvm/versions/node/v22.17.0/bin:/home/shenjy/java/amazon-corretto-11.0.25.9.1-linux-x64/bin

# 查看命令位置
shenjy@shenjy:~$ which echo
/usr/bin/echo

shenjy@shenjy:~$ whereis echo
echo: /usr/bin/echo /usr/share/man/man1/echo.1.gz

# 打印当前所在路径：绝对路径、相对路径
shenjy@shenjy:~$ pwd # print working directory, print current path
/home/shenjy

# cd： change directory, 切换路径
# . 当前路径，.. 上一级路径
shenjy@shenjy:~$ cd /home
shenjy@shenjy:/home$ cd ..
shenjy@shenjy:/$ pwd
/
shenjy@shenjy:/$ cd ./home
shenjy@shenjy:/home$

# 解释下 drwxr-xr-x 25 root   root   4096 Dec 10 08:45 ../
```

这是一个 Linux/Unix 文件系统下的目录权限和属性信息，属于 `ls -l` 命令的输出格式。以下是逐项解释：

---

### 分解内容：

```plaintext
drwxr-xr-x 25 root root 4096 Dec 10 08:45 ../
```

1. **文件类型与权限 (`drwxr-xr-x`)**

   - 第一个字符 `d` 表示这是一个 **目录**（`directory`）。
     - `-` 表示普通文件，`l` 表示符号链接，`c`/`b` 表示设备文件等。
   - 后续 9 个字符分为三组，每 3 个一组表示不同用户的权限：
     - **`rwx`**（前三位）：**所有者（owner）**权限为读（`r`）、写（`w`）、执行（`x`）。
     - **`r-x`**（中三位）：**所属组（group）**权限为读（`r`）、执行（`x`），无写权限（`-`）。
     - **`r-x`**（后三位）：**其他用户（others）**权限与组相同，读和执行，无写权限。
     - 执行权限 `x` 对目录表示可进入（`cd`）或遍历。

2. **硬链接数 (`25`)**

   - 表示该目录下有 **25 个子目录**（包括 `.` 和 `..` 的引用）。
   - 注：每个子目录会通过 `..` 指向父目录，因此父目录的硬链接数等于其直接子目录数 + 2（自身 `.` 和父目录的链接）。

3. **所有者 (`root`)**

   - 该目录的所有者是 `root` 用户。

4. **所属组 (`root`)**

   - 该目录的所属组是 `root` 组。

5. **大小 (`4096`)**

   - 目录本身占用 **4096 字节**（这是文件系统块的最小分配单元，实际内容可能更少）。
   - 注：目录大小不包含其下文件的总大小，仅表示目录元数据占用的空间。

6. **修改时间 (`Dec 10 08:45`)**

   - 该目录的最后修改时间是 12 月 10 日 08:45。

7. **目录名 (`../`)**
   - 表示这是**父目录**的条目（即当前目录的上层目录）。
   - 在 `ls -l` 中显示 `..` 时表示列出父目录的信息。

---

### 总结：

- 这是一个由 `root` 用户和 `root` 组所有的目录，权限为：所有者可读/写/进入，组和其他用户可读/进入但不可写。
- 其下有 25 个子目录链接，最后修改时间为 12 月 10 日。

### 补充：权限的数字表示

`rwxr-xr-x` 对应数字权限 `755`：

- `rwx` = 4 (r) + 2 (w) + 1 (x) = **7**
- `r-x` = 4 + 0 + 1 = **5**
- `r-x` = 4 + 0 + 1 = **5**

```shell
# 使用绝对路径运行 echo 命令
shenjy@shenjy:~$ cd /home/shenjy/c/demo/
shenjy@shenjy:~/c/demo$ ../../../../bin/echo hello world
hello world

# 列出当前路径多有的文件
ls

# 列出上一级路径的我呢见
ls ..

# 直接到指定的路径 /
cd /

# 回家
cd

# 回到从哪里来
cd -

# 查看命令帮助
ls --help
man ls
info ls

# 查看更加详细的信息
ls -l

# 查看命令的原始命令是啥
type ll

# 创建文件
shenjy@shenjy:~$ touch a.txt
shenjy@shenjy:~$ ls
a.txt
shenjy@shenjy:~$ mkdir file
shenjy@shenjy:~$ ls
a.txt  file
# 修改文件名
shenjy@shenjy:~$ mv a.txt b.txt
shenjy@shenjy:~$ ls
b.txt  file
# 把文件移动到 file 目录下
shenjy@shenjy:~$ mv b.txt file/
shenjy@shenjy:~$ cd file
shenjy@shenjy:~/file$ ls
b.txt

# 删除文件夹以及问价夹中的内容
shenjy@shenjy:~$ rm -rf file/

# cp 命令
shenjy@shenjy:~/cp$ mkdir d
shenjy@shenjy:~/cp$ echo "hello world" > file.txt
shenjy@shenjy:~/cp$ ls
d  file.txt
shenjy@shenjy:~/cp$ cat file.txt
hello world
shenjy@shenjy:~/cp$ ls
d  file.txt
shenjy@shenjy:~/cp$
shenjy@shenjy:~/cp$ ls
d  file.txt
shenjy@shenjy:~/cp$ cp file.txt d/
shenjy@shenjy:~/cp$ ls
d  file.txt
shenjy@shenjy:~/cp$ cd d
shenjy@shenjy:~/cp/d$ ls
file.txt
shenjy@shenjy:~/cp/d$ cat file.txt
hello world

# 移除空目录
rmdir d

# 清屏
clear
# 或者
ctrl + l # 快捷键
```

### 在命令行中，快速到命令行头、尾的案件

| 操作       | Linux/macOS（Bash） | Windows CMD | Windows PowerShell |
| ---------- | ------------------- | ----------- | ------------------ |
| **到行首** | `Ctrl + A`          | `Home`      | `Home`             |
| **到行尾** | `Ctrl + E`          | `End`       | `End`              |

### 输入、输出

```shell
shenjy@shenjy:~$ echo hello > hello.txt
shenjy@shenjy:~$ cat hello.txt
hello
shenjy@shenjy:~$ cat < hello.txt
hello
shenjy@shenjy:~$ cat < hello.txt > hello2.txt
shenjy@shenjy:~$ cat hello2.txt
hello
shenjy@shenjy:~$ cat < hello.txt >> hello2.txt
shenjy@shenjy:~$ cat hello2.txt
hello
hello

shenjy@shenjy:~$ ls -l /
total 2816
lrwxrwxrwx   1 root root       7 May  2  2023 bin -> usr/bin
drwxr-xr-x   2 root root    4096 Apr 18  2022 boot
drwxr-xr-x   3 root root    4096 Mar  3  2025 data
drwxr-xr-x  15 root root    3840 Dec 10 08:45 dev
drwxr-xr-x 108 root root    4096 Dec 10 08:45 etc
drwxr-xr-x   5 root root    4096 Nov 11  2024 home
-rwxr-xr-x   1 root root 2781552 Oct 10 08:22 init
lrwxrwxrwx   1 root root       7 May  2  2023 lib -> usr/lib
lrwxrwxrwx   1 root root       9 May  2  2023 lib32 -> usr/lib32
lrwxrwxrwx   1 root root       9 May  2  2023 lib64 -> usr/lib64
lrwxrwxrwx   1 root root      10 May  2  2023 libx32 -> usr/libx32
drwx------   2 root root   16384 Apr 11  2019 lost+found
drwxr-xr-x   2 root root    4096 May  2  2023 media
drwxr-xr-x  10 root root    4096 Jul 24 10:32 mnt
drwxr-xr-x   4 root root    4096 Feb 12  2025 opt
dr-xr-xr-x 263 root root       0 Dec 10 08:45 proc
drwx------  15 root root    4096 Oct 10 09:51 root
drwxr-xr-x  23 root root     760 Dec 10 09:10 run
lrwxrwxrwx   1 root root       8 May  2  2023 sbin -> usr/sbin
drwxr-xr-x  10 root root    4096 Nov 11  2024 snap
drwxr-xr-x   2 root root    4096 May  2  2023 srv
dr-xr-xr-x  13 root root       0 Dec 10 08:43 sys
drwxrwxrwt   8 root root   12288 Dec 10 10:27 tmp
drwxr-xr-x  14 root root    4096 May  2  2023 usr
drwxr-xr-x  13 root root    4096 May  2  2023 var
drwx------   2 root root    4096 Mar 28  2025 wslGBhABF
drwx------   2 root root    4096 Mar 28  2025 wslJfeeFE
drwx------   2 root root    4096 Mar 28  2025 wslMPeIAF
drwx------   2 root root    4096 Mar 28  2025 wslPfcNpE
drwx------   2 root root    4096 Mar 28  2025 wsleHIMmE
shenjy@shenjy:~$ ls -l / | tail -n1
drwx------   2 root root    4096 Mar 28  2025 wsleHIMmE

shenjy@shenjy:~$ curl --head --silent qq.com
HTTP/1.1 302 Moved Temporarily
Server: stgw
Date: Wed, 10 Dec 2025 02:39:49 GMT
Content-Type: text/html
Content-Length: 137
Connection: keep-alive
Location: https://www.qq.com/

shenjy@shenjy:~$ curl --head --silent qq.com |  grep -i content-length
Content-Length: 137
shenjy@shenjy:~$ curl --head --silent qq.com |  grep -i content-length | cut --delimiter=' ' -f2
137

shenjy@shenjy:/sys$ ls
block  bus  class  dev  devices  firmware  fs  hypervisor  kernel  module  power
shenjy@shenjy:/sys$ cd class/
shenjy@shenjy:/sys/class$ ls
ata_device   devlink          hwmon             iscsi_transport  phy           sas_end_device  spi_transport
ata_link     dma              ieee80211         mdio_bus         power_supply  sas_expander    thermal
ata_port     drm              input             mem              powercap      sas_host        tpm
backlight    fc_host          intel_scu_ipc     misc             ppp           sas_phy         tpmrm
bdi          fc_remote_ports  iommu             nd               pps           sas_port        tty
block        fc_transport     iscsi_connection  net              ptp           scsi_device     vc
bsg          fc_vports        iscsi_endpoint    nvme             pwm           scsi_disk       virtio-ports
dca          firmware         iscsi_host        nvme-generic     raid_devices  scsi_generic    vtconsole
devcoredump  gpio             iscsi_iface       nvme-subsystem   rtc           scsi_host       wakeup
devfreq      graphics         iscsi_session     pci_bus          sas_device    spi_host        watchdog
```

在 Linux 系统中，`/sys/class` 是一个虚拟文件系统（sysfs）的目录，它按设备功能分类显示了内核注册的设备、驱动和子系统信息。以下是该路径下常见内容的概述：

---

### **主要子目录分类**

`/sys/class/` 下的子目录通常是按设备类型（功能）分类的，例如：

1. **块设备**

   - `block/`：系统的所有块设备（如硬盘、分区），例如 `sda`, `sda1`, `nvme0n1` 等。
     - 子目录包含设备属性（如 `size`、`stat`）、所属的物理设备链接（如 `../devices/pci0000:00/...`）。

2. **输入设备**

   - `input/`：输入设备（如键盘、鼠标、触摸板），子目录通常以 `inputX`（如 `input0`）命名。
     - 包含设备信息（如 `name`, `id`）和事件接口链接（如 `eventX`）。

3. **网络接口**

   - `net/`：网络接口（如 `eth0`, `wlan0`）。
     - 包含接口配置（如 `mtu`、`statistics`）、所属驱动信息等。

4. **图形设备**

   - `drm/`：DRM（Direct Rendering Manager）设备，如显卡（`card0`）。
   - `graphics/`：其他图形相关设备（如 `fbcon`）。

5. **声卡和音频设备**

   - `sound/`：声卡设备（如 `card0`），包含编解码器和控制接口。

6. **串口和终端**

   - `tty/`：串口和虚拟终端设备（如 `ttyS0`, `ttyUSB0`）。
   - `vtconsole/`：虚拟终端控制台。

7. **电源和电池**

   - `power_supply/`：电源信息（如 `BAT0` 电池或 `AC` 适配器）。
   - 包含电池状态（`status`）、容量（`capacity`）等。

8. **LED 和背光**

   - `leds/`：硬件 LED 控制（如键盘背光 `phy0-led`）。
   - `backlight/`：屏幕背光控制（如 `intel_backlight`）。

9. **内存管理**

   - `mem/`：内存设备（如 `mem`, `kmem`）。
   - `memory/`：物理内存信息（如 `memory0`）。

10. **其他常见设备**
    - `thermal/`：温度传感器和冷却设备。
    - `watchdog/`：硬件看门狗定时器。
    - `scsi_device/` 和 `scsi_generic/`：SCSI 设备。
    - `rtc/`：实时时钟（如 `rtc0`）。

---

### **目录结构特点**

- **符号链接**：每个子目录通常是符号链接，指向 `/sys/devices/` 下的具体设备路径。
  - 例如：`/sys/class/net/eth0` 可能链接到 `/sys/devices/pci0000:00/.../net/eth0`。
- **统一接口**：每个设备的子目录包含标准文件（如 `uevent`、`dev`）和设备的特定属性，用户可通过读写这些文件与内核交互。

---

### **实际示例**

查看 `/sys/class/net/` 下的网络接口：

```bash
ls /sys/class/net/
# 输出可能包含: eth0  lo  wlan0
```

查看键盘输入设备信息：

```bash
ls /sys/class/input/input0/
# 可能包含: name  id  capabilities
```

---

### **注意事项**

- `sysfs` 是内核的实时视图，文件内容动态生成，不可直接修改（除非明确允许）。
- 不同系统（或硬件配置）下，`/sys/class` 的内容会有所不同。

如果需要具体设备的信息，可以进一步探索对应子目录中的文件。

```shell
$ echo 500 > brightness
$ sudo su
# echo 500 > brightness
$ echo 1060 > sudo tee brightness
```

## Lecture 2: Shell Tools and Scripting

```shell
shenjy@shenjy:~$ foo=bar
shenjy@shenjy:~$ echo $foo
bar
shenjy@shenjy:~$ echo "hello"
hello
shenjy@shenjy:~$ echo 'world'
world
shenjy@shenjy:~$ echo "hello, $foo"
hello, bar
shenjy@shenjy:~$ echo 'hello, $foo'
hello, $foo
```

`mcd.sh`

```shell
#!/bin/bash
mcd() {
   mkdir -p "$1"
   cd "$1"
}
```

接下来执行下面的命令

```shell
source mcd.sh
mcd test

# $0-$9
```

- **`$0`**：当前脚本或 shell 的名称。
- **`$1` ~ `$9`**：脚本或函数的前 9 个参数，按顺序对应。
- 超过 9 个的参数需要用 `${10}`、`${11}` 的形式引用。

- **`$?`**: 获取上一个命令的错误代码
- **`$_`**: 获取最后一个参数

```shell
shenjy@shenjy:~/sh/ms$ ls
mcd.sh  puma
shenjy@shenjy:~/sh/ms$ mkdir test
shenjy@shenjy:~/sh/ms$ cd $_
shenjy@shenjy:~/sh/ms/test$ echo "Hello"
Hello
shenjy@shenjy:~/sh/ms/test$ echo $?
0
```

### **1. `$?` 的作用**

- **含义**：`$?` 保存上一个命令/脚本/函数的退出状态。
- **返回值范围**：
  - **0**：表示成功（没有错误）。
  - **非 0**：表示失败（具体数值通常由程序定义，不同值代表不同的错误原因）。

---

### **2. `$?` 的常见返回值及含义**

| 返回值 | 含义                              | 示例命令              |
| ------ | --------------------------------- | --------------------- |
| `0`    | 成功                              | `true; echo $?` → 0   |
| `1`    | 一般错误（未指定具体原因）        | `false; echo $?` → 1  |
| `2`    | 命令语法错误                      | `ls --invalid-option` |
| `126`  | 命令不可执行（权限不足）          | `./no-permission.sh`  |
| `127`  | 命令未找到                        | `nonexistent-command` |
| `130`  | 命令被终止（如 Ctrl+C）           | 按下`Ctrl+C`中断      |
| `137`  | 进程被强制终止（`kill -9`）       | `kill -9 <PID>`       |
| `255`  | 退出状态超出范围（>255 会被取模） | 某些程序自定义错误    |

> **注意**：非 0 值的具体含义取决于程序，可通过 `man` 手册或程序文档查询（如 `grep` 返回 `1` 表示未匹配到内容，不是错误）。

---

### **3. 类似的 Shell 特殊变量**

| 变量      | 描述                                              |
| --------- | ------------------------------------------------- |
| `$0`      | 当前脚本或 Shell 的名称。                         |
| `$1`-`$9` | 脚本或函数的第 1-9 个参数。                       |
| `$#`      | 传递给脚本/函数的参数个数。                       |
| `$@`      | 所有参数的列表（每个参数作为独立字符串）。        |
| `$*`      | 所有参数的列表（合并为单个字符串）。              |
| `$$`      | 当前 Shell 的进程 ID（PID）。                     |
| `$!`      | 最后一个后台运行的进程的 PID。                    |
| `$_`      | 上一个命令的最后一个参数。                        |
| `$-`      | 当前 Shell 的选项标志（如 `set -x` 会包含 `x`）。 |

---

### **4. 使用示例**

#### 检查命令是否成功：

```bash
ls /valid/path
if [ $? -eq 0 ]; then
    echo "成功"
else
    echo "失败"
fi
```

#### 简化为直接判断：

```bash
if ls /valid/path; then
    echo "成功"
fi
```

---

### **5. 注意事项**

- 某些命令（如 `grep`）会 intentionally 返回非 0 值表示逻辑失败（如未找到匹配），而非运行时错误。
- 自定义脚本中，应通过 `exit <数值>` 返回明确的退出状态。

```shell
shenjy@shenjy:~/sh/ms$ grep foobar mcd.sh
shenjy@shenjy:~/sh/ms$ echo $?
1
shenjy@shenjy:~/sh/ms$ true
shenjy@shenjy:~/sh/ms$ echo $?
0
shenjy@shenjy:~/sh/ms$ false
shenjy@shenjy:~/sh/ms$ echo $?
1
shenjy@shenjy:~/sh/ms$ false || echo "Oops fail"
Oops fail
shenjy@shenjy:~/sh/ms$ echo $?
0
shenjy@shenjy:~/sh/ms$ true || echo "Will be not be printed."
shenjy@shenjy:~/sh/ms$
shenjy@shenjy:~/sh/ms$ true && echo "Things went well"
Things went well
shenjy@shenjy:~/sh/ms$ false && echo "This will not print"
shenjy@shenjy:~/sh/ms$ false; echo "This will always print"
This will always print

shenjy@shenjy:~/sh/ms$ foo=$(pwd)
shenjy@shenjy:~/sh/ms$ echo $foo
/home/shenjy/sh/ms
shenjy@shenjy:~/sh/ms$ echo "we are in $(pwd)"
we are in /home/shenjy/sh/ms
shenjy@shenjy:~/sh/ms$ cat <(ls) <(ls ..)
mcd.sh
puma
test
demo2.sh
install.sh
ms
print_all_process_opened_all_obj.sh
sys.sh
sysinfo.sh
warp.sh
```

`example.sh` 脚本

```shell
#!/bin/bash
echo "Starting program at $(date)" # Date will be substitutes
echo "Running program $0 with $# arguments with pid $$"

for file in "$@"; do
    grep foobar "$file" > /dev/null 2> /dev/null
    # when pattern is not found, grep has exit status
    # We redirect STDOUT and STDERR to a null register
    if [ "$?" -ne 0 ]; then
        echo "File $file does not have any foobar, adding one"
        echo "# foobar" >> "$file"
    fi
done
```

shell 脚本中 `if` 的语法

In Linux shell scripting, you can use conditional logic with `if`, `elif` (else if), and `else` statements. Here's how to structure them:

### Basic Syntax

```bash
if [ condition ]; then
    # commands to execute if condition is true
elif [ another_condition ]; then
    # commands to execute if another_condition is true
else
    # commands to execute if none of the above conditions are true
fi
```

### Example

```bash
#!/bin/bash

read -p "Enter a number: " num

if [ $num -gt 0 ]; then
    echo "The number is positive."
elif [ $num -lt 0 ]; then
    echo "The number is negative."
else
    echo "The number is zero."
fi
```

### Important Notes:

1. Spaces are required around brackets and operators:

   - Correct: `[ $var -eq 10 ]`
   - Incorrect: `[$var-eq10]`

2. Common comparison operators:

   - Numeric:
     - `-eq` (equal)
     - `-ne` (not equal)
     - `-gt` (greater than)
     - `-lt` (less than)
     - `-ge` (greater than or equal)
     - `-le` (less than or equal)
   - String:
     - `=` (equal)
     - `!=` (not equal)
     - `-z` (string is empty)
     - `-n` (string is not empty)

3. For file tests:
   - `-f` (file exists)
   - `-d` (directory exists)
   - `-r` (readable)
   - `-w` (writable)
   - `-x` (executable)

### Advanced Example with Multiple Conditions

```bash
#!/bin/bash

file="/path/to/file"

if [ -f "$file" ] && [ -r "$file" ]; then
    echo "File exists and is readable."
elif [ -d "$file" ]; then
    echo "This is a directory, not a file."
elif [ ! -e "$file" ]; then
    echo "File does not exist."
else
    echo "File exists but is not readable."
fi
```
