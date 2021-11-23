---
title: Function Mesh
id: functionmesh-concepts
category: concepts
---

Function Mesh 是一个 Kubernetes 的运算器 (operator)，用户在 Kubernetes 上运行 Pulsar 原生函数和 Pulsar 连接器，可充分运用 Kubernetes 的功能和资源。

Function Mesh 提供无服务器框架，用户可在框架上组织 Pulsar Function 和连接器，因此 Function Mesh 简化了创建复杂流式作业的过程。Function Mesh 是寻求云原生无服务器 (serverless) 流计算解决方案的用户的有价值的工具。

 

# 概念

通过运用 [Apache Pulsar](https://pulsar.apache.org/) 和现代流处理技术，可实现利用 Function Mesh 建立事件流应用。如下三个概念是建立应用程序的基础： _流、 _Function_，和_连接器_。

## 流

“流”是分区的、不可修改的、仅附加的时间序列，代表一系列过去发生的事实。例如，采用流事件可以模拟金融交易的序列，如先发生了“杰克借给爱丽丝一百美元”，然后发生了“爱丽丝借给鲍勃五十美元”。

流连接**Function**和**连接器**。 

流通过消息系统中的主题来展示。Function Mesh 中的流通过 Apache Pulsar 中的主题来实现。 

图 1 中给出一个**流**的例子。

![Stream](../../image/stream.png)

图 1. 流连接**功能**和**连接器** 

## Function

**Function** 是轻量级的事件处理器，它从一个或多个输入流中得到消息，将用户提供的处理逻辑应用于一个或多个消息，并将处理逻辑的结果输出到另一个流。 

Function Mesh 中的 **Functions** 是在 Pulsar Function 的基础上执行的。

图 2 中给出一个 **function** 的例子。

![Function](../../image/function.png)

图 2. 图中显示一个 function，它从一个或多个输入流中得到消息，并将结果输出成另一个流。 

## 连接器

**连接器**是一种处理器，**流**对连接器输入事件，或者连接器将事件输出成流。Function Mesh 提供两种类型的连接器：

- *Source Connector（也称为“Source”）*：从外源数据系统吸收事件，通过 Source 处理器，将事件吸收成为**流**。 
- *Sink Connector （也称为 “Sink”）*：从**流**中输出事件，将事件输出到外部数据系统。  

Function Mesh 中的**连接器**是基于 Pulsar IO 连接器实现的。访问 [StreamNative Hub](https://hub.streamnative.io/) 可查看可用的 Pulsar I/O 连接器。

## FunctionMesh

**FunctionMesh**，又称为 Mesh，是 **function** 和**连接器**的集合，而这些 **function** 和**连接器**是通过**流**来连接的，三者相互协调，共同实现强大的流处理逻辑。

**FunctionMesh** 中，所有的 **function** 和**连接器**都有共同的生命周期，即均在 **FunctionMesh** 创建时启动，销毁时终止。所有的事件处理器都是长期运行的进程，根据 Function Mesh 控制器的工作量自行调整。 

 **FunctionMesh** 可以是有向无环图（Directed Acyclic Graph，DAG），也可以是用流连接的功能和/或连接器的循环图。图 5 给出 **FunctionMesh** 的例子，这个 **FunctionMesh** 由 Debezium 源连接器、浓缩函数和 Elastic 接受端连接器组成。

![Function Mesh](../../image/function-mesh.png)

图 5. FunctionMesh 示意图：通过流连接的 function 和/或连接器组成的集合。 

# API

Function Mesh 的 API 建立在现有的 Kubernetes API 之上，因此 Function Mesh 资源与其他 Kubernetes 原生资源兼容，并且集群管理员可使用现有的 Kubernetes 工具进行管理。 

Function Mesh 包含通用语言和框架， 其中包含对 Kubernetes 友好的工具与 Function Mesh 匹配，为解决常见部署问题节约时间。 

上述基础概念以 Kubernetes 自定义资源定义（CRD）的形式交付，集群管理员可以对其进行配置，用来开发事件流应用。 

可用的 Function Mesh CRD 有：

- [**Function**](/user-guides/admin/work-with-functions/function-functionmesh.md#function-crd)：`Function` 资源对 Pulsar Function 的整个生命周期进行自动管理。
- [**Source**](/user-guides/admin/work-with-connectors/connector-functionmesh/connector-crd.md#source-crd-configurations)：`Source` 资源对 Pulsar Source 连接器的整个生命周期进行自动管理。 
- [**Sink**](/user-guides/admin/work-with-connectors/connector-functionmesh/connector-crd.md#sink-crd-configurations)：`Sink` 资源对 Pulsar Sink 连接器的整个生命周期进行自动管理。 
- **FunctionMesh**：`FunctionMesh` 资源自动管理事件应用流程序的整个生命周期。**FunctionMesh** 自动控制其他对象的创建，确保网状结构中定义的 **Function** 和**连接器**的正常运行，并且这些 **Function** 和**连接器**通过**流**互相联系。 

# Function Mesh 如何运行

设计 Function Mesh 是为了满足在 Kubernetes 上原生运行 Pulsar Function 和连接器。使用 `kubectl` 直接向 Kubernetes 集群提交 Function Mesh CRD，无需使用 pulsar-admin CLI 工具向 Pulsar 集群发送 function 请求。相应的 Mesh 控制器安装在 Kubernetes 内部，可单独启动 function 和连接器，组织调度，实现负载平衡。

Function Mesh 的工作原理如下所述，元数据和运行状态直接储存在 Kubernetes 中。

1. 创建 Pulsar Functions、source、sink，和 Function Mesh `.yaml` 文件，均用作 CRD。

2. Function 控制器从 Kubernetes 服务中接收 CRD，然后进调度单个 pod 来运行 function/连接器实例。

3. 运行中的 function/连接器实例与已配置的 Pulsar 集群和主题共同运行。 

下图给出 Function Mesh 如何运行的示例。

![Function Mesh](../../image/function-mesh-overview.png)
