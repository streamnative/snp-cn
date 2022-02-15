---
title: 配置 KoP
id: kop
category: operator-guides
---

Kafka on Pulsar（KoP）通过在 Pulsar broker 上引入 Kafka 协议处理程序，实现了 Apache Pulsar 对原生 Apache Kafka 协议的支持 。通过向现有的 Pulsar 集群添加 KoP 协议处理程序，可以将现有的 Kafka 应用程序和服务迁移到 Pulsar 上，而不需要修改代码。

在 Kubernetes 集群外访问 KoP ，需要安装 [Istio](https://istio.io/latest/about/service-mesh/) 并启用 TLS。

# 安装 Istio 以实现 KoP 访问

[Istio](https://istio.io/latest/about/service-mesh/) 是一个开源服务网格，可以透明地分层到现有的分布式应用程序上。它提供了一种统一和更有效的方式来保护、连接和监视服务。StreamNative Platform 使用 Istio 服务来暴露 KoP 服务。

> **注意**
> 
> StreamNative Platform 为 Istio 服务暴露的端口是 9093。

1. 下载 Istio 并切换到目标目录。

    ```
    curl -L https://istio.io/downloadIstio | sh -
    cd istio-*
    ```

2. 下载 Istio 配置文件并安装 Istio。

    ```
    wget https://raw.githubusercontent.com/streamnative/examples/master/platform/istio.yaml
    bin/istioctl install -y -f ./istio.yaml
    ```

# 启用 KoP

按照如下步骤启用 KoP：

1. 为 KoP 启用 TLS。详情参见[使用手动生成的证书在 KoP 上启用 TLS](/operator-guides/configure/security/network-encryption/tls-proxy.md#使用手动生成的证书在-kop-上启用-tls)。

2. 在 Pulsar 的 YAML 配置文件中设置以下内容：

    ```
    # The domain name for accessing KoP outside from the Kubernetes cluster.
    advertisedDomain: "kop.sn.dev"
    kop:
      enabled: true
    istio:
      gateway:
        selector:
          # set your istio gateway label here
          istio: "ingressgateway"
    ```

3. 使新配置生效。

    ```
    helm upgrade -f /path/to/your/file.yaml CLUSTER_NAME $PULSAR_CHART/
    ```
