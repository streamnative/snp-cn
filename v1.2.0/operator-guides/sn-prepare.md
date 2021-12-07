---
title: 准备 Kubernetes 集群
id: sn-prepare
category: operator-guides
---

本文档描述了在部署 StreamNative Platform 前，准备 Kubernetes 集群所需完成的任务。要完成这些任务，必须具有适当的 Kubernetes 集群级别权限。

# 为 StreamNative Platform 创建 Kubernetes 命名空间 

1. 创建用于部署 StreamNative Platform 的 Kubernetes 命名空间：

    ```
    kubectl create namespace KUBERNETES_NAMESPACE 
    ```

2. （可选）将新命名空间设置为当前的命名空间。

    ```
    kubectl config set-context --current --namespace=KUBERNETES_NAMESPACE
    ```

将新命名空间设置为当前的命名空间，就无需在后续的 `kubectl` 命令中添加 `--namespace` 参数，因为命令会默认使用当前的命名空间。

