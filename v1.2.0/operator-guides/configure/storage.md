---
title: 配置存储
id: storage
category: operator-guides
---

# 本地 PV 和存储类

> **注意**
> 
> 如果你部署了一个本地 Kubernetes 集群，则需要配置本地[持久卷（PersistentVolume，即 PV）](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)和存储类，以便将数据持久化到本地存储。

Pulsar 集群组件，如 BookKeeper 和 ZooKeeper，需要持久化存储数据。在 Kubernetes 中持久化数据需要使用 PV。PV 包含可供 Pulsar 集群使用的存储的详细信息。管理员可以使用 [存储类（StorageClass）](https://kubernetes.io/docs/concepts/storage/storage-classes/) 静态或动态地配置 PV。存储类为管理员提供了一种描述其提供的存储 "类"的方法。不同的类可以映射到服务质量（Quality-of-Service，即 QoS）级别、备份策略或由集群管理员决定的任意策略。

PV 和 Pod 受[持久卷申领（PersistentVolumeClaim，即 PVC）](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistentvolumeclaims)的约束。持久卷申领（PersistentVolumeClaim，即 PVC）是一种用户对存储的请求，和 Pod 类似。Pod 消耗节点资源，而 PVC 消耗 PV 资源。

配置本地 PV 和存储类的步骤如下：

1. 为每个集群节点预先分配本地存储。 

    下面的例子分别创建了五个固态硬盘（Solid State Drive，即 SSD）和混合硬盘（Hybrid Hard Drive，即 HDD）卷。 

    > **注意**
    > 
    > 以下代码举例仅用于测试环境。请根据你的生产环境类来配置本地存储。

    ```
    #!/bin/bash
    for i in $(seq 1 5); do
      mkdir -p /mnt/ssd-bind/vol${i}
      mkdir -p /mnt/ssd/vol${i}
      mount --bind /mnt/ssd-bind/vol${i} /mnt/ssd/vol${i}
    done
    for i in $(seq 1 5); do
      mkdir -p /mnt/hdd-bind/vol${i}
      mkdir -p /mnt/hdd/vol${i}
      mount --bind /mnt/hdd-bind/vol${i} /mnt/hdd/vol${i}
    done
    ```

2. 安装本地卷配置器。

    > **注意**
    >
    > 本地卷配置器通过检测并为主机上的每个本地磁盘创建 PV，来管理预先分配的磁盘 PV 的生命周期，然后在释放磁盘时进行清理。不支持动态配置。

    1. 定义 YAML 文件来配置本地卷配置器。

        [点击此处](https://github.com/streamnative/examples/tree/master/platform)查看用于配置本地卷配置器的 YAML 文件举例。

    2. 使用 YAML 文件来安装本地卷配置器。

        ```
        kubectl apply -f /path/to/local-volume-provisioner/file.yaml
        ```

3. 验证本地卷配置器是否成功创建。

     ```
     kubectl get po -n KUBERNETES_NAMESPACE |grep local-volume
     ```

4. 验证所有 PV 是否成功创建。

     ```
     kubectl get pv
     ```

5. 验证所有的存储类是否成功创建。

     ```
     kubectl get storageclasses
    ```

# Kubernetes 默认存储类

如果没有在 CR 中提供 `spec.storageClassName`，Pulsar operator 会使用默认的存储类。

可以使用以下命令获得当前默认存储类的名称：

```
kubectl get sc
```

如要使用 Kubernetes 默认存储类，建议在默认存储类上设置以下属性：

- `volumeBindingMode: WaitForFirstConsumer`
- `reclaimPolicy: Retain`
- `allowVolumeExpansion: true` （生产环境必填字段）