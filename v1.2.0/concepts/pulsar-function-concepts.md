---
title: Pulsar Functions
id: pulsar-function-concepts
category: concepts
---

**Pulsar Functions** 是轻量级计算程序，具有如下特点： 

- 从一个或多个 Pulsar 主题中消费消息；
- 将用户提供的处理逻辑应用于每条消息；
- 将运行结果发布到另一个主题。

Pulsar Functions 是 Pulsar 消息系统的计算基础架构。Pulsar Functions 让你能够轻松创建复杂的处理逻辑，而无需部署单独的类似系统（如 [Apache Storm](http://storm.apache.org/)、 [Apache Heron](https://heron.incubator.apache.org/) 或 [Apache Flink](https://flink.apache.org/)）。

可以把 Pulsar Functions 看作是 [Lambda](https://aws.amazon.com/lambda/) 风格的函数，经过专门的设计，将 Pulsar 作为消息总线。

## 编程模型

Pulsar Functions 提供了全面的功能，其核心编程模型却很简单。Functions 从一个或多个**输入主题**接收消息，并在收到消息后，完成以下任务：

- 将处理逻辑应用于输入消息，然后将输出消息写入 Pulsar 的**输出主题**。 

- 将日志写入**日志主题**。日志主题主要用于调试和处理故障。 

![Pulsar Functions Programing Model](../../image/pulsar-functions-overview.png)

## 处理保证

Pulsar Functions 提供了三种不同的消息传递语义，可将其应用于任何 function。 

| 传递语义                         | 描述                                                         |
| -------------------------------- | ------------------------------------------------------------ |
| **至多一次（At-most-once)**      | 发送给 function 的消息最多被处理一次。因此消息可能不被处理。 |
| **至少一次（At-least-once）**    | 发送给 function 的消息被处理至少一次。因此消息可能被重复处理。 |
| **有效一次（Effectively-once）** | 发送给 function 的消息只被处理一次，并有一个与之关联的输出。 |

## 支持语言

目前可以用  Java、Python 和 Go 来编写 Pulsar Functions。详情参见 [function 实例](https://github.com/streamnative/function-mesh/tree/master/config/samples)。
