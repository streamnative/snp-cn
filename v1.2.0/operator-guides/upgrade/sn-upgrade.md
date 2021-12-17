---
title: 升级 StreamNative Platform
id: sn-upgrade
category: operator-guides
---

本文介绍了如何升级 StreamNative Platform。

# 在互联网条件下升级 StreamNative Platform

在有互联网连接的条件下，按照如下步骤升级 StreamNative Platform：

1. 获取最新版本的 Vault operator、cert-manager operator、Pulsar operator 和 Function Mesh operator。

    ```
    helm repo update
    ```

2. 使用 `helm upgrade` 命令将这些 operator 逐一升级。

- Vault operator

    ```
    helm upgrade --install vault-operator banzaicloud-stable/vault-operator -n K8S_NAMESPACE_NAME
    ```

- cert-manager operator

    ```
    helm upgrade --install cert-manager jetstack/cert-manager -n K8S_NAMESPACE_NAME --set installCRDs=true
    ```

- Pulsar operator

    ```
    helm upgrade --install pulsar-operator streamnative/pulsar-operator -n $K8S_NAMESPACE_NAME
    ```

- Function Mesh operator

    ```
    helm upgrade --install function-mesh streamnative/function-mesh-operator -n $K8S_NAMESPACE_NAME
    ```

# 在无互联网条件下升级 StreamNative Platform

当有新版本的 StreamNative Platform 可用时，可以对 StreamNative Platform 进行升级。

1. 使用 `wget` 命令下载最新的 StreamNative Platform 安装包。

    - AWS S3

        ```
        wget https://sn-products.s3.amazonaws.com/snp/v1.2.0/streamnative_platform-v1.2.0.tar.gz
        ```

    - 阿里云

        ```
        wget https://downloads-streamnative-cloud.oss-cn-beijing.aliyuncs.com/sn-products/snp/v1.2.0/streamnative_platform-v1.2.0.tar.gz
        ```

2. 将压缩包解压并使用 `cd` 命令切换到目标目录。

    ```
    tar zxvf streamnative_platform-v1.2.0.tar.gz
    cd streamnative-platform
    ```

3. 为 StreamNative Platform 的 Kubernetes 命名空间添加环境变量。

    ```
    export NAMESPACE=KUBERNETES_NAMESPACE
    ```

4. 安装 StreamNative Platform。

    ```
    sh install.sh
    ```
