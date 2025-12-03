---
title: logrotate 命令使用
date: 2025-12-03 19:54:30
tags:
  - Linux
---

# Linux logrotate 命令详解

logrotate 是 Linux 系统中用于**自动管理日志文件轮转、压缩、删除和归档**的工具，防止日志文件无限增长占满磁盘空间。

---

## **一、核心概念**

### 1. **工作原理**

- **定期执行**：通过 cron 定时任务（通常每天执行一次）
- **配置驱动**：读取配置文件决定如何处理日志
- **状态记录**：记录上次轮转时间到状态文件

### 2. **典型应用场景**

- Web 服务器日志（Nginx/Apache）
- 应用日志（Java/Python 应用）
- 系统日志（syslog/rsyslog）
- 数据库日志（MySQL/PostgreSQL）

---

## **二、配置文件结构**

### 1. **主配置文件**

```bash
/etc/logrotate.conf          # 全局配置
/etc/logrotate.d/            # 各应用独立配置目录
/var/lib/logrotate/status    # 状态文件（记录轮转历史）
```

### 2. **配置文件示例**

```bash
# /etc/logrotate.d/nginx
/var/log/nginx/*.log {
    daily                    # 每天轮转
    rotate 7                 # 保留7个历史文件
    missingok               # 日志文件缺失不报错
    notifempty              # 空文件不轮转
    compress                # 压缩旧日志
    delaycompress           # 延迟一个周期压缩
    sharedscripts           # 多个日志共享脚本
    postrotate              # 轮转后执行脚本
        [ -f /var/run/nginx.pid ] && kill -USR1 `cat /var/run/nginx.pid`
    endscript
}
```

---

## **三、核心配置参数详解**

### 1. **轮转频率**

```bash
daily       # 每天轮转
weekly      # 每周轮转（默认周日）
monthly     # 每月轮转
yearly      # 每年轮转
size 100M   # 文件达到100MB时轮转（优先级高于时间）
```

### 2. **保留策略**

```bash
rotate 7              # 保留7个历史文件
maxage 30             # 删除30天前的日志
minsize 100k          # 文件小于100KB不轮转
maxsize 500M          # 超过500MB强制轮转（即使未到时间）
```

### 3. **压缩选项**

```bash
compress              # 使用gzip压缩
nocompress            # 不压缩
delaycompress         # 延迟到下次轮转时压缩（保留最近一次未压缩）
compresscmd /usr/bin/bzip2   # 指定压缩命令
compressext .bz2      # 压缩文件扩展名
compressoptions "-9"  # 压缩参数（最高压缩率）
```

### 4. **文件命名**

```bash
dateext               # 使用日期作为后缀（access.log-20240115）
dateformat -%Y%m%d    # 自定义日期格式
extension .log        # 保留原始扩展名
olddir /var/log/old   # 旧日志移动到指定目录
noolddir              # 旧日志保留在原目录
```

### 5. **权限控制**

```bash
create 0644 nginx nginx   # 创建新日志文件的权限和所有者
nocreate                  # 不创建新文件
copytruncate              # 复制后清空原文件（适用于持续写入的进程）
nocopytruncate            # 移动文件（默认）
```

### 6. **错误处理**

```bash
missingok       # 日志文件不存在不报错
nomissingok     # 日志文件不存在报错（默认）
notifempty      # 空文件不轮转
ifempty         # 空文件也轮转
sharedscripts   # 多个日志文件只执行一次脚本
nosharedscripts # 每个日志文件都执行脚本
```

### 7. **执行脚本**

```bash
prerotate
    # 轮转前执行的命令
    /usr/bin/chattr -a /var/log/messages
endscript

postrotate
    # 轮转后执行的命令
    /usr/bin/killall -HUP syslogd
endscript

firstaction
    # 第一个日志轮转前执行（配合sharedscripts）
endscript

lastaction
    # 最后一个日志轮转后执行
endscript
```

---

## **四、实战案例**

### 案例 1：Nginx 访问日志配置

```bash
# /etc/logrotate.d/nginx
/var/log/nginx/access.log {
    daily
    rotate 14
    missingok
    notifempty
    compress
    delaycompress
    dateext
    dateformat -%Y%m%d
    create 0640 nginx adm
    sharedscripts
    postrotate
        # 重新打开日志文件（发送 USR1 信号）
        if [ -f /var/run/nginx.pid ]; then
            kill -USR1 `cat /var/run/nginx.pid`
        fi
    endscript
}
```

**效果**：

- 每天轮转，保留 14 天
- 旧日志命名如：`access.log-20240115`
- 压缩后变成：`access.log-20240114.gz`

---

### 案例 2：应用日志按大小轮转

```bash
# /etc/logrotate.d/myapp
/opt/myapp/logs/*.log {
    size 500M
    rotate 5
    copytruncate
    compress
    notifempty
    missingok
}
```

**适用场景**：应用持续写入日志文件，无法优雅重启

---

### 案例 3：MySQL 慢查询日志

```bash
# /etc/logrotate.d/mysql
/var/log/mysql/slow.log {
    weekly
    rotate 52
    missingok
    notifempty
    compress
    delaycompress
    create 0640 mysql mysql
    postrotate
        # 刷新 MySQL 日志
        /usr/bin/mysqladmin -u root -p'password' flush-logs
    endscript
}
```

---

### 案例 4：多路径日志统一管理

```bash
# /etc/logrotate.d/app-cluster
/var/log/app1/*.log /var/log/app2/*.log {
    daily
    rotate 30
    compress
    sharedscripts
    postrotate
        systemctl reload app-cluster
    endscript
}
```

---

## **五、命令行操作**

### 1. **手动执行轮转**

```bash
# 测试配置但不实际执行（调试模式）
logrotate -d /etc/logrotate.conf

# 强制执行轮转（忽略时间限制）
logrotate -f /etc/logrotate.conf

# 详细输出执行过程
logrotate -v /etc/logrotate.conf

# 指定状态文件
logrotate -s /var/lib/logrotate/custom.status /etc/logrotate.d/nginx
```

### 2. **验证配置语法**

```bash
logrotate -d /etc/logrotate.d/nginx
```

---

## **六、常见问题排查**

### 问题 1：日志未自动轮转

**排查步骤**：

```bash
# 1. 检查 cron 任务
cat /etc/cron.daily/logrotate

# 2. 查看状态文件
cat /var/lib/logrotate/status

# 3. 手动调试
logrotate -d -f /etc/logrotate.d/nginx

# 4. 检查文件权限
ls -la /var/log/nginx/
```

**常见原因**：

- 状态文件权限问题
- 日志文件被其他进程占用
- 配置语法错误

---

### 问题 2：轮转后应用无法写入日志

**原因**：应用仍持有旧文件句柄

**解决方案**：

```bash
# 方案1：使用 copytruncate
copytruncate

# 方案2：轮转后重启/重载应用
postrotate
    systemctl reload nginx
endscript

# 方案3：应用自己管理日志轮转（如 Java Logback）
```

---

### 问题 3：压缩文件占用大量 CPU

**优化策略**：

```bash
# 1. 使用较低压缩率
compressoptions "-1"

# 2. 延迟压缩
delaycompress

# 3. 限制并发压缩数量（修改 cron 执行时间）
nice -n 19 logrotate /etc/logrotate.conf
```

---

## **七、高级技巧**

### 1. **按小时轮转**

需结合 cron 实现：

```bash
# /etc/cron.hourly/logrotate-hourly
#!/bin/bash
/usr/sbin/logrotate /etc/logrotate.d/hourly-app
```

配置文件：

```bash
/var/log/app/access.log {
    hourly        # 需自定义实现
    rotate 24
    dateformat -%Y%m%d-%H
}
```

### 2. **邮件通知**

```bash
/var/log/critical.log {
    daily
    rotate 7
    mail admin@example.com
    mailfirst      # 发送轮转前的旧日志
}
```

### 3. **远程备份**

```bash
postrotate
    rsync -az /var/log/nginx/*.gz backup@remote:/backup/
endscript
```

---

## **八、性能优化建议**

1. **避免在高峰期执行**：调整 cron 时间到凌晨 3-4 点
2. **分散轮转时间**：不同应用配置在不同时间段
3. **使用 `delaycompress`**：减少 I/O 压力
4. **定期清理旧日志**：结合 `maxage` 或独立脚本

---

## **九、安全注意事项**

1. **配置文件权限**：

```bash
chmod 644 /etc/logrotate.d/*
chown root:root /etc/logrotate.d/*
```

2. **避免脚本注入**：

```bash
# 危险示例（不要这样做）
postrotate
    eval $CUSTOM_COMMAND  # 可能被注入恶意命令
endscript
```

3. **日志敏感信息处理**：

```bash
postrotate
    # 删除旧日志中的敏感信息
    sed -i 's/password=[^&]*/password=***/g' /var/log/app/*.log.1
endscript
```

---

## **总结**

| 维度         | 关键点                                     |
| ------------ | ------------------------------------------ |
| **核心功能** | 自动轮转、压缩、删除、归档                 |
| **执行方式** | Cron 定时任务 + 配置文件驱动               |
| **重点参数** | rotate、compress、postrotate、copytruncate |
| **调试命令** | `logrotate -d -v`                          |
| **常见陷阱** | 文件句柄未释放、权限错误、时间判断失效     |

**最佳实践**：

- 优先使用应用自带日志轮转（如 Nginx 的 `reopen` 信号）
- 测试环境验证配置后再上线
- 监控磁盘空间和轮转执行状态
- 定期审查日志保留策略是否符合合规要求

完~
