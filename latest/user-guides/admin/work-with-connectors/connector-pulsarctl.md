---
title: Work with connectors using pulsarctl CLI tool
id: connector-pulsarctl
category: user-guides
---

Messaging systems are most powerful when you can easily use them with external systems like databases and other messaging systems. **Pulsar connectors** enable you to easily create, deploy, and manage connectors that interact with external systems.

This document describes how to create, update, and delete connectors using the pulsarctl CLI tool. For a full list of operations supported by the pulsarctl CLI tool, see [here](https://docs.streamnative.io/pulsarctl/v2.7.0.7/).

> **Note**
>
> Before working with connectors, ensure that the namespaces have been granted with the `function` permission.

# Create connectors

This section describes how to create source and sink connectors.

## Create source connectors

A source connector ingests data from an external system to Pulsar topics. This example shows how to create a source connector using the pulsarctl CLI tool.

1. Connect to the target cluster using the pulsarctl CLI tool. For details, see [here](/user-guides/connect/connect-pulsar-cluster/cli-tools/connect-pulsarctl.md).

2. Create a connector by specifying the specific fields. For all supported fields, see [supported source and sink configuration options](#supported-source-and-sink-configuration-options).

    This example creates a source connector named `activemq-source`.
    
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

3. Verify that the source connector is created successfully.

    **Input**
    ```
    pulsarctl source list
    --tenant public
    --namespace default
    ```

    **Output**
    ```
    +--------------------+
    | Source Name |
    +--------------------+
    | activemq-source |
    +--------------------+
    ```

    As shown in the output, the source connector is created under the `public` tenant and the `default` namespace.

## Create sink connectors

The sink connector exports data from Pulsar topics to external systems. This example describes how to create an ActiveMQ sink connector.

1. Connect to the target cluster using the pulsarctl CLI tool. For details, see [here](/user-guides/connect/connect-pulsar-cluster/cli-tools/connect-pulsarctl.md).

2. Create a sink connector by specifying the specific fields. For all supported fields, see [supported source and sink configuration options](#supported-source-and-sink-configuration-options).

    This example creates a sink connector named `activemq-sink`.

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

3. Verify that the sink connector is created successfully.

    **Input**

    ```
    pulsarctl source list
    --tenant public
    --namespace default
    ```

    **Output**

    ```
    +--------------------+
    | Sink Name |
    +--------------------+
    | activemq-sink |
    +--------------------+
    ```

    As shown in the output, the sink connector is created under the `public` tenant and the `default` namespace.

# Update connectors

When you want to modify sink or sink configurations, or update resources for a sink or source connector, you can update connectors.

This example shows how to update the parallelism of the activemq-sink sink connector to 2.

**Input**
```
pulsarctl sinks update \
--name activemq-sink \
--parallelism 2
```

**Output**
```
Updated successfully
```

# Delete connectors

When you want to remove a connector from a tenant or namespace, you can use the pulsarctl CLI tool to delete the connector.

This example shows how to delete the `activemq-sink` sink connector.

**Input**

```
pulsarctl sinks delete \
--tenant public \
--namespace default \
--name activemq-sink
```

**Output**
```
"Deleted successfully"
```

You can use the `pulsarctl sinks get` command to verify that the sink connector is deleted successfully.

**Input**
```
pulsarctl sinks get \
--tenant public \
--namespace default \
--name activemq-sink
```

**Output**
```
HTTP 404 Not Found

Reason: Sink activemq-sink doesn't exist
```
The result shows that the sink connector does not exist.

# Supported source and sink configuration options

This section lists all supported configuration options for the source and sink connector.

## Source configuration options

This table lists all fields available for creating a source connector.

|Field|Description|
|----|---|
| `-a`, `--archive` | The path to the NAR archive for the source. <br> It also supports url-path (http/https/file [file protocol assumes that file already exists on worker host]) from which worker can download the package.
| `--classname` | The source's class name if `archive` is file-url-path (file://).
| `--cpu` | The CPU (in cores) that needs to be allocated per source instance (applicable only to Docker runtime).
| `--deserialization-classname` | The SerDe classname for the source.
| `--destination-topic-name` | The Pulsar topic to which data is sent.
| `--disk` | The disk (in bytes) that needs to be allocated per source instance (applicable only to Docker runtime).
|`--name` | The source's name.
| `--namespace` | The source's namespace.
| ` --parallelism` | The source's parallelism factor, that is, the number of source instances to run.
| `--processing-guarantees` | The processing guarantees (also named as delivery semantics) applied to the source. A source connector receives messages from the external system and writes messages to a Pulsar topic. The `--processing-guarantees` ensures the processing guarantees for writing messages to the Pulsar topic. <br>The available values are ATLEAST_ONCE, ATMOST_ONCE, EFFECTIVELY_ONCE.
| `--ram` | The RAM (in bytes) that needs to be allocated per source instance (applicable only to the process and Docker runtimes).
| `-st`, `--schema-type` | The schema type.<br> Either a builtin schema (for example, AVRO and JSON) or custom schema class name to be used to encode messages emitted from source.
| `--source-config` | Source config key/values.
| `--source-config-file` | The path to a YAML config file specifying the source's configuration.
| `-t`, `--source-type` | The source's connector provider.
| `--tenant` | The source's tenant.
|`--producer-config`| The custom producer configuration (as a JSON string).

## Sink configuration option

This table lists all fields available for creating a sink connector.

|Field|Description|
|----|---|
| `-a`, `--archive` | The path to the archive file for the sink. <br> It also supports url-path (http/https/file [file protocol assumes that file already exists on worker host]) from which worker can download the package.
| `--auto-ack` |  Whether or not the framework will automatically acknowledge messages.
| `--classname` | The sink's class name if `archive` is file-url-path (file://).
| `--cpu` | The CPU (in cores) that needs to be allocated per sink instance (applicable only to Docker runtime).
| `--custom-schema-inputs` | The map of input topics to schema types or class names (as a JSON string).
| `--custom-serde-inputs` | The map of input topics to SerDe class names (as a JSON string).
| `--disk` | The disk (in bytes) that needs to be allocated per sink instance (applicable only to Docker runtime).
|`-i, --inputs` | The sink's input topic or topics (multiple topics can be specified as a comma-separated list).
|`--name` | The sink's name.
| `--namespace` | The sink's namespace.
| ` --parallelism` | The sink's parallelism factor, that is, the number of sink instances to run.
| `--processing-guarantees` | The processing guarantees (also known as delivery semantics) applied to the sink. The `--processing-guarantees` implementation in Pulsar also relies on sink implementation. <br>The available values are ATLEAST_ONCE, ATMOST_ONCE, EFFECTIVELY_ONCE.
| `--ram` | The RAM (in bytes) that needs to be allocated per sink instance (applicable only to the process and Docker runtimes).
| `--retain-ordering` | Sink consumes and sinks messages in order.
| `--sink-config` | Sink config key/values.
| `--sink-config-file` | The path to a YAML config file specifying the sink's configuration.
| `-t`, `--sink-type` | The sink's connector provider. The `sink-type` parameter of the currently built-in connectors is determined by the setting of the `name` parameter specified in the pulsar-io.yaml file.
| `--subs-name` | Pulsar source subscription name if user wants a specific subscription-name for input-topic consumer.
| `--tenant` | The sink's tenant.
| `--timeout-ms` | The message timeout in milliseconds.
| `--topics-pattern` | The topic pattern to consume from a list of topics under a namespace that matches the pattern. <br>`--input` and `--topics-Pattern` are mutually exclusive. <br>Add SerDe class name for a pattern in `--customSerdeInputs` (supported for Java function only).