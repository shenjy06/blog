---
title: 如何制作网站镜像
date: 2026-01-11 19:54:30
tags:
  - Linux
---

prompt> 如何使用 wget 镜像一个网站，在镜像的过程中还不会被发现？

DeepSeek-v3:
使用 `wget` 镜像网站时，如果不希望被目标网站轻易发现，需要注意以下几点：**降低请求频率、模拟合法用户行为、遵守 `robots.txt` 规则**。但请注意：

### ⚠️ 法律与道德提示

1. **务必遵守目标网站的 `robots.txt` 规则**（除非你明确知道自己在做什么）。
2. **未经授权的镜像可能违反服务条款或法律**（如数据隐私法、版权法等）。
3. 以下方法仅供**合法用途**（如备份自有网站、研究公开数据等）。

---

### 📌 基础镜像命令（静默模式）

```bash
wget \
  --mirror             \ # 递归下载整个网站
  --convert-links      \ # 转换链接为本地可用形式
  --adjust-extension   \ # 添加文件扩展名（如.html）
  --page-requisites    \ # 下载页面所需资源（CSS/JS/图片）
  --no-parent          \ # 不下载父目录内容
  --random-wait        \ # 随机延迟（0.5~1.5秒）
  --wait=1             \ # 基础延迟1秒
  --limit-rate=200k    \ # 限速200KB/s
  --user-agent="Mozilla/5.0 (Windows NT 10.0; rv:91.0) Gecko/20100101 Firefox/91.0" \ # 伪装UA
  --execute robots=off \ # 忽略robots.txt（慎用！）
  https://example.com
```

---

### 🛡️ 降低被检测概率的技巧

1. **伪装浏览器流量**：

   ```bash
   --user-agent="Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/103.0.5060.114 Safari/537.36"
   ```

2. **控制请求速率**：

   ```bash
   --wait=2                # 最小2秒间隔
   --random-wait           # 实际等待时间=wait*(0.5~1.5)
   --limit-rate=100k       # 限制带宽
   ```

3. **分散请求时间**：

   ```bash
   --restrict-file-names=unix \ # 避免特殊字符
   --timestamping           \ # 仅下载更新的文件
   ```

4. **使用代理/轮换 IP**（如需）：
   ```bash
   --header="X-Forwarded-For: 123.123.123.123" \
   --proxy=on --proxy-user=user --proxy-password=pass
   ```

---

### 🚨 高风险操作（不推荐）

```bash
--execute robots=off      \ # 强制忽略robots.txt
--reject-regex "logout|admin" \ # 排除敏感路径
--quiet                   \ # 完全静默模式
```

---

### 📦 事后处理建议

1. **检查下载内容**：
   ```bash
   grep -r "captcha" ./downloaded_site/  # 搜索是否存在反爬提示
   ```
2. **离线测试**：
   使用本地 Web 服务器（如 `python3 -m http.server`）测试镜像是否完整。

---

### 🌐 合法替代方案

如果目标网站提供：

- **API 接口**（检查 `/api/` 或开发者文档）
- **官方数据导出功能**
- **RSS/XML 馈送**
  优先使用这些方式获取数据。

务必谨慎操作，避免对目标服务器造成负担。
