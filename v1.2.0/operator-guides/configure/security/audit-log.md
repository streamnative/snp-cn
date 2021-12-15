---
title: 审计日志
id: audit-log
category: operator-guides
---

审计日志捕获、保护和保留 Pulsar 集群、租户、命名空间和主题中的授权活动。一旦在一个大型团队中启动并运行了 Pulsar，就必须密切关注谁在接触数据以及他们在做什么。结构化审计日志提供了一种简单的方法来跟踪用户/应用程序的访问，可以识别潜在的异常情况和不良行为者。

结构化审计日志能够在本地或远程集群上的一组专用 Pulsar 主题中捕获审计日志，包括：

- 捕获低容量的与管理相关的活动，例如创建或删除租户、命名空间或主题（默认启用）。
- 捕获高容量的活动，如生产、消费和确认事件（需要时启用）。

可以使用 Pulsar 集成工具，如 Pulsar Functions、Pulsar SQL 和 Flink SQL，来处理和分析存储在 Pulsar 主题中的审计事件。此外，可以使用 Pulsar IO 连接器将审计事件卸载到外部数据湖或数据仓库（如 Snowflake 或 Databricks）进行分析。

# 配置审计日志 

审计记录器使用以下默认设置将审计日志写入 Pulsar 主题：

- 审计日志的主题名为：`persistent://sn/system/audit_log_all`。
- 审核日志仅捕获 `管理`类别中的事件。

为了定制 Pulsar 审计日志，你可以在 `values.yaml` 文件中的 `PULSAR_PREFIX_snAuditLogConfig` 部分进行配置。

例如，要根据权限设置将审计日志写入不同的 Pulsar 主题，可以在 `PULSAR_PREFIX_snAuditLogConfig` 部分添加以下配置。然后在授权成功的情况，日志被写入 `persistent://sn/system/audit_log_allowed`；如授权失败，则写入 `persistent://sn/system/audit_log_denied`。

```
PULSAR_PREFIX_brokerInterceptors: "audit-log"
PULSAR_PREFIX_brokerInterceptorsDirectory: "./interceptors"
PULSAR_PREFIX_snAuditLogConfig: >
      {"defaultTopics":{"allowed":"persistent://sn/system/audit_log_allowed","denied":"persistent://sn/system/audit_log_denied"}}

```

要捕获[更多审计日志事件](#事件类型)，在 `PULSAR_PREFIX_snAuditLogConfig` 部分添加 `captured` 设置。

要将不同类别的事件写入不同的主题，在 `PULSAR_PREFIX_snAuditLogConfig` 部分添加 `routes` 设置。

如下示例显示如何捕获`管理`和`生产`类别的审计日志事件。对于`生产`类别中的事件，如果授权成功，则写入 `persistent://sn/system/audit_log_produce_allowed`；如果权限失败，则写入 `persistent://sn-system/audit/audit_log_produce_denied`。

```
PULSAR_PREFIX_brokerInterceptors: "audit-log"
PULSAR_PREFIX_brokerInterceptorsDirectory: "./interceptors"
PULSAR_PREFIX_snAuditLogConfig: >
      {"captured":{"principal://User:bob":{"srn://cluster=.*/tenant=.*/namespace=.*/topic=.*": {"category":"Management|Produce","eventType":".*"}}},”routes”:{"srn://cluster=.*/tenant=.*/namespace=.*/topic=.*":{"Produce":{"allowed":"persistent://sn/system/audit_log_produce_allowed","denied":"persistent://sn/system/audit_log_produce_denied"}}},defaultTopics":{"allowed":"persistent://sn/system/audit_log_allowed","denied":"persistent://sn/system/audit_log_denied"}}
```

完成配置后，用以下命令更新集群：

```
helm upgrade  -f /path/to/your/file.yaml CLUSTER_NAME $PULSAR_CHART/
```

Pulsar broker 在设置后自动重新启动，然后你可以创建一个新的命名空间，并在为审计日志创建的主题中检查审计日志。

# 验证审计日志

审计日志主题存储了审计日志信息。为了验证审计日志的配置，请完成以下步骤：

1. 在 `public` 租户中创建一个新的命名空间。

    ```
    bin/pulsar-admin namespaces create public/audit_log
    ```

2. 检查 `persistent://sn/system/audit_log_all` 主题中的审计日志。

    ```
    bin/pulsar-admin topics peek-messages -n 1 -s audit persistent://sn/system/audit_log_all
    ```

    如果看到类似如下的审计日志，则说明审计日志配置有效。

    ```json
    {"id":"11c5296d-bf17-431a-80be-79ba66ba8a35","specVersion":"0.1","category":"Management","time":"2021-06-15T04:58:41.710Z","eventType":"CreateNamespace","resourceInfo":{"resourceType":"Namespace","cluster":"RELEASE_NAME-sn-platform","tenant":"public","namespace":"audit_log"},"authenticationInfo":{"role":"admin"},"authorizationInfo":{"granted":true,"superUserAuthorization":true},"requestInfo":{"metadata":{"clientAddress":"10.225.14.43","uri":"/admin/v2/namespaces/public/audit_log","method":"PUT"}},"responseInfo":{"responseType":"SUCCESS","responseCode":204}}
    ```

# 事件类型

每条审计日志事件包括关于事件的信息、事件时间和权限状态。支持的审计事件类型包括：


| 类型 | 事件类型                 | 描述 | 分类 | 默认值 |
|---|---|---|---|---|
| 集群 |||||
| | `ListClusters` | 列出 Pulsar 集群。 | 描述 | false|
| | `GetCluster` | 获取集群信息。                 | 描述 | false|
| | `CreateCluster` | 新建一个 Pulsar 集群。 | 管理 | true|
| | `UpdateCluster` | 更新 Pulsar 集群信息。 | 管理 | true|
| | `DeleteCluster` | 删除一个 Pulsar 集群。         | 管理 | true|
| 租户 |||||
| |`ListTenants` | 列出 Pulsar 租户。             | 描述 | false|
| | `GetTenant` | 获取租户信息。                 | 描述 | false|
| | `CreateTenant` | 新建一个 Pulsar 租户。 | 描述 | true|
| | `UpdateTenant` | 更新租户信息。 | 描述 | true|
| | `DeleteTenant` | 删除一个 Pulsar 租户。         | 描述 | true|
| 命名空间 |||||
| | `CreateNamespace` | 新建一个命名空间。             | 管理 | true|
| | `DeleteNamespace` | 删除一个命名空间。             | 管理 | true|
| | `ListNamespaces` | 列出 Pulsar 命名空间。 | 描述 | false|
| | `GetNamespace` | 获取命名空间信息。 | 描述 | false|
| 主题 |||||
| | `CreatePartitionedTopic` | 新建一个分区主题。             | 管理 | true|
| | `UpdatePartitions` | 为一个分区主题更新分区。       | 管理 | true|
| | `DeletePartitionedTopic` | 删除一个分区主题。             | 管理 | true|
| | `ListTopics` | 列出 Pulsar 主题。 | 描述 | false|
| | `ListPartitionedTopics` | 列出分区的 Pulsar 主题。       | 描述 | false|
| | `GetPartitions` | 获取一个分区主题的分区。       | 描述 | false|
| 订阅 |||||
| | `CreateSubscription` | 新建一个订阅。                 | 管理 | true|
| | `DeleteSubscription` | 删除一个订阅。                 | 管理 | true|
| | `ListSubscriptions` | 列出一个主题的订阅情况。       | 描述 | false|
| 消息 |||||
| | `NewProducer` | 新建一个附加于该主题的生产者。 | 生产 | false|
| | `CloseProducer` | 关闭一个生产者。               | 生产 | false|
| | `Produce` | 向 broker 生产消息。 | 生产 | false|
| | `NewConsumer` | 新建一个附加于该主题的消费者。 | 消费 | false|
| | `CloseConsumer` | 关闭一个消费者。               | 消费 | false|
| | `Consume` | 消费来自主题的消息。 | 消费 | false|
| | `Acknowledge` | 消费者确认消息。 | 消费 | false|