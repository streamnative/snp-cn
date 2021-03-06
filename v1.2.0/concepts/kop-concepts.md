---
title: KoP
id: kop-concepts
category: concepts
---

Kafka on Pulsar (KoP) 在 Pulsar broker 上引入 Kafka 协议处理程序，使 Apache Pulsar 支持原生 Apache Kafka 协议。通过向现有 Pulsar 集群添加 KoP 协议处理程序，可在无需修改代码的情况下，将已有的 Kafka 应用程序和服务迁移到 Pulsar。这样，Kafka 应用程序也可利用 Pulsar 的强大功能，如： 

- 支持企业级多租户，操作精简流畅
- 无再平衡框架，简化操作
- 通过 Apache BookKeeper 和分层存储实现无限的事件流留存
- 利用 Pulsar Functions 进行无服务器（Serverless）事件处理 

KoP 是协议名称为 "kafka" 的 Pulsar 协议处理插件，在 Pulsar broker 启动时加载。在 Apache Pulsar 上提供原生的 Kafka 协议支持，可以帮助使用 Pulsar 的用户容易实现业务的成功。KoP  通过整合两种主流的事件流生态系统，解锁了新的应用场景。用户可发挥每个生态系统的优势，用 Apache Pulsar 建立真正统一的事件流平台，加快实时应用程序和服务的开发。

KoP 通过利用 Pulsar 已有的组件（如主题发现、分布式日志库——ManagedLedger、游标等），在 Pulsar 上实现 Kafka wire 协议。如需使用 KoP，可以下载 StreamNative Platform，并[快速上手](/quickstart.md)。

下图描述如何在 Pulsar 上添加 Kafka 协议支持。 

![](../../../image/kop-architecture.png)

# 主题

在 Kafka 中，所有的主题都存储在一个平面的命名空间中。而在 Pulsar 中，主题被分层组织到多租户命名空间中。KoP 在 broker 配置中引入 `kafkaNamespace` 设置，这一设置允许管理员将 Kafka 主题映射到 Pulsar 主题。

为了让 Kafka 用户利用 Apache Pulsar 的多租户功能，Kafka 用户在使用 SASL （“Simple Authentication and Security Layer” 的缩写，即“简单认证和安全层”）认证机制对 Kafka 客户端进行认证时，可以指定 Pulsar 租户和命名空间作为其 SASL 用户名。

## 主题查找

KoP 对 Kafka 请求处理程序（request handler）和 Pulsar 请求处理程序（request handler）使用相同的主题查找方法。请求处理程序（request handler）进行主题发现，在请求的主题分区查找所有权，并将所有权信息作为 Kafka `TopicMetadata` 的一部分返回给 Kafka 客户端。

# 消息

Kafka 消息和 Pulsar 消息都有 key、value、timestamp 和 header。（在 Pulsar 中，`headers` 被称为 `properties`。) KoP 处理 Kafka 消息和 Pulsar 消息时，会对这些字段进行自动转换。

## 消息 ID 和偏移量

Kafka 中，一旦在某个主题分区成功生产了消息，即会对这条消息分配一个偏移量。在 Pulsar 中，每条消息都被分配了一个`MessageID`。这个消息 ID 的构成包括： `ledger-id`、`entry-id`、和 `batch-index` 。KoP 在 Pulsar-Kafka 的包装中使用同样的方法，将 Pulsar 的 `MessageID ` 转换为偏移量，反之亦然。

## 生产消息

当 Kafka 请求处理程序（request handler）收到来自 Kafka 客户端生产的消息时，它通过逐一映射字段（如 key、value、timestamp 和 header）将 Kafka 消息转换为 Pulsar 消息，并使用 ManagedLedger append API 将这些转换后的 Pulsar 消息追加到 BookKeeper。将 Kafka 消息转换为 Pulsar 后，现有的 Pulsar 应用程序就能消费 Kafka 客户端生产的消息。

## 消费消息

当 Kafka 请求处理程序（request handler）收到来自 Kafka 客户端的消费请求时，它会打开一个非持久性游标，从请求的偏移量开始读取 entry。Kafka 请求处理程序（request handler）将 Pulsar 消息转换回 Kafka 消息，以使现有的 Kafka 应用程序能够消费 Pulsar 客户端生产的消息。

# 组协调器（group coordinator）& 偏移量管理 

最具挑战性的部分是实现组协调器（group coordinator）和偏移量管理。Pulsar 没有集中的组协调器（group coordinator）来为每个消费者组中的消费者分配分区或管理每个消费者组的偏移量。在 Pulsar 中，分区的分配是由 broker 在每个分区的基础上进行管理的，而偏移量的管理则是由该分区的所有者 broker 通过在游标中存储确认来完成的。

要将 Pulsar 模型与 Kafka 模型进行对应匹配是很有难度的。因此，为了与 Kafka 客户端完全兼容，KoP 通过在 Pulsar 的 `public/kafka/__offsets` 系统主题中存储协调器组的变化和偏移量，来实现 Kafka 组协调器（group coordinator）。这样就搭建了连接 Pulsar 和 Kafka 的桥梁，我们可以使用现有的 Pulsar 工具和策略，管理订阅和监控 Kafka 消费者。KoP 在已经实现的组协调器（group coordinator）中添加了后台线程，定期将偏移量更新从系统主题同步到 Pulsar 游标。因此，Kafka 消费者组实际上被视为 Pulsar 订阅。所有现有的 Pulsar 工具也可用于管理 Kafka 消费者组。
