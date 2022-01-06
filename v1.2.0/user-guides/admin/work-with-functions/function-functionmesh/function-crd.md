---
title: Pulsar Functions CRD 配置
id: function-crd
category: user-guides
---

打包 Pulsar Functions 后，可以将 Pulsar Functions 提交到 Pulsar 集群。本文介绍了如何使用 Function CRD 提交 Pulsar Functions。可以使用 `image` 字段来指定用于创建 Pulsar Functions 的运行器镜像。你也可以指定软件包或 Docker 镜像的存储位置。


Pulsar Functions 是一种简洁的计算抽象，Apache Pulsar 通过它可以让用户能够处理简单的 ETL 和流任务。目前 Function Mesh 支持使用 Java、Python 或 Go 编程语言来定义 Function 的 YAML 文件。

本节列出了可用于 Pulsar Functions 的 CRD 配置。Pulsar Functions 的 CRD 配置包括 Function 配置和通用 CRD 配置。

# Function 配置

下表列出了一条 Pulsar Function 的相关配置。

| 字段 | 描述 |
| ---|---|
| `name` | Pulsar Function 的名称。 |
| `classname` | Pulsar Function 的类名。 |
| `tenant` | Pulsar Function 的租户。 |
| `namespace` | Pulsar Function 的命名空间。 |
| `Replicas`| 运行此 Pulsar Function 的 Pulsar 实例数。                   |
| `MaxReplicas`| 运行此 Pulsar Function 的最大 Pulsar 实例数。当 `maxReplicas` 参数的值大于 `replicas` 的值时，表示 Function 控制器根据 CPU 的使用情况对 Pulsar Functions 进行自动扩展。默认情况下，`maxReplicas` 设置为 0，表示禁用自动调节。 |
| `Timeout` | 消息超时时间（以毫秒为单位）。                        |
| `DeadLetterTopic` | 所有未处理成功的消息被发送到的主题。Python 函数不支持这一参数。 |
| `FuncConfig` | 到 ConfigMap 的映射，指定 Pulsar 函数的配置。            |
| `LogTopic` | 生产 Pulsar Function 日志的主题。                         |
| `AutoAck` | 框架是否自动确认消息。                                       |
| `MaxMessageRetry` | 放弃前尝试处理消息的次数。                       |
| `ProcessingGuarantee` | 用于 function 的处理保证（传递语义）。可用值：`ATLEAST_ONCE`、`ATOST_ONCE`、`EFFECTIVELY_ONCE`。 |
| `RetainOrdering` | Function 按顺序消费和处理消息。 |
| `RetainKeyOrdering`| 配置是否保留消息的 key 顺序。                             |
| `SubscriptionName` | 如果要给输入主题消费者设定特定的订阅名称，可使用此参数设定 Pulsar Functions 的订阅名称。 |
| `CleanupSubscription` | 配置是否清除订阅。 |
| `SubscriptionPosition` | 订阅的位置。 |

# 镜像

本节介绍了可用于 Pulsar Functions、source、sink 和 Function Mesh CRD 的镜像选项。

## 基础运行器

基础运行器是其他运行器的镜像基础。基础运行器位于 `./pulsar-functions-base-runner`。基础运行器镜像包含基本的工具链，如 `/pulsar/bin`、`/pulsar/conf` 和 `/pulsar/lib`，用于确保 pulsar-admin CLI（命令行工具）能正常工作，以支持 [Apache Pulsar Packages](http://pulsar.apache.org/docs/en/next/admin-api-packages/)。

## 运行器镜像

Function Mesh 使用运行器镜像作为 Pulsar Functions 和连接器的镜像。每个运行器镜像仅包含指定运行时所需的工具链和库。

下表列出了 Function 运行时可用的运行器镜像。

| 类别 | 描述 |
| --- | --- |
| Java 运行器 | Java 运行器基于基础运行器，包含运行 Java 函数或连接器的 Java 函数实例。Java 运行器（`streamnative/pulsar-functions-java-runner` ）存储在 [Docker Hub](https://hub.docker.com/r/streamnative/pulsar-functions-java-runner) ，可自动更新以与 Apache Pulsar 版本保持一致。 |
| Python 运行器 | Python 运行器基于基础运行器，包含用于运行 Python 函数的 Python 函数实例。你可以创建自己的 Python 运行器来来自定义 Python 依赖项。Python 运行器（`streamnative/pulsar-functions-python-runner` ）储存在 [Docker Hub](https://hub.docker.com/r/streamnative/pulsar-functions-java-runner) ，可自动更新以与 Apache Pulsar 版本保持一致。 |
| Golang 运行器 | Golang 运行器提供了运行 Golang 函数所需的所有工具链和依赖项。Golang 运行器（`streamnative/pulsar-functions-go-runner`）储存在 [Docker Hub](https://hub.docker.com/r/streamnative/pulsar-functions-java-runner)，可自动更新以与 Apache Pulsar 版本保持一致。 |

# 输入

Pulsar Functions 的输入主题。下表列出了 `Input` 的可用选项。

| 字段 | 描述 |
| --- | --- |
| `Topics` | 消息来源主题的配置。 |
| `CustomSerdeSources` | 输入主题到 SerDe 类名称的映射（作为 JSON 字符串）。 |
| `CustomSchemaSources` | 输入主题到 Schema 类名称的映射（作为 JSON 字符串）。 |
| `SourceSpecs` | Source 具体配置到消费者具体配置的映射。消费者具体配置包括以下选项：<br />- `SchemaType`：function 获取的消息使用的内置 schema 类型或自定义 schema 类的名称。<br />- `SerdeClassName`：function 获取的消息的 SerDe 类。<br />- `IsRegexPattern`：配置输入主题是否采用正则表达式模式。<br />- `SchemaProperties`：function 获取的消息的 schema 属性。<br />- `ConsumerProperties`：function 获取的消息的消费者属性。<br />- `ReceiverQueueSize`：消费者接收队列的大小。<br /> - `CryptoConfig`：消费者的加密配置。 |

# 输出

Pulsar Functions 的输出主题。下表列出 `Output` 的可用选项。

|名称 | 描述 |
| --- | --- |
| `Topics` | Pulsar Functions 的输出主题（如果未指定，则不写入输出）。 |
| `SinkSerdeClassName` | 输出主题到 SerDe 类名称的映射（作为 JSON 字符串）。 |
| `SinkSchemaType` | 内置 schema 类型或自定义 schema 类名称，用于函数发送的消息。 |
| `ProducerConf` | 生产者的具体配置。可用的选项：<br />- `MaxPendingMessages`：待处理消息的最大数量。<br />- `MaxPendingMessagesAcrossPartitions`：跨分区待处理消息的最大数量。<br />- `UseThreadLocalProducers`：配置生产者是否使用一个线程。<br />- `CryptoConfig`：生产者的加密配置。<br />- `BatchBuilder`：支持基于 key 的批处理器。 |
| `CustomSchemaSinks` | 输出主题到 Schema 类名称的映射（作为 JSON 字符串）。 |

# 资源

在指定 Pulsar Function 或连接器时，可以选择指定它们需要多少资源。可指定的资源包括 CPU 和内存（RAM）。

如果运行 Pod 的节点有足够的可用资源，则 Pod 可能、也被允许使用高于 `request` 的更多资源。但 Pod 使用的资源不得超过该资源的 `limit`。

# Secret

在 Function Mesh 中，secret 是通过 secretsMap 来定义的。要使用 secret，Pod 需要引用 secret。Pod 可以使用 secretsMap 作为卷（volume）的环境变量。可以在为 secret 创建配置文件时指定 `data` 字段。

要在 Pod 的环境变量中使用 secret，需按照以下步骤操作：

1. 新建一个 secret 或使用一个现有的 secret。多个 Pod 可以引用相同的 secret。
2. 对于需要使用密钥的值的容器，需要为其修改的 Pod 定义，为每个要使用的密钥添加环境变量。
3. 修改镜像和/或命令行，以便程序查找指定的环境变量的值。

Pulsar 集群支持使用 TLS 或其他身份验证插件进行身份验证。

- TLS Secret

    | 字段 | 描述 |
    | --- | --- |
    | tlsAllowInsecureConnection | 允许不安全的 TLS 连接。  |
    | tlsHostnameVerificationEnable | 启用 hostname 验证。    |
    | tlsTrustCertsFilePath | TLS 信任证书文件的路径。 |

- Auth Secret

    | 字段 | 描述 |
    | --- | --- |
    | clientAuthenticationPlugin | 客户端身份验证插件。 |
    | clientAuthenticationParameters | 客户端身份验证参数。 |

# 包

Function Mesh 支持在 Java、Python 和 Go 中运行 Pulsar Functions。下表列出了在不同语言运行环境中可用的 Pulsar Functions 字段。

| 字段 | 描述 |
| --- | --- |
| `jarLocation` | Function 的 JAR 文件的路径。仅适用于用 Java 编写的 Pulsar Functions。 |
| `goLocation` | Function 的 JAR 文件的路径。仅适用于用 Go 编写的 Pulsar Functions。 |
| `pyLocation` | Function 的 JAR 文件的路径。仅适用于用 Python 编写的 Pulsar Functions。 |
| `extraDependenciesDir` | 指定 JAR 包的依赖目录。 |

# 集群位置

Function Mesh 中，Pulsar 集群是通过 ConfigMap 定义的。Pod 可以使用 ConfigMap 作为卷（volume）中的环境变量。Pulsar 集群 ConfigMap 定义了 Pulsar 集群 URL。

| 字段 | 描述 |
| --- | --- |
| `webServiceURL` | Pulsar 集群的 Web 服务 URL。    |
| `brokerServiceURL` | Pulsar 集群的 broker 服务 URL。 |

# Pod 具体配置

Function Mesh 支持自定义 Pod 运行 function 实例。下表列出了可用于 `pod` 字段的子字段。

| 字段 | 描述 |
| --- | --- |
| `Labels` | 指定附加到 Pod 的标签。                                      |
| `NodeSelector` | 指定键值对的映射。对于在节点上运行的 Pod，节点必须将每个指定的键值对作为标签。 |
| `Affinity` | 指定 Pod 的调度约束。                                        |
| `Tolerations` | 指定 Pod 的容忍度。                                          |
| `Annotations`| 指定附加到 Pod 的注解。                                      |
| `SecurityContext` | 指定 Pod 的安全上下文。                                      |
| `TerminationGracePeriodSeconds` | Kubernetes 终止 Pod 前的时长。                        |
| `Volumes` | 可以由属于 Pod 的容器所挂载的卷（volume）列表。               |
| `ImagePullSecrets` | 可选列表，用于引用同一命名空间中的 secret，以拉取 Pod 使用的任何镜像。 |
| `InitContainers` | 属于 Pod 的初始化容器。典型的用例为使用初始化容器来下载远程 JAR 到本地路径。 |
| `Sidecars` | Sidecar 容器与 Pod 中的主要 function 容器一起运行。 |
