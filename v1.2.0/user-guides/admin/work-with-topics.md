---
title: Work with topics
id: work-with-topics
category: user-guides
---

In Pulsar, topics are named channels for transmitting messages from producers to consumers. Pulsar supports persistent and non-persistent topics. By default, a persistent topic is created if you do not specify the topic type.

This document describes how to create and manage topics for a cluster using the pulsarctl CLI tool or the StreamNative Console.

# Work with topics using the pulsarctl CLI tool

This section describes how to create and manage topics using the pulsarctl CLI tool. For a full list of supported operations on topics, see [pulsarctl command reference](https://docs.streamnative.io/pulsarctl/v2.7.0.7/#-em-update-em--32).

Before using the pulsarctl CLI tool to create and manage topics, you need to create a [tenant](/user-guides/admin/work-with-tenants.md#create-tenants) and a [namespace](/user-guides/admin/work-with-namespaces.md#create-namespaces).

## Create topics

You can use the `pulsarctl topics create TOPIC_NAME` command to create a topic. 

* If you want to create a non-partitioned topic, you need to set the number of partitions to `0`.

    This example shows how to create a non-partitioned topic in the `example-tenant/example-ns` namespace.

    **Input**
    ```
    pulsarctl topics create example-tenant/example-ns/topic-test 0
    ```

    **Output**
    ```
    Create topic persistent://example-tenant/example-ns/topic-test with 0 partitions successfully
    ```

* If you want to create a partitioned topic, you need to set the number of partitions to a specific number.

    This example shows how to create a topic with 5 partitions in the `example-tenant/example-ns` namespace.

    **Input**
    ```
    pulsarctl topics create example-tenant/example-ns/test-topic 5
    ```

    **Output**
    ```
    Create topic persistent://example-tenant/example-ns/test-topic with 5 partitions successfully
    ```

## Get topic status

You can use the `pulsarctl topics get TOPIC_NAME` command to get information about a specific topic.

* This example shows how to list all topics available for the `example-tenant/example-ns` namespace. 

    **Input**
    ```
    pulsarctl topics list example-tenant/example-ns/
    ```

    **Output**
    ```
    +--------------------------------------------------------------------------------+-----------------------+
    |                 TOPIC NAME                                                          | PARTITIONED ? |
    +--------------------------------------------------------------------------------+-----------------------+
    | persistent://example-tenant/example-ns/topic-test                  | N                         |
    | persistent://example-tenant/example-ns/test-topic                  | Y                         |
    | persistent://example-tenant/example-ns/test-topic-partition-0 | N                         |
    | persistent://example-tenant/example-ns/test-topic-partition-1 | N                         |
    | persistent://example-tenant/example-ns/test-topic-partition-2 | N                         |
    | persistent://example-tenant/example-ns/test-topic-partition-3 | N                         |
    | persistent://example-tenant/example-ns/test-topic-partition-4 | N                         |
    +--------------------------------------------------------------------------------+-----------------------+
    ```

* This example shows how to get detailed information about the `topic-test` topic.

    **Input** 
    ```
    pulsarctl topics get topic-test
    ```
    **Output**
    ```shell
    {
    "partitions": 0
    }
    ```

## Delete topics

You can use the `pulsarctl topics delete TOPIC_NAME` command to delete a partitioned topic. To delete a non-partitioned topic, you need to set the `--non-partitioned` parameter.

* This example shows how to delete the `test-topic` partitioned topic.

    **Input**
    ```
    pulsarctl topics delete topic-test
    ```

    **Output**
    ```
    Delete topic persistent://example-tenant/example-ns/test-topic  successfully
    ```

* This example shows how to delete the `topic-test` non-partitioned topic.

    **Input**
    ```
    pulsarctl topics delete --non-partitioned topic-test
    ```

    **Output**
    ```
    Delete topic persistent://example-tenant/example-ns/topic-test  successfully
    ```

# Work with topics using StreamNative Console

You can create and manage topics using the StreamNative Console. Before using the StreamNative Console to create and manage topics, you need to create a [tenant](/user-guides/admin/work-with-tenants.md#create-tenants) and a [namespace](/user-guides/admin/work-with-namespaces.md#create-namespaces).

## Create topics

To create a topic, follow these steps.

1. From the left navigation pane, click **Topics**.

2. Click **New Topic** and a dialog box is displayed.

   ![](../../image/create-topic.png)

3. Configure the topic, as listed in the following table.

    <table>
    <tr>
    <td>
    Item
    </td>
    <td>Description
    </td>
    </tr>
    <tr>
    <td>Domain
    </td>
    <td>You can configure one of the following types of the topics. 
    <ul>

    <li>Persistent: These topics are durably persisted and messages are stored on the storage disk.

    <li>Non-persistent: These topics are never persisted to disk and live only in memory. 

    <p>
    By default, it is set to <em>Persistent</em>.
    </li>
    </ul>
    </td>
    </tr>
    <tr>
    <td>Topic Name
    </td>
    <td>Enter a name for the topic. It is a string of characters, supporting lowercase letters (a-z), numeric characters (0-9), and the special character hyphen (_).
    </td>
    </tr>
    <tr>
    <td>Partitions
    </td>
    <td>Configure the number of partitions for a partitioned topic. If this value is set to 0, it means that this is a non-partitioned topic. Up to 16 partitions are available for a partitioned topic.
    </td>
    </tr>
    </table>

4. Click **Confirm**.

## Manage topics

You can view detailed information about the target topic, configure the subscription name of the topics, and unload, terminate, compact, and offload topics.

To perform basic operations for a topic, follow these steps.

1. From the left navigation pane, click **Topics**.

2. Click the link of the topic name to display detailed information about the topic.

3. (Only for partitioned topics) click the link of a specific partition on atopic.

4. Select the **OVERVIEW** tab. You can perform the following operations.

    ![](../../image/view-topic-details.png)

   * Unload the topic: click **Unload** to unload the topic.
   * Terminate the topic: click **Terminate** to terminate the topic.
   * Compact the topic: click **Compact** to compact the topic.
   * View detailed information about the topic.

## View detailed topic information

To view detailed information about a topic, follow these steps.

1. From the left navigation pane, click **Topics**.

2. Click the link of the topic name to display detailed information about the topic.

3. (Only for partitioned topics) click the link of a specific partition on atopic.

4. Select the **OVERVIEW** tab to view detailed information about the topic.


    <table>
    <tr>
    <td>Item
    </td>
    <td>Description
    </td>
    </tr>
    <tr>
    <td>Info
    </td>
    <td>View basic information about the topic.
    <ul>

    <li>In Rate: the ingress rate of the topic.

    <li>Out Rate: the egress rate of the topic.

    <li>In Throughput: the ingress throughput of the topic.

    <li>Out Throughput: the egress throughput of the topic.
    </li>
    </ul>
    </td>
    </tr>
    <tr>
    <td>Producers
    </td>
    <td>View information about the producers.
    <ul>

    <li>Producer ID: the producer ID of the topic. 

    <li>Producer Name: the producer name of the topic.

    <li>Out Throughput: the egress throughput of the topic.

    <li>In Rate: the ingress rate of the topic.

    <li>Avg Msg Size: the average message size of the topic. 

    <li>Address: the address of the topic.

    <li>Since: The time when the producer is created.
    </li>
    </ul>
    </td>
    </tr>
    <tr>
    <td>Subscription
    </td>
    <td>View information about the subscription.
    <ul>

    <li>Subscription Name: the subscription name of the topic. Click <strong>New Subscription</strong> to create a new subscription name for the target topic.

    <li>Type: the subscription type of the topic. Currently, the StreamNative Console does not support configuring the subscription type for a topic. You can configure the subscription type using the pulsarctl CLI tool. 

    <li>Out Rate: the egress rate of the topic. 

    <li>Out Throughput: the egress throughput of the topic. 

    <li>Msg Expired: the expired messages for the topic. 

    <li>Backlog: configure the backlog for the subscription.  
    <ul>
    
    <li>SKIP: skip some messages for the subscription. 
    
    <li>EXPIRE: expire messages that are older than the given expiry time (in seconds) for the subscription. 
    
    <li>CLEAR: clear the backlog for the subscription. 
    
    <li>RESET BY ID: reset the ID of the message at which the subscription is created. 
    
    <li>RESET BY TIME: reset the time when the subscription is created. 
    
    <li>PEEK: peek some messages for the subscription. 
    
    <li>UNSUBSCRIBE: delete the subscription. |
    </li> 
    </ul>
    </li> 
    </ul>
    </td>
    </tr>
    <tr>
    <td>Partitions
    </td>
    <td>View the information about partitions of a partitioned topic.
    <ul>

    <li>Partition: the partition ID.

    <li>Producers: the number of the producers of the partition.

    <li>Subscriptions: the number of subscriptions of the partition.

    <li>In Rate: the ingress rate of the partition.

    <li>Out Rate: the egress rate of the partition.

    <li>In Throughput: the ingress throughput of the partition.

    <li>Out Throughput: the egress throughput of the partition.

    <li>Storage Size: the storage size of the partition.
    </li>
    </ul>
    </td>
    </tr>
    </table>

## Manage schemas

When a schema is enabled, Pulsar takes bytes as inputs and sends bytes as outputs when parsing data. 

> **Note**
> 
> Currently, only Avro-type and JSON-type schemas are supported on StreamNative Console.

To configure schema for topics, follow these steps.

1. From the left navigation pane, click **Topics**.

2. Click the link of the topic name.

3. (Only for partitioned topics) click the link of a partition of a partitioned topic. 

4. Select the **SCHEMA** tab.

5. Click **Create schema** to switch to the page for configuring schema for the target topic.

   * Select a schema type from the **Schema Type** drop-down list. 
   * At the **Schema Definition** area, define the schema. In addition, you can click **Format Code** to format your code.
   * At the **Properties** area,  Click the plus (+) icon and a dialog box is displayed, where you can configure the key and value for the schema. Then, click **Confirm**.

        ![](../../image/create-topic-schema.png)

6. Click **Create schema**.

## View topic storage

To view topic storage, follow these steps.

1. From the left navigation pane, click **Topics**.

2. Click the link of the topic name.

3. (Only for partitioned topics) click the link of a partition of a partitioned topic. 

4. Select the **Storage** tab and view storage information for this topic.

   ![](../../image/view-topic-storage.png)

    <table>
    <tr>
    <td>Item
    </td>
    <td>Description
    </td>
    </tr>
    <tr>
    <td>Storage size
    </td>
    <td>The storage size.
    </td>
    </tr>
    <tr>
    <td>Entries
    </td>
    <td>The number of entries.
    </td>
    </tr>
    <tr>
    <td>Segments
    </td>
    <td>The number of segments.
    </td>
    </tr>
    <tr>
    <td>Segment
    </td>
    <td>View information about the ledger.
    <ul>

    <li>Ledger ID: the ledger ID.

    <li>Entries: the total number of entries that belong to the ledger.

    <li> Size: the size of messages written to the ledger (in bytes). 

    <li>Status: the state of the ledger for writing messages. If it is set to `Opening`, the ledger is open for saving published messages.

    <li>Offload: whether the ledger can be offloaded or not. 
    </li>
    </ul>
    </td>
    </tr>
    <tr>
    <td>Cursors
    </td>
    <td>View information about the cursor.
    <ul>

    <li>Cursor: the cursor ID. 

    <li>Mark Delete Position: all messages before the `markDeletePosition` are acknowledged by the subscriber. 

    <li>Read Position: the latest position for the subscriber to read messages. 

    <li>Waiting Read Op: it is set to `True` when the subscription has read the latest message published to the topic and is waiting for new messages to be published. 

    <li>Pending Read Op: the number of read requests to the BookKeepers in progress. 

    <li>Entries Since First Not AckedMessage: entries that are not acknowledged.
    </li>
    </ul>
    </td>
    </tr>
    </table>

## Configure topic policies

You can configure policies for a topic. To configure policies for topics, follow these steps.

1. From the left navigation pane, click **Topics**.

2. Click the link of the topic name.

3. (Only for partitioned topics) click the link of a partition of a partitioned topic. 

4. Select the **POLICIES** tab and configure authorization policies for the topic.

   ![](../../image/configure-topic-policy.png)

    <table>
    <tr>
    <td>Item
    </td>
    <td>Description
    </td>
    </tr>
    <tr>
    <td>Authorization
    </td>
    <td>Grant/Revoke permissions to other client roles. If you have granted or revoked permissions to a namespace, all topics in this namespace are granted or revoked the same permissions.
    <ul>

    <li>consume: grant/revoke the consuming action.

    <li>produce: grant/revoke the producing action.

    <li>functions: grant/revoke the Pulsar functions action.
    </li>
    </ul>
    </td>
    </tr>
    </table>

5. (Optional) Click **Add Role** to add more permissions for the topic.

## Delete topics

If you do not want to subscribe to a topic, you can remove that topic. To delete a topic, follow these steps.

1. From the left navigation pane, click **Topics**.

2. Click the link of the topic name.

3. (Only for partitioned topics) click the link of a partition of a partitioned topic.

4. Select the **POLICIES** tab and then click **Delete Topic**. A dialog box is displayed, asking _Are you sure you want to delete this topic?_

   ![](../../image/delete-topic.png)

5. Click **Confirm**.