---
title: 规划 StreamNative Platform 部署
id: sn-plan
category: operator-guides
---

本文介绍了部署 StreamNative Platform 的推荐选项。

# 部署流程

一般来说，手动安装 StreamNative Platform 和部署 Pulsar 集群的流程包括以下步骤：

> **注意**
>
> 查看 StreamNative Platform 部署的先决条件。详细内容参见[先决条件](#先决条件)。

1. 准备好 Kubernetes 环境。

2. 配置 StreamNative Platform。

3. 部署 StreamNative Platform。

4. 管理 StreamNative Platform。

5. 监控 StreamNative Platform。

# 先决条件

本节列出了安装 StreamNative Platform 的最低要求。

## 软件要求

本表列出了安装 StreamNative Platform 的软件要求。

<table>
  <tr>
   <td>软件名
   </td>
   <td>版本
   </td>
  </tr>
  <tr>
   <td>Docker
   </td>
   <td>Docker CE v18.09.6
   </td>
  </tr>
  <tr>
   <td>Kubernetes
   </td>
   <td>v1.16 或更高
   </td>
  </tr>
  <tr>
   <td>CentOS
   </td>
   <td>v7.6 和 kernel v3.10.0-957 或更高
   </td>
  </tr>
</table>

## 硬件要求

本表列出了用于测试的 3 节点 Kubernetes 集群的最低硬件要求。可根据你的生产环境要求来定制 Kubernetes 的节点配置。 

| 项目 | 数值 |
| --- | --- |
| CPU | 4 核 |
| 内存 | 16 GB|
| 磁盘 | 50 GB |

## 所需工具

* [kubectl](https://kubernetes.io/docs/tasks/tools/#kubectl) v1.16 或更高版本。
* [Helm](https://helm.sh/docs/intro/install/) v3.0.0 或更高版本。
* [pulsarctl](https://github.com/streamnative/pulsarctl#install) v2.8.0 CLI（命令行工具）。

## 配置本地 PV 和存储类

如果你部署了一个本地的 Kubernetes 集群，则需要配置本地 PV 和存储类，实现数据持久化本地存储。详情参见[本地 PV 和存储类](/operator-guides/configure/storage.md#local-pvs-and-storage-classes)。

# Docker 注册服务器

可以从 Docker 注册服务器获得部署 StreamNative Platform 所需的所有 Docker 镜像，然后将其部署到你的 Kubernetes 集群上。

> **注意**
>
> 默认情况下，所有公开可用的 Docker 镜像都托管在 Docker Hub 的 `streamnative` 镜像仓库中。

如果选择使用自己的 Docker 注册服务器和镜像仓库，则必须从 `streamnative` 镜像仓库中提取镜像，并将它们上传到你自己的 Docker 镜像仓库。

## 预加载 Docker 镜像

如果无法访问互联网，可以在安装 StreamNative Platform 之前预加载 Docker 镜像。Docker 镜像包含应用程序代码、库、工具、依赖项和其他使应用程序运行所需的文件。运行一个镜像时，它将成为一个或多个容器的实例。支持的 Docker 镜像及其下载链接，请参阅[此处](/operator-guides/operator-reference/sn-version-matrix.md)。

可以使用 [`docker load`](https://docs.docker.com/engine/reference/commandline/load/) 命令，将这些镜像上传到你自己的 Docker 服务器的镜像仓库。

# 存储

你必须为所有 StreamNative Platform 组件提供动态持久存储。如果部署本地 Kubernetes 集群，则需要配置本地 PV 和存储类，以便将数据持久化到本地存储。详情参见[配置存储](/operator-guides/configure/storage.md)。

# 安全

本节介绍 Kubernetes 安全和 StreamNative Platform 安全。

## Kubernetes 安全

借助 Kubernetes 基于角色的访问控制 (RBAC) 和命名空间，你可以通过以下两种方式之一部署 StreamNative Platform：

- （推荐）提供访问权限，允许在一个指定的命名空间中配置和管理 StreamNative Platform 资源。

- 提供访问权限，允许在 Kubernetes 集群的所有命名空间中配置和管理 StreamNative Platform 资源。

## StreamNative Platform 安全

StreamNative Platform 通过以下流程增强安全性。 

- 验证

- 授权

- 网络加密

- 配置密文

对于生产环境的部署，StreamNative Platform 推荐以下安全机制：

- 为 Kafka 客户端身份验证启用 SSL/Plain。

- 为 Pulsar 客户端身份验证启用 JSON Web Token (JWT) 身份验证。

- 对 StreamNative Platform 组件之间的内部通讯和从客户端到 StreamNative Platform 组件的外部通讯，启用 TLS 进行网络加密。

# 联网

客户端应用程序可以通过以下不同方式，访问部署在同一个 Kubernetes 集群或不同集群中的 StreamNative Platform 组件。

- 如果 StreamNative Platform 组件部署在同一个 Kubernetes 集群中，可通过 `ClusterIP` 模式进行访问。

- 如果 StreamNative Platform 组件部署在 Kubernetes 集群之外，可通过 `NodePort` 或 `LoadBalancer` 模式进行访问。

详情参见[配置联网](/operator-guides/configure/networking.md)。