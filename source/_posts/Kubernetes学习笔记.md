---
title: Kubernetes学习笔记
date: 2022-12-07 14:10:11
tags:
  - Java
  - 运维
  - 云原生
---

## Kubernetes 学习资料

- [kubernetes 官网](https://kubernetes.io/)
- [Kubernetes 基础教程](https://lib.jimmysong.io/kubernetes-handbook/)
- [Kubernetes 加固指南](https://lib.jimmysong.io/kubernetes-hardening-guidance/)
- [kubernetes-the-hard-way](https://github.com/kelseyhightower/kubernetes-the-hard-way)

## Kubernetes 常用命令

### 集群信息查看

```bash
kubectl cluster-info                    # 查看集群信息
kubectl version                         # 查看客户端和服务端版本
kubectl get nodes                       # 查看节点列表
kubectl describe node <node-name>       # 查看节点详细信息
kubectl top nodes                       # 查看节点资源使用情况
kubectl get componentstatuses           # 查看集群组件状态
```

### 命名空间操作

```bash
kubectl get namespaces                  # 查看所有命名空间
kubectl create namespace <name>         # 创建命名空间
kubectl delete namespace <name>         # 删除命名空间
kubectl config set-context --current --namespace=<name>  # 切换默认命名空间
```

### 查看 Pod

```bash
kubectl get pods                        # 查看当前命名空间的 Pod
kubectl get pods -A                     # 查看所有命名空间的 Pod
kubectl get pods -n <namespace>         # 查看指定命名空间的 Pod
kubectl get pods -o wide                # 查看 Pod 详细信息（包括 IP、节点等）
kubectl get pods --show-labels          # 显示 Pod 标签
kubectl get pods -l app=nginx           # 根据标签筛选 Pod
kubectl describe pod <pod-name>         # 查看 Pod 详细描述
kubectl top pod <pod-name>              # 查看 Pod 资源使用情况
```

### 创建和删除 Pod

```bash
kubectl run <pod-name> --image=<image>  # 快速创建 Pod
kubectl create -f <file.yaml>           # 根据 YAML 文件创建资源
kubectl apply -f <file.yaml>            # 应用 YAML 配置（推荐）
kubectl delete pod <pod-name>           # 删除 Pod
kubectl delete -f <file.yaml>           # 根据 YAML 文件删除资源
kubectl delete pods --all               # 删除所有 Pod
```

### Pod 日志和调试

```bash
kubectl logs <pod-name>                 # 查看 Pod 日志
kubectl logs <pod-name> -c <container>  # 查看多容器 Pod 中指定容器的日志
kubectl logs -f <pod-name>              # 实时查看日志
kubectl logs --tail=100 <pod-name>      # 查看最后 100 行日志
kubectl logs --since=1h <pod-name>      # 查看最近 1 小时的日志
kubectl exec -it <pod-name> -- /bin/bash  # 进入 Pod 执行命令
kubectl exec <pod-name> -- <command>    # 在 Pod 中执行命令
kubectl port-forward <pod-name> 8080:80 # 端口转发
kubectl cp <pod-name>:/path /local/path # 从 Pod 复制文件
```

### Deployment 操作

```bash
kubectl get deployments                 # 查看 Deployment
kubectl describe deployment <name>      # 查看 Deployment 详情
kubectl create deployment <name> --image=<image>  # 创建 Deployment
kubectl delete deployment <name>        # 删除 Deployment

# 扩缩容
kubectl scale deployment <name> --replicas=3  # 扩展副本数

# 更新
kubectl set image deployment/<name> <container>=<new-image>  # 更新镜像
kubectl rollout status deployment/<name>      # 查看滚动更新状态
kubectl rollout history deployment/<name>     # 查看更新历史
kubectl rollout undo deployment/<name>        # 回滚到上一版本
kubectl rollout undo deployment/<name> --to-revision=2  # 回滚到指定版本
kubectl rollout restart deployment/<name>     # 重启 Deployment

# 编辑
kubectl edit deployment <name>          # 在线编辑 Deployment
```

### Service 操作

```bash
kubectl get services                    # 查看 Service
kubectl get svc                         # 简写形式
kubectl describe service <name>         # 查看 Service 详情
kubectl expose deployment <name> --port=80 --type=NodePort  # 暴露服务
kubectl delete service <name>           # 删除 Service
```

### ConfigMap

```bash
kubectl get configmaps                  # 查看 ConfigMap
kubectl describe configmap <name>       # 查看详情
kubectl create configmap <name> --from-file=<file>  # 从文件创建
kubectl create configmap <name> --from-literal=key=value  # 从字面值创建
kubectl delete configmap <name>         # 删除
```

### Secret

```bash
kubectl get secrets                     # 查看 Secret
kubectl describe secret <name>          # 查看详情
kubectl create secret generic <name> --from-literal=password=123456
kubectl create secret docker-registry <name> --docker-server=<server> \
  --docker-username=<user> --docker-password=<pwd>  # 创建镜像拉取凭证
kubectl delete secret <name>            # 删除
```

### StatefulSet

```bash
kubectl get statefulsets
kubectl describe statefulset <name>
kubectl delete statefulset <name>
kubectl scale statefulset <name> --replicas=3
```

### DaemonSet

```bash
kubectl get daemonsets
kubectl describe daemonset <name>
kubectl delete daemonset <name>
```

### Job 和 CronJob

```bash
kubectl get jobs
kubectl get cronjobs
kubectl describe job <name>
kubectl delete job <name>
```

### Ingress

```bash
kubectl get ingress
kubectl describe ingress <name>
kubectl delete ingress <name>
```

### PersistentVolume 和 PersistentVolumeClaim

```bash
kubectl get pv                          # 查看持久卷
kubectl get pvc                         # 查看持久卷声明
kubectl describe pv <name>
kubectl describe pvc <name>
```

### 标签和注解

```bash
kubectl label pods <pod-name> env=prod  # 添加标签
kubectl label pods <pod-name> env-      # 删除标签
kubectl annotate pods <pod-name> description="my app"  # 添加注解
```

### 资源管理

```bash
kubectl apply -f <directory>/           # 应用目录下所有 YAML
kubectl diff -f <file.yaml>             # 查看配置差异
kubectl replace -f <file.yaml>          # 替换资源
kubectl patch deployment <name> -p '{"spec":{"replicas":3}}'  # 部分更新
kubectl get all                         # 查看所有资源
kubectl delete all --all                # 删除所有资源（危险操作）
```

### 上下文和配置

```bash
kubectl config view                     # 查看配置
kubectl config get-contexts             # 查看所有上下文
kubectl config current-context          # 查看当前上下文
kubectl config use-context <context>    # 切换上下文
kubectl config set-context <context> --namespace=<ns>  # 设置上下文命名空间
```

### 故障排查

```bash
kubectl get events                      # 查看事件
kubectl get events --sort-by=.metadata.creationTimestamp  # 按时间排序
kubectl describe <resource> <name>      # 查看资源详细信息
kubectl logs <pod-name> --previous      # 查看上一个容器的日志（容器重启后）
kubectl get pods --field-selector=status.phase=Failed  # 查看失败的 Pod
```

### 常用输出格式

```bash
kubectl get pods -o json                # JSON 格式输出
kubectl get pods -o yaml                # YAML 格式输出
kubectl get pods -o wide                # 宽格式输出
kubectl get pods -o name                # 仅输出名称
kubectl get pods -o jsonpath='{.items[*].metadata.name}'  # JSONPath 输出
```

### 其他实用命令

```bash
kubectl api-resources                   # 查看所有资源类型
kubectl explain pod                     # 查看资源定义说明
kubectl explain pod.spec                # 查看资源字段说明
kubectl drain <node-name>               # 驱逐节点上的 Pod
kubectl cordon <node-name>              # 标记节点为不可调度
kubectl uncordon <node-name>            # 取消节点不可调度状态
kubectl taint nodes <node> key=value:NoSchedule  # 添加污点
kubectl auth can-i create pods          # 检查权限
```

这些是 Kubernetes 中最常用的命令，实际使用时可以通过 `kubectl <command> --help` 查看更详细的用法。

## 云原生 学习资料

- [云原生基础架构](https://lib.jimmysong.io/cloud-native-infra/)
- [awesome-cloud-native](https://github.com/rootsongjc/awesome-cloud-native)
- [CNCF](https://www.cncf.io/)
- [online programs](https://www.cncf.io/online-programs/)
