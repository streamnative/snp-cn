---
title: Function Mesh
id: functionmesh-concepts
category: concepts
---

Function Mesh 是一个 Kubernetes 的 operator，用于帮助用户在 Kubernetes 上原生运行 Pulsar Functions 和 Pulsar 连接器，以充分利用 Kubernetes 的强大功能和丰富资源。

通过 Function Mesh 提供的无服务器（Serverless）框架，用户可以管理一组 Pulsar Functions 和连接器。Function Mesh 简化了创建复杂流式作业的过程。对于寻求云原生无服务器（Serverless）流技术解决方案的用户来说，Function Mesh 提供了巨大的价值。

# 概念

通过运用 [Apache Pulsar](https://pulsar.apache.org/) 和现代流处理技术，Function Mesh 可帮助你创建事件流应用程序。如下三个概念是创建应用程序的基础： _流_、_Function_、和_连接器_。

## 流

“流”是分区的、不可修改的、只追加（Append Only）的事件序列，代表一系列过去的事实。例如，流事件可以模拟金融交易的序列，如先发生了“Jack 借给 Alice 一百美元”，然后发生了“Alice 借给 Bob 五十美元”。

流连接 **Function** 和**连接器**。 

消息系统中的主题通常用来表示流。Function Mesh 中的流通过 Apache Pulsar 中的主题来实现。 

图 1 中给出一个**流**的例子。

![Stream](../../image/stream.png)

图 1. 流连接 **Function** 和**连接器** 

## Function

**Function** 是从单个或多个输入流中消费消息的轻量级的事件处理器，将用户提供的处理逻辑应用于一条或多条消息，并将处理逻辑的结果输出到另一个流。 

Function Mesh 中的 **functions** 是在 Pulsar Functions 的基础上实现的。

图 2 中给出一个 **function** 的例子。

![Function](../../image/function.png)

图 2. 图中显示的是一个 function，这个 function 从一个或多个输入流消费消息，并将结果输出到另一个流。 

## 连接器

**连接器**是一种处理器，将事件输入**流**或从**流**中输出事件。Function Mesh 提供两种类型的连接器：

- *Source 连接器（也称为“Source”）*：从外部数据系统将事件输入到**流**。 
- *Sink 连接器（也称为“Sink”）*：从**流**中输出事件到外部数据系统。  

Function Mesh 的**连接器**是基于 Pulsar IO 连接器实现的。访问 [StreamNative Hub](https://hub.streamnative.io/) 可查看可用的 Pulsar I/O 连接器。

## Function Mesh

**Function Mesh**，又称为 Mesh，是 **function** 和**连接器**的集合，而这些 **function** 和**连接器**是通过**流**来连接的，它们相互协调，共同实现强大的流处理逻辑。

**Function Mesh** 中的所有 **function** 和**连接器**都有共同的生命周期，即均在 **Function Mesh** 创建时启动，销毁时终止。所有的事件处理器都是长期运行的进程，Function Mesh 控制器根据它们的负载进行自动扩缩调整。 

**Function Mesh** 可以是有向无环图（Directed Acyclic Graph，DAG），也可以是流连接的 function 和/或连接器的循环图。图 5 是一个 **Function Mesh** 的例子，这个 **Function Mesh** 由 Debezium source 连接器、enrichement function 和 Elastic sink 连接器组成。

![Function Mesh](../../image/function-mesh.png)

图 5. Function Mesh 是由流连接的 function 和/或连接器的集合

# API

Function Mesh 的 API 建立在现有的 Kubernetes API 之上，因此 Function Mesh 资源与其他 Kubernetes 原生资源兼容，并且集群管理员可使用现有的 Kubernetes 工具对其进行管理。 

包含 Kubernetes 工具的通用语言和框架能够方便地与 Function Mesh 配套使用，为解决常见的部署问题节约了时间。  

上述基础概念以 Kubernetes 自定义资源定义（CRD）的形式交付，集群管理员可以对其进行配置，用来开发事件流应用程序。 

可用的 Function Mesh CRD 有：

- [**Function**](/user-guides/admin/work-with-functions/function-functionmesh.md#function-crd)：`Function`  资源对 Pulsar Functions 的整个生命周期进行自动管理。
- [**Source**](/user-guides/admin/work-with-connectors/connector-functionmesh/connector-crd.md#source-crd-configurations)：`Source`  资源对 Pulsar Source 连接器的整个生命周期进行自动管理。 
- [**Sink**](/user-guides/admin/work-with-connectors/connector-functionmesh/connector-crd.md#sink-crd-configurations)：`Sink` 资源对 Pulsar Sink 连接器的整个生命周期进行自动管理。 
- **FunctionMesh**：`FunctionMesh` 资源自动管理事件流应用程序的整个生命周期。**Function Mesh** 自动控制其他对象的创建，确保 Mesh 中定义的 **Function** 和**连接器**正常运行，并且由定义的**流**进行连接。 

# Function Mesh 如何运行

设计 Function Mesh 是为了满足在 Kubernetes 上原生运行 Pulsar Functions 和连接器。可以使用 `kubectl` 直接向 Kubernetes 集群提交 Function Mesh CRD，而无需使用 pulsar-admin CLI 工具向 Pulsar 集群发送 function 请求。安装在 Kubernetes 内部相应的 Mesh operator 可单独启动 function 和连接器，组织调度，实现负载平衡。

Function Mesh 的工作原理如下所述，元数据和运行状态直接储存在 Kubernetes 中。

1. 创建 Pulsar Functions、source、sink，和 Function Mesh `.yaml` 文件，均用作 CRD。

2. Function 控制器从 Kubernetes 服务中接收 CRD，然后调度单个 pod 来运行 function/连接器实例。

3. 运行中的 function/连接器实例与配置好的 Pulsar 集群和主题共同运行。 

下图给出了 Function Mesh 如何运行的示例。

![Function Mesh](../../image/function-mesh-overview.png)
