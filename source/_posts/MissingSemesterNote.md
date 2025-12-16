---
title: Missing Semester of Your CS Education Note
date: 2025-12-10 19:54:30
tags:
  - Linux
---

> Linux command line tldr: https://tldr.sh
>
> 课程 2026 年度更新：https://missing.csail.mit.edu/

## [Lecture 1 Course Overview + the Shell](https://missing.csail.mit.edu/2020/course-shell/)

> 中文版本：https://missing-semester-cn.github.io/2020/course-shell/

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

## [Lecture 2: Shell Tools and Scripting](https://missing.csail.mit.edu/2020/shell-tools/)

> 中文版：https://missing-semester-cn.github.io/2020/shell-tools/

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
- **`$1`** ~ **`$9`**：脚本或函数的前 9 个参数，按顺序对应。
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

命令行

````shell
shenjy@shenjy:~/images/grandfather$ ls
IMG_0684.JPG  IMG_0685.JPG
shenjy@shenjy:~/images/grandfather$ convert IMG_0684.JPG image.png
shenjy@shenjy:~/images/grandfather$ ls
IMG_0684.JPG  IMG_0685.JPG  image.png
shenjy@shenjy:~/images/grandfather$ mkdir foo bar
shenjy@shenjy:~/images/grandfather$ touch {foo,bar}/{a..j}
shenjy@shenjy:~/images/grandfather$ ls
IMG_0684.JPG  IMG_0685.JPG  bar  foo  image.png
shenjy@shenjy:~/images/grandfather$ cd bar/
shenjy@shenjy:~/images/grandfather/bar$ ls
a  b  c  d  e  f  g  h  i  j
shenjy@shenjy:~/images/grandfather/bar$ cd ..
shenjy@shenjy:~/images/grandfather$ ls
IMG_0684.JPG  IMG_0685.JPG  bar  foo  image.png
shenjy@shenjy:~/images/grandfather$ cd foo/
shenjy@shenjy:~/images/grandfather/foo$ ls
a  b  c  d  e  f  g  h  i  j
shenjy@shenjy:~/images/grandfather$ touch foo/x bar/y
shenjy@shenjy:~/images/grandfather$ diff <(ls foo) <(ls bar)
11c11
< x
---
> y

# script.py
#!/usr/bin/python3 # shebang
#!/usr/bin/env python3 # 推荐使用
import sys
for arg in reversed(sys.argv[1:]):
    print(arg)

shenjy@shenjy:~/sh/ms$ vim script.py
shenjy@shenjy:~/sh/ms$ ls
example.sh  mcd.sh  script.py
shenjy@shenjy:~/sh/ms$ chmod +x script.py
shenjy@shenjy:~/sh/ms$ ls
example.sh  mcd.sh  script.py
shenjy@shenjy:~/sh/ms$ ./script.py 1 2 3 4
-bash: ./script.py: /usr/local/bin/python3: bad interpreter: No such file or directory
shenjy@shenjy:~/sh/ms$ which python
shenjy@shenjy:~/sh/ms$ which python3
/usr/bin/python3
shenjy@shenjy:~/sh/ms$ vim script.py
shenjy@shenjy:~/sh/ms$ ./script.py 1 2 3 4
4
3
2
1

shenjy@shenjy:~/sh/ms$ shellcheck mcd.sh

In mcd.sh line 4:
   cd "$1"
   ^-----^ SC2164 (warning): Use 'cd ... || exit' or 'cd ... || return' in case cd fails.

Did you mean:
   cd "$1" || exit

For more information:
  https://www.shellcheck.net/wiki/SC2164 -- Use 'cd ... || exit' or 'cd ... |...
shenjy@shenjy:~/sh/ms$ man rg
No manual entry for rg
shenjy@shenjy:~/sh/ms$ rg
Command 'rg' not found, but can be installed with:
sudo snap install ripgrep  # version 12.1.0, or
sudo apt  install ripgrep  # version 13.0.0-2ubuntu0.1
See 'snap info ripgrep' for additional versions.
shenjy@shenjy:~/sh/ms$ sudo apt install ripgrep
shenjy@shenjy:~/sh/ms$ man rg
shenjy@shenjy:~/sh/ms$ tldr convert

  convert

  This command is an alias of `magick convert`.
  Note: This alias is deprecated since ImageMagick 7. It has been replaced by `magick`.
  Use `magick convert` if you need to use the old tool in versions 7+.

  - View documentation for the original command:
    tldr magick convert

shenjy@shenjy:~/sh/ms$ ag
Entering AI mode. Type your prompt (or 'exit' to quit):
prompt> 请使用 tldr 的方式介绍下 convert 命令。

DeepSeek-v3:
`convert` 是 ImageMagick 工具集的一部分，用于图像格式转换和处理。

**主要功能：**
- 格式转换（如 JPEG → PNG）
- 调整大小、旋转、裁剪图片
- 添加滤镜、水印、文本等效果

**基本用法：**
```bash
convert 输入文件 [选项] 输出文件
````

**常用示例：**

- `convert image.jpg image.png` - 转换格式
- `convert image.jpg -resize 50% small.jpg` - 缩放图片
- `convert image.jpg -rotate 90 rotated.jpg` - 旋转图片
- `convert image.jpg -quality 85 compressed.jpg` - 调整质量
- `convert *.jpg merged.pdf` - 合并多张图片为 PDF

`````

> 这里要强烈推荐这个开源项目，希望你可以使用它来提高你的学习效率，同时也可以一起来共同开发它，为其添加更加丰富的 feature。项目源码：https://github.com/shenjy712/ag

````shell
shenjy@shenjy:~/sh/ms$ tldr ffmpeg

  ffmpeg

  Video conversion tool.
  See also: `gst-launch-1.0`.
  More information: https://ffmpeg.org/ffmpeg.html#Options.

  - Extract the sound from a video and save it as MP3:
    ffmpeg -i path/to/video.mp4 -vn path/to/sound.mp3

  - Transcode a FLAC file to Red Book CD format (44100kHz, 16bit):
    ffmpeg -i path/to/input_audio.flac -ar 44100 -sample_fmt s16 path/to/output_audio.wav

  - Save a video as GIF, scaling the height to 1000px and setting framerate to 15:
    ffmpeg -i path/to/video.mp4 -filter:v 'scale=-1:1000' -r 15 path/to/output.gif

  - Combine numbered images (frame_1.jpg, frame_2.jpg, etc) into a video or GIF:
    ffmpeg -i path/to/frame_%d.jpg -f image2 video.mpg|video.gif

  - Trim a video from a given start time mm:ss to an end time mm2:ss2 (omit the -to flag to trim till the end):
    ffmpeg -i path/to/input_video.mp4 -ss mm:ss -to mm2:ss2 -codec copy path/to/output_video.mp4

  - Convert AVI video to MP4. AAC Audio @ 128kbit, h264 Video @ CRF 23:
    ffmpeg -i path/to/input_video.avi -codec:a aac -b:a 128k -codec:v libx264 -crf 23 path/to/output_video.mp4

  - Remux MKV video to MP4 without re-encoding audio or video streams:
    ffmpeg -i path/to/input_video.mkv -codec copy path/to/output_video.mp4

  - Convert MP4 video to VP9 codec. For the best quality, use a CRF value (recommended range 15-35) and -b:v MUST be 0:
    ffmpeg -i path/to/input_video.mp4 -codec:v libvpx-vp9 -crf 30 -b:v 0 -codec:a libopus -vbr on -threads number_of_threads path/to/output_video.webm

shenjy@shenjy:~/sh/ms$ tldr tar

  tar

  Archiving utility.
  Often combined with a compression method, such as `gzip` or `bzip2`.
  More information: https://www.gnu.org/software/tar/manual/tar.html.

  - [c]reate an archive and write it to a [f]ile:
    tar cf path/to/target.tar path/to/file1 path/to/file2 ...

  - [c]reate a g[z]ipped archive and write it to a [f]ile:
    tar czf path/to/target.tar.gz path/to/file1 path/to/file2 ...

  - [c]reate a g[z]ipped (compressed) archive from a directory using relative paths:
    tar czf path/to/target.tar.gz --directory path/to/directory .

  - E[x]tract a (compressed) archive [f]ile into the current directory [v]erbosely:
    tar xvf path/to/source.tar[.gz|.bz2|.xz]

  - E[x]tract a (compressed) archive [f]ile into the target directory:
    tar xf path/to/source.tar[.gz|.bz2|.xz] --directory path/to/directory

  - [c]reate a compressed archive and write it to a [f]ile, using the file extension to [a]utomatically determine the compression program:
    tar caf path/to/target.tar.xz path/to/file1 path/to/file2 ...

  - Lis[t] the contents of a tar [f]ile [v]erbosely:
    tar tvf path/to/source.tar

  - E[x]tract files matching a pattern from an archive [f]ile:
    tar xf path/to/source.tar --wildcards "*.html"
shenjy@shenjy:~/sh/ms$ ag
Entering AI mode. Type your prompt (or 'exit' to quit):
prompt> please introduce find command with tldr pattern.

DeepSeek-v3:
Here's a `tldr`-style introduction to the `find` command:

```bash
# find
# Search for files and directories in a directory hierarchy.

# Find files by name (case-sensitive):
find /path/to/dir -name "*.txt"

# Find files by name (case-insensitive):
find /path/to/dir -iname "*.txt"

# Find directories:
find /path/to/dir -type d

# Find regular files:
find /path/to/dir -type f

# Find files modified in the last 7 days:
find /path/to/dir -mtime -7

# Find files larger than 10MB:
find /path/to/dir -size +10M

# Find and delete files (use with caution!):
find /path/to/dir -name "*.tmp" -delete

# Find and execute a command on each file ({} represents the filename):
find /path/to/dir -name "*.jpg" -exec chmod 644 {} \;

# Find files owned by a specific user:
find /path/to/dir -user username
```

Key options:
- `-name`: Search by filename (case-sensitive)
- `-iname`: Search by filename (case-insensitive)
- `-type`: Filter by type (f=file, d=directory, l=symlink)
- `-mtime`: Filter by modification time
- `-size`: Filter by size
- `-exec`: Execute command on found files
- `-delete`: Delete matching files

The `find` command is incredibly powerful for searching and operating on files. For more details, check `man find`.

shenjy@shenjy:~$ find . -name bar -type d
find: ‘./docker/vectordb/volumes/milvus/etcd’: Permission denied
./.nvm/test/fast/Unit tests/mocks/project_dirs/inside-n_m-nested-pkg/node_modules/foo/bar
./.nvm/test/fast/Unit tests/mocks/project_dirs/inside-n_m-nested/node_modules/foo/bar
find: ‘./neo4j/import’: Permission denied
./qwen-code/node_modules/requireindex/test/lib/bar
./images/grandfather/bar

shenjy@shenjy:~$ find . -path '**/*.sh' -type f
# 找到匹配的文件并删除
shenjy@shenjy:~$ find . -name "*.tmp" -exec rm {} \;
find: ‘./docker/vectordb/volumes/milvus/etcd’: Permission denied
find: ‘./neo4j/import’: Permission denied

Entering AI mode. Type your prompt (or 'exit' to quit):
prompt> introduce fd command in linux.

DeepSeek-v3:
# The `fd` Command in Linux

`fd` is a modern, user-friendly alternative to the traditional `find` command in Linux/Unix systems. It's designed to be faster and easier to use while offering sensible defaults.

## Key Features of `fd`

1. **Colorized output**: Results are displayed with syntax highlighting
2. **Fast search**: Uses parallelism for quicker results
3. **Smart case**: Case-insensitive by default, switches to case-sensitive if uppercase is used
4. **Ignores hidden files and `.gitignore` by default**: More focused results
5. **Simpler syntax**: Easier to remember commands

## Basic Usage

```bash
fd PATTERN [PATH]
```

## Common Options

| Option | Description |
|--------|-------------|
| `-H`/`--hidden` | Include hidden files/directories |
| `-I`/`--no-ignore` | Don't respect `.gitignore` files |
| `-t`/`--type` | Filter by type (`f`=file, `d`=directory, `l`=symlink) |
| `-e`/`--extension` | Filter by file extension |
| `-E`/`--exclude` | Exclude files/directories matching pattern |
| `-L`/`--follow` | Follow symbolic links |
| `-p`/`--full-path` | Search the full path (not just filename) |

## Examples

1. Find all Markdown files:
   ```bash
   fd '.md$'
   ```

2. Find Python files ignoring `.gitignore`:
   ```bash
   fd -I '.py$'
   ```

3. Find directories named 'config':
   ```bash
   fd -t d '^config$'
   ```

4. Find JPEG images:
   ```bash
   fd -e jpg -e jpeg
   ```

5. Case-sensitive search for 'README':
   ```bash
   fd 'README'
   ```

## Installation

Most Linux distributions don't include `fd` by default (it's often packaged as `fd-find`). Installation options:

- Ubuntu/Debian: `sudo apt install fd-find`
- Arch Linux: `sudo pacman -S fd`
- Fedora: `sudo dnf install fd-find`
- Via cargo (Rust): `cargo install fd-find`

Note: The command is typically `fdfind` on Debian/Ubuntu systems to avoid conflict with another package. You can alias it with `alias fd=fdfind`.

prompt> introduce fd command with tldr pattern.

DeepSeek-v3:
Here's the `fd` command introduced in the `tldr` pattern:

```markdown
# fd

A simple, fast, and user-friendly alternative to `find`.
More information: <https://github.com/sharkdp/fd>.

- Find files matching a specific pattern in the current directory:
  `fd "{{pattern}}"`

- Find files with a specific extension (e.g., `.txt`):
  `fd --extension "{{txt}}"`

- Find files in a specific directory (e.g., `/tmp`):
  `fd "{{pattern}}" {{/tmp}}`

- Include hidden and ignored files in the search:
  `fd --hidden --no-ignore "{{pattern}}"`

- Execute a command on each search result (e.g., `ls -l`):
  ` fd "{{pattern}}" --exec {{ls -l}}`

- Search for files matching a case-insensitive pattern (e.g., `PATTERN`):
  `fd --ignore-case "{{pattern}}"`

- Exclude specific files or directories from the search:
  `fd "{{pattern}}" --exclude "{{*.o}}" --exclude "{{/path/to/dir}}"`

- Show version information:
  `fd --version`
```

Key features highlighted:
- Simpler syntax than traditional `find`
- Colorized output by default
- Fast due to parallel directory traversal
- Sensible defaults (ignores hidden files and gitignored files by default)

`````

`locate` 是一个基于数据库的快速文件查找工具，它通过预先建立的索引来搜索文件，速度比 `find` 快很多。以下是详细使用方法：

---

### **一、locate 基本用法**

**1. 安装 locate**

```bash
# Debian/Ubuntu
sudo apt install mlocate

# CentOS/RHEL
sudo yum install mlocate

# Arch Linux
sudo pacman -S mlocate
```

### **2. 基本搜索语法**

```bash
locate [选项] <文件名或模式>
```

### **3. 常用示例**

```bash
# 搜索包含 "passwd" 的文件
locate passwd

# 搜索以 ".conf" 结尾的配置文件
locate "*.conf"

# 搜索特定路径下的文件
locate /etc/*.conf

# 精确搜索（使用 -b 只匹配文件名中的 basename）
locate -b "nginx.conf"
```

---

### **二、常用选项**

| **选项** | **作用**         | **示例**               |
| -------- | ---------------- | ---------------------- |
| `-i`     | 忽略大小写       | `locate -i README`     |
| `-c`     | 只显示匹配数量   | `locate -c "*.log"`    |
| `-l N`   | 限制输出数量     | `locate -l 10 "*.txt"` |
| `-r`     | 使用正则表达式   | `locate -r "\.conf$"`  |
| `-e`     | 只显示存在的文件 | `locate -e "tempfile"` |
| `-S`     | 显示数据库统计   | `locate -S`            |

### **示例组合**

```bash
# 忽略大小写，限制显示5个结果
locate -i -l 5 "readme"

# 统计系统中PDF文件的数量
locate -c "*.pdf"
```

---

### **三、更新 locate 数据库**

### **1. 手动更新数据库**

```bash
# 更新数据库（需要 root 权限）
sudo updatedb
```

**更新过程可能需要几秒到几分钟**，取决于文件系统大小。

### **2. 自动更新机制**

- 大多数 Linux 发行版会**每天自动更新一次** locate 数据库
- 通过 `cron` 任务定时执行，通常位于：
  ```bash
  # 查看自动更新任务
  cat /etc/cron.daily/mlocate  # 或 /etc/cron.daily/updatedb
  ```

### **3. 更新选项**

```bash
# 查看更新过程的详细信息
sudo updatedb -v

# 排除特定目录（如不索引临时文件）
sudo updatedb --prunepaths="/tmp /var/tmp"

# 只索引特定文件系统
sudo updatedb --localpaths="/home /etc"
```

---

### **四、数据库位置和信息**

```bash
# locate 数据库默认位置
ls -lh /var/lib/mlocate/mlocate.db

# 查看数据库统计信息
sudo updatedb --stats
# 或
locate -S
```

**输出示例**：

```
数据库 /var/lib/mlocate/mlocate.db：
  2,457 个目录
  248,921 个文件
  18.7 MB 实际文件大小
```

---

### **五、常见问题解决**

### **1. 文件已存在但 locate 找不到**

```bash
# 1. 先更新数据库
sudo updatedb

# 2. 再搜索
locate filename
```

### **2. 权限不足错误**

```bash
# 使用 sudo 更新数据库
sudo updatedb
```

### **3. 排除网络/虚拟文件系统**

编辑 `/etc/updatedb.conf`：

```bash
# 查看当前配置
cat /etc/updatedb.conf

# 典型配置示例
PRUNE_BIND_MOUNTS="yes"
PRUNEPATHS="/tmp /var/spool /media"
PRUNEFS="NFS afs autofs"
```

---

### **六、locate 与 find 对比**

| **特性**   | **locate**               | **find**                           |
| ---------- | ------------------------ | ---------------------------------- |
| **速度**   | 极快（基于数据库）       | 较慢（实时搜索）                   |
| **时效性** | 非实时（依赖数据库更新） | 实时                               |
| **功能**   | 简单文件名搜索           | 复杂条件搜索（时间、权限、大小等） |
| **资源**   | 占用内存（数据库）       | 占用 CPU/I/O                       |

### **使用场景建议**

- **用 locate**：快速查找已知名称的文件
- **用 find**：需要按时间、大小、权限等条件搜索时

---

### **七、实用技巧**

### **1. 创建别名快速更新并搜索**

```bash
# 添加到 ~/.bashrc
alias findup='sudo updatedb && locate'
```

### **2. 查找并统计某类文件**

```bash
# 查找所有 JPEG 图片并统计
locate "*.jpg" | wc -l

# 查找最近更改的文件（需要结合其他命令）
locate "*.log" | xargs ls -lh 2>/dev/null | head -20
```

### **3. 限制搜索范围**

```bash
# 只在 /home 目录中搜索
locate "*.pdf" | grep "^/home"
```

---

### **八、注意事项**

1. **隐私问题**：locate 数据库包含所有可访问文件路径，确保数据库文件权限为 `600`
2. **自动更新**：生产环境中可调整自动更新时间以避免高峰时段
3. **网络存储**：默认可能不索引 NFS 等网络文件系统，需在配置中调整

通过合理使用 `locate` 和定期更新数据库，可以极大提高文件搜索效率。对于新创建的文件，记得先运行 `sudo updatedb` 再搜索。

```shell
# 在指定文件重查找字符
grep foobar mcd.sh
# 在指定路径下查找有 foobar 字符的文件
grep -R foobar .
rg "import requests" -t py ~/scratch
rg -u --file-without-match "^#\!" -t sh
history
ctrl + R: 进行反向搜索
fzf
ls -R
tree
broot
nnn
```

## [Lecture 3 Vim Editor](https://missing.csail.mit.edu/2020/editors/)

> 中文版：https://missing-semester-cn.github.io/2020/editors/

![Vim 命令](https://coolshell.org/wp-content/uploads/2011/09/vim_cheat_sheet_for_programmers_print.png)

#### 相关文章

- [游戏：VIM 大冒险](https://coolshell.org/articles/7166.html/)
  - https://vim-adventures.com/
- [无插件 Vim 编程技巧](https://coolshell.org/articles/11312.html/)
- [给程序员的 VIM 速查卡](https://coolshell.org/articles/5479.html/)
- [简明 Vim 练级攻略](https://coolshell.org/articles/5426.html/)

### Vim 编辑器模式

![](https://github.com/user-attachments/assets/70bfc79c-1b76-45ef-a9b1-45ab732f0958)

Entering AI mode. Type your prompt (or 'exit' to quit):
prompt> Linux 程序 exit status 都有啥？

DeepSeek-v3:
在 Linux 中，程序的退出状态（exit status）是一个 0 到 255 之间的整数值，用于表示程序的终止状态。通常，退出状态为 0 表示成功，非零值表示错误或异常情况。以下是常见的退出状态及其含义：

---

### **标准退出状态**

1. **0**

   - **成功**：程序正常执行完毕，没有错误。

2. **1**

   - **通用错误**：未指定的错误（常见于脚本或命令行工具）。

3. **2**

   - **命令行用法错误**（例如`bash`中`Misuse of shell builtins`）。

4. **126**

   - **命令不可执行**：权限不足或文件不是可执行程序。

5. **127**

   - **命令未找到**：输入的命令或脚本不存在。

6. **128**
   - **无效退出参数**：`exit` 命令的参数不是整数或超出范围（0-255）。

---

### **信号相关的退出状态（128 + 信号编号）**

当程序被信号（Signal）终止时，退出状态为 **128 + 信号编号**：

- **130 (128 + SIGINT, 2)**：用户按下 `Ctrl+C` 中断程序。
- **137 (128 + SIGKILL, 9)**：进程被强制终止（`kill -9`）。
- **143 (128 + SIGTERM, 15)**：进程收到终止信号（默认的 `kill`）。

常见信号编号：
| 信号编号 | 信号名 | 含义 |
| -------- | ------- | ------------------ |
| 1 | SIGHUP | 终端挂断 |
| 2 | SIGINT | 键盘中断（Ctrl+C） |
| 9 | SIGKILL | 强制终止 |
| 15 | SIGTERM | 优雅终止 |

---

### **编程中自定义的退出状态**

开发者可以自定义非零退出状态（通常为 1-127）。例如：

- **64**：BSD 系统中用于表示命令行用法错误（类似`EX_USAGE`）。
- **其他值**：程序文档中会明确约定（如`grep`返回 1 表示未匹配到内容）。

---

### **如何获取退出状态？**

在 Shell 中通过 `$?` 查看上一个命令的退出状态：

```bash
ls /nonexistent
echo $?  # 输出2（文件不存在）
```

---

### **总结**

- **0** = 成功。
- **1-127** = 错误或自定义状态。
- **128+** = 信号终止（`128 + 信号编号`）。

具体程序的退出状态需参考其文档（如`man`手册）。

prompt> how to show line number in vim editor?

DeepSeek-v3:
To display line numbers in Vim, you can use the following methods:

### 1. **Enable Relative Line Numbers**

- Shows line numbers relative to the current line:
  ```vim
  :set relativenumber
  ```
- Short form:
  ```vim
  :set rnu
  ```

### 2. **Enable Absolute Line Numbers**

- Shows standard line numbers:
  ```vim
  :set number
  ```
- Short form:
  ```vim
  :set nu
  ```

### 3. **Enable Both Relative and Absolute Line Numbers**

- Current line shows absolute number, others show relative:
  ```vim
  :set relativenumber
  :set number
  ```
- Or combined:
  ```vim
  :set number relativenumber
  ```

### 4. **Disable Line Numbers**

```vim
:set nonumber
```

```vim
:set norelativenumber
```

- Short forms:
  ```vim
  :set nonu
  :set nornu
  ```

### 5. **Permanently Enable Line Numbers**

Add these lines to your `.vimrc` or `init.vim` (Neovim):

```vim
" For absolute line numbers
set number

" For hybrid mode (relative + absolute)
set relativenumber
set number
```

### Bonus

- Toggle line numbers quickly by adding a keybinding (e.g., `<Leader>n`) in your `.vimrc`:
  ```vim
  nnoremap <Leader>n :set number! relativenumber!<CR>
  ```
  Press `<Leader>n` to toggle line numbers on/off.

Let me know if you'd like further customization!

### **Normal Mode (Default Mode)**

These commands work when you start Vim or press `Esc`.

#### **Cursor Movement**

- `h`, `j`, `k`, `l` – Move (left, down, up, right)
- `w` / `b` – Move forward/backward by word
- `0` / `^` / `$` – Jump to start/start-of-line/end-of-line
- `gg` / `G` – Go to top/bottom of file
- `Ctrl + f` / `Ctrl + b` – Page down/page up
- `{` / `}` – Jump between paragraphs

#### **Editing**

- `x` – Delete character under cursor
- `dd` – Delete (cut) current line
- `yy` – Yank (copy) current line
- `p` / `P` – Paste after/before cursor
- `u` / `Ctrl + r` – Undo/Redo
- `.` – Repeat last edit

#### **Search & Replace**

- `/pattern` – Search forward
- `?pattern` – Search backward
- `n` / `N` – Next/previous match
- `:%s/old/new/g` – Replace all (`g` = global)
- `:%s/old/new/gc` – Replace with confirmation

#### **File Operations**

- `:w` – Save
- `:q` – Quit
- `:wq` or `:x` – Save and quit
- `:q!` – Quit without saving
- `:e filename` – Open another file

---

### **Insert Mode (Enter with `i`, `a`, etc.)**

- `i` – Insert before cursor
- `a` – Append after cursor
- `o` / `O` – Insert new line below/above
- `Esc` – Exit Insert Mode

---

### **Visual Mode (Enter with `v`, `V`, `Ctrl+v`)**

- `v` – Character-wise selection
- `V` – Line-wise selection
- `Ctrl + v` – Block selection
- `y` / `d` – Yank/delete selected text
- `>` / `<` – Indent/unindent

---

### **Command-Line Mode (`:` commands)**

- `:set nu` / `:set nonu` – Show/hide line numbers
- `:split filename` – Horizontal split
- `:vsplit filename` – Vertical split
- `:nohl` – Clear search highlights
- `:help command` – Open Vim help

---

### **Advanced**

- `Ctrl + w` + `hjkl` – Switch between split windows
- `*` / `#` – Search word under cursor
- `q<letter>` – Start recording macro to register `<letter>`
- `@<letter>` – Execute macro

---

### **Tips**

1. Combine commands with numbers:
   - `5dd` – Delete 5 lines
   - `3p` – Paste 3 times
2. Use `:!command` to run shell commands (e.g., `:!ls`).

在命令行中，"从头删除到尾" 可以理解为删除从光标当前位置到行首或行尾的内容，或者删除整行内容。以下是不同操作系统和终端环境下的常用方法：

---

### **1. 删除从光标位置到行首（删除开头部分）**

- **Linux/macOS (Bash, Zsh 等)**:
  `Ctrl + U`
  （多数 Unix-like 系统的默认设置）

- **Windows (CMD)**:
  无默认快捷键，但可以用：
  `Esc`（清除整行）或手动退格

- **Windows (PowerShell)**:
  `Ctrl + Home`

---

### **2. 删除从光标位置到行尾（删除末尾部分）**

- **Linux/macOS (Bash, Zsh 等)**:
  `Ctrl + K`

- **Windows (CMD)**:
  无直接快捷键，需手动用 `Del` 键或 `Esc`（清除整行）

- **Windows (PowerShell)**:
  `Ctrl + End`

---

### **3. 删除整行内容**

- **Linux/macOS/Windows (通用方法)**:
  连用 `Ctrl + U` + `Ctrl + K`（先删行首，再删行尾）
  或 `Ctrl + C` 终止当前行（会换新行）。

- **Windows (CMD)**:
  `Esc`

- **Windows (PowerShell)**:
  `Ctrl + C`（类似 Linux 行为）

---

### **4. 其他场景**

- **在文本编辑器（如 Vim）中**：

  - 删除到行首：`d0`
  - 删除到行尾：`d$` 或 `D`

- **在 Emacs 风格的终端中**：
  - 行首：`Ctrl + A` → `Ctrl + K`
  - 行尾：直接 `Ctrl + K`

---

### **注意事项**

- 不同终端或 Shell（如 Fish、Tcsh）可能有差异，可通过 `stty -a`（Linux/macOS）查看当前键位绑定。
- 如果修改过终端配置（如`.bashrc`），快捷键可能被覆盖。

希望这些方法能覆盖你的需求！如果有特定环境需要进一步说明，请补充提问。

prompt> tell me tmux command line usage with tldr pattern.

DeepSeek-v3:
Here's a `tldr`-style cheat sheet for `tmux`:

### **Basic Commands**

```sh
tmux                      # Start new session
tmux new -s <name>        # Start new named session
tmux ls                   # List sessions
ctrl-b d                  # Detach from session
tmux attach -t <name>     # Reattach to session
tmux kill-session -t <name> # Kill session
```

### **Session Management**

```sh
ctrl-b $                  # Rename session
ctrl-b s                  # Switch sessions
ctrl-b (                  # Move to previous session
ctrl-b )                  # Move to next session
```

### **Window Management**

```sh
ctrl-b c                  # Create new window
ctrl-b ,                  # Rename window
ctrl-b p                  # Previous window
ctrl-b n                  # Next window
ctrl-b 0-9                # Switch to window by number
ctrl-b &                  # Kill current window
```

### **Pane Management**

```sh
ctrl-b "                  # Split pane horizontally
ctrl-b %                  # Split pane vertically
ctrl-b arrow key          # Switch pane
ctrl-b z                  # Toggle pane zoom
ctrl-b x                  # Kill current pane
ctrl-b {                  # Move pane left
ctrl-b }                  # Move pane right
ctrl-b space              # Toggle layouts
```

### **Copy Mode (Scroll/Buffer)**

```sh
ctrl-b [                  # Enter copy mode
q                         # Exit copy mode
↑ ↓ ← →                   # Navigate
ctrl-u / ctrl-d           # Half-page up/down
/ or ?                    # Search (n/N to navigate)
space                     # Start selection
enter                     # Copy selection
ctrl-b ]                  # Paste
```

### **Miscellaneous**

```sh
ctrl-b :                  # Enter command mode
ctrl-b t                  # Show clock
ctrl-b ?                  # Show key bindings
ctrl-b r                  # Reload config (~/.tmux.conf)
tmux -V                   # Show version
```

🔥 **Pro Tip**: Default prefix is `ctrl-b`, but many users remap it to `ctrl-a` (add `set -g prefix C-a` in `~/.tmux.conf`).

Want a specific deep dive on any part? 🚀

在 Linux 或 Unix 系统中，`jobs`命令用于查看当前 shell 会话中正在运行或暂停（后台）的作业（jobs）。以下是它的基本用法和示例：

---

### **1. 基本命令**

```bash
jobs
```

**作用**：列出当前终端会话中的所有作业，并显示它们的状态（运行中、暂停、终止等）和作业编号（如 `[1]`, `[2]`）。

示例输出：

```
[1]-  Running                 sleep 100 &
[2]+  Stopped                 vim file.txt
```

- `[n]`：作业编号。
- `+`：默认作业（最近一次放到后台的作业）。
- `-`：次默认作业（倒数第二个放到后台的作业）。
- `Running`/`Stopped`：作业状态。

---

### **2. 常用选项**

| 选项 | 作用                            |
| ---- | ------------------------------- |
| `-l` | 显示作业的 PID（进程 ID）。     |
| `-p` | 仅显示作业的 PID。              |
| `-r` | 仅显示运行中的作业（Running）。 |
| `-s` | 仅显示暂停的作业（Stopped）。   |

示例：

```bash
jobs -l    # 显示作业及其PID
jobs -r    # 仅显示运行中的作业
```

---

### **3. 结合作业控制命令**

- **将命令放到后台运行**：

  ```bash
  command &
  ```

  例如：

  ```bash
  sleep 100 &  # 后台运行sleep命令
  ```

- **暂停当前前台作业**：
  按 `Ctrl + Z`，作业会变为`Stopped`状态。

- **恢复作业**：

  - 恢复到前台：\*\*\*\*
    ```bash
    fg %n    # n是作业编号，如 fg %1
    ```
  - 恢复到后台继续运行：
    ```bash
    bg %n    # 如 bg %2
    ```

- **终止作业**：

  ```bash
  kill %n    # 如 kill %1
  ```

---

### **4. 使用场景示例**

1. **启动一个后台作业**：

   ```bash
   sleep 300 &
   ```

2. **查看作业列表**：

   ```bash
   jobs -l
   ```

   输出：

   ```
   [1]+ 12345 Running                 sleep 300 &
   ```

3. **暂停一个前台作业**：

   - 运行 `vim file.txt`。
   - 按 `Ctrl + Z` 暂停，状态变为 `Stopped`。

4. **恢复暂停的作业到后台**：

   ```bash
   bg %2    # 假设vim是作业2
   ```

5. **终止作业**：
   ```bash
   kill %1  # 终止作业1（sleep命令）
   ```

`tmux` 是一个终端复用工具，可以在单个终端窗口中管理多个终端会话，且会话在断连后仍能保持运行。以下是基本使用方法：

---

### **1. 安装 tmux**

- **Linux (Debian/Ubuntu)**:
  ```bash
  sudo apt-get install tmux
  ```
- **macOS**:
  ```bash
  brew install tmux
  ```

---

### **2. 基本操作**

#### **启动 tmux**

```bash
tmux
```

或指定会话名称：

```bash
tmux new -s session_name
```

#### **断开当前会话（保持后台运行）**

- 快捷键：`Ctrl + B`，然后按 `D`
  （先按 `Ctrl+B`，松手后再按 `D`）

#### **重新连接会话**

```bash
tmux attach -t session_name
```

或使用会话 ID：

```bash
tmux attach -t 0
```

#### **查看所有会话**

```bash
tmux ls
```

#### **关闭会话**

- 在会话内直接输入 `exit` 或 `Ctrl + D`。
- 从外部关闭：
  ```bash
  tmux kill-session -t session_name
  ```

---

### **3. 窗口（Window）管理**

- **新建窗口**：`Ctrl + B`，然后按 `C`
- **切换窗口**：
  - 下一个窗口：`Ctrl + B` + `N`
  - 上一个窗口：`Ctrl + B` + `P`
  - 选择窗口编号：`Ctrl + B` + `0~9`
- **重命名窗口**：`Ctrl + B` + `,`，输入新名称后回车。
- **关闭窗口**：`Ctrl + B` + `&`

---

### **4. 面板（Pane）管理**

- **水平分割**：`Ctrl + B` + `"`
- **垂直分割**：`Ctrl + B` + `%`
- **切换面板**：`Ctrl + B` + 方向键（`↑↓←→`）
- **关闭面板**：`Ctrl + B` + `X`，或直接输入 `exit`
- **调整面板大小**：按住 `Ctrl + B` + `Alt + 方向键`

---

### **5. 其他实用功能**

- **滚动查看历史输出**：`Ctrl + B` + `[`，用方向键滚动，按 `Q` 退出。
- **复制模式**：
  1. `Ctrl + B` + `[` 进入复制模式。
  2. 用方向键移动光标，按 `Space` 开始选择，`Enter` 复制。
  3. 粘贴：`Ctrl + B` + `]`。
- **同步输入到所有面板**（广播模式）：
  - 开启：`Ctrl + B` + `:set synchronize-panes on`
  - 关闭：`:set synchronize-panes off`

---

### **6. 配置文件**

自定义配置（如快捷键、主题）需编辑 `~/.tmux.conf`。例如：

```plaintext
# 设置前缀键为 Ctrl+A（默认是 Ctrl+B）
set -g prefix C-a
unbind C-b
bind C-a send-prefix

# 启用鼠标支持（切换窗口/调整面板大小）
set -g mouse on

# 设置状态栏颜色
set -g status-bg blue
```

重新加载配置：

```bash
tmux source-file ~/.tmux.conf
```

---

### **7. 小技巧**

- **快速创建会话并运行命令**：
  ```bash
  tmux new -s mysession "ping example.com"
  ```
- **嵌套会话**：在 tmux 会话内再启动 tmux 时，用 `Ctrl + B` + `D` 逐层退出。

---

掌握以上操作后，你可以高效管理多任务终端，尤其适合远程服务器维护或长时间运行任务。更多功能可通过 `man tmux` 查看手册。

## [Lecture 4 Data Wrangling](https://missing.csail.mit.edu/2020/data-wrangling/)

> 中文版本：https://missing-semester-cn.github.io/2020/data-wrangling/

```shell
ssh tsp journalctl
```

todo...
