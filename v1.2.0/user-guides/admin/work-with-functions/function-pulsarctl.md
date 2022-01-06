---
title: 通过 pulsarctl 使用 Pulsar Functions
id: function-pulsarctl
category: user-guides
---

为 Pulsar 集群创建一条 Pulsar Function 后，可以根据需要更新或删除这条 function。本文介绍了如何使用 pulsarctl CLI （命令行工具）来更新和删除一条 Pulsar Function。有关 pulsarctl CLI（命令行工具）支持操作的完整列表，参见 [pulsarctl 命令参考](https://docs.streamnative.io/pulsarctl/v2.7.0.7/)。

# 创建 Pulsar Functions

创建集群后，可以使用 `pulsarctl functions create` 命令将 Pulsar Functions 部署到集群中。
如下示例显示了如何通过指定 JAR 包来为 `public` 租户和 `default` 命名空间来创建一条 function。

**输入**

```
pulsarctl functions create
--tenant public
--namespace default
--name function1
--inputs test-input-topic
--output persistent://public/default/test-output-topic
--classname org.apache.pulsar.functions.api.examples.ExclamationFunction
--jar /examples/api-examples.jar

```

**输出**

```
Created function1 successfully
```

下表列出了用于创建一条 Pulsar Function 的所有可用字段。



字段 | 描述 | 默认值 
---|---|---
auto-ack | 框架是否自动确认消息。                                       | true 
classname | Pulsar Function 的类名。 |
CPU | 每个 function 实例需要分配的 CPU 核数（仅适用于 docker 运行时）。 |
custom-runtime-options | 对选项进行编码以自定义运行时的字符串。 |
custom-schema-inputs | 输入主题到 Schema 类名的映射（为 JSON 字符串）。 |
custom-serde-inputs | 输入主题到 SerDe 类名称的映射（为 JSON 字符串）。 |
dead-letter-topic | 所有未成功处理的消息被发送到的主题。Python 函数不支持这一参数。 |
disk | 每个 function 实例需要分配的磁盘（以字节为单位）（仅适用于 docker 运行时）。 |
fqfn | Function 的全限定函数名（FQFN）。 |
function-config-file | 配置 Pulsar Function 的 YAML 配置文件的路径。 |
go | Function 的主要 Go 可执行二进制文件的路径（如果 function 是用 Go 编写的）。 |
inputs | Pulsar Function 的输入主题（多个主题可以用逗号分隔的列表来表示）。 |
jar | Function 的 jar 文件的路径（如果 function 是用 Java 编写的）。同时还支持 URL 路径 [http/https/file（文件协议，假定文件已经存在于 worker 主机上）]，worker 可以从中下载包。 |
log-topic | 生产 Pulsar Function 日志的主题。                        |
max-message-retries | 放弃前尝试处理消息的次数。                                   |
name | Pulsar Function 的名称。 |
namespace | Pulsar Function 的命名空间。 |
output | Pulsar Function 的输出主题（如未指定，则不写入输出）。      |
output-serde-classname | 用于 function 输出消息的 SerDe 类。                          |
parallelism | Pulsar Function 的并行度（即运行 function 的实例数）。 |
processing-guarantees | 应用于 function 的处理保证（传递语义）。可用值：[ATLEAST_ONCE、ATMOST_ONCE、EFFECTIVELY_ONCE]。 | ATLEAST_ONCE
py | Function 的主 Python 文件/Python Wheel 文件的路径（如果 function 是使用 Python 编写的）。 |
ram | 每个 function 实例需要分配的 ram 字节数（仅适用于进程/docker 运行时）。 |
retain-ordering | Function 按顺序消费和处理消息。                              |
schema-type | 内置 schema 类型或自定义 schema 类名称，用于函数输出消息。 | <empty string>
sliding-interval-count | 窗口滑动后的消息数。                                         |
sliding-interval-duration-ms | 窗口滑动的持续时间。                                         |
subs-name | 用于用户想要为输入主题消费者指定特定的订阅名称时，表示 Pulsar source 订阅名称。 |
tenant | Pulsar Function 的租户。 |
timeout-ms | 消息超时时间（以毫秒为单位）。 |
topics-pattern | 主题模式，命名空间主题列表中与该主题模式匹配的主题将被消费。`--input` 和 `--topics-Pattern` 是互斥的。<br/>可在 `--custom-serde-inputs` 中为模式添加 SerDe 类名（仅支持 Java 函数）。 |
user-config | 用户定义的 config 键/值。 |
window-length-count | 每个窗口的消息数。 |
window-length-duration-ms | 窗口的持续时间（以毫秒为单位）。                             |

# 更新 Pulsar Functions

要更新 Pulsar Functions 的配置项，可以使用 `pulsarctl functions update` 命令来进行更新。

* 如下示例显示了如何为 `public` 租户下的 `default` 命名空间更新 `function1` 的输出主题。

    **输入**

    ```
    pulsarctl functions update
    --tenant public
    --namespace default
    --name function1
    --output test-output-topic
    ```

    **输出**

    ```
    Updated function1 successfully
    ```

* 如下示例显示了如何为 `public` 租户下的 `default` 命名空间更新 `function1` 的日志主题。

    **输入**

    ```
    pulsarctl functions update
    --log-topic persistent://public/default/test-log-topic
    // other function parameters
    ```

    **输出**

    ```
    Updated function1 successfully
    ```

# 删除 Pulsar Functions

要删除 function，可以使用 `pulsarctl functions delete` 命令来进行删除。如下示例显示了如何从 `public` 租户下 `default` 命名空间删除 `function1`。

**输入**

```
pulsarctl functions delete
--tenant public
--namespace default
--name function1
```

**输出**

```
Deleted successfully
```
