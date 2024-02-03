---
title: 《Prometheus监控实战》读书笔记
date: 2024-02-03 19:54:30
tags:
  - 监控
---

# 监控简介

> [Prometheus-陈皓][4]，本书的核心是Prometheus，一个开源的监控系统，它从应用程序中实时获取时间序列数据，然后通过功能强大的规则引擎，帮助你识别监控环境所需的信息。本书可以指导你使用Prometheus来构建监控系统，重点放在动态云环境、Kubernetes环境和容器环境的监控。同时，还将研究如何从应用程序获取数据并将这些数据用于警报和可视化。

相关的开源地址：

- prometheus 官网：https://prometheus.io/
- prometheus github地址：https://github.com/prometheus/prometheus

监控是构建和管理应用程序服务层级结构的基础。

**从底向上**依次是监控，应急事件处理，事后总结/问题根源分析，测试+发布，容量规划，软件开发，产品设计。

根据服务价值设计自上而下的健康空系统是一个很好的方式，自上而下分别是业务逻辑，应用程序，操作系统，监控框架。

### 指标

- 测量型：类型是上下增减的数字，本质上是特定度量的快照。如CPU, 内存和磁盘使用率等。
- 计数型：随时间增加而不会减少的数字。
- 直方图：对观察点进行采样的指标类型。

### 指标摘要

- 计数：计算特定时间间隔内的观察点数。
- 求和：将特定时间间隔内所有观察点的值累计相加。
- 平均值：提供特定时间间隔内所有值的平均值。
- 中间数：数值的几何中点，正好50%的数值位于它前面，而另外一半位于它的后面。
- 百分位数：度量占总数特定百分比的观察点的值。
- 标准差：显示指标分布中与平均值的标准差，这可以侧聊出数据集的差异程度。标准差为0表示数据都等于平均值，较高的标准差意味着数据分布的范围很广。
- 变化率：显示时间序列中数据之间的变化程度。

## 监控方法论

### Brendan Gregg 的 USE(Utilization, Saturation和Error) 

这个方法侧重于主机级监控，其中U指的是使用率，S指的是饱和度，E指的是错误。具体[USE方法][1]描述。USE可以被概括成**针对每个资源，检查使用率，饱和度和错误。**

- **资源**：系统的一个组件。在Gregg对模型的定义中，他是一个传统意义上的物理服务器组件，入CPU,磁盘等，但许多人也将软件资源定义在内。
- **使用率**：资源忙于平时工作的平均时间。它通常用随时间变化的百分比表示。
- **饱和度**：资源排队工作的指标，无法再处理额外的工作。通常用队列长度表示。
- **错误**：资源错误事件的技术。

例如：从CPU开始

- CPU使用率随时间的百分比
- CPU饱和度，等待CPU的进程数。
- 错误，通常对CPU资源不太有影响。

### Google的四个黄金指标

这四个指标分别是**延迟，流量，错误和饱和度**。这四个指标来自于Google SRE手册，是专注与应用程序级的监控。

- **延迟**：服务请求所花费的时间，需要区分成功请求和失败请求。
- **流量**：针对系统，如每秒HTTP的请求数，或者数据库系统的事务。
- **错误**：请求失败的速率，如HTTP 500错误等显示失败，或者是返回错误内容或无效内容等隐式失败，或者是基于策略原因导致的失败。
- **饱和度**：应用程序有多“满”，或者受限的资源，如内存，IO【网络IO，磁盘IO】，磁盘。

### Weaveworks的RED(Rate, Error和Duration)

可以读这篇[文章][2]，也可以看下这个[PPT][3]。

## 警报和通知

要建立一个出色的通知系统，需要考虑一下基础信息：

- 哪些问题需要通知
- 谁需要被告知
- 如何告知他们
- 多久告知他们一次
- 何时停止告知以及何时升级到其他人

### 通知的标准

- 使通知清晰、准确、可操作。使用由人而不是计算机编写的通知在清晰度和实用性方面有显著差异
- 为通知添加上下文，通知应包含组件的其他相关信息
- 仅发有意义的通知。

## 可视化

### 可视化构建

- 清晰地显示数据
- 引发思考（而不是视觉效果）
- 避免扭曲数据
- 使数据集保持一致
- 允许更改颗粒度而不影响理解

### 推荐书籍

- The Visual Display of Quantitative Information-Edward Tufte
- https://www.datadoghq.com/blog/timeseries-metric-graphs-101/
- The Art of Monitoring
- https://riemann.io/



# Prometheus安装

### 下载软件`Prometheus`

- https://prometheus.io/download/

在Linux上安装prometheus

```shell
cd ~
mkdir prometheus
cd prometheus
wget https://github.com/prometheus/prometheus/releases/download/v2.49.1/prometheus-2.49.1.linux-amd64.tar.gz
tar xvf prometheus-2.49.1.linux-amd64.tar.gz
```

Prometheus默认的配置

```yml
# my global config
global:
  scrape_interval: 15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
  evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.
  # scrape_timeout is set to the global default (10s).

# Alertmanager configuration
alerting:
  alertmanagers:
    - static_configs:
        - targets:
          # - alertmanager:9093

# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
rule_files:
  # - "first_rules.yml"
  # - "second_rules.yml"

# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
  - job_name: "prometheus"

    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.

    static_configs:
      - targets: ["localhost:9090"]
```

- global: 控制prometheus服务行为的安全配置
  - scrape_interval: 指定应用程序或服务抓取数据的时间间隔
  - evaluation_interval: 用来指定Prometheus评估规则的频率。目前有两种规则：
    - 记录规则：允许预先计算使用频率且开销大的表达式，并将结果保存为一个新的时间序列数据。
    - 报警规则：允许定义报警条件
- alerting: 用来设置Prometheus的报警
- rule_files: 指定包含记录规则或报警规则的文件列表。
- scrape_configs：用来指定prometheus抓取的所有目标。

### 启动程序

```shell
mkdir -p /etc/prometheus
cp prometheus.yml /etc/prometheus/
prometheus --help
prometheus --config.file "/etc/prometheus/prometheus.yml"

# 代码校验工具
promtool check config prometheus.yml
Checking prometheus.yml
 SUCCESS: prometheus.yml is valid prometheus config file syntax
```

### 使用Docker运行Prometheus

```shell
docker run -p 9090:9090 -v /tmp/prometheus.yml:/etc/prometheus/
```

访问：http://ip:9090/graph

https://prometheus.io/docs/prometheus/latest/querying/basics/

```json
{quantile="0.5",instance="localhost:9090",__name__="go_gc_duration_seconds"}
```

https://graphiteapp.org/#gettingStarted

https://www.robustperception.io/translating-between-monitoring-languages/

### 使用prometheus + grafana + node_exporter搭建机器的监控

#### 下载

- prometheus的下载见上面的步骤
- grafana的下载：https://grafana.com/grafana/download?edition=oss
  - 分两个版本，一个是企业版，一个是OSS版本，也可以使用docker版本
  - `docker run -d -p 3000:3000 --name grafana grafana/grafana-enterprise:9.4.7`

- node_exporter 下载：https://github.com/prometheus/node_exporter

#### 安装

- 要实现`Node Exporter Full`这个功能，请先参考文章，https://grafana.com/grafana/dashboards/1860-node-exporter-full/

- 先配置`prometheus`，在`/etc/prometheus/prometheus.yml`中添加如下配置

```yml
# my global config
global:
  scrape_interval: 15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
  evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.
  # scrape_timeout is set to the global default (10s).

# Alertmanager configuration
alerting:
  alertmanagers:
    - static_configs:
        - targets:
          # - alertmanager:9093

rule_files:
  # - "first_rules.yml"
  # - "second_rules.yml"

scrape_configs:
  - job_name: "prometheus"
    static_configs:
      - targets: ["localhost:9090"]  
  - job_name: node
    static_configs:
      - targets: ['localhost:9100']
```

- 配置完成后启动`prometheus`

```shell
prometheus --config.file "/etc/prometheus/prometheus.yml"
```

- 把node_exporter命令放入路径`/usr/local/bin`路径下，并启动node_exporter。

```shell
node_exporter --collector.systemd --collector.processes
```

- 解压grafana软件包，或者使用grafana docker镜像

```shell
# 命令行方式
./grafana_server

# docker 启动方式
docker run -d -p 3000:3000 --name grafana grafana/grafana-enterprise:9.4.7
```

到此，都全部都配置完成了。

### 配置监控

验证prometheus是否启动成功，浏览器地址栏中输入 `ip:9090/graph`，看是否有下面的页面。

![1](https://github.com/MarkShen1992/markshen1992.github.io/assets/40328786/4cc08049-13a3-42c2-adad-6918e7e4eba7)

接下来是验证grafana是否启动成功，在浏览器中输入`ip:3000`,能看到下面页面就成功了。

![2](https://github.com/MarkShen1992/markshen1992.github.io/assets/40328786/a6983db4-68f5-4f59-b5b5-a0acd1baea57)

输入用户名和密码，默认的是`admin/admin`. 用默认密码登录后，请马上更新新的密码。

![3](https://github.com/MarkShen1992/markshen1992.github.io/assets/40328786/5e79a6d1-200d-416c-8661-0e6cf65f1846)

接下来创建dashboard，新建一个tab页，输入网址https://grafana.com/grafana/dashboards/1860-node-exporter-full/

![4](https://github.com/MarkShen1992/markshen1992.github.io/assets/40328786/f00ffda4-f33e-4dd3-926b-3f6fd961fa15)

具体操作如下面这个gif图片中所示，按照图片下来就已经配置完成了，这里还用到了视频转gif的网站：https://www.img2go.com/zh/convert-video-to-gif

![Video_2024-02-03_214731](https://github.com/MarkShen1992/markshen1992.github.io/assets/40328786/e6c63884-c309-4cac-a4db-20312d35bc6e)

本文暂时告一段落~



[1]: https://www.brendangregg.com/usemethod.html
[2]: https://www.weave.works/blog/the-red-method-key-metrics-for-microservices-architecture/
[3]: https://grafana.com/files/grafanacon_eu_2018/Tom_Wilkie_GrafanaCon_EU_2018.pdf
[4]: https://www.bilibili.com/video/BV1a64y1X7ys/
