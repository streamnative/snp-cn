---
title: Work with namespaces
id: work-with-namespaces
category: user-guides
---

In Pulsar, a namespace is the administrative unit nomenclature within a tenant. This document describes how to create and manage namespaces for a cluster using the pulsarctl CLI tool or the StreamNative Console.

# Work with namespaces using the pulsarctl CLI tool

You can create and manage namespaces using the pulsarctl CLI tool. For a full list of supported operations on namespaces, see the [pulsarctl command reference](https://docs.streamnative.io/pulsarctl/v2.7.0.7/#-em-update-em--32).

> **Note**
> 
> This section uses a tenant named `example-tenant` as an example. For details about how to create a tenant, see [work with tenants](/user-guides/admin/work-with-tenants.md).

## Create namespaces

After creating and authorizing a tenant, you can create and manage namespaces and topics.

This example shows how to create a namespace named `example-ns` for `example-tenant`.

**Input**

```
pulsarctl namespaces create example-tenant/example-ns
```

**Output**

```
Created example-tenant/example-ns successfully
```

## Clear namespace backlog

Pulsar stores all unacknowledged messages in backlogs until they are processed and acknowledged. You can clear backlogs of messages for a specific namespace to release more backlog quota for the namespace.

This example shows how to clear the backlog for all topics of `example-tenant/example-ns`.

**Input**

```
pulsarctl namespaces clear-backlog example-tenant/example-ns
```

**Output**

```
Are you sure you want to clear the backlog? (Y or N)

y

Successfully clear backlog for all topics of the example-tenant/example-ns
```

## Unload namespaces

This example shows how to unload `example-tenant/example-ns` from the current serving broker.

**Input**

```
pulsarctl namespaces unload example-tenant/example-ns
```

**Output**

```
Unload namespace example-tenant/example-ns successfully
```

## Delete namespaces

> **Note**
> 
> You cannot delete a namespace until no resource is associated with the namespace.

This example shows how to delete `example-tenant/example-ns`.

**Input**

```
pulsarctl namespaces delete example-tenant/example-ns
```

**Output**

```
Deleted example-tenant/example-ns successfully
```

# Work with namespaces using StreamNative Console

This section describes how to create and manage namespaces using the StreamNative Console. Before using the StreamNative Console to create and manage namespaces, you need to create a tenant. For details, see [work with tenants](/user-guides/admin/work-with-tenants.md).

## Create namespaces

To create a namespace, follow these steps.

1. From the left navigation pane, click **Namespaces**.

2. Click **Create Namespace**. A dialog box is displayed.

   ![](../../image/create-namespace.png)

3. Enter a name for the namespace and then click **Confirm**. The namespace name is a string of up to 40 characters, supporting lowercase letters (a-z), numeric characters (0-9), and the special character hyphen (-).

## View namespace statistics

To view statistics about a namespace, follow these steps.

1. From the left navigation pane, click the **Namespaces**.

2. Select the **OVERVIEW** tab.

3. Select the target tenant and namespace from the drop-down lists respectively. The following table lists statistics about the namespace.

   ![](../../image/check-ns-1.png)

    <table>
    <tr>
    <td>
    Item
    </td>
    <td>Description
    </td>
    </tr>
    <tr>
    <td>In Rate
    </td>
    <td>The ingress rate of the namespace. 
    </td>
    </tr>
    <tr>
    <td>Out Rate
    </td>
    <td>The egress rate of the namespace.
    </td>
    </tr>
    <tr>
    <td>In Throughput 
    </td>
    <td>The ingress throughput of the namespace.
    </td>
    </tr>
    <tr>
    <td>Out Throughput
    </td>
    <td>The egress throughput of the namespace.
    </td>
    </tr>
    </table>

4. Select the **TOPICS** tab to check the number of topics available for the namespace and statistics about topics. In addition, you can create new topics and update specific topics.

## Unload namespace bundles

For assignment, a namespace is sharded into a list of bundles, with each bundle comprising a portion of the overall hash range of the namespace. By default, four bundles are supported for each namespace.

To unload bundles for a namespace, follow these steps.

1. From the left navigation pane, click **Namespaces**.

2. Select the **OVERVIEW** tab.

3. Select the target tenant and namespace from the drop-down lists respectively.

4. At the **Bundle** section, select the target cluster from the **Cluster** drop-down list, and then click **Unload All** to unload all namespace bundles from the cluster.

5. At the **Bundle** section, click **Unload** next to a specific bundle to unload the bundle for the namespace.

## Split namespace bundles

Since the load for topics in a bundle might change over time, one bundle can be split in two bundles by brokers. Then, the new smaller bundles are reassigned to different brokers. By default, the newly split bundles are immediately offloaded to other brokers to facilitate the traffic distribution.

To split bundles for a namespace, follow these steps.

1. From the left navigation pane, click **Namespaces**.

2. Select the **OVERVIEW** tab.

3. Select the target tenant and namespace from the drop-down lists respectively.

4. At the **Bundle** section, click **Split** next to a specific bundle to split the bundle for the namespace.

## Clear namespace backlog

You can clear backlog for bundles of a namespace.

1. From the left navigation pane, click **Namespaces**.

2. Select the **OVERVIEW** tab.

3. Select the target tenant and namespace from the drop-down lists respectively.

4. At the **Bundle** section, select the target cluster from the **Cluster** drop-down list, and then click **Clean All Backlog** to clear the backlog for all topics of the namespace.

5. At the **Bundle** section, click **Clear Backlog** next to a specific bundle to clear the backlog for all topics of the namespace bundle.

## Configure namespace policies

The policies set on a namespace apply to all the topics created in that namespace.

To configure policies for a namespace, follow these steps.

1. From the left navigation pane, click **Namespaces**.

2. Select the **POLICY** tab.

3. Select the target tenant and namespace from the drop-down lists respectively.

4. Configure policies for the namespace, as listed in the following table.

   ![](../../image/configure-ns-policy.png)

    <table>
    <tr>
    <td>
    Field
    </td>
    <td>Description
    </td>
    </tr>
    <tr>
    <td>Clusters
    </td>
    <td>Select a replication cluster. Messages of the topics in this namespace are asynchronically replicated between the configured replication clusters. In this release, the replication cluster is the same with the cluster you created because there is only one cluster available for an instance.
    </td>
    </tr>
    <tr>
    <td>Authorization
    </td>
    <td>Grant/Revoke permissions to other client roles in this namespace.
    <ul>

    <li>consume: grant/revoke the consuming action. 

    <li>produce: grant/revoke the producing action. 

    <li>functions: grant/revoke the Pulsar functions action.

    <p>
    Click <strong>Add Role</strong> to set one or more service accounts as the administrator of the namespace.
    <p>
    Click <strong>Delete Role</strong> to remove a specific service account from the administrator list of the namespace.
    </li>
    </ul>
    </td>
    </tr>
    <tr>
    <td>Subscription Authentication
    </td>
    <td>Configure the subscription authentication mode to limit the naming convention for subscriptions when authentication is enabled. 
    <ul>

    <li>None: Pulsar clients can use any allowed subscription name to connect to the cluster. 

    <li>Prefix: Pulsar clients can only use subscription names that are prefixed with their role names to subscribe messages. 

    <p>
    By default, it is set to <em>None</em>.
    </li>
    </ul>
    </td>
    </tr>
    <tr>
    <td>Storage
    </td>
    <td>Configure the storage policy for the namespace.

    **Replication Factor**: configure the storage replication settings. <ul><li> Ensemble size: configure the number of bookies to be used when creating a ledger. <li> Write Quorum Size: configure the number of replicas to be stored for each message. <li> Ack Quorum Size: configure the number of responses to wait before claiming a message is guaranteed to be stored. </ul>
**Mark-Delete Rate**: configure the number of mark-deletion calls that are allowed for each second. When the value is set to 0, the rate limiter is disabled. By default, the value is set to 0. </p>
**Encryption Required**: enable/disable message encryption for the namespace. <ul><li> Enabled: enable message encryption for the namespace. <li> Disabled: disable message encryption for the namespace. </ul>
**Deduplication**: enable/disable deduplication for the namespace. <ul><li> Enabled: enable deduplication for the namespace. <li> Disabled: disable deduplication for the namespace.</ul>
    </td>
    </tr>
    <tr>
    <td>Backlog
    </td>
    <td>Configure the backlog policy for the namespace. 
    
    **Backlog Quota Size**: configure the maximum backlog quota (in bytes) allowed for the namespace. The backlog quota size must be smaller than 1000000000 bytes. By default, it is set to -1073741824 bytes.</p> **Backlog Retention Policy**: configure the backlog retention policy. <ul><li> consumer_backlog_eviction: the broker starts discarding backlog messages. <li>producer_exception: the broker disconnects with the client by giving an exception. <li>producer_request_hold: the broker holds but does not persist producer request payload.</ul>
    </td>
    </tr>
    <tr>
    <td>Schema
    </td>
    <td>Configure the schema policy for the namespace. 
    <ul>

    <li>AutoUpdate Strategy: configure the automatic update strategy of the schema for the namespace. 

    <li>Schema Validation Enforced: enable/disable schema validation for producers without schema. If it is disabled, producers without schema are disallowed to produce messages to topics with schema.
    </li>
    </ul>
    </td>
    </tr>
    <tr>
    <td>Cleanup Policy
    </td>
    <td>Configure the cleanup policy for the namespace. 
    <ul>

    <li>Message TTL (seconds): configure the message TTL in seconds. If messages are not consumed by any consumer of a subscription, they are marked as "consumed" after the configured TTL period expires. 

    <li>Retention Size (bytes): configure the retention size. This field is only applied to the messages that are acknowledged by all subscriptions. The retention size must be smaller than 1000 MB. By default, it is set to 0. 

    <li>Retention Period (minutes): configure the retention period. This field is only applied to the messages that are acknowledged by all subscriptions. By default, it is set to 0. 

    <li>Compaction Threshold (bytes): configure the threshold to compact topics. The compaction is automatically triggered when the threshold is reached. By default, it is set to 0. 

    <li>Offload Threshold (bytes): configure the threshold to offload messages. Messages are automatically offloaded to the tiered storage when the threshold is reached. By default, it is set to -1. 

    <p>
    Offload Deletion Lag (milliseconds): configure the time (in milliseconds) to wait before deleting a ledger offloaded from the BookKeeper. When the value is set to a negative value, offloading deletion is disabled.
    </li>
    </ul>
    </td>
    </tr>
    <tr>
    <td>Throttling
    </td>
    <td>Configure the throttling policy for the namespace. 
    <ul>

    <li>Max Producers Per Topic: configure the maximum number of producers allowed for each topic.

    <li>Max Consumers Per Topic: configure the maximum number of consumers allowed for each topic. 

    <li>Max Consumers Per Subscription: configure the maximum number of consumers allowed for each subscription.
    </li>
    </ul>
    </td>
    </tr>
    <tr>
    <td rowspan="3" >Dispatch Rate
    </td>
    <td>Dispatch Rate Per Topic: configure the dispatch rate based on the topic. 
    <ul>

    <li>Throughput (bytes/second): configure the dispatch rate of bytes in a topic. 

    <li>Rate (messages/second): configure the dispatch rate of messages in a topic. Time Period (seconds): configure the dispatch rate period. 
    </li>
    </ul>
    </td>
    </tr>
    <tr>
    <td>Dispatch Rate Per Subscription: configure the dispatch rate based on the subscription mode. 
    <ul>

    <li>Throughput (bytes/second): configure the dispatch rate of bytes for a subscription.

    <li>Rate (messages/second): configure the dispatch rate of messages for a subscription.

    <li>Time Period (seconds): configure the dispatch rate period. 
    </li>
    </ul>
    </td>
    </tr>
    <tr>
    <td>Dispatch Rate Per Consumer: configure the rate at which a consumer subscribes to a topic. 
    <ul>

    <li>Throughput (bytes/second): configure the throughput for a topic that a consumer subscribes to. 

    <li>Rate (messages/second): configure the rate for a topic that a consumer subscribes to.
    </li>
    </ul>
    </td>
    </tr>
    </table>

## Delete namespaces

To delete a namespace, follow these steps.

1. From the left navigation pane, click **Namespaces**.

2. Select the **POLICY** tab.

3. Select the target tenant and namespace from the drop-down lists respectively.

4. Scroll down the page and then click **Delete** **Namespace**. A dialog box is displayed, asking “_Are you sure you want to delete this?_”

   ![](../../image/delete-ns.png)

5. Enter the namespace name and then click **Confirm**.