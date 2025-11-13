---
title: Docker 常用命令完整列表
date: 2025-05-29 18:00:30
tags:
  - Docker
---

## 一、基础命令

```bash
docker version          # 查看 Docker 版本信息
docker info            # 查看 Docker 系统信息
docker --help          # 查看 Docker 帮助
docker [命令] --help   # 查看具体命令的帮助
```

## 二、镜像管理

### 查看镜像

```bash
docker images                    # 列出本地所有镜像
docker images -a                 # 列出所有镜像（包括中间层）
docker images -q                 # 只显示镜像 ID
docker images --digests          # 显示镜像摘要信息
docker images --no-trunc         # 显示完整的镜像信息
docker image ls                  # 列出镜像（新语法）
docker search [镜像名]            # 在 Docker Hub 搜索镜像
```

### 获取镜像

```bash
docker pull [镜像名][:标签]       # 拉取镜像
docker pull -a [镜像名]          # 拉取所有标签的镜像
```

### 构建镜像

```bash
docker build -t [镜像名][:标签] [路径]     # 从 Dockerfile 构建镜像
docker build -f [Dockerfile路径] .        # 指定 Dockerfile 构建
docker build --no-cache -t [镜像名] .     # 不使用缓存构建
docker commit [容器ID] [镜像名][:标签]     # 从容器创建镜像
```

### 删除镜像

```bash
docker rmi [镜像ID/名称]                  # 删除镜像
docker rmi -f [镜像ID]                    # 强制删除镜像
docker rmi $(docker images -q)            # 删除所有镜像
docker image prune                        # 删除悬空镜像
docker image prune -a                     # 删除所有未使用的镜像
docker image prune -a --filter "until=24h"  # 删除24小时前的镜像
```

### 镜像导入导出

```bash
docker save -o [文件名.tar] [镜像名]      # 导出镜像
docker save [镜像名] > [文件名.tar]       # 导出镜像（另一种方式）
docker load -i [文件名.tar]               # 导入镜像
docker load < [文件名.tar]                # 导入镜像（另一种方式）
docker export [容器ID] > [文件名.tar]     # 导出容器为镜像
docker import [文件名.tar] [镜像名][:标签] # 导入容器快照
```

### 镜像标签

```bash
docker tag [源镜像][:标签] [目标镜像][:标签]  # 为镜像打标签
docker image inspect [镜像名]                 # 查看镜像详细信息
docker history [镜像名]                       # 查看镜像历史
```

## 三、容器管理

### 查看容器

```bash
docker ps                      # 列出运行中的容器
docker ps -a                   # 列出所有容器
docker ps -l                   # 列出最近创建的容器
docker ps -n 5                 # 列出最近创建的 5 个容器
docker ps -q                   # 只显示容器 ID
docker ps -s                   # 显示容器大小
docker container ls            # 列出容器（新语法）
docker container ls -a         # 列出所有容器（新语法）
```

### 创建和运行容器

```bash
docker run [镜像名]                           # 创建并启动容器
docker run -d [镜像名]                        # 后台运行容器
docker run -it [镜像名] /bin/bash             # 交互式运行容器
docker run --name [容器名] [镜像名]           # 指定容器名称
docker run -p [主机端口]:[容器端口] [镜像名]  # 端口映射
docker run -P [镜像名]                        # 随机端口映射
docker run -v [主机路径]:[容器路径] [镜像名]  # 挂载数据卷
docker run -v [数据卷名]:[容器路径] [镜像名]  # 使用命名数据卷
docker run --volumes-from [容器名] [镜像名]   # 共享数据卷
docker run -e [变量名]=[值] [镜像名]          # 设置环境变量
docker run --env-file [文件路径] [镜像名]     # 从文件读取环境变量
docker run --network [网络名] [镜像名]        # 指定网络
docker run --link [容器名]:[别名] [镜像名]    # 链接容器
docker run --restart=always [镜像名]          # 自动重启
docker run --rm [镜像名]                      # 容器停止后自动删除
docker run -m [内存限制] [镜像名]             # 限制内存
docker run --cpus=[CPU数量] [镜像名]          # 限制 CPU
docker run -w [工作目录] [镜像名]             # 指定工作目录
docker run -u [用户名/UID] [镜像名]           # 指定用户
docker run --privileged [镜像名]              # 特权模式
docker run -h [主机名] [镜像名]               # 设置主机名
docker create [镜像名]                        # 创建容器但不启动
```

### 启动和停止容器

```bash
docker start [容器ID/名称]                    # 启动容器
docker start -i [容器ID/名称]                 # 启动并进入容器
docker stop [容器ID/名称]                     # 停止容器
docker stop $(docker ps -q)                   # 停止所有运行的容器
docker restart [容器ID/名称]                  # 重启容器
docker pause [容器ID/名称]                    # 暂停容器
docker unpause [容器ID/名称]                  # 恢复容器
docker kill [容器ID/名称]                     # 强制停止容器
```

### 删除容器

```bash
docker rm [容器ID/名称]                       # 删除容器
docker rm -f [容器ID/名称]                    # 强制删除运行中的容器
docker rm $(docker ps -aq)                    # 删除所有容器
docker rm $(docker ps -qf status=exited)      # 删除所有停止的容器
docker container prune                        # 删除所有停止的容器
docker container prune -f                     # 强制删除所有停止的容器
```

### 容器操作

```bash
docker exec -it [容器ID/名称] [命令]          # 在运行的容器中执行命令
docker exec -it [容器ID] /bin/bash            # 进入容器终端
docker exec -d [容器ID] [命令]                # 后台执行命令
docker exec -u [用户名] [容器ID] [命令]       # 以指定用户执行命令
docker attach [容器ID/名称]                   # 连接到运行中的容器
docker top [容器ID/名称]                      # 查看容器中的进程
docker port [容器ID/名称]                     # 查看容器端口映射
docker rename [旧名称] [新名称]               # 重命名容器
docker update [选项] [容器ID]                 # 更新容器配置
docker wait [容器ID]                          # 阻塞直到容器停止
```

### 容器日志和信息

```bash
docker logs [容器ID/名称]                     # 查看容器日志
docker logs -f [容器ID/名称]                  # 实时查看容器日志
docker logs --tail 100 [容器ID]               # 查看最后 100 行日志
docker logs --since 30m [容器ID]              # 查看最近 30 分钟的日志
docker logs -t [容器ID]                       # 显示时间戳
docker inspect [容器ID/名称]                  # 查看容器详细信息
docker inspect --format='{{.State.Status}}' [容器ID]  # 格式化输出
docker stats                                  # 查看所有容器资源使用情况
docker stats [容器ID]                         # 查看指定容器资源使用
docker stats --no-stream                      # 只显示一次统计信息
docker diff [容器ID]                          # 查看容器文件系统变化
docker events                                 # 实时查看 Docker 事件
```

### 容器文件操作

```bash
docker cp [容器ID]:[容器路径] [主机路径]      # 从容器复制到主机
docker cp [主机路径] [容器ID]:[容器路径]      # 从主机复制到容器
```

## 四、网络管理

```bash
docker network ls                             # 列出所有网络
docker network create [网络名]                # 创建网络
docker network create --driver bridge [网络名] # 创建指定驱动的网络
docker network create --subnet=[子网] [网络名] # 创建指定子网的网络
docker network rm [网络名]                    # 删除网络
docker network prune                          # 删除所有未使用的网络
docker network inspect [网络名]               # 查看网络详细信息
docker network connect [网络名] [容器名]      # 将容器连接到网络
docker network disconnect [网络名] [容器名]   # 断开容器网络连接
```

## 五、数据卷管理

```bash
docker volume ls                              # 列出所有数据卷
docker volume create [数据卷名]               # 创建数据卷
docker volume rm [数据卷名]                   # 删除数据卷
docker volume prune                           # 删除所有未使用的数据卷
docker volume inspect [数据卷名]              # 查看数据卷详细信息
```

## 六、Docker Compose

```bash
docker-compose up                             # 启动服务
docker-compose up -d                          # 后台启动服务
docker-compose up --build                     # 重新构建并启动
docker-compose down                           # 停止并删除服务
docker-compose down -v                        # 停止并删除服务及数据卷
docker-compose start                          # 启动已存在的服务
docker-compose stop                           # 停止服务
docker-compose restart                        # 重启服务
docker-compose pause                          # 暂停服务
docker-compose unpause                        # 恢复服务
docker-compose ps                             # 列出服务
docker-compose ps -a                          # 列出所有服务
docker-compose logs                           # 查看服务日志
docker-compose logs -f                        # 实时查看服务日志
docker-compose logs [服务名]                  # 查看指定服务日志
docker-compose exec [服务名] [命令]           # 在服务中执行命令
docker-compose run [服务名] [命令]            # 运行一次性命令
docker-compose build                          # 构建服务
docker-compose build --no-cache               # 不使用缓存构建
docker-compose pull                           # 拉取服务镜像
docker-compose push                           # 推送服务镜像
docker-compose config                         # 验证配置文件
docker-compose top                            # 显示运行的进程
docker-compose port [服务名] [端口]           # 显示端口映射
docker-compose scale [服务名]=[数量]          # 扩展服务
docker-compose rm                             # 删除停止的服务容器
docker-compose kill                           # 强制停止服务
```

## 七、镜像仓库

```bash
docker login                                  # 登录 Docker Hub
docker login [仓库地址]                       # 登录私有仓库
docker logout                                 # 登出
docker push [镜像名][:标签]                   # 推送镜像到仓库
docker pull [镜像名][:标签]                   # 从仓库拉取镜像
```

## 八、系统管理

```bash
docker system df                              # 查看磁盘使用情况
docker system df -v                           # 详细查看磁盘使用
docker system prune                           # 清理未使用的数据
docker system prune -a                        # 清理所有未使用的数据
docker system prune -a --volumes              # 清理包括数据卷
docker system events                          # 查看系统事件
docker system info                            # 查看系统信息
```

## 九、其他常用命令

```bash
docker version                                # 查看版本
docker info                                   # 查看系统信息
docker context ls                             # 列出上下文
docker context use [上下文名]                 # 切换上下文
docker plugin ls                              # 列出插件
docker plugin install [插件名]                # 安装插件
docker plugin rm [插件名]                     # 删除插件
```

## 十、常用组合命令

```bash
# 停止所有容器
docker stop $(docker ps -aq)

# 删除所有容器
docker rm $(docker ps -aq)

# 删除所有镜像
docker rmi $(docker images -q)

# 删除所有悬空镜像
docker rmi $(docker images -f "dangling=true" -q)

# 清理所有未使用的资源
docker system prune -a --volumes -f

# 进入最新创建的容器
docker exec -it $(docker ps -lq) /bin/bash

# 查看容器 IP 地址
docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' [容器ID]
```

这些是 Docker 最常用的命令，涵盖了日常开发和运维的大部分场景。
