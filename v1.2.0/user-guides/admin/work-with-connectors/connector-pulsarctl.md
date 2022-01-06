---
title: 通过 pulsarctl 使用连接器
id: connector-pulsarctl
category: user-guides
---

当消息系统可以方便地和外部系统（如数据库和其他消息系统）一起使用时，就能最大程度地发挥消息系统的作用。使用 **Pulsar 连接器**，可以轻松创建、部署和管理与外部系统交互的连接器。

本文介绍了如何使用 pulsarctl CLI（命令行工具）来创建、更新和删除连接器。有关 pulsarctl CLI（命令行工具）支持操作的完整列表，参见[此处](https://docs.streamnative.io/pulsarctl/v2.7.0.7/)。

> **注意**
>
> 在使用连接器之前，需确保命名空间已被授予 `function` 权限。

# 创建连接器

本节描述了如何创建 source 和 sink 连接器。

## 创建 source 连接器

Source 连接器将数据从外部系统传入 Pulsar 主题。如下示例显示了如何使用 pulsarctl CLI（命令行工具）创建 source 连接器。

1. 使用 pulsarctl CLI（命令行工具），连接到目标集群。详细信息，参见[此处](/user-guides/connect/connect-pulsar-cluster/cli-tools/connect-pulsarctl.md)。

2. 通过指定特定字段，创建连接器。如需了解所有支持的字段，参见 [source 和 sink 配置支持选项](#source-和-sink-配置支持选项)。

    本示例介绍了如何创建一个名为 `activemq-source` 的 source 连接器。

    ```
    pulsarctl sources create
    --tenant public
    --namespace default
    --name activemq-source
    --destination-topic-name activemq-source-topic
    --classname org.apache.pulsar.io.activemq.ActiveMQSource
    --source-config-file activemq-source.yaml
    --parallelism 1
    # other source configurations
    ```

3. 验证该 source 连接器是否被成功创建。

    **输入**
    ```
    pulsarctl source list
    --tenant public
    --namespace default
    ```
    
    **输出**
    ```
    +--------------------+
    | Source Name |
    +--------------------+
    | activemq-source |
    +--------------------+
    ```

    如输出所示，source 连接器在 `public` 租户和 `default` 命名空间下成功创建。

## 创建 sink 连接器

Sink 连接器将 Pulsar 主题中的数据传出到外部系统。如下示例描述了如何创建 ActiveMQ sink 连接器。

1. 使用 pulsarctl CLI（命令行工具）连接到目标集群。详细信息，参见[此处](/user-guides/connect/connect-pulsar-cluster/cli-tools/connect-pulsarctl.md)。

2. 通过指定特定字段，创建 sink 连接器。如需查看所有支持的字段，参见 [Source 和 sink 配置支持选项](#source-和-sink-配置支持选项)。

    如下示例介绍了如何创建一个名为 `activemq-sink` 的 sink 连接器。

    ```
    pulsarctl sink create
    --tenant public
    --namespace default
    --name (name of the sink connector)
    --inputs test-activemq
    --sink-config-file activemq-sink.yaml
    --parallelism 1
    # other sink configurations
    ```

3. 验证该 sink 连接器是否被成功创建。

    **输入**

    ```
    pulsarctl source list
    --tenant public
    --namespace default
    ```

    **输出**

    ```
    +--------------------+
    | Sink Name |
    +--------------------+
    | activemq-sink |
    +--------------------+
    ```

    如输出所示，sink 连接器在 `public` 租户和 `default` 命名空间下成功创建。

# 更新连接器

如果想要修改 sink 或 source 连接器的配置，或者更新 sink 或 source 连接器的资源，可以对连接器进行更新。

如下示例显示了如何将 activemq-sink 连接器的并行度更新为 2。

**输入**
```
pulsarctl sinks update \
--name activemq-sink \
--parallelism 2
```

**输出**
```
Updated successfully
```

# 删除连接器

如果想要从租户或者命名空间中移除连接器，可以使用 pulsarctl CLI（命令行工具）来删除连接器。

如下示例显示了如何删除 `activemq-sink` sink 连接器。

**输入**

```
pulsarctl sinks delete \
--tenant public \
--namespace default \
--name activemq-sink
```

**输出**
```
"Deleted successfully"
```

可以使用 `pulsarctl sinks get` 命令来验证 sink 连接器是否已被成功删除。

**输入**
```
pulsarctl sinks get \
--tenant public \
--namespace default \
--name activemq-sink
```

**输出**
```
HTTP 404 Not Found

Reason: Sink activemq-sink doesn't exist
```
输出结果表示 sink 连接器已不存在。

# Source 和 sink 配置支持选项

本节介绍了 source 和 sink 连接器支持的所有配置选项。

## Source 配置选项

下表列出了创建 source 连接器的所有可用字段。

|字段|描述|
|----|---|
| `-a`, `--archive` | Source 的 NAR 存档的路径。<br/>同样支持 url-path（http/https/file [文件协议，假定文件已经存在于 worker 主机上]），worker 可以从该路径下载包。 |
| `--classname` | 在 `archive` 为 file-url-path（file://）情况下，source 的类名。 |
| `--cpu` | 需要为每个 source 实例分配的 CPU（核数）（仅适用于 Docker 运行时）。 |
| `--deserialization-classname` | Source 的 SerDe 类名。 |
| `--destination-topic-name` | 作为数据发送对象的 Pulsar 主题。 |
| `--disk` | 需要为每个 source 实例分配的磁盘数（以字节为单位）（仅适用于 Docker 运行时）。 |
|`--name` | Source 的名称。 |
| `--namespace` | Source 的命名空间。 |
| ` --parallelism` | Source 的并行度因子，即运行 source 的实例数量。 |
| `--processing-guarantees` | 应用于 source 的处理保证（也称为传递语义）。Source 连接器从外部系统接收消息并将消息写入 Pulsar 主题。`--processing-guarantees` 规定了将消息写入 Pulsar 主题的处理保证。<br/>可用的值有 ATLEAST_ONCE、ATMOST_ONCE、EFFECTIVELY_ONCE。 |
| `--ram` | 需要为每个 source 实例分配的 RAM（以字节为单位）（仅适用于进程和 Docker 运行时）。 |
| `-st`, `--schema-type` | Schema 的类型。<br>可以是内置 schema（例如，AVRO 和 JSON）或是自定义 schema 类的名称，用于对从 source 发出的消息进行编码。 |
| `--source-config` | Source 配置的键/值。 |
| `--source-config-file` | 用于定义 source 配置的 YAML 配置文件的路径。 |
| `-t`, `--source-type` | Source 连接器的 provider。 |
| `--tenant` | Source 的租户。 |
|`--producer-config`| 自定义生产者配置项（为 JSON 字符串）。 |

## Sink 配置选项

下表列出了创建 sink 连接器的所有可用字段。

|字段|描述|
|----|---|
| `-a`, `--archive` | Sink 的存档文件的路径。<br/>同样支持 url-path（http/https/file [文件协议，假定文件已经存在于 worker 主机上]），worker 可以从该路径下载包。 |
| `--auto-ack` | 框架是否会自动确认消息。 |
| `--classname` | 在 `archive` 为 file-url-path (file://) 的情况下，sink 的类名。 |
| `--cpu` | 需要为每个 sink 实例分配的 CPU（核数）（仅适用于 Docker 运行时）。 |
| `--custom-schema-inputs` | 输入主题到 schema 类别或类名称的映射（为 JSON 字符串）。 |
| `--custom-serde-inputs` | 输入主题到 SerDe 类名称的映射（为 JSON 字符串）。 |
| `--disk` | 需要为每个 sink 实例分配的磁盘（以字节为单位）（仅适用于 Docker 运行时）。 |
|`-i, --inputs` | Sink 的输入主题或主题（多个主题可以表示为以逗号分隔的列表）。 |
|`--name` | Sink 的名称。 |
| `--namespace` | Sink 的命名空间。 |
| ` --parallelism` | Sink 的并行度因子，即运行 sink 的实例的数量。                |
| `--processing-guarantees` | 应用于 sink 的处理保证（也称为传递语义）。`--processing-guarantees` 在 Pulsar 上的实现也依赖于 sink 的实现。<br/>可用的值有 ATLEAST_ONCE、ATMOST_ONCE、EFFECTIVELY_ONCE。 |
| `--ram` | 需要为每个 sink 实例分配的 RAM（以字节为单位）（仅适用于进程和 Docker 运行时）。 |
| `--retain-ordering` | Sink 按顺序消费和传出消息。 |
| `--sink-config` | Sink 配置的键/值。 |
| `--sink-config-file` | 用于定义 sink 配置的 YAML 配置文件的路径。 |
| `-t`, `--sink-type` | Sink 连接器的 provider。当前内置连接器的 `sink-type` 参数由 pulsar-io.yaml 文件中指定的 `name` 参数决定。 |
| `--subs-name` | 当用户想要为输入主题的消费者指定特定的订阅名称，表示 Pulsar source 订阅名称。 |
| `--tenant` | Sink 的租户。 |
| `--timeout-ms` | 消息超时时间（以毫秒为单位）。 |
| `--topics-pattern` | 定义主题列表的主题模式。命名空间下与主题模式匹配的主题将被消费。<br/>`--input` 和 `--topics-Pattern` 是互斥的。<br/>可在 `--customSerdeInputs` 中为模式添加 SerDe 类名（仅支持 Java 函数）。 |