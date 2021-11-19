---
title: 概述
id: overview
category: overview
---

本节介绍以下内容：
- [什么是 StreamNative Platform？](#什么是-streamnative-platform)
- [为什么选择 StreamNative Platform？](#为什么选择-streamnative-platform)
- [什么是 Apache Pulsar？](#什么是-apache-pulsar)
- [StreamNative Platform 与 Apache Pulsar 主要功能对比](#streamnative-platform-与-apache-pulsar-主要功能对比)

# 什么是 StreamNative Platform？

作为一个云原生批流融合数据平台，StreamNative Platform 可以帮助你为实时和历史事件创建实时应用程序和数据基础架构。[StreamNative](https://streamnative.io/) 由 [Apache Pulsar](https://pulsar.apache.org/en/) 和 [Apache BookKeeper](https://bookkeeper.apache.org/) 的原始团队打造，提供了一个完整的、可自我管理的平台。StreamNative Platform 提供丰富的顾客体验，可在整个组织中持续流转数据，支撑由数据驱动的业务。用户可以选择将 StreamNative Platform 部署在本地或云端。

借助 Apache Pulsar，StreamNative Platform 将不同来源的数据整合到公司统一的批流融合数据平台中，从而可以轻松创建关键性任务消息、流应用与实时数据通道。 StreamNative Platform 让你可以专注于从实时数据中最大化业务价值，而无需担心底层机制上数据如何在各个系统之间传递，以及如何可靠地存储数据以供处理等问题。

具体而言，StreamNative Platform 带来了以下便利：

- 用流行的消息传递协议（包括 Apache Pulsar 和 Apache Kafka）发布和消费消息
- 将各种数据源连接到 Pulsar
- 用 Pulsar 构建实时应用程序
- 将数据处理基础设施与 Pulsar 集成
- 保护、监控和管理 Pulsar 部署

最重要的是，StreamNative Platform 可以让你：

- 在你的私有云环境中部署和管理 Pulsar
- 将平台作为 Kubernetes 上的云原生系统进行部署和管理
- 使用 [Pulsar 侦查器](/operator-guides/configure/backup-restore/detector.md)和 StreamNative 控制台等专用工具监控 Pulsar 集群的健康和性能

![](../../image/sn-platform-architecture.png)

每个 StreamNative Platform 版本都包含了 Pulsar 的最新功能和 StreamNative 的附加功能。

# 为什么选择 StreamNative Platform？
使用 StreamNative Platform 可为你带来以下好处：

- **无限生产力**：让更多开发人员通过使用 Pulsar 提高构建事件流应用程序的效率。

- **规模化的高效运作**：随着组织中事件流的增长，最大限度地减少操作的复杂性，并确保高性能和可扩展性。

- **企业级生产**：在生产中实现具有企业级功能的事件流。

- **灵活部署**：在本地、私有云或公共云中部署，从裸机到 Kubernetes，或使用 StreamNative Platform 的完全托管云服务。

- **提交者驱动的专业知识**：Pulsar 专家和提交者的丰富经验，将在整个应用程序开发生命周期中为你提供支持。

# 什么是 Apache Pulsar？

StreamNative Platform 的核心是 [Apache Pulsar](https://pulsar.apache.org)，一个开源的分布式云原生批流融合数据平台。

Apache Pulsar 的主要功能包括：

- 规模化地发布和订阅事件流
- 使用 [Apache BookKeeper](https://bookkeeper.apache.org) 和层级存储，以可靠且可扩展的方式存储无限的事件流
- 使用 serverless 函数处理和查询事件流

Apache Pulsar 已经得到许多全球[主流技术公司](https://pulsar.apache.org/en/powered-by/)的采用，如 Verizon Media、Narvar、Overstock、Nutanix、Yahoo！JAPAN、腾讯、OVHCloud 和 Clever Cloud 等，Apache Pulsar 为这些公司提供其在性能、可扩展性和弹性方面的能力。Pulsar 的普及是市场对实时数据支持技术不断增长的需求所推动的，这些需求包括异步应用程序、核心业务应用程序以及 ETL 等。

Pulsar 的主要组件包括：

- [Broker](https://pulsar.apache.org/docs/en/concepts-architecture-overview/#brokers)

    Broker 构成了 Pulsar 的消息传递和服务层。

- [Bookie](https://bookkeeper.apache.org)

    Bookie 构成了 Pulsar 的存储层。

- [Functions Worker](https://pulsar.apache.org/docs/en/functions-overview/)

    Functions Worker 构成了 Pulsar 的无服务器（Serverless）处理层。

- [Proxy](https://pulsar.apache.org/docs/en/next/administration-proxy/)

    Pulsar proxy 是一个可选网关，运行在 Pulsar 集群的 broker 之前。

- [ZooKeeper](https://zookeeper.apache.org/)

    Pulsar 依靠 ZooKeeper 来完成各种与配置和协调相关的任务。

- 客户端 API
    - [生产者](https://pulsar.apache.org/docs/en/concepts-messaging/#producers) API：允许应用程序将事件流发布到一个或多个 Pulsar 主题。
    - [消费者](https://pulsar.apache.org/docs/en/concepts-messaging/#consumers) API：允许应用程序订阅一个或多个主题，并接收为其生成的事件流。
    - [Functions](https://pulsar.apache.org/docs/en/next/functions-overview/) API：允许应用程序创建、部署和管理 serverless 函数，这些函数从一个或多个 Pulsar 主题消费事件，将用户提供的处理逻辑应用于每个事件，并将计算结果发布到另一个主题。
    - [连接器](https://pulsar.apache.org/docs/en/next/io-overview/) API：允许应用程序创建、部署和管理与外部系统（如 Apache Cassandra、Aerospike 等等）交互的连接器。
    - [Admin API](https://pulsar.apache.org/docs/en/admin-api-overview/)：允许应用程序以可编程方式管理 Pulsar 资源（例如，命名空间、主题、functions、连接器等）。

# StreamNative Platform 与 Apache Pulsar 主要功能对比

StreamNative Platform 的每个版本都包含最新版本的 Pulsar 的全部功能与特性，并包含 StreamNative 额外的工具和服务。这些工具和服务让创建和管理云原生批流融合平台变得更加简单。StreamNative Platform 同时包含了社区版和商业版的功能，可以很好地补充并加强你的 Pulsar 部署。

下表为 Apache Pulsar 和 StreamNative Platform 功能对比。

| 功能 | Apache Pulsar | StreamNative Platform |
|----------|---------------|-----------------------|
|Pulsar Functions| √    | √                  |
|Pulsar IO       | √    | √                  |
|跨地域复制       | √    | √                  |
|多租户          | √    | √                  |
|事务            | √    | √                  |
|Pulsar Manager  | √    | √                  |
|StreamNative 控制台：Pulsar Manager 的升级版|    | √   |
|监控堆栈：集成仪表板及 Pulsar 警报规则（基于 Prometheus/Grafana/Alertmanager）|    | √   |
|多集群管理：同时管理多个 Pulsar 集群| | √                  |
|[pulsarctl](https://docs.streamnative.io/pulsarctl/v2.7.0.7/)：用于管理 Pulsar 集群的 CLI（命令行工具）。       |         | √                  |
|[Function Mesh](/concepts/functionmesh-concepts.md)：在 Kubernetes 上运行 Pulsar Functions 和连接器的无服务器（Serverless）框架  |         | √                  |
|[KoP](/concepts/kop-concepts.md)：将现有的 Kafka 应用和服务迁移到 Pulsar，无需修改代码 |   | √ |
|[备份和恢复](/operator-guides/configure/backup-restore-metadata-tool.md)：用于备份 Pulsar 集群元数据并将集群恢复到备份点的工具|         | √        |
|[Pulsar 侦查器](/operator-guides/configure/detector.md)：用于监测 Pulsar 健康状况的新工具 | | √ |

[开始使用](/quickstart.md) StreamNative Platform。