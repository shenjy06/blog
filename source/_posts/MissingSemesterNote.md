---
title: Missing Semester of Your CS Education Note
date: 2025-12-10 19:54:30
tags:
  - Linux
---

> Linux command line tldr: https://tldr.sh
>
> 课程 2026 年度更新：https://missing.csail.mit.edu/
>
> Linux man pages online: https://man7.org/linux/man-pages/index.html

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
> Vim 编辑器搜索和替换：https://vim.fandom.com/wiki/Search_and_replace

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

| 信号编号 | 信号名  | 含义               |
| -------- | ------- | ------------------ |
| 1        | SIGHUP  | 终端挂断           |
| 2        | SIGINT  | 键盘中断（Ctrl+C） |
| 9        | SIGKILL | 强制终止           |
| 15       | SIGTERM | 优雅终止           |

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

### AWK 相关文章

- [AWK 简明教程](https://coolshell.org/articles/9070.html/)

### SED 简明教程

- [sed 简明教程](https://coolshell.org/articles/9104.html/)

### 📝 journalctl

**系统日志查看器（Systemd 日志管理工具）**
用于查询和显示 `systemd-journald` 收集的日志。

#### 1️⃣ 基本用法

```bash
journalctl                      # 查看所有日志（默认分页显示）
journalctl -n 20                # 查看最新 20 条日志
journalctl -f                   # 实时追踪日志（类似 tail -f）
journalctl --since "2023-01-01" --until "2023-01-02"  # 按时间范围筛选
```

#### 2️⃣ 按服务/单元过滤

```bash
journalctl -u nginx.service     # 查看指定服务的日志
journalctl -u docker --since today  # 查看当天 Docker 的日志
```

#### 3️⃣ 按优先级过滤

```bash
journalctl -p err               # 仅显示错误（及以上级别）
journalctl -p warning -b        # 本次启动后的所有警告日志
```

**优先级级别**: `emerg`(0), `alert`(1), `crit`(2), `err`(3), `warning`(4), `notice`(5), `info`(6), `debug`(7)

#### 4️⃣ 按启动周期

```bash
journalctl -b                   # 本次启动的日志
journalctl -b -1                # 上一次启动的日志（-2 为上上次，类推）
```

#### 5️⃣ 高级选项

```bash
journalctl --disk-usage         # 显示日志占用的磁盘空间
journalctl --vacuum-size=500M   # 清理日志至剩余 500MB
journalctl -k                   # 仅显示内核日志
journalctl --output=json        # 以 JSON 格式输出（适合脚本处理）
```

#### 🎯 常用组合

```bash
journalctl -p err --since yesterday  # 昨天至今的所有错误
journalctl -u sshd -p crit           # SSH 服务的严重错误
journalctl -f -u apache              # 实时监控 Apache 日志
```

---

### 💡 小技巧

- 用 `+`/`-` 翻页，按 `q` 退出。
- 组合 `-x` 可显示更详细的解释（如错误代码说明）。

### `awk`

**文本处理工具**，支持模式匹配和字段处理。

#### 基本用法

```bash
awk 'pattern {action}' 文件
```

#### 常见示例

1. **打印文件的每一行**：

   ```bash
   awk '{print}' file.txt
   ```

2. **打印指定字段（默认按空格/TAB 分割）**：

   ```bash
   awk '{print $1, $3}' file.txt  # 打印第1和第3列
   ```

3. **指定分隔符**（如逗号）：

   ```bash
   awk -F ',' '{print $2}' file.csv
   ```

4. **匹配包含模式的行**（如含 "error" 的行）：

   ```bash
   awk '/error/ {print}' file.log
   ```

5. **内置变量**：

   ```bash
   awk '{print NR, $0}' file.txt  # NR=行号，$0=整行
   ```

6. **条件判断**（如第 2 列大于 100）：

   ```bash
   awk '$2 > 100 {print $1}' data.txt
   ```

7. **求和与统计**（如第 3 列总和）：

   ```bash
   awk '{sum += $3} END {print sum}' data.txt
   ```

8. **BEGIN/END 块**（预处理和后处理）：
   ```bash
   awk 'BEGIN {print "Start"} {print} END {print "End"}' file.txt
   ```

#### 常用选项

- `-F`：指定输入字段分隔符（默认空格/TAB）。
- `-v`：定义变量（如 `-v var=value`）。

---

这种格式符合 `tldr` 的简洁风格，适合快速查阅。如需更复杂的功能，可结合 `man awk` 进一步学习。

### `sed` - 流编辑器，用于文本替换、删除、插入等操作

#### 基本语法：

```bash
sed [选项] '动作' 文件名
```

#### 常用选项：

- `-i` : 原地修改文件（慎用，建议先不加 `-i` 测试）
- `-n` : 禁止默认输出，常与 `p` 动作配合
- `-E` : 使用扩展正则表达式（或 `-r`）

#### 示例：

1. **替换文本**（首次出现）：

   ```bash
   sed 's/原内容/新内容/' file.txt
   ```

2. **全局替换**：

   ```bash
   sed 's/原内容/新内容/g' file.txt
   ```

3. **删除行**：

   ```bash
   sed '3d' file.txt          # 删除第3行
   sed '/pattern/d' file.txt  # 删除匹配的行
   ```

4. **原地修改文件**（直接保存）：

   ```bash
   sed -i 's/原内容/新内容/g' file.txt
   ```

5. **打印特定行**：

   ```bash
   sed -n '5p' file.txt       # 仅打印第5行
   sed -n '/pattern/p' file.txt # 打印匹配的行
   ```

6. **多命令组合**（用分号分隔）：

   ```bash
   sed 's/foo/bar/; s/hello/world/' file.txt
   ```

7. **从文件读取命令**（适用于复杂操作）：
   ```bash
   sed -f script.sed file.txt
   ```

#### 小贴士：

- 默认情况下 `sed` 输出到终端，不会直接修改原文件。
- 使用 `^` 和 `$` 匹配行首/行尾。
- 特殊字符（如 `/`）需用反斜杠转义：`\/`。

---

\> 📌 提示：运行 `tldr sed` 可获取更多社区整理的快速示例（需先安装 `tldr` 工具）。

```shell
shenjy@shenjy:~/msoce/data_wrangling$ tldr awk

  awk

  A versatile programming language for working on files.
  Note: Different implementations of AWK often make this a symlink of their binary.
  See also: `gawk`.
  More information: https://github.com/onetrueawk/awk.

  - Print the fifth column (a.k.a. field) in a space-separated file:
    awk '{print $5}' path/to/file

  - Print the second column of the lines containing "foo" in a space-separated file:
    awk '/foo/ {print $2}' path/to/file

  - Print the last column of each line in a file, using a comma (instead of space) as a field separator:
    awk -F ',' '{print $NF}' path/to/file

  - Sum the values in the first column of a file and print the total:
    awk '{s+=$1} END {print s}' path/to/file

  - Print every third line starting from the first line:
    awk 'NR%3==1' path/to/file

  - Print different values based on conditions:
    awk '{if ($1 == "foo") print "Exact match foo"; else if ($1 ~ "bar") print "Partial match bar"; else print "Baz"}' path/to/file

  - Print all the lines which the 10th column value is between a min and a max:
    awk '($10 >= min_value && $10 <= max_value)'

  - Print table of users with UID >=1000 with header and formatted output, using colon as separator (%-20s mean: 20 left-align string characters, %6s means: 6 right-align string characters):
    awk 'BEGIN {FS=":";printf "%-20s %6s %25s\n", "Name", "UID", "Shell"} $4 >= 1000 {printf "%-20s %6d %25s\n", $1, $4, $7}' /etc/passwd

shenjy@shenjy:~/msoce/data_wrangling$ tldr sed

  sed

  GNU stream editor for filtering and transforming text.
  See also: `awk`, `ed`.
  More information: https://www.gnu.org/software/sed/manual/sed.html.

  - Replace "apple" with "mango" on all lines using basic regex, print to stdout:
    command | sed 's/apple/mango/g'

  - Replace "apple" with "APPLE" on all lines using extended regex, print to stdout:
    command | sed --regexp-extended 's/(apple)/\U\1/g'

  - Use basic regex to replace "apple" with "mango" and "orange" with "lime" in-place in a file (overwriting original file):
    sed --in-place -e 's/apple/mango/g' -e 's/orange/lime/g' path/to/file

  - Execute a specific sed script file and print the result to stdout:
    command | sed --file path/to/script.sed

  - [p]rint only the first line to stdout:
    command | sed --quiet '1p'

  - [d]elete lines 1 to 5 of a file and back up the original file with a .orig extension:
    sed --in-place=.orig '1,5d' path/to/file

  - [i]nsert a new line at the beginning of a file, overwriting the original file in-place:
    sed --in-place '1i\your new line text\' path/to/file

  - Delete blank lines (with or without spaces/tabs) from a file, overwriting the original file in-place:
    sed --in-place '/^[[:space:]]*$/d' path/to/file

Found 5 pages with the same name under the platforms: common, netbsd, openbsd, freebsd, osx

shenjy@shenjy:~/msoce/data_wrangling$ tldr ed

  ed

  The original Unix text editor.
  See also: `awk`, `sed`.
  More information: https://www.gnu.org/software/ed/manual/ed_manual.html.

  - Start an interactive editor session with an empty document:
    ed

  - Start an interactive editor session with an empty document and a specific prompt:
    ed --prompt '> '

  - Start an interactive editor session with user-friendly errors:
    ed --verbose

  - Start an interactive editor session with an empty document and without diagnostics, byte counts and '!' prompt:
    ed --quiet --script

  - Start an interactive editor session without exit status change when command fails:
    ed --loose-exit-status

  - Edit a specific file (this shows the byte count of the loaded file):
    ed path/to/file

  - Replace a string with a specific replacement for all lines:
    ,s/regex/replacement/g<Enter>

  - Exit ed:
    q<Enter>

Found 1 page with the same name under the platform: osx\

shenjy@shenjy:~/msoce/data_wrangling$ echo 'aba' | sed 's/[ab]//'
ba
# 输入字符串是 "a b a"（字母顺序为：第1个a、第1个b、第2个a）。
# `sed` 会从左到右扫描，找到第一个匹配 `[ab]` 的字符（即第一个 `a`），然后将其删除。
# 后续的 `b` 和第二个 `a` 不会被处理（因为缺少全局替换标志 `g`）。

shenjy@shenjy:~/msoce/data_wrangling$ echo 'bba' | sed 's/[ab]//'
ba
# 输入字符串是 "b b a"（字母顺序为：第1个b、第1个b、第2个a）。
# `sed` 会从左到右扫描，找到第一个匹配 `[ab]` 的字符（即第一个 `b`），然后将其删除。
# 后续的 `b` 和第二个 `a` 不会被处理（因为缺少全局替换标志 `g`）。

shenjy@shenjy:~/msoce/data_wrangling$ echo 'bba' | sed 's/[ab]//g'
shenjy@shenjy:~/msoce/data_wrangling$ echo 'bbac' | sed 's/[ab]//g'
c
shenjy@shenjy:~/msoce/data_wrangling$ echo 'bcbzac' | sed 's/[ab]//g'
czc
shenjy@shenjy:~/msoce/data_wrangling$ echo 'abcaba' | sed 's/(ab)//g'
abcaba
shenjy@shenjy:~/msoce/data_wrangling$ echo 'abcaba' | sed 's/(ab)*//g'
abcaba
shenjy@shenjy:~/msoce/data_wrangling$ echo 'abcaba' | sed -E 's/(ab)*//g'
ca
shenjy@shenjy:~/msoce/data_wrangling$ echo 'abcaba' | sed 's/\(ab\)*//g'
ca
shenjy@shenjy:~/msoce/data_wrangling$ echo 'abcabaabc' | sed -E 's/(ab|bc)*//g'
cac
shenjy@shenjy:~/msoce/data_wrangling$ echo 'abcababc' | sed -E 's/(ab|bc)*//g'
cc
shenjy@shenjy:~/msoce/data_wrangling$ echo 'abcabbc' | sed -E 's/(ab|bc)*//g'
c
```

- [regular expressions](https://regex101.com/)
- [正则表达式](https://www.regular-expressions.info/)
- [url-regex](https://mathiasbynens.be/demo/url-regex)
- [Comparing E-mail Address Validating Regular Expressions](https://fightingforalostcause.net/content/misc/2006/compare-email-regex.php)
- [emailregex](https://emailregex.com/)

### **1. sort 命令**

**功能**：对文本文件的行进行排序（默认按字典序，排序分为字典序和）。
**常见场景**：

- 排序日志文件的时间戳
- 对 CSV 文件的某一列排序
- 作为去重（`uniq`）的前置步骤（因为`uniq`要求输入必须是有序的）

**常用选项**：

- `-n`：按数值排序
- `-r`：倒序排序
- `-k`：指定排序的列（如`-k 2`表示按第二列排序）

**示例**：

```bash
# 对文件内容按字典序排序
sort file.txt
```

---

### **2. uniq 命令**

**功能**：过滤或统计连续的重复行。
**常见场景**：

- 统计日志中重复出现的错误
- 提取唯一值（需先排序）
- 计数重复行数量

**常用选项**：

- `-c`：统计每行重复出现的次数
- `-d`：仅输出重复行
- `-u`：仅输出唯一行（不重复的行）

**注意**：`uniq`只能处理相邻的重复行，因此通常需先通过`sort`排序。

**示例**：

```bash
# 先排序，再统计重复行次数
sort file.txt | uniq -c
```

---

### **3. wc 命令**

**功能**：统计文件的行数、单词数和字符数。
**常见场景**：

- 统计代码行数
- 计算日志文件的总条目数
- 统计文本的单词量

**常用选项**：

- `-l`：仅统计行数
- `-w`：仅统计单词数
- `-c`：仅统计字节数

**示例**：

```bash
# 统计文件行数
wc -l file.txt
```

---

### **管道组合使用示例**

**需求**：统计一个日志文件中各错误类型的出现次数，并按频率倒序排列：

```bash
# 假设日志的每一行包含一个错误类型（如 "ERROR: xxx"）
grep "ERROR:" logfile.txt | cut -d' ' -f2 | sort | uniq -c | sort -nr
```

**步骤解析**：

1. `grep "ERROR:"`：过滤出包含错误日志的行。
2. `cut -d' ' -f2`：提取错误类型（假设第二列为错误名）。
3. `sort`：排序，使相同错误名连续排列。
4. `uniq -c`：统计每个错误名的出现次数。
5. `sort -nr`：按次数倒序排列。

---

**另一个例子**：统计文本中不同单词的数量：

```bash
# 拆分单词、排序、去重、计数
tr ' ' '\n' < text.txt | sort | uniq | wc -l
```

**步骤解析**：

1. `tr ' ' '\n'`：将空格替换为换行，使每个单词单独成行。
2. `sort`：排序以便`uniq`去重。
3. `uniq`：去除重复单词。
4. `wc -l`：统计剩余行数（即唯一单词数量）。

---

通过管道组合这些命令，可以实现高效的文本批处理，满足日志分析、数据清洗等需求
以下是 `paste` 命令的 **tldr (Too Long; Didn't Read)** 风格介绍，简洁明了：

---

### `paste`

**合并文件或输入的行**
将多个文件的行按列合并，默认用制表符分隔。

#### 常用用法：

```bash
paste 文件1 文件2            # 合并两个文件的行
paste -d ',' 文件1 文件2     # 指定分隔符（例中用逗号）
paste -s 文件                # 将文件的所有行合并为一行
paste -d '\t' - - -          # 从标准输入合并3列（用Tab分隔）
```

#### 选项：

- `-d `：指定分隔符（默认是制表符 `\t`），例如 `-d ','`。
- `-s` ：将输入的所有行合并为一行。
- `-` ：从标准输入读取（通常结合管道使用）。

#### 示例：

```bash
$ echo -e "a\nb\nc" | paste -s -d ','
a,b,c
$ paste 文件1.txt 文件2.txt > 合并.txt
```

```shell
shenjy@shenjy:~/msoce/data_wrangling$ tldr paste

  paste

  Merge lines of files.
  More information: https://www.gnu.org/software/coreutils/manual/html_node/paste-invocation.html.

  - Join all the lines into a single line, using TAB as delimiter:
    paste --serial path/to/file

  - Join all the lines into a single line, using the specified delimiter:
    paste --serial --delimiters delimiter path/to/file

  - Merge two files side by side, each in its column, using TAB as delimiter:
    paste path/to/file1 path/to/file2

  - Merge two files side by side, each in its column, using the specified delimiter:
    paste --delimiters delimiter path/to/file1 path/to/file2

  - Merge two files, with lines added alternatively:
    paste --delimiters '\n' path/to/file1 path/to/file2

shenjy@shenjy:~/msoce/data_wrangling$ for i in {1..100000}; do echo $i >> num.txt; done
shenjy@shenjy:~/msoce/data_wrangling$ cat num.txt | awk '{print $1}' | R --slave -e 'x <- scan(file="stdin", quiet=TRUE); summary(x)'
   Min. 1st Qu.  Median    Mean 3rd Qu.    Max.
      1   25001   50000   50000   75000  100000

cat num.txt | uniq -c | sort | tail -n5 | gnuplot -p -e 'set boxwidth 0.5; plot "-" using 1:xtic(2) with boxes'
```

Here’s a comprehensive list of command-line tools and utilities commonly used for data wrangling tasks, organized by functionality:

---

### **1. File Inspection**

- **`head` / `tail`**: View top/bottom lines of a file.
  ```bash
  head -n 10 file.csv  # First 10 lines
  tail -n 5 file.log   # Last 5 lines
  ```
- **`less` / `more`**: Scroll through files interactively.
  ```bash
  less large_file.txt
  ```
- **`wc`**: Count lines, words, or characters.
  ```bash
  wc -l data.csv       # Line count
  ```
- **`file`**: Check file type/encoding.
  ```bash
  file data.csv
  ```
- **`stat`**: File metadata (size, modification time).
  ```bash
  stat report.txt
  ```

---

### **2. Text Processing**

- **`grep`**: Search patterns in files.
  ```bash
  grep "error" log.txt       # Find lines with "error"
  grep -v "debug" log.txt    # Exclude lines with "debug"
  ```
- **`sed`**: Stream editor (find/replace, deletions).
  ```bash
  sed 's/old/new/g' file.txt       # Replace text
  sed '1,5d' file.txt              # Delete lines 1-5
  ```
- **`awk`**: Powerful text processing (column manipulation).
  ```bash
  awk '{print $1, $3}' data.txt    # Print 1st and 3rd columns
  awk -F',' '{sum += $2} END {print sum}' file.csv  # Sum column 2
  ```
- **`cut`**: Extract columns.
  ```bash
  cut -d',' -f1,3 file.csv        # Split by comma, columns 1 & 3
  ```
- **`tr`**: Translate/delete characters.
  ```bash
  tr 'a-z' 'A-Z' < file.txt       # Convert to uppercase
  ```
- **`sort`**: Sort lines.
  ```bash
  sort -k2 -n data.txt            # Sort by 2nd column (numeric)
  ```
- **`uniq`**: Deduplicate lines (use with `sort`).
  ```bash
  sort file.txt | uniq -c         # Count unique lines
  ```

---

### **3. CSV/JSON/XML Tools**

- **`csvkit`** (Toolsuite):
  ```bash
  csvstat data.csv                # Summary stats
  csvcut -c col1,col2 data.csv    # Select columns
  csvjoin -c id file1.csv file2.csv  # Join CSVs
  ```
- **`jq`**: JSON processing.
  ```bash
  jq '.key' file.json             # Extract key
  jq '.[] | select(.price > 100)' data.json  # Filter
  ```
- **`xsv`**: Fast CSV toolkit (Rust-based).
  ```bash
  xsv select col1,col2 data.csv   # Select columns
  xsv stats data.csv              # Statistics
  ```
- **`xmlstarlet`**: XML processing.
  ```bash
  xmlstarlet sel -t -v "//title" file.xml  # Extract titles
  ```

---

### **4. Data Transformation**

- **`paste`**: Merge lines from files.
  ```bash
  paste file1.txt file2.txt > combined.txt
  ```
- **`join`**: Combine files by a common column.
  ```bash
  join -t',' file1.csv file2.csv  # Join on first column
  ```
- **`column`**: Format into columns.
  ```bash
  column -t -s',' data.csv        # Tabular format
  ```

---

### **5. File Conversion**

- **`iconv`**: Convert text encoding.
  ```bash
  iconv -f utf8 -t ascii file.txt
  ```
- **`dos2unix`**: Convert line endings (Windows → Unix).
  ```bash
  dos2unix file.txt
  ```

---

### **6. Network Data Fetching**

- **`curl` / `wget`**: Download files.
  ```bash
  curl -O https://example.com/data.json
  wget https://example.com/data.csv
  ```
- **`jq` + `curl`**: Process API responses.
  ```bash
  curl -s https://api.example.com/data | jq '.results'
  ```

---

### **7. Compression/Archives**

- **`gzip` / `gunzip`**: Compress/decompress.
  ```bash
  gzip data.txt                   # Compress to .gz
  gunzip data.txt.gz              # Decompress
  ```
- **`tar`**: Archive files.
  ```bash
  tar -czvf archive.tar.gz /path  # Create .tar.gz
  tar -xzvf archive.tar.gz        # Extract
  ```

---

### **8. System Monitoring**

- **`du`**: Disk usage.
  ```bash
  du -sh *                        # Human-readable sizes
  ```
- **`df`**: Free disk space.
  ```bash
  df -h
  ```

---

### **9. Advanced Tools**

- **`datamash`**: Statistics (group by, sums, etc.).
  ```bash
  datamash -t, groupby 1 sum 2 < data.csv
  ```
- **`q` (q-text-as-data)**: SQL on CSVs.
  ```bash
  q "SELECT * FROM data.csv WHERE col1 > 100"
  ```
- **`Miller`**: Like `awk` for structured data.
  ```bash
  mlr --csv cut -f col1 then sort -n col2 file.csv
  ```

---

### **10. Pipeline Examples**

- Count unique IPs in a log:
  ```bash
  awk '{print $1}' access.log | sort | uniq -c | sort -nr
  ```
- CSV to JSON conversion:
  ```bash
  csvjson data.csv > data.json
  ```
- Find top 5 largest files:
  ```bash
  find /path -type f -exec du -h {} + | sort -rh | head -n 5
  ```

---

### **Note**: Combine tools using pipes (`|`) for complex workflows. Install tools via package managers (e.g., `apt`, `brew`, `pip`). For large datasets, prefer tools like `xsv` or `q` for speed.

Here's a `tldr`-style summary for the `rustup` command in Linux:

```text
rustup

The Rust toolchain installer and manager.

- Install a specific stable version:
  rustup install stable

- Switch to a specific version (e.g., nightly):
  rustup default nightly

- Update all installed toolchains:
  rustup update

- List installed toolchains:
  rustup toolchain list

- Install a component (e.g., rust-src for the current toolchain):
  rustup component add rust-src

- Show the currently active Rust version:
  rustup show

- Install a target for cross-compilation (e.g., for ARM):
  rustup target add arm-unknown-linux-gnueabihf

- Install rustup completions for your shell:
  rustup completions bash|fish|zsh
```

This follows the tldr format with:

1. Brief description
2. Most common commands in a simplified form
3. Clear examples with placeholders where needed

Would you like any modifications to this format or additional common commands included?

## [Lecture 5 Command-line Environmen](https://missing.csail.mit.edu/2020/command-line/)

> 中文版本：https://missing-semester-cn.github.io/2020/command-line/

- Job Controll
- tmux
- Dotfiles
- Remote machine

````markdown
## 主要内容

### 1. 作业控制（Job Control）

在终端中管理多个任务（进程）的运行状态。

- **`Ctrl+Z`**: 暂停当前前台进程（挂起），并放入后台。
- **`fg`**: 将最近暂停的作业恢复到前台继续运行。
- **`bg`**: 让暂停的作业在后台继续运行（需先 `Ctrl+Z`）。
- **`jobs`**: 查看当前 shell 中的所有作业（包括后台和暂停的任务）。

示例：

```bash
# 运行一个耗时命令（如编辑器）
vim file.txt
# 按 Ctrl+Z 暂停
[1]+  Stopped        vim file.txt

jobs
# 输出：[1]+ stopped  vim file.txt

fg %1   # 恢复第一个作业到前台
bg %1   # 在后台运行（适用于可输出的命令）
```
````

---

### 2. 终端复用器 —— `tmux`

`tmux` 是一个强大的终端复用工具，允许你在一个终端窗口中运行多个会话、窗格和窗口，并支持断开后重新连接。

#### 基本概念

- **Session（会话）**: 一次独立的终端工作环境，可在后台运行。
- **Window（窗口）**: 类似浏览器标签页，在一个会话中可创建多个。
- **Pane（窗格）**: 分屏功能，一个窗口可分割为多个区域。

#### 常用命令

| 命令                             | 说明             |
| -------------------------------- | ---------------- |
| `tmux`                           | 新建一个匿名会话 |
| `tmux new -s mysession`          | 创建命名会话     |
| `tmux ls`                        | 列出所有会话     |
| `tmux attach -t mysession`       | 重新连接指定会话 |
| `tmux kill-session -t mysession` | 删除会话         |

**快捷键前缀**：默认是 `Ctrl+B`，之后按以下键：

| 快捷键    | 功能                            |
| --------- | ------------------------------- |
| `%`       | 垂直分屏                        |
| `"`       | 水平分屏                        |
| `→ ← ↑ ↓` | 切换窗格                        |
| `c`       | 新建窗口                        |
| `n` / `p` | 切换下一个/上一个窗口           |
| `d`       | 脱离当前会话（detach）          |
| `:`       | 输入命令（如 `rename-session`） |

> 💡 推荐配置 `.tmux.conf` 文件来自定义快捷键、启用鼠标等。

---

### 3. 配置文件（Dotfiles）

以点开头的隐藏文件（如 `.bashrc`, `.vimrc`, `.gitconfig`），用于自定义你的命令行环境。

#### 常见 dotfiles

| 文件                 | 用途                                      |
| -------------------- | ----------------------------------------- |
| `.bashrc` / `.zshrc` | Shell 启动脚本，设置别名、环境变量        |
| `.gitconfig`         | Git 全局配置                              |
| `.vimrc`             | Vim 编辑器配置                            |
| `.tmux.conf`         | Tmux 配置文件                             |
| `.inputrc`           | Readline 行编辑行为（如启用上下搜索历史） |

#### 管理建议

- 使用 Git 托管你的 dotfiles：
  ```bash
  git init --bare $HOME/.dotfiles
  alias config='git --git-dir=$HOME/.dotfiles --work-tree=$HOME'
  config add .bashrc .vimrc
  config commit -m "Add bash and vim config"
  config push origin main
  ```
- 在新机器上快速部署：
  ```bash
  git clone --bare https://github.com/yourname/dotfiles.git $HOME/.dotfiles
  config checkout
  ```

---

### 4. 远程机器访问 —— `ssh`

安全地连接远程服务器执行命令或传输文件。

#### 基础用法

```bash
ssh username@hostname
ssh user@192.168.1.100
ssh -p 2222 user@host  # 指定端口
```

#### 免密登录（公钥认证）

```bash
# 生成密钥对
ssh-keygen -t ed25519 -C "your_email@example.com"

# 将公钥复制到远程主机
ssh-copy-id user@host

# 成功后即可免密码登录
ssh user@host
```

#### SSH 配置文件（`~/.ssh/config`）

简化常用连接：

```text
Host myserver
    HostName 192.168.1.100
    User alice
    Port 2222
    IdentityFile ~/.ssh/id_ed25519_myserver
```

之后只需输入：

```bash
ssh myserver
```

#### 文件传输

- **`scp`**: 安全拷贝
  ```bash
  scp file.txt user@host:/path/
  scp user@host:/path/file.txt ./
  ```

```shell
sleep 20
man 7 signal
shenjy@shenjy:~$ kill -l
 1) SIGHUP       2) SIGINT       3) SIGQUIT      4) SIGILL       5) SIGTRAP
 6) SIGABRT      7) SIGBUS       8) SIGFPE       9) SIGKILL     10) SIGUSR1
11) SIGSEGV     12) SIGUSR2     13) SIGPIPE     14) SIGALRM     15) SIGTERM
16) SIGSTKFLT   17) SIGCHLD     18) SIGCONT     19) SIGSTOP     20) SIGTSTP
21) SIGTTIN     22) SIGTTOU     23) SIGURG      24) SIGXCPU     25) SIGXFSZ
26) SIGVTALRM   27) SIGPROF     28) SIGWINCH    29) SIGIO       30) SIGPWR
31) SIGSYS      34) SIGRTMIN    35) SIGRTMIN+1  36) SIGRTMIN+2  37) SIGRTMIN+3
38) SIGRTMIN+4  39) SIGRTMIN+5  40) SIGRTMIN+6  41) SIGRTMIN+7  42) SIGRTMIN+8
43) SIGRTMIN+9  44) SIGRTMIN+10 45) SIGRTMIN+11 46) SIGRTMIN+12 47) SIGRTMIN+13
48) SIGRTMIN+14 49) SIGRTMIN+15 50) SIGRTMAX-14 51) SIGRTMAX-13 52) SIGRTMAX-12
53) SIGRTMAX-11 54) SIGRTMAX-10 55) SIGRTMAX-9  56) SIGRTMAX-8  57) SIGRTMAX-7
58) SIGRTMAX-6  59) SIGRTMAX-5  60) SIGRTMAX-4  61) SIGRTMAX-3  62) SIGRTMAX-2
63) SIGRTMAX-1  64) SIGRTMAX
```

这段代码是 Linux 系统中 `kill -l` 命令的输出，显示了系统支持的所有信号（Signals）。让我详细解释每个信号的含义：

## 标准信号（1-31）

### 进程控制信号：

- **SIGHUP (1)**: 终端挂断时发送，常用于让守护进程重新读取配置
- **SIGINT (2)**: 键盘中断（Ctrl+C），请求进程正常终止
- **SIGQUIT (3)**: 键盘退出（Ctrl+\），进程会生成核心转储文件并退出
- **SIGSTOP (19)**: 暂停进程执行（无法被捕获或忽略）
- **SIGTSTP (20)**: 终端暂停（Ctrl+Z），将前台进程放入后台暂停
- **SIGCONT (18)**: 继续执行被暂停的进程
- **SIGTTIN (21)**: 后台进程尝试从终端读取时收到
- **SIGTTOU (22)**: 后台进程尝试向终端写入时收到

### 终止信号：

- **SIGKILL (9)**: 强制终止进程（无法被捕获或忽略，立即生效）
- **SIGTERM (15)**: 优雅终止信号（默认的 kill 命令信号）
- **SIGABRT (6)**: 调用 abort() 函数时发送，生成核心转储

### 错误信号：

- **SIGSEGV (11)**: 段错误（非法内存访问）
- **SIGILL (4)**: 非法指令错误
- **SIGFPE (8)**: 浮点运算异常（如除以零）
- **SIGBUS (7)**: 总线错误（内存对齐问题）
- **SIGSYS (31)**: 系统调用参数错误

### 定时器信号：

- **SIGALRM (14)**: alarm() 或 setitimer() 设置的定时器到期
- **SIGVTALRM (26)**: 虚拟定时器到期（仅消耗 CPU 时间）
- **SIGPROF (27)**: 剖析定时器到期（包含内核时间）

### 进程间通信信号：

- **SIGPIPE (13)**: 向已关闭的管道或套接字写入时产生
- **SIGCHLD (17)**: 子进程状态改变（停止、终止或继续）
- **SIGPOLL (29)**: I/O 事件发生（等同于 SIGIO）

### 用户定义信号：

- **SIGUSR1 (10)**: 用户自定义信号 1
- **SIGUSR2 (12)**: 用户自定义信号 2

### 其他信号：

- **SIGURG (23)**: 套接字紧急数据到达
- **SIGXCPU (24)**: CPU 时间限制超时
- **SIGXFSZ (25)**: 文件大小限制超时
- **SIGWINCH (28)**: 终端窗口大小改变
- **SIGSTKFLT (16)**: 协处理器栈错误（已废弃）

## 实时信号（34-64）

- **SIGRTMIN 到 SIGRTMAX**: 实时信号，用于应用程序自定义用途
- 编号格式：`SIGRTMIN+n` 或 `SIGRTMAX-n`（n=0-15）
- 实时信号的特点：
  - 保证按顺序传递
  - 可以附加数据
  - 支持队列化（不会丢失）

## 使用场景

```bash
# 发送信号示例
kill -9 1234        # 强制终止进程ID为1234的进程
kill -SIGTERM 1234  # 优雅终止进程
kill -1 1234        # 发送 SIGHUP（重载配置）

# 在代码中处理信号
signal(SIGINT, handler);  # 捕获 Ctrl+C
```

这些信号是 Unix/Linux 进程间通信和进程控制的基础机制，在系统编程、服务管理和调试中都非常重要。

```python
#!/usr/bin/env python3
import signal, time

def handler(signum, time):
    print("\n I got a SIGINT, but I am not stopping")

signal.signal(signal.SIGINT, handler)
i = 0
while True:
    time.sleep(.1)
    print("\r{}".format(i), end="")
    i += 1
```

在上面这个例子中，使用 `ctrl + c` 根本无法停止程序，要使用 `ctrl + \` 来停止程序。

### Tmux

- Sessions:
- Windows:
- Panes:

Tmux（Terminal Multiplexer）是一个强大的终端复用工具，它允许在一个终端窗口中创建、访问和控制多个终端会话。以下是 Tmux 的全面详细介绍：

---

## **一、Tmux 核心概念**

### **三层结构**

```
Session (会话) → Window (窗口) → Pane (面板)
```

- **Session**：独立的终端工作环境，包含一组窗口
- **Window**：会话中的单个视图（类似浏览器标签页）
- **Pane**：窗口中的分屏区域，可同时运行多个命令

### **工作模式**

- **Attached**：会话在前台运行，与用户交互
- **Detached**：会话在后台运行，可随时重新连接

---

## **二、安装与启动**

### **安装**

```bash
# Ubuntu/Debian
sudo apt install tmux

# CentOS/RHEL
sudo yum install tmux

# macOS
brew install tmux
```

### **基本命令**

```bash
tmux                  # 启动新会话（自动命名如 0, 1, 2...）
tmux new -s mysession # 启动命名会话
tmux ls               # 列出所有会话
tmux attach -t name   # 连接到指定会话
tmux kill-session -t name # 终止会话
```

---

## **三、Tmux 前缀键（Prefix Key）**

所有 Tmux 命令都需要先按 **前缀键**，默认是 `Ctrl+b`。

### **修改前缀键（推荐改为 `Ctrl+a`）**

```bash
# 在 ~/.tmux.conf 中添加
unbind C-b
set -g prefix C-a
bind C-a send-prefix
```

---

## **四、会话管理**

### **会话操作（前缀键后）**

| 快捷键     | 功能                   |
| ---------- | ---------------------- |
| `Ctrl+b d` | 分离当前会话（Detach） |
| `Ctrl+b $` | 重命名当前会话         |
| `Ctrl+b s` | 列出所有会话并切换     |
| `Ctrl+b (` | 切换到上一个会话       |
| `Ctrl+b )` | 切换到下一个会话       |

### **命令行操作**

```bash
# 创建分离会话
tmux new -s work -d

# 重命名会话
tmux rename-session -t oldname newname

# 在会话间切换
tmux switch -t sessionname
```

---

## **五、窗口管理**

### **窗口操作**

| 快捷键       | 功能                   |
| ------------ | ---------------------- |
| `Ctrl+b c`   | 创建新窗口             |
| `Ctrl+b ,`   | 重命名当前窗口         |
| `Ctrl+b &`   | 关闭当前窗口           |
| `Ctrl+b p`   | 切换到上一个窗口       |
| `Ctrl+b n`   | 切换到下一个窗口       |
| `Ctrl+b 0-9` | 切换到指定编号窗口     |
| `Ctrl+b w`   | 列出所有窗口供选择     |
| `Ctrl+b l`   | 切换到上一个活动的窗口 |

### **窗口布局**

```bash
# 查看窗口列表
tmux list-windows

# 移动窗口位置
tmux swap-window -s 2 -t 1  # 交换窗口2和窗口1
```

---

## **六、面板管理**

### **创建与分割面板**

| 快捷键     | 功能                 |
| ---------- | -------------------- |
| `Ctrl+b "` | 水平分割（上下分屏） |
| `Ctrl+b %` | 垂直分割（左右分屏） |
| `Ctrl+b x` | 关闭当前面板         |
| `Ctrl+b z` | 最大化/恢复当前面板  |

### **面板导航**

| 快捷键          | 功能                         |
| --------------- | ---------------------------- |
| `Ctrl+b 方向键` | 切换到相邻面板               |
| `Ctrl+b q`      | 显示面板编号，然后按数字切换 |
| `Ctrl+b o`      | 切换到下一个面板             |
| `Ctrl+b ;`      | 切换到上次活动的面板         |

### **面板布局**

| 快捷键         | 功能                        |
| -------------- | --------------------------- |
| `Ctrl+b Space` | 循环切换预设布局            |
| `Ctrl+b Alt+1` | 切换到 even-horizontal 布局 |
| `Ctrl+b Alt+2` | 切换到 even-vertical 布局   |
| `Ctrl+b Alt+3` | 切换到 main-horizontal 布局 |
| `Ctrl+b Alt+4` | 切换到 main-vertical 布局   |
| `Ctrl+b Alt+5` | 切换到 tiled 布局           |

### **调整面板大小**

```bash
# 前缀键后按 Ctrl+方向键 调整大小
Ctrl+b Ctrl+↑   # 向上扩展
Ctrl+b Ctrl+↓   # 向下扩展
Ctrl+b Ctrl+←   # 向左扩展
Ctrl+b Ctrl+→   # 向右扩展

# 或者使用命令行模式
Ctrl+b :resize-pane -L 10  # 向左扩展10个单位
```

---

## **七、复制模式与缓冲区**

### **进入复制模式**

```bash
Ctrl+b [   # 进入复制模式
# 在复制模式中：
- 方向键移动光标
- Space 开始选择
- Enter 复制选中内容
- q 退出复制模式
```

### **粘贴缓冲区内容**

```bash
Ctrl+b ]   # 粘贴最近复制的内容
```

### **缓冲区管理**

| 快捷键     | 功能                                |
| ---------- | ----------------------------------- |
| `Ctrl+b =` | 列出所有缓冲区供选择                |
| `Ctrl+b -` | 删除最近缓冲区                      |
| `Ctrl+b :` | 进入命令行模式，输入 `list-buffers` |

---

## **八、自定义配置**

### **配置文件位置**

```bash
~/.tmux.conf   # 用户配置文件
/etc/tmux.conf # 系统配置文件
```

### **常用配置示例**

```bash
# ~/.tmux.conf

# 基础设置
set -g default-terminal "screen-256color"
set -g base-index 1          # 窗口编号从1开始
setw -g pane-base-index 1    # 面板编号从1开始
set -g history-limit 10000   # 历史记录限制

# 启用鼠标支持
set -g mouse on

# 状态栏设置
set -g status-interval 1     # 状态栏刷新间隔
set -g status-justify centre # 状态栏居中对齐
set -g status-left-length 20
set -g status-right-length 50

# 状态栏颜色
set -g status-bg black
set -g status-fg white

# 窗口列表设置
setw -g window-status-format "#I:#W#F"
setw -g window-status-current-format "#I:#W#F"

# 快捷键绑定
# 重新加载配置
bind r source-file ~/.tmux.conf \; display "Reloaded!"

# 快速面板创建
bind | split-window -h
bind - split-window -v
bind h select-pane -L
bind j select-pane -D
bind k select-pane -U
bind l select-pane -R

# 调整面板大小（使用 Vim 风格）
bind -r H resize-pane -L 5
bind -r J resize-pane -D 5
bind -r K resize-pane -U 5
bind -r L resize-pane -R 5
```

### **应用配置**

```bash
# 重新加载配置文件（在 tmux 中）
Ctrl+b :source-file ~/.tmux.conf
# 或
Ctrl+b r   # 如果配置了上面的快捷键
```

---

## **九、高级功能**

### **1. 会话组**

```bash
# 将两个会话链接到一起
tmux -S /tmp/pair
tmux -S /tmp/pair attach

# 允许多个用户同时访问
chmod 777 /tmp/pair  # 设置权限
```

### **2. 脚本自动化**

```bash
#!/bin/bash
# 创建带特定布局的会话
SESSION="dev"

tmux new-session -d -s $SESSION
tmux rename-window -t $SESSION:1 "editor"
tmux send-keys -t $SESSION:1 "nvim" Enter

tmux new-window -t $SESSION:2 -n "shell"
tmux send-keys -t $SESSION:2 "cd ~/projects" Enter

tmux new-window -t $SESSION:3 -n "logs"
tmux send-keys -t $SESSION:3 "tail -f /var/log/syslog" Enter

tmux select-window -t $SESSION:1
tmux attach-session -t $SESSION
```

### **3. 插件管理（通过 TPM）**

```bash
# 安装 TPM（Tmux Plugin Manager）
git clone https://github.com/tmux-plugins/tpm ~/.tmux/plugins/tpm

# 在 .tmux.conf 中添加插件
set -g @plugin 'tmux-plugins/tpm'
set -g @plugin 'tmux-plugins/tmux-sensible'
set -g @plugin 'tmux-plugins/tmux-resurrect'  # 会话保存/恢复

# 初始化 TPM（放在配置文件末尾）
run '~/.tmux/plugins/tpm/tpm'
```

---

## **十、实用技巧**

### **1. 快速复制粘贴**

```bash
# 启用鼠标后：
- 拖动选择文本（自动复制）
- 点击中键或 Shift+Insert 粘贴
```

### **2. 同步输入到多个面板**

```bash
# 前缀键后按
Ctrl+b :setw synchronize-panes  # 切换同步模式
# 同步后，在一个面板输入会复制到所有面板
```

### **3. 保存和恢复会话**

```bash
# 使用 tmux-resurrect 插件
Ctrl+b Ctrl+s   # 保存会话
Ctrl+b Ctrl+r   # 恢复会话

# 手动保存
tmux list-windows -a -F "#S:#W" > ~/.tmux-session
```

### **4. 终端兼容性问题**

```bash
# 如果颜色显示异常，在 .tmux.conf 中添加：
set -g default-terminal "tmux-256color"
set -as terminal-overrides ',xterm*:Tc:sitm=\E[3m'
```

---

## **十一、Tmux 与 Screen 对比**

| **特性** | **Tmux**                  | **GNU Screen** |
| -------- | ------------------------- | -------------- |
| 配置文件 | 单一文件 (`~/.tmux.conf`) | 多个文件       |
| 分屏布局 | 灵活，支持多种布局        | 相对简单       |
| 复制模式 | 功能强大，类似 vi         | 基本功能       |
| 插件系统 | 有（TPM）                 | 无官方插件系统 |
| 性能     | 更快，资源占用少          | 相对较慢       |
| 社区生态 | 更活跃，现代工具          | 较老但稳定     |

---

## **十二、故障排除**

### **常见问题**

```bash
# 1. 按键无响应
# 检查前缀键是否正确，或重启 tmux

# 2. 颜色显示异常
# 在 .tmux.conf 中设置正确的终端类型

# 3. 鼠标滚动问题
# 确保启用了鼠标支持：set -g mouse on

# 4. 会话丢失
# 使用 tmux-resurrect 插件定期保存
```

### **调试命令**

```bash
tmux info          # 显示 tmux 信息
tmux list-keys     # 列出所有快捷键
tmux show-options  # 显示所有选项
tmux show-options -g  # 显示全局选项
```

---

## **十三、学习路线建议**

1. **初级阶段**：掌握基本会话、窗口、面板操作
2. **中级阶段**：学习配置文件定制、鼠标支持、复制模式
3. **高级阶段**：掌握脚本自动化、插件使用、会话共享

---

Tmux 的学习曲线起初可能较陡，但一旦掌握，它能极大提升终端工作效率。建议从基本操作开始，逐步探索高级功能。

### alias 命令行

`alias` 是 Linux/Unix 系统中用于**创建命令别名**的内置 Shell 命令。它允许你为复杂的命令或常用命令序列创建简短的替代名称，从而提高工作效率。

---

## **一、alias 的基本语法**

```bash
alias [别名]='原始命令 [参数]'
```

### **重要规则**

1. **等号两侧不能有空格**：`alias ll='ls -l'` ✅
2. **复杂命令用引号包裹**：包含空格、管道、重定向等特殊字符时
3. **单引号优先**：避免变量被提前展开

---

## **二、创建别名**

### **临时别名（当前会话有效）**

```bash
# 为常用命令创建简短别名
alias ll='ls -alF'
alias la='ls -A'
alias l='ls -CF'

# 为安全操作添加确认
alias rm='rm -i'      # 删除前确认
alias cp='cp -i'      # 覆盖前确认
alias mv='mv -i'      # 移动前确认

# 简化目录导航
alias ..='cd ..'
alias ...='cd ../..'
alias ....='cd ../../..'
alias ~='cd ~'

# 修复常见拼写错误
alias cd..='cd ..'
alias sl='ls'
```

### **带参数的别名**

```bash
# 查找并高亮文本
alias grep='grep --color=auto'
alias fgrep='fgrep --color=auto'
alias egrep='egrep --color=auto'

# 人性化显示
alias df='df -h'                          # 磁盘使用（易读格式）
alias du='du -h'                          # 目录大小（易读格式）
alias free='free -m'                      # 内存使用（MB单位）

# 网络相关
alias myip='curl ifconfig.me'             # 查看公网IP
alias ports='sudo netstat -tulpn'         # 查看监听端口
alias ping='ping -c 5'                    # 限制ping次数
```

### **组合命令的别名**

```bash
# 更新系统（Ubuntu/Debian）
alias update='sudo apt update && sudo apt upgrade'

# 递归查找文件
alias findf='find . -type f -name'

# 按大小排序文件
alias lt='ls -lhS'                        # 从大到小
alias ltr='ls -lhSr'                      # 从小到大

# 查看历史命令并过滤
alias histg='history | grep'
```

---

## **三、永久别名**

### **用户级配置**

```bash
# 1. 编辑 Shell 配置文件
# Bash 用户
nano ~/.bashrc

# Zsh 用户
nano ~/.zshrc

# Fish 用户（语法不同）
nano ~/.config/fish/config.fish

# 2. 添加别名到文件末尾
alias ll='ls -alF'
alias update='sudo apt update && sudo apt upgrade'

# 3. 重新加载配置
source ~/.bashrc   # Bash
source ~/.zshrc    # Zsh
```

### **系统级配置（所有用户）**

```bash
# 编辑全局配置文件（需要管理员权限）
sudo nano /etc/bash.bashrc      # Debian/Ubuntu
sudo nano /etc/bashrc           # CentOS/RHEL
sudo nano /etc/profile          # 通用
```

---

## **四、查看和管理别名**

### **查看别名**

```bash
alias                    # 列出所有别名
alias ll                 # 查看特定别名定义

# 格式化输出
alias | column -t -s "="
```

### **输出示例**

```bash
alias ll='ls -alF'
alias ls='ls --color=auto'
alias rm='rm -i'
```

### **删除别名**

```bash
unalias ll               # 删除单个别名
unalias -a               # 删除所有别名
```

### **检查别名是否已定义**

```bash
type ll                  # 显示命令类型和定义
which ls                 # 显示命令路径（不受别名影响）
command -v ll            # 显示命令类型
```

---

## **五、高级别名技巧**

### **使用函数创建复杂别名**

```bash
# 创建目录并立即进入
mkcd() { mkdir -p "$1" && cd "$1"; }
alias mcd='mkcd'

# 快速压缩文件夹
targz() { tar -czf "$1.tar.gz" "$1"; }
alias tgz='targz'

# 计算文件夹大小
dsize() { du -sh "$1" 2>/dev/null || echo "无法访问: $1"; }
```

### **带参数的函数别名**

```bash
# 根据扩展名查找文件
findext() {
    find . -type f -name "*.$1"
}
alias fe='findext'

# 使用：fe txt   # 查找所有.txt文件
```

### **条件别名**

```bash
# 根据不同系统设置别名
if [ -f /etc/debian_version ]; then
    alias update='sudo apt update && sudo apt upgrade'
elif [ -f /etc/redhat-release ]; then
    alias update='sudo yum update'
fi
```

### **安全相关别名**

```bash
# 显示文件权限变更
alias chmod='chmod -v'
alias chown='chown -v'
alias chgrp='chgrp -v'

# 安全删除（覆盖后删除）
alias srm='shred -u -z -n 5'

# 查看可疑进程
alias psc='ps aux | grep -v "\["'
```

---

## **六、别名实战示例**

### **开发者专用别名**

```bash
# Git 简化
alias gs='git status'
alias ga='git add'
alias gc='git commit'
alias gcm='git commit -m'
alias gp='git push'
alias gl='git log --oneline --graph'

# Docker 简化
alias dps='docker ps'
alias dpsa='docker ps -a'
alias di='docker images'
alias drm='docker rm'
alias drmi='docker rmi'

# Python 开发
alias py='python3'
alias pip='pip3'
alias venv='python3 -m venv'
alias pyclean='find . -name "*.pyc" -delete'
```

### **系统管理员别名**

```bash
# 进程管理
alias pstop='ps aux | sort -nrk 3,3 | head -5'  # CPU使用前5
alias psmem='ps aux | sort -nrk 4,4 | head -5'  # 内存使用前5

# 日志查看
alias logerr='sudo journalctl -p 3 -xb'         # 查看系统错误
alias logtail='sudo tail -f /var/log/syslog'    # 实时系统日志

# 服务管理
alias start='sudo systemctl start'
alias stop='sudo systemctl stop'
alias restart='sudo systemctl restart'
alias status='sudo systemctl status'
alias reload='sudo systemctl reload'
```

### **网络诊断别名**

```bash
# 网络测试
alias pingg='ping 8.8.8.8'                      # 测试谷歌DNS
alias speedtest='curl -s https://raw.githubusercontent.com/sivel/speedtest-cli/master/speedtest.py | python -'

# 端口扫描
alias scanports='nmap -sT -p- localhost'        # 扫描本地所有端口

# 路由追踪
alias mtrg='mtr 8.8.8.8'                        # 到谷歌的路由追踪
```

---

## **七、注意事项和最佳实践**

### **常见问题**

1. **循环别名**

   ```bash
   # 错误：无限循环
   alias ls='ls -l'
   alias ls='ls --color=auto'  # 覆盖了上一个

   # 正确：合并选项
   alias ls='ls -l --color=auto'
   ```

2. **参数传递问题**

   ```bash
   # 错误：无法传递参数
   alias ll='ls -l *.txt'      # *.txt 会在定义时展开

   # 正确：使用函数
   ll() { ls -l "$@" *.txt; }
   ```

### **绕过别名使用原命令**

```bash
# 方法1：使用反斜杠
\ls                          # 忽略别名，使用原始 ls

# 方法2：使用 command 命令
command ls                   # 调用原始命令

# 方法3：指定完整路径
/bin/ls                     # 直接调用二进制文件
```

### **别名命名规范**

```bash
# 好习惯
alias ll='ls -l'            # 简洁明了
alias update-system='sudo apt update && sudo apt upgrade'

# 避免
alias l='ls -l'             # 太简短可能冲突
alias 1='cd ..'             # 使用数字不直观
```

---

## **八、实用配置文件示例**

### **完整的 ~/.bash_aliases 文件**

```bash
# 安全操作
alias rm='rm -i'
alias cp='cp -i'
alias mv='mv -i'
alias ln='ln -i'

# ls 增强
alias ls='ls --color=auto'
alias ll='ls -alF'
alias la='ls -A'
alias l='ls -CF'
alias lx='ls -lXB'         # 按扩展名排序
alias lk='ls -lSr'         # 按大小排序（从小到大）
alias lt='ls -ltr'         # 按日期排序（最近修改在前）
alias lm='ls -al | more'   # 分页显示

# 目录导航
alias ..='cd ..'
alias ...='cd ../..'
alias ....='cd ../../..'
alias ~='cd ~'
alias home='cd ~'

# 系统信息
alias df='df -h'
alias du='du -h'
alias free='free -m'
alias meminfo='free -m -l -t'
alias psmem='ps auxf | sort -nr -k 4'
alias pscpu='ps auxf | sort -nr -k 3'

# 网络
alias myip='curl ifconfig.me'
alias ports='netstat -tulanp'
alias ping='ping -c 5'
alias wget='wget -c'       # 断点续传

# Git 简化
alias gs='git status'
alias ga='git add'
alias gb='git branch'
alias gc='git commit'
alias gd='git diff'
alias go='git checkout'
alias gp='git push'
alias gl='git log --oneline --graph --all'

# 快速编辑配置
alias bashrc='nano ~/.bashrc && source ~/.bashrc'
alias vimrc='nano ~/.vimrc'
alias tmuxconf='nano ~/.tmux.conf && tmux source-file ~/.tmux.conf'

# 实用函数
mkcd() { mkdir -p "$1" && cd "$1"; }
dsize() { du -sh "$@"; }

# 查看命令的真实命令
shenjy@shenjy:~/msoce/env$ type ll
ll is aliased to `ls -alF'
shenjy@shenjy:~/msoce$ alias ll
alias ll='ls -alF'
```

---

## **九、总结**

### **alias 的优点**

1. **提高效率**：简化复杂命令输入
2. **减少错误**：避免重复输入长命令
3. **增强安全**：为危险命令添加安全选项
4. **个性化定制**：根据习惯定制工作环境

### **常用命令速查**

| **操作**       | **命令**                      | **示例**                |
| -------------- | ----------------------------- | ----------------------- |
| 创建临时别名   | `alias 名='命令'`             | `alias ll='ls -l'`      |
| 创建永久别名   | 编辑 `~/.bashrc` 等文件       | 添加 `alias ll='ls -l'` |
| 查看所有别名   | `alias`                       | `alias \| grep ls`      |
| 删除别名       | `unalias 名`                  | `unalias ll`            |
| 临时使用原命令 | `\命令名` 或 `command 命令名` | `\ls` 或 `command ls`   |

### **推荐学习路径**

1. 从简单的 `ll`、`la` 等别名开始
2. 逐步添加符合自己工作习惯的别名
3. 将常用别名整理到配置文件中
4. 学习使用函数创建更强大的别名

合理使用别名可以极大提升命令行工作效率，建议根据自己的工作流创建个性化的别名集合。

`ssh`（Secure Shell）是 Linux/Unix 系统中用于**加密远程登录和安全管理**的核心命令。它通过加密通道提供安全的远程访问、文件传输和命令执行功能。

---

### **一、SSH 基本概念与架构**

### **工作原理**

```
客户端（ssh） ↔ 加密通道 ↔ 服务器（sshd）
    ↓                           ↓
 本地主机                   远程服务器
```

### **核心组件**

1. **客户端**：`ssh` 命令（本文重点）
2. **服务器**：`sshd` 守护进程
3. **密钥对**：非对称加密（RSA/ECDSA/Ed25519）
4. **配置文件**：`~/.ssh/config`, `/etc/ssh/ssh_config`

---

### **二、基本连接语法**

```bash
ssh [选项] [用户名@]主机名 [命令]
```

### **常用格式**

```bash
# 1. 基本连接（使用当前用户名）
ssh 主机名或IP地址

# 2. 指定用户名
ssh 用户名@主机名

# 3. 指定端口（默认22）
ssh -p 端口号 用户名@主机名

# 4. 执行远程命令
ssh 用户名@主机名 "命令"
```

### **实用示例**

```bash
# 连接到远程服务器
ssh alice@192.168.1.100

# 使用非标准端口
ssh -p 2222 bob@example.com

# 执行远程命令并返回结果
ssh root@web-server "uptime; df -h"

# 执行本地脚本
ssh user@server < local_script.sh
```

---

### **三、连接认证方式**

### **1. 密码认证（默认但不够安全）**

```bash
ssh user@hostname
# 提示输入密码
```

### **2. 密钥认证（推荐）**

```bash
# 生成密钥对（默认 RSA 3072位）
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"

# 更安全的 Ed25519 算法
ssh-keygen -t ed25519 -C "your_email@example.com"

# 将公钥复制到远程服务器
ssh-copy-id -i ~/.ssh/id_ed25519.pub user@hostname
# 或手动复制
cat ~/.ssh/id_ed25519.pub | ssh user@hostname "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
```

### **3. 代理转发（Agent Forwarding）**

```bash
# 启动 SSH 代理
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519

# 连接时启用代理转发
ssh -A user@hostname

# 永久配置（~/.ssh/config）
Host *
    ForwardAgent yes
```

---

### **四、SSH 常用选项详解**

### **连接选项**

| **选项** | **作用**         | **示例**                                   |
| -------- | ---------------- | ------------------------------------------ |
| `-p`     | 指定端口         | `ssh -p 2222 user@host`                    |
| `-i`     | 指定私钥文件     | `ssh -i ~/.ssh/custom_key user@host`       |
| `-l`     | 指定登录用户名   | `ssh -l username host`                     |
| `-v`     | 详细输出（调试） | `ssh -v user@host`（可重复 `-vvv` 更详细） |
| `-q`     | 安静模式         | `ssh -q user@host`                         |
| `-t`     | 强制分配伪终端   | `ssh -t user@host "top"`                   |

### **安全和网络选项**

| **选项** | **作用**              | **示例**                                 |
| -------- | --------------------- | ---------------------------------------- |
| `-A`     | 启用代理转发          | `ssh -A user@host`                       |
| `-L`     | 本地端口转发          | `ssh -L 8080:localhost:80 user@host`     |
| `-R`     | 远程端口转发          | `ssh -R 9000:localhost:3000 user@host`   |
| `-D`     | 动态端口转发（SOCKS） | `ssh -D 1080 user@host`                  |
| `-o`     | 指定配置选项          | `ssh -o "StrictHostKeyChecking=no" host` |
| `-C`     | 启用压缩              | `ssh -C user@host`（适合慢速网络）       |

---

### **五、端口转发（隧道）**

### **1. 本地端口转发（Local Port Forwarding）**

```bash
# 将本地端口映射到远程服务
ssh -L [本地IP:]本地端口:目标主机:目标端口 用户名@SSH服务器

# 示例：访问远程数据库
ssh -L 3306:localhost:3306 dev@web-server
# 现在本地连接 localhost:3306 即连接到远程MySQL
```

### **2. 远程端口转发（Remote Port Forwarding）**

```bash
# 将远程端口映射到本地服务
ssh -R [远程IP:]远程端口:本地主机:本地端口 用户名@SSH服务器

# 示例：暴露本地Web服务
ssh -R 8080:localhost:80 user@public-server
# 从 public-server:8080 访问本地80端口服务
```

### **3. 动态端口转发（SOCKS 代理）**

```bash
# 创建 SOCKS5 代理
ssh -D 1080 user@proxy-server

# 配置浏览器使用 SOCKS5 代理
# 地址：localhost:1080
```

### **4. 保持隧道活跃**

```bash
# 防止超时断开
ssh -o ServerAliveInterval=60 -o ServerAliveCountMax=3 -L 3306:localhost:3306 user@host
```

---

### **六、SCP 和 SFTP 文件传输**

### **SCP（基于 SSH 的文件复制）**

```bash
# 复制文件到远程
scp 本地文件 用户名@主机名:远程路径
scp file.txt alice@server:/home/alice/

# 从远程复制文件
scp 用户名@主机名:远程文件 本地路径
scp alice@server:/var/log/app.log .

# 复制目录（递归）
scp -r 本地目录/ 用户名@主机名:远程路径
scp -r ./project/ bob@server:/opt/

# 保留文件属性
scp -p file.txt user@host:/tmp/

# 使用特定端口
scp -P 2222 file.txt user@host:/tmp/
```

### **SFTP（交互式文件传输）**

```bash
# 启动 SFTP 会话
sftp user@hostname

# 常用 SFTP 命令
get file.txt          # 下载文件
put file.txt          # 上传文件
ls                    # 列出远程文件
lls                   # 列出本地文件
cd path               # 切换远程目录
lcd path              # 切换本地目录
exit                  # 退出
```

---

### **七、SSH 配置文件（~/.ssh/config）**

### **配置文件结构**

```bash
Host 别名
    选项 值
    选项 值
```

### **完整配置示例**

```bash
# ~/.ssh/config

# 通用配置（匹配所有主机）
Host *
    ServerAliveInterval 60
    ServerAliveCountMax 3
    TCPKeepAlive yes
    Compression yes
    ForwardAgent yes
    IdentitiesOnly yes
    StrictHostKeyChecking accept-new

# 特定服务器配置
Host webserver
    HostName 192.168.1.100
    User alice
    Port 22
    IdentityFile ~/.ssh/id_ed25519_web

Host database
    HostName db.example.com
    User dbadmin
    Port 2222
    IdentityFile ~/.ssh/id_rsa_db

# GitHub 配置
Host github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_github
    IdentitiesOnly yes

# 跳板机配置（Bastion Host）
Host internal-*
    ProxyJump bastion
    User developer

Host bastion
    HostName jump.example.com
    User jumper
    IdentityFile ~/.ssh/id_ed25519_jump
```

### **使用配置简化连接**

```bash
# 原本需要
ssh -p 2222 -i ~/.ssh/id_rsa_db dbadmin@db.example.com

# 配置后只需
ssh database
```

---

### **八、高级功能与技巧**

### **1. 多路复用（连接共享）**

```bash
# 配置连接复用（~/.ssh/config）
Host *
    ControlMaster auto
    ControlPath ~/.ssh/ssh_mux_%h_%p_%r
    ControlPersist 10m

# 验证连接是否复用
ssh -O check webserver
```

### **2. 跳板机/堡垒机（Jump Host）**

```bash
# 方法1：使用 ProxyJump（SSH 7.3+）
ssh -J user@jump-host user@target-host

# 方法2：嵌套 SSH
ssh -t user@jump-host "ssh user@target-host"

# 方法3：配置文件
Host target-host
    HostName target.internal
    ProxyJump jump-user@jump-host:22
```

### **3. 执行复杂命令**

```bash
# 多命令执行
ssh user@host "cd /var/log && tail -f app.log"

# 带变量的命令
ssh user@host 'VAR="value" && echo $VAR'

# 使用 heredoc
ssh user@host << 'EOF'
cd /tmp
ls -la
w > users.txt
EOF
```

### **4. X11 转发（图形界面）**

```bash
# 启用 X11 转发
ssh -X user@host

# 运行图形程序
ssh -X user@host "gedit"

# 信任模式（性能更好但有风险）
ssh -Y user@host
```

### **5. 连接测试与调试**

```bash
# 详细调试
ssh -vvv user@host

# 测试连接速度
time ssh user@host "exit"

# 检查认证方式
ssh -o PreferredAuthentications=none user@host
```

---

### **九、安全最佳实践**

### **1. 密钥管理**

```bash
# 使用强密码保护私钥
ssh-keygen -t ed25519 -a 100

# 定期更换密钥
ssh-keygen -p -f ~/.ssh/id_rsa

# 限制密钥使用
# 在公钥前添加选项（~/.ssh/authorized_keys）
from="192.168.1.*",command="/usr/bin/monitor.sh",no-agent-forwarding,no-port-forwarding ssh-ed25519 AAAAC3...
```

### **2. 服务器端安全**

```bash
# 修改默认端口
Port 2222

# 禁用 root 登录
PermitRootLogin no

# 禁用密码认证
PasswordAuthentication no

# 限制用户访问
AllowUsers alice bob
AllowGroups ssh-users

# 使用 fail2ban 防暴力破解
```

### **3. 客户端安全**

```bash
# 设置严格的文件权限
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys ~/.ssh/config
chmod 644 ~/.ssh/*.pub

# 检查已知主机文件
ssh-keygen -l -f ~/.ssh/known_hosts

# 移除过期的已知主机
ssh-keygen -R hostname
```

---

### **十、故障排除**

### **常见错误及解决**

| **错误信息**                       | **原因**             | **解决方案**                         |
| ---------------------------------- | -------------------- | ------------------------------------ |
| `Permission denied (publickey)`    | 密钥认证失败         | 检查密钥权限、服务器 authorized_keys |
| `Connection refused`               | 端口错误或服务未运行 | `ssh -v` 调试，检查防火墙            |
| `Host key verification failed`     | 主机密钥变更         | `ssh-keygen -R hostname`             |
| `Too many authentication failures` | 认证尝试过多         | 使用 `-o PubkeyAuthentication=no`    |
| `Broken pipe`                      | 连接超时             | 配置 `ServerAliveInterval`           |

### **调试命令**

```bash
# 检查连接详情
ssh -v user@host 2>&1 | grep -E "Authenticating|debug1"

# 测试特定端口
nc -zv hostname 22

# 检查 SSH 服务状态
ssh -T user@host  # 测试连接但不执行命令
```

---

### **十一、实用脚本示例**

### **1. 批量执行命令**

```bash
#!/bin/bash
# multi-ssh.sh
HOSTS="server1 server2 server3"
CMD="uptime; free -m"

for host in $HOSTS; do
    echo "=== $host ==="
    ssh -o ConnectTimeout=5 "$host" "$CMD"
    echo ""
done
```

### **2. 自动备份脚本**

```bash
#!/bin/bash
# backup-via-ssh.sh
REMOTE_USER="backup"
REMOTE_HOST="backup-server"
LOCAL_DIR="/data"
REMOTE_DIR="/backup/$(hostname)"

rsync -avz -e "ssh -i ~/.ssh/backup_key" \
    "$LOCAL_DIR/" \
    "$REMOTE_USER@$REMOTE_HOST:$REMOTE_DIR/"
```

### **3. SSH 隧道监控**

```bash
#!/bin/bash
# check-tunnel.sh
TUNNEL_PID=$(ps aux | grep "ssh.*-L.*:3306" | grep -v grep | awk '{print $2}')

if [ -z "$TUNNEL_PID" ]; then
    echo "隧道未运行，正在启动..."
    ssh -fN -L 3306:localhost:3306 db-user@db-host
else
    echo "隧道运行中 (PID: $TUNNEL_PID)"
fi
```

---

### **十二、总结**

### **核心要点**

1. **安全性**：SSH 使用加密连接，优先使用密钥认证
2. **灵活性**：支持端口转发、代理跳转、X11 转发等
3. **效率**：配置文件可简化复杂连接，多路复用提高速度

### **日常使用速查**

```bash
# 基础连接
ssh user@host                    # 密码登录
ssh -i key.pem user@host         # 密钥登录
ssh -p 2222 user@host            # 指定端口

# 文件传输
scp file.txt user@host:/path/    # 上传文件
scp user@host:/path/file.txt .   # 下载文件
sftp user@host                   # 交互传输

# 端口转发
ssh -L 8080:localhost:80 user@host    # 本地转发
ssh -R 9000:localhost:3000 user@host  # 远程转发
ssh -D 1080 user@host                 # SOCKS代理

# 远程执行
ssh user@host "ls -la"           # 单命令
ssh user@host << 'EOF'           # 多命令
  cd /tmp
  ls
EOF
```

SSH 是现代系统管理和开发不可或缺的工具，掌握其高级功能能极大提升工作效率和安全性。

### SSH-COPY-ID 命令介绍

`ssh-copy-id` 是一个用于将本地公钥自动复制到远程主机上的实用工具，主要用途是简化 SSH 公钥认证的配置过程。

#### 功能原理

1. 将本地用户的 `~/.ssh/id_rsa.pub`（或其他公钥文件）内容追加到远程主机的 `~/.ssh/authorized_keys` 文件中。
2. 若远程主机尚无 `~/.ssh` 目录，会自动创建并设置正确的权限（700）。
3. 会确保 `authorized_keys` 文件权限为 600。

---

#### 基本语法

```bash
ssh-copy-id [-i identity_file] [user@]hostname
```

---

#### 常用选项

| 选项 | 作用                                       |
| ---- | ------------------------------------------ |
| `-i` | 指定公钥文件（默认为 `~/.ssh/id_rsa.pub`） |
| `-p` | 指定远程主机的 SSH 端口                    |
| `-f` | 强制模式（不检查密钥是否已存在）           |
| `-n` | 试运行（不实际修改文件）                   |

---

#### 使用示例

1. **基础用法**（使用默认公钥）：

   ```bash
   ssh-copy-id user@remote-server
   ```

2. **指定非默认公钥**：

   ```bash
   ssh-copy-id -i ~/.ssh/my_key.pub user@remote-server
   ```

3. **指定端口**（远程主机使用非 22 端口时）：

   ```bash
   ssh-copy-id -p 2222 user@remote-server
   ```

4. **组合使用**：
   ```bash
   ssh-copy-id -i ~/.ssh/work_key.pub -p 2222 admin@remote-server
   ```

---

#### 注意事项

1. **首次连接验证**：命令执行时会要求确认远程主机指纹（类似首次手动 SSH 连接时的提示）。
2. **密码认证**：需要输入远程用户的密码（这是最后一次需要密码，之后即可用密钥登录）。
3. **权限要求**：远程用户必须具有写入 `~/.ssh/authorized_keys` 文件的权限。
4. **密钥存在性检查**：默认会跳过已存在的密钥，使用 `-f` 可强制覆盖。

---

#### 等效手动操作

如果不使用 `ssh-copy-id`，你可以通过以下命令手动实现：

```bash
cat ~/.ssh/id_rsa.pub | ssh user@host "mkdir -p ~/.ssh && chmod 700 ~/.ssh && cat >> ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys"
```

---

#### 系统支持

- 大多数 Linux 发行版和 macOS 已预装该命令
- 若未安装，通常可通过 `openssh-client` 包获取

此工具极大简化了 SSH 无密码登录的配置过程，是管理多服务器时的实用利器。

prompt> 请使用 tldr 的 d 方式再介绍下 ssh-copy-id 命令。

DeepSeek-v3:
`ssh-copy-id` 快速指南：

```tldr
# 基本用法（默认使用默认密钥）
ssh-copy-id user@host

# 指定要复制的公钥文件
ssh-copy-id -i ~/.ssh/mykey.pub user@host

# 使用特定端口（非22）
ssh-copy-id -p 2222 user@host

# 功能：
1. 将本地公钥复制到远程主机的authorized_keys文件中
2. 自动设置正确的文件权限
3. 验证密钥登录是否生效

# 前提条件：
- 远程主机SSH服务已开启
- 知道远程主机的密码（首次使用）
```

注：该命令是设置 SSH 密钥认证的最快捷方式，避免手动复制公钥的繁琐步骤。

SCP (Secure Copy Protocol) 是一种基于 SSH 的安全文件传输协议，用于在本地主机和远程主机之间或两个远程主机之间安全地复制文件和目录。

### 基本语法

```bash
scp [选项] [源文件] [目标路径]
```

### 常用选项

| 选项 | 描述                                 |
| ---- | ------------------------------------ |
| `-P` | 指定远程主机的 SSH 端口（默认是 22） |
| `-p` | 保留文件的修改时间、访问时间和权限   |
| `-r` | 递归复制整个目录                     |
| `-C` | 启用压缩传输                         |
| `-q` | 静默模式，不显示传输进度             |
| `-v` | 详细模式，输出调试信息               |
| `-i` | 指定身份文件（私钥文件）             |

### 使用示例

### 1. 从本地复制到远程

```bash
# 复制文件到远程主机
scp local_file.txt username@remote_host:/remote/directory/

# 复制目录到远程主机（递归）
scp -r local_dir username@remote_host:/remote/directory/

# 使用特定端口
scp -P 2222 local_file.txt username@remote_host:/remote/directory/
```

### 2. 从远程复制到本地

```bash
# 从远程主机复制文件
scp username@remote_host:/remote/file.txt /local/directory/

# 从远程主机复制目录
scp -r username@remote_host:/remote/dir/ /local/directory/
```

### 3. 在两个远程主机之间复制

```bash
scp username1@host1:/path/to/file username2@host2:/path/to/destination
```

### 4. 使用特定密钥

```bash
scp -i ~/.ssh/private_key.pem local_file.txt username@remote_host:/remote/directory/
```

### 5. 保留文件属性

```bash
scp -p local_file.txt username@remote_host:/remote/directory/
```

### 注意事项

1. SCP 使用 SSH 协议，因此需要远程主机上运行 SSH 服务
2. 首次连接时会提示验证主机密钥指纹
3. 密码验证方式与 SSH 相同
4. 传输大文件时可以考虑使用 `-C` 选项启用压缩
5. SCP 会加密所有传输的数据

### SCP 替代方案

虽然 SCP 简单易用，但它有一些局限性（如不能恢复中断的传输）。现代替代方案包括：

- `rsync` - 更强大的文件同步工具
- `sftp` - 交互式文件传输协议
- `rsync over SSH` - 结合了 rsync 和 SSH 的优势

### 安全最佳实践

1. 尽可能使用 SSH 密钥认证而非密码
2. 限制远程主机的 SCP 访问权限
3. 定期更新 SSH 和相关软件
4. 考虑使用更安全的替代方案如 SFTP

SCP 是一种快速、简单且安全的方式来传输文件，特别是在需要一次性传输而无需复杂同步功能的情况下。

`rsync` 是一个功能强大的文件同步工具，用于在本地或远程系统之间高效地传输和同步文件。它通过差异算法仅传输文件中发生变化的部分，极大地提升了传输效率。以下是 `rsync` 的详细介绍：

---

### **基本语法**

```bash
rsync [选项] 源路径 目标路径
```

- **源路径**和**目标路径**可以是本地路径或远程路径（如 `user@host:/path`）。
- 路径末尾的 `/` 有特殊含义：
  - 有 `/`：同步目录内的内容（不创建目录本身）。
  - 无 `/`：同步目录本身（包括目录名）。

---

### **核心功能**

1. **增量同步**
   仅传输差异部分，节省带宽和时间。
2. **保留文件属性**
   可保留权限、时间戳、符号链接等（需配合选项）。
3. **支持远程同步**
   通过 SSH 加密传输数据（默认使用 SSH）。
4. **断点续传**
   支持部分传输的恢复。
5. **试运行（Dry Run）**
   模拟操作而不实际执行。

---

### **常用选项**

| 选项                       | 说明                                                          |
| -------------------------- | ------------------------------------------------------------- |
| `-a`（`--archive`）        | 归档模式，保留权限、符号链接等（相当于 `-rlptgoD`）。         |
| `-v`（`--verbose`）        | 显示详细输出。                                                |
| `-z`（`--compress`）       | 压缩传输的数据。                                              |
| `-h`（`--human-readable`） | 人类可读的格式输出。                                          |
| `-P`                       | 结合 `--progress`（显示进度）和 `--partial`（保留部分文件）。 |
| `--delete`                 | 删除目标中源不存在的文件（完全同步）。                        |
| `--exclude=PATTERN`        | 排除匹配的文件/目录。                                         |
| `--include=PATTERN`        | 包含匹配的文件/目录（需结合 `--exclude`）。                   |
| `-e "ssh"`                 | 指定远程 Shell（如自定义 SSH 端口：`-e "ssh -p 2222"`）。     |
| `--bwlimit=RATE`           | 限制带宽（单位：KB/s）。                                      |
| `--dry-run`                | 试运行，不实际同步。                                          |

---

### **典型示例**

#### 1. **本地同步**

```bash
# 同步目录（保留属性）
rsync -av /source/ /destination/

# 同步目录（排除某些文件）
rsync -av --exclude='*.tmp' /source/ /destination/
```

#### 2. **同步到远程服务器**

```bash
# 通过 SSH 同步到远程
rsync -avz -e ssh /local/path/ user@remote:/remote/path/

# 从远程同步到本地
rsync -avz user@remote:/remote/path/ /local/path/
```

#### 3. **删除目标多余文件**

```bash
# 严格同步（目标与源完全一致）
rsync -av --delete /source/ /destination/
```

#### 4. **限速同步**

```bash
# 限制带宽为 500 KB/s
rsync -avz --bwlimit=500 /source/ user@remote:/destination/
```

#### 5. **部分传输恢复**

```bash
# 支持断点续传（-P 显示进度）
rsync -avzP /largefile user@remote:/path/
```

#### 6. **试运行（Dry Run）**

```bash
rsync -avn --delete /source/ /destination/
```

---

### **高级用法**

- **批量排除文件**：
  将排除规则写入文件（如 `exclude.txt`），通过 `--exclude-from` 指定：

  ```bash
  rsync -av --exclude-from='exclude.txt' /source/ /destination/
  ```

- **自定义 SSH 参数**：
  使用 `-e` 指定 SSH 选项（如端口或密钥）：

  ```bash
  rsync -avz -e "ssh -p 2222 -i ~/.ssh/key.pem" /source/ user@remote:/path/
  ```

- **守护进程模式**（Daemon Mode）：
  若远程启用 `rsyncd` 服务，可直接使用 `rsync://` 协议（需配置 `/etc/rsyncd.conf`）：
  ```bash
  rsync -av /source/ rsync://remote/module/path/
  ```

---

### **注意事项**

1. **root 权限**：
   同步系统文件时可能需要 `sudo`，或用 `-a` 保留权限（目标用户需有权限）。
2. **符号链接**：
   `-a` 会同步符号链接本身，若需跟踪链接指向的实际内容，用 `-L`。
3. **空格处理**：
   路径含空格时用引号包裹（如 `"/path/with space/"`）。

---

通过灵活组合选项，`rsync` 可以适应各种文件同步场景，是备份、迁移和部署的利器。建议通过 `man rsync` 查阅完整

## [Lecture 6 Version Controll](https://missing.csail.mit.edu/2020/version-control/)

> 中文版本：https://missing-semester-cn.github.io/2020/version-control/

- https://xkcd.com/1597/

`git` 是一个**分布式版本控制系统**，用于跟踪文件变化、协调多人协作开发。以下是 Git 的全面详细介绍：

---

### **一、Git 核心概念**

### **1. 工作区与版本库**

```
工作区 (Working Directory)
     ↓ git add
暂存区 (Staging Area / Index)
     ↓ git commit
本地仓库 (Local Repository)
     ↓ git push
远程仓库 (Remote Repository)
```

### **2. 三种状态**

- **已修改 (modified)**：文件已更改但未暂存
- **已暂存 (staged)**：文件已标记，准备提交
- **已提交 (committed)**：数据已安全存入本地仓库

### **3. 关键对象**

- **Blob**：文件内容
- **Tree**：目录结构
- **Commit**：提交记录
- **Tag**：版本标签
- **Branch**：分支指针

---

### **二、Git 安装与配置**

### **安装 Git**

```bash
# Ubuntu/Debian
sudo apt update && sudo apt install git

# CentOS/RHEL
sudo yum install git

# macOS
brew install git

# Windows
# 下载 https://git-scm.com/
```

### **初始配置**

```bash
# 设置用户信息（全局）
git config --global user.name "你的姓名"
git config --global user.email "你的邮箱"

# 设置编辑器
git config --global core.editor "vim"

# 启用颜色输出
git config --global color.ui auto

# 查看配置
git config --list
git config user.name  # 查看特定配置
```

### **配置别名**

```bash
# 常用别名配置（添加到 ~/.gitconfig）
[alias]
    co = checkout
    br = branch
    ci = commit
    st = status
    lg = log --oneline --graph --all
    last = log -1 HEAD
    unstage = reset HEAD --
```

---

### **三、Git 基础操作**

### **1. 创建仓库**

```bash
# 初始化新仓库
git init

# 克隆现有仓库
git clone <仓库URL>
git clone https://github.com/user/repo.git
git clone https://github.com/user/repo.git myfolder  # 指定目录名

# 克隆特定分支
git clone -b develop https://github.com/user/repo.git
```

### **2. 查看状态**

```bash
git status                  # 完整状态
git status -s               # 简洁状态
git status --short          # 同上
```

### **3. 添加文件**

```bash
git add file.txt            # 添加单个文件
git add *.js                # 添加所有js文件
git add .                   # 添加所有文件
git add -A                  # 添加所有文件（包括删除的）
git add -u                  # 只添加已跟踪文件

# 交互式添加
git add -i
git add -p                  # 分块添加
```

### **4. 提交更改**

```bash
git commit -m "提交说明"
git commit -m "fix: 修复登录bug"  # 使用约定式提交

# 直接提交（跳过暂存区）
git commit -am "提交说明"

# 修改上一次提交
git commit --amend          # 修改提交信息
git commit --amend --no-edit  # 只修改内容，不修改信息
```

### **5. 查看历史**

```bash
git log                     # 完整日志
git log --oneline           # 单行显示
git log -3                  # 最近3条
git log --since="2024-01-01"
git log --author="名字"
git log --grep="关键词"
git log -p                  # 显示差异
git log --stat              # 显示文件统计
git log --graph --oneline --all  # 图形化显示
```

### **6. 比较差异**

```bash
git diff                    # 工作区 vs 暂存区
git diff --staged           # 暂存区 vs 仓库
git diff HEAD               # 工作区 vs 最新提交
git diff branch1 branch2    # 比较分支
git diff commit1 commit2    # 比较提交
```

---

### **四、分支管理**

### **1. 分支操作**

```bash
# 查看分支
git branch                  # 本地分支
git branch -a               # 所有分支（包括远程）
git branch -r               # 远程分支

# 创建分支
git branch feature-login    # 创建分支
git checkout -b feature-login  # 创建并切换

# 切换分支
git checkout main
git checkout -              # 切换到上一个分支

# 重命名分支
git branch -m old-name new-name

# 删除分支
git branch -d feature-login     # 安全删除（已合并）
git branch -D feature-login     # 强制删除（未合并）

# 推送分支到远程
git push origin feature-login
git push -u origin feature-login  # 设置上游分支
```

### **2. 合并与变基**

```bash
# 合并分支
git checkout main
git merge feature-login

# 变基（保持线性历史）
git checkout feature-login
git rebase main

# 交互式变基（修改历史）
git rebase -i HEAD~3        # 修改最近3次提交

# 解决冲突后继续
git add .
git rebase --continue
git rebase --abort          # 中止变基
```

### **3. 标签管理**

```bash
# 创建标签
git tag v1.0.0              # 轻量标签
git tag -a v1.0.0 -m "Release v1.0.0"  # 附注标签

# 查看标签
git tag
git tag -l "v1.*"

# 推送标签
git push origin v1.0.0
git push origin --tags      # 推送所有标签

# 删除标签
git tag -d v1.0.0
git push origin --delete v1.0.0
```

---

### **五、远程仓库操作**

### **1. 远程仓库配置**

```bash
# 查看远程仓库
git remote -v

# 添加远程仓库
git remote add origin https://github.com/user/repo.git
git remote add upstream https://github.com/original/repo.git

# 修改远程仓库URL
git remote set-url origin https://new-url.git

# 删除远程仓库
git remote remove origin
```

### **2. 拉取与推送**

```bash
# 拉取更新
git pull                    # 拉取并合并（fetch + merge）
git pull --rebase           # 拉取并变基
git fetch                   # 只获取，不合并

# 推送到远程
git push                    # 推送到上游分支
git push origin branch-name
git push --force            # 强制推送（慎用）
git push --force-with-lease # 更安全的强制推送

# 推送所有分支
git push --all
```

### **3. 跟踪远程分支**

```bash
# 设置上游分支
git branch --set-upstream-to=origin/main main
git push -u origin main     # 推送时自动设置

# 查看跟踪关系
git branch -vv
```

---

### **六、撤销与回退**

### **1. 撤销工作区修改**

```bash
# 丢弃工作区修改（危险！不可恢复）
git checkout -- file.txt
git restore file.txt        # Git 2.23+

# 丢弃所有修改
git checkout -- .
git restore .               # Git 2.23+
```

### **2. 撤销暂存区修改**

```bash
# 取消暂存
git reset HEAD file.txt
git restore --staged file.txt  # Git 2.23+
```

### **3. 撤销提交**

```bash
# 软重置（保留修改）
git reset --soft HEAD~1     # 撤销提交，保留更改到暂存区

# 混合重置（默认）
git reset HEAD~1            # 撤销提交，保留更改到工作区

# 硬重置（危险！丢失所有修改）
git reset --hard HEAD~1     # 彻底删除提交

# 撤销特定提交（创建新提交）
git revert commit-hash
```

### **4. 恢复删除的文件**

```bash
# 恢复已删除的文件
git checkout HEAD -- file.txt
git restore --source=HEAD file.txt
```

---

### **七、高级功能**

### **1. 储藏（Stash）**

```bash
# 储藏当前修改
git stash
git stash push -m "正在开发登录功能"

# 查看储藏列表
git stash list

# 应用储藏
git stash apply stash@{0}
git stash pop               # 应用并删除

# 删除储藏
git stash drop stash@{0}
git stash clear             # 清空所有储藏

# 从储藏创建分支
git stash branch new-branch stash@{0}
```

### **2. 子模块（Submodule）**

```bash
# 添加子模块
git submodule add https://github.com/user/lib.git libs/

# 克隆包含子模块的仓库
git clone --recurse-submodules https://github.com/user/repo.git

# 初始化子模块
git submodule init
git submodule update

# 更新所有子模块
git submodule foreach git pull
```

### **3. 二分查找（Bisect）**

```bash
# 开始二分查找
git bisect start

# 标记好的提交
git bisect good v1.0.0

# 标记坏的提交
git bisect bad HEAD

# 测试当前提交，标记好坏
git bisect good
git bisect bad

# 结束查找
git bisect reset
```

### **4. 钩子（Hooks）**

```bash
# 钩子位置
.git/hooks/

# 预提交钩子示例（.git/hooks/pre-commit）
#!/bin/sh
# 运行代码检查
npm run lint

# 使钩子可执行
chmod +x .git/hooks/pre-commit
```

### **5. 大文件存储（Git LFS）**

```bash
# 安装 Git LFS
git lfs install

# 跟踪大文件
git lfs track "*.psd"
git lfs track "*.zip"

# 查看跟踪的文件
git lfs ls-files
```

---

### **八、工作流模型**

### **1. 集中式工作流**

```bash
# 类似 SVN，只有一个主分支
git clone <repo>
git checkout -b feature     # 创建功能分支
git add/commit
git pull origin main        # 更新主分支
git checkout main
git merge feature           # 合并到主分支
git push origin main
```

### **2. Git Flow（经典）**

```bash
# 分支类型
- main      : 主分支（稳定版）
- develop   : 开发分支
- feature/* : 功能分支
- release/* : 发布分支
- hotfix/*  : 热修复分支

# 常用命令
git flow init               # 初始化
git flow feature start login
git flow feature finish login
```

### **3. GitHub Flow（简化）**

```bash
# 步骤
1. 从 main 创建分支
2. 提交更改
3. 创建 Pull Request
4. 讨论和审查代码
5. 部署和测试
6. 合并到 main
```

### **4. GitLab Flow（环境分支）**

```bash
# 分支结构
main → staging → production
        ↓
    feature/*
```

---

### **九、Git 内部原理**

### **1. 对象存储**

```bash
# 查看对象类型
git cat-file -t <hash>

# 查看对象内容
git cat-file -p <hash>

# 所有对象存储在
.git/objects/
```

### **2. 引用（Refs）**

```bash
# 查看引用
cat .git/HEAD
cat .git/refs/heads/main

# 符号引用
git symbolic-ref HEAD
```

### **3. 打包文件**

```bash
# 手动打包
git gc

# 查看包内容
git verify-pack -v .git/objects/pack/*.idx
```

---

### **十、最佳实践**

### **1. 提交规范**

```bash
# 约定式提交（Conventional Commits）
<类型>[可选范围]: <描述>

# 类型示例：
- feat:     新功能
- fix:      修复bug
- docs:     文档更新
- style:    代码格式
- refactor: 重构
- test:     测试
- chore:    构建/工具
```

### **2. .gitignore 文件**

```bash
# 常用忽略规则
*.log
*.tmp
*.swp
node_modules/
.DS_Store
.env
dist/
build/

# 忽略除特定文件外的所有
/*
!/src
!/README.md
```

### **3. 分支命名规范**

```bash
# 功能分支
feature/login-page
feature/add-user-api

# 修复分支
fix/login-error
hotfix/critical-bug

# 发布分支
release/v1.2.0

# 其他
docs/update-readme
test/add-coverage
```

### **4. 代码审查**

```bash
# 创建 Pull Request 前
git fetch origin
git rebase origin/main     # 变基到最新
git push -f                # 强制推送（私有分支）

# 审查时查看更改
git diff main..feature
git log --oneline main..feature
```

---

### **十一、常见问题解决**

### **1. 合并冲突**

```bash
# 查看冲突文件
git status

# 使用工具解决冲突
git mergetool

# 手动解决后
git add .
git commit
```

### **2. 恢复丢失的提交**

```bash
# 查找丢失的提交
git reflog
git log -g

# 恢复提交
git cherry-pick <hash>
git checkout -b recovered <hash>
```

### **3. 清理仓库**

```bash
# 清理未跟踪文件
git clean -f               # 删除文件
git clean -fd              # 删除文件和目录
git clean -n               # 预览将要删除的文件

# 优化仓库
git gc --aggressive --prune=now
```

---

### **十二、Git 工具生态**

### **1. 图形化工具**

> https://git-scm.com/tools/guis

- **GitHub Desktop**：GitHub 官方客户端
- **GitKraken**：跨平台专业工具
- **SourceTree**：Atlassian 出品
- **VS Code Git**：编辑器集成

### **2. 命令行增强**

```bash
# tig - 文本界面浏览器
sudo apt install tig
tig

# lazygit - 终端UI
brew install lazygit
lazygit

# diff-so-fancy - 美化diff
npm install -g diff-so-fancy
git config --global core.pager "diff-so-fancy | less --tabs=4 -RFX"
```

---

### **十三、学习资源**

### **1. 官方文档**

- [Git 官方文档](https://git-scm.com/doc)
- [Pro Git 书籍](https://git-scm.com/book/zh/v2)（免费）

### **2. 在线练习**

- [Learn Git Branching](https://learngitbranching.js.org/)
- [GitHub Learning Lab](https://lab.github.com/)

### **3. 可视化工具**

- [Git Graph Extension](https://marketplace.visualstudio.com/items?itemName=mhutchie.git-graph)（VS Code）
- [GitKraken](https://www.gitkraken.com/)

---

### **十四、总结**

### **核心命令速查**

```bash
# 初始化与克隆
git init
git clone <url>

# 日常工作流
git add .
git commit -m "msg"
git push
git pull

# 分支管理
git branch
git checkout
git merge
git rebase

# 查看信息
git status
git log
git diff

# 撤销操作
git reset
git revert
git checkout --
```

### **Git 的优势**

1. **分布式**：每个开发者都有完整仓库
2. **高效**：本地操作快速，支持离线工作
3. **灵活**：强大的分支和合并功能
4. **安全**：使用 SHA-1 哈希保证数据完整性

### **学习建议**

1. 从基础命令开始，理解工作区-暂存区-仓库的概念
2. 熟练使用分支和合并，掌握解决冲突的方法
3. 学习高级功能如 stash、rebase、bisect
4. 建立适合团队的工作流规范

Git 是现代软件开发的基石，掌握它对于任何开发者都是必不可少的技能。
