---
title: git 常用命令
date: 2025-10-14 20:10:14
tags:
  - Git
category:
  - 版本控制
---

## 下载好 git 后的配置

```shell
# 配置全局用户和邮箱
git config --global user.name "shenjy"
git config --global user.email "asjdfasdfj@163.com"

# 生成公钥
ssh-keygen -t rsa -C 'asjdfasdfj@163.com'

# copy公钥
cd ~/.ssh
cat id_rsa.pub
```

## 创建新的仓库

```shell
echo "#shenjy.github.io" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/shenjy/shenjy.github.io.git
git push -u origin main
```

## 将已经存在的项目上传到 github

```shell
git remote add origin https://github.com/shenjy/shenjy.github.io.git
git branch -M main
git push -u origin main
```

完~
