---
title: Linux 压缩、解压缩
date: 2025-11-11 19:30:51
tags:
  - Linux
category:
  - Linux
---

# Linux 压缩和解压缩命令：tar、zip、unzip 使用指南

## tar 命令

`tar` 是 Linux 系统中最常用的归档工具，可以将多个文件打包成一个文件，还可以结合压缩功能。

### 常用选项

- `-c`：创建新的归档文件
- `-x`：解开归档文件
- `-f`：指定归档文件名
- `-v`：详细显示处理过程
- `-z`：使用 gzip 压缩/解压
- `-j`：使用 bzip2 压缩/解压
- `-J`：使用 xz 压缩/解压
- `-t`：列出归档内容
- `-C`：指定解压目录

### 常用示例

**创建 tar 归档（不压缩）**

```bash
tar -cvf archive.tar file1 file2 directory1
```

**创建 tar.gz 压缩包**

```bash
tar -czvf archive.tar.gz file1 file2 directory1
```

**创建 tar.bz2 压缩包**

```bash
tar -cjvf archive.tar.bz2 file1 file2 directory1
```

**创建 tar.xz 压缩包**

```bash
tar -cJvf archive.tar.xz file1 file2 directory1
```

**解压 tar 文件**

```bash
tar -xvf archive.tar
```

**解压 tar.gz 文件**

```bash
tar -xzvf archive.tar.gz
```

**解压 tar.bz2 文件**

```bash
tar -xjvf archive.tar.bz2
```

**解压 tar.xz 文件**

```bash
tar -xJvf archive.tar.xz
```

**解压到指定目录**

```bash
tar -xzvf archive.tar.gz -C /target/directory
```

**查看压缩包内容（不解压）**

```bash
tar -tvf archive.tar
```

## zip 命令

`zip` 用于创建 ZIP 格式的压缩文件，兼容性好，常用于跨平台文件传输。

### 常用选项

- `-r`：递归处理目录
- `-q`：安静模式，不显示压缩过程
- `-9`：最大压缩率
- `-e`：加密压缩文件
- `-u`：更新文件
- `-j`：不保存目录结构

### 常用示例

**创建 zip 压缩包**

```bash
zip archive.zip file1 file2
```

**递归压缩目录**

```bash
zip -r archive.zip directory1
```

**创建加密 zip 压缩包**

```bash
zip -e archive.zip file1 file2
# 会提示输入密码
```

**最大压缩率压缩**

```bash
zip -9r archive.zip directory1
```

**不显示压缩过程**

```bash
zip -qr archive.zip directory1
```

## unzip 命令

`unzip` 用于解压缩 ZIP 格式的文件。

### 常用选项

- `-d`：指定解压目录
- `-l`：列出压缩包内容而不解压
- `-q`：安静模式
- `-o`：覆盖已存在文件
- `-n`：不覆盖已存在文件

### 常用示例

**解压 zip 文件到当前目录**

```bash
unzip archive.zip
```

**解压 zip 文件到指定目录**

```bash
unzip archive.zip -d /target/directory
```

**列出 zip 文件内容（不解压）**

```bash
unzip -l archive.zip
```

**安静模式解压**

```bash
unzip -q archive.zip
```

**解压并覆盖已有文件**

```bash
unzip -o archive.zip
```

**解压但不覆盖已有文件**

```bash
unzip -n archive.zip
```

**解压加密的 zip 文件**

```bash
unzip archive.zip
# 会提示输入密码
```

## 总结

- `tar` 主要用于 Linux 系统内的文件归档和压缩，支持多种压缩格式
- `zip/unzip` 更适合跨平台文件传输，兼容性好
- 对于 Linux 系统内部使用，通常推荐使用 `tar + gzip/bzip2/xz`
- 需要与 Windows 或其他系统交换文件时，常用 `zip/unzip`
