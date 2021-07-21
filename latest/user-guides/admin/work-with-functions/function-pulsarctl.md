---
title: Work with Pulsar Functions using pulsarctl CLI tool
id: function-pulsarctl
category: user-guides
---

After creating a Pulsar Function for a Pulsar cluster, you can update and remove it based on your requirements. This document describes how to update and delete Pulsar Functions using the pulsarctl CLI tool. For a full list of operations supported by the pulsarctl CLI tool, see [pulsarctl command reference](https://docs.streamnative.io/pulsarctl/v2.7.0.7/).

# Create Pulsar Functions

After creating a cluster, you can use the `pulsarctl functions create` command to deploy a Pulsar Function to the cluster.
This example shows how to create a Function for the `public` tenant and `default` namespace by specifying the JAR package.

**Input**

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

**Output**

```
Created function1 successfully
```

This table lists all fields available for creating a Pulsar function.

This table lists all fields available for creating a Pulsar function.

Field | Description | Default
---|---|---
auto-ack | Whether or not the framework acknowledges messages automatically. | true |
classname | The class name of a Pulsar Function. |  |
CPU | The CPU in cores that need to be allocated per function instance (applicable only to docker runtime).| |
custom-runtime-options | A string that encodes options to customize the runtime. | |
custom-schema-inputs | The map of input topics to Schema class names (as a JSON string). | |
custom-serde-inputs | The map of input topics to SerDe class names (as a JSON string). | |
dead-letter-topic | The topic where all messages that were not processed successfully are sent. This parameter is not supported in Python Functions. | |
disk | The disk in bytes that need to be allocated per function instance (applicable only to docker runtime). | |
fqfn | The Fully Qualified Function Name (FQFN) for the function. |  |
function-config-file | The path to a YAML config file specifying the configuration of a Pulsar Function. |  |
go | Path to the main Go executable binary for the function (if the function is written in Go). |  |
inputs | The input topic or topics of a Pulsar Function (multiple topics can be specified as a comma-separated list). | |
jar | Path to the jar file for the function (if the function is written in Java). It also supports URL-path [http/https/file (file protocol assumes that file already exists on worker host)] from which worker can download the package. |  |
log-topic | The topic to which the logs of a Pulsar Function are produced. |  |
max-message-retries | How many times should we try to process a message before giving up. |  |
name | The name of a Pulsar Function. |  |
namespace | The namespace of a Pulsar Function. |  |
output | The output topic of a Pulsar Function (If none is specified, no output is written). |  |
output-serde-classname | The SerDe class to be used for messages output by the function. |  |
parallelism | The parallelism factor of a Pulsar Function (i.e. the number of function instances to run). |  |
processing-guarantees | The processing guarantees (delivery semantics) applied to the function. Available values: [ATLEAST_ONCE, ATMOST_ONCE, EFFECTIVELY_ONCE]. | ATLEAST_ONCE
py | Path to the main Python file/Python Wheel file for the function (if the function is written in Python). |  |
ram | The ram in bytes that need to be allocated per function instance (applicable only to process/docker runtime). |  |
retain-ordering | Function consumes and processes messages in order. |  |
schema-type | The builtin schema type or custom schema class name to be used for messages output by the function. | <empty string>
sliding-interval-count | The number of messages after which the window slides. |  |
sliding-interval-duration-ms | The time duration after which the window slides. |  |
subs-name | Pulsar source subscription name if user wants a specific subscription-name for the input-topic consumer. |  |
tenant | The tenant of a Pulsar Function. |  |
timeout-ms | The message timeout in milliseconds. |  |
topics-pattern | The topic pattern to consume from a list of topics under a namespace that matches the pattern. [--input] and [--topic-pattern] are mutually exclusive. Add SerDe class name for a pattern in --custom-serde-inputs (only supported in Java Function). |  |
user-config | User-defined config key/values. |  |
window-length-count | The number of messages per window. |  |
window-length-duration-ms | The time duration of the window is milliseconds. | |

# Update Pulsar Functions

When you want to update the configuration options of a Pulsar Function, you can use the `pulsarctl functions update` command to delete it. 

* This example shows how to update the output topic of `function1` for the `default` namespace under the `public` tenant.

    **Input**

    ```
    pulsarctl functions update
    --tenant public
    --namespace default
    --name function1
    --output test-output-topic
    ```

    **Output**

    ```
    Updated function1 successfully
    ```

* This example shows how to update the log topic of `function1` for the `default` namespace under the `public` tenant.

    **Input**

    ```
    pulsarctl functions update
    --log-topic persistent://public/default/test-log-topic
    // other function parameters
    ```

    **Output**

    ```
    Updated function1 successfully
    ```

# Delete Pulsar Functions

When you want to remove a Function, you can use the `pulsarctl functions delete` command to delete it. This example shows how to delete `function1` from the `default` namespace under the `public` tenant.

**Input**

```
pulsarctl functions delete
--tenant public
--namespace default
--name function1
```

**Output**

```
Deleted successfully
```
