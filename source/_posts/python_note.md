---
title: Python 相关
date: 2026-01-10 19:54:30
tags:
  - Python
---

## Python 官网

- https://www.python.org/

## Python 语言官方文档

- https://docs.python.org/zh-cn

## Python 的包管理工具

- pip

```shell
python -m pip install --upgrade pip
```

- uv
- pipx

## Python 三方依赖搜索

- https://pypi.org/

## Build a big large language model

> https://github.com/rasbt/LLMs-from-scratch

## 错误笔记

1.在安装依赖包的时候，会报下面的问题：

```plaintext
root@ECS529558553731:~# pip install openai
error: externally-managed-environment

× This environment is externally managed
╰─> To install Python packages system-wide, try apt install
    python3-xyz, where xyz is the package you are trying to
    install.

    If you wish to install a non-Debian-packaged Python package,
    create a virtual environment using python3 -m venv path/to/venv.
    Then use path/to/venv/bin/python and path/to/venv/bin/pip. Make
    sure you have python3-full installed.

    If you wish to install a non-Debian packaged Python application,
    it may be easiest to use pipx install xyz, which will manage a
    virtual environment for you. Make sure you have pipx installed.

    See /usr/share/doc/python3.12/README.venv for more information.

note: If you believe this is a mistake, please contact your Python installation or OS distribution provider. You can override this, at the risk of breaking your Python installation or OS, by passing --break-system-packages.
hint: See PEP 668 for the detailed specification.
```

如何解决这个问题呢？

可以在安装命令后面添加 `--break-system-packages`

```shell
pip install openai --break-system-packages
```

第二种方式

```shell
python3 -m venv ~/py_envs
source ~/py_envs/bin/activate
python3 -m pip install openai

# 让当前 python 虚拟环境失效的办法
deactivate
```
