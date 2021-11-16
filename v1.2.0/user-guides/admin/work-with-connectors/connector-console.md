---
title: Work with connectors using StreamNative Console
id: connector-console
category: user-guides
---

# Work with connectors using StreamNative Console

This document describes how to work with connectors through StreamNative Console. 

Currently, StreamNative Console supports the following connectors: 

* AMQP1_0 [source](https://hub.streamnative.io/connectors/amqp-1-0-source/v2.7.1.1) and [sink](https://hub.streamnative.io/connectors/amqp-1-0-sink/v2.7.1.1) connectors
* AWS SQS [source](https://hub.streamnative.io/connectors/sqs-source/2.7.0) and [sink](https://hub.streamnative.io/connectors/sqs-sink/2.7.0) connector 
* [Cloud Storage [sink](https://hub.streamnative.io/connectors/cloud-storage-sink/2.5.1) connector]([https://hub.streamnative.io/connectors/cloud-storage-sink/2.5.1](https://hub.streamnative.io/connectors/cloud-storage-sink/2.5.1))
* Kinesis source and [sink](https://hub.streamnative.io/connectors/kinesis-sink/2.5.1) connectors [source](https://hub.streamnative.io/connectors/kinesis-source/2.5.1) and [sink]([https://hub.streamnative.io/connectors/kinesis-sink/2.5.1](https://hub.streamnative.io/connectors/kinesis-sink/2.5.1))
* Data generator source and sink connectors (only for internal test purpose)

>**Note**
>
> Currently, the Cloud Storage sink connector is only available for the AWS platform.

# Create connectors

This section describes how to create source and sink connectors.

## Create source connectors

To create a source connector, follow these steps.

1. From the left navigation pane, click **Connector** > **Source**.

2. Click the icon of the source connector type that you want to create to enter the page where you can configure the source connector.

   ![](../../image/create-source.png)

3. Configure items about the source connector, as listed in the following table.

<table>
  <tr>
   <td>
Field
   </td>
   <td>Description
   </td>
  </tr>
  <tr>
   <td>Source Type
   </td>
   <td>(Read-only) the type of the source connector.
   </td>
  </tr>
  <tr>
   <td>Source Name
   </td>
   <td>Enter a name for the source connector. It is a string of characters, supporting lowercase characters, numeric characters, and the special character hyphen (-)
   </td>
  </tr>
  <tr>
   <td>Tenant
   </td>
   <td>Select the tenant for the source connector.
   </td>
  </tr>
  <tr>
   <td>Namespace
   </td>
   <td>Select the namespace for the source connector.
   </td>
  </tr>
  <tr>
   <td>Output Topic
   </td>
   <td>Select the Pulsar topic into which the messages are ingested.
   </td>
  </tr>
  <tr>
   <td>Replicas
   </td>
   <td>Set the number of instances for running the source connector.
   </td>
  </tr>
  <tr>
   <td>Enable auto scaling
   </td>
   <td>Enable or disable auto-scaling. If enabled, you should set the maximum number of the instances for running the source connector. The maximum number of the instances must be greater than the value of the `Replicas` and equal to or smaller than `10`.
   </td>
  </tr>
  <tr>
   <td>Config
   </td>
   <td>(Optional) Configure the source connector. For detailed descriptions about configurations of the supported source connectors, see [StreamNative Hub](<a href="https://hub.streamnative.io/">https://hub.streamnative.io/</a>). If you do not configure the source connector, the connector is created with default configurations. 
   </td>
  </tr>
</table>

4. Click **Create**.

## Create sink connectors

To create a sink connector, follow these steps.

1. From the left navigation pane, click **Connector** > **Sink**. 

2. Click the icon of the sink connector that you want to create to enter the page where you can configure the sink connector.

   ![](../../image/create-sink.png)

3. Configure items about the sink connector, as listed in the following table.

<table>
  <tr>
   <td>
Field
   </td>
   <td>Description
   </td>
  </tr>
  <tr>
   <td>Sink Type
   </td>
   <td>(Read-only) the type of the sink connector.
   </td>
  </tr>
  <tr>
   <td>Sink Name
   </td>
   <td>Enter a name for the sink connector.
   </td>
  </tr>
  <tr>
   <td>Tenant
   </td>
   <td>Select the tenant for the sink connector.
   </td>
  </tr>
  <tr>
   <td>Namespace
   </td>
   <td>Select the namespace for the sink connector.
   </td>
  </tr>
  <tr>
   <td>Input Topic
   </td>
   <td>Click <strong>Add</strong> to add one more Pulsar topics from which the messages are exported.
   </td>
  </tr>
  <tr>
   <td>Replicas
   </td>
   <td>Configure the number of instances for running the sink connector.
   </td>
  </tr>
  <tr>
   <td>Enable auto scaling
   </td>
   <td>Enable or disable auto-scaling. If enabled, you should set the maximum number of the instances for running the sink connector. The maximum number of the instances must be greater than the value of the `Replicas` and equal to or smaller than `10`.
   </td>
  </tr>
  <tr>
   <td>Config
   </td>
   <td>(Optional) Configure the sink connector. For detailed descriptions about configurations of the supported sink connectors, see [StreamNative Hub](<a href="https://hub.streamnative.io/">https://hub.streamnative.io/</a>). If you do not configure the source connector, the connector is created with default configurations. 
   </td>
  </tr>
</table>

4. Click **Create**.

# Update connectors

After a connector is created, you can update some of its fields. This section describes how to update source and sink connectors.

## Update source connectors

To update a source connector, follow these steps.

1. From the left navigation pane, click **Connector** > **Source**. 

2. Click the target source connector and you can check information about the source connector.

3. Update the following items about the source connector. 

    * Tenant: configure the target tenant for the source connector.
    * Namespace: configure the target namespace for the source connector.
    * Output Topic: update the Pulsar topic into which the messages are ingested.
    * Replicas: update the number of instances for running the source connector.
    * Auto-scaling: when auto-scaling is enabled, update the maximum number of the instances for running the source connector.
    * Config: update configurations about the source connector.
4. Click **Update**.

## Update sink connectors

To update a sink connector, follow these steps.

1. From the left navigation pane, click **Connector** > **Sink**. 
2. Click the target sink connector and you can check information about the sink connector.
3. Update the following items about the sink connector.
    * Tenant: configure the target tenant for the sink connector.
    * Namespace: configure the target namespace for the sink connector.
    * Input Topic: 
        * Click **Add** to add one or more Pulsar topics for the sink connector. 
        * Click **Delete** to delete one or more Pulsar topics for the sink connector.
    * Replicas: update the number of instances for running the sink connector.
    * Auto-scaling: when auto-scaling is enabled, update the maximum number of the instances for running the sink connector.
    * Config: update configurations about the sink connector.
4. Click **Update**.

# Clone connectors

If you want to create a new connector without modifying too many configurations, you can clone an existing connector and then update some of the configurations based on your requirements.

## Clone source connectors

To clone a source connector, follow these steps.

1. From the left navigation pane, click **Connector** > **Source**. 
2. Click the target source connector.
3. Scroll down the page and then click **Clone**. A page is displayed, where you can configure the source connector based on your requirements.

   ![](../../image/clone-source.png)

<table>
  <tr>
   <td>
Field
   </td>
   <td>Description
   </td>
  </tr>
  <tr>
   <td>Source Name
   </td>
   <td>Enter a name for the source connector. It is a string of characters, supporting lowercase characters, numeric characters, and the special character hyphen (-)
   </td>
  </tr>
  <tr>
   <td>Tenant
   </td>
   <td>Select the tenant for the source connector.
   </td>
  </tr>
  <tr>
   <td>Namespace
   </td>
   <td>Select the namespace for the source connector.
   </td>
  </tr>
  <tr>
   <td>Output Topic
   </td>
   <td>Select the Pulsar topic into which the messages are ingested.
   </td>
  </tr>
  <tr>
   <td>Replicas
   </td>
   <td>Set the number of instances for running the source connector.
   </td>
  </tr>
  <tr>
   <td>Enable auto scaling
   </td>
   <td>Enable or disable auto-scaling. If enabled, you should set the maximum number of the instances for running the source connector. The maximum number of the instances must be greater than the value of the `Replicas` and equal to or smaller than `10`.
   </td>
  </tr>
  <tr>
   <td>Config
   </td>
   <td>(Optional) Configure the source connector. For detailed descriptions about configurations of the supported source connectors, see [StreamNative Hub](<a href="https://hub.streamnative.io/">https://hub.streamnative.io/</a>). If you do not configure the source connector, the connector is created with default configurations. 
   </td>
  </tr>
</table>

4. Click **Create**.

## Clone sink connectors

To clone a sink connector, follow these steps.

1. From the left navigation pane, click **Connector** > **Sink**. 
2. Click the target sink connector.
3. Scroll down the page and then click **Clone**. A page is displayed, where you can configure the connector based on your requirements.

   ![](../../image/clone-sink.png)


<table>
  <tr>
   <td>Field
   </td>
   <td>Description
   </td>
  </tr>
  <tr>
   <td>Sink Name
   </td>
   <td>Enter a name for the sink connector.
   </td>
  </tr>
  <tr>
   <td>Tenant
   </td>
   <td>Select the tenant for the source connector.
   </td>
  </tr>
  <tr>
   <td>Namespace
   </td>
   <td>Select the namespace for the source connector.
   </td>
  </tr>
  <tr>
   <td>Input Topic
   </td>
   <td>
<ul>

<li>Click <strong>Add</strong> to add one or more Pulsar topics for the sink connector.

<li>Click <strong>Delete</strong> to delete one or more Pulsar topics for the sink connector.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Replicas
   </td>
   <td>Configure the number of instances for running the sink connector.
   </td>
  </tr>
  <tr>
   <td>Enable auto scaling
   </td>
   <td>Enable or disable auto-scaling. If enabled, you should set the maximum number of the instances for running the sink connector. The maximum number of the instances must be greater than the value of the `Replicas` and equal to or smaller than `10`.
   </td>
  </tr>
  <tr>
   <td>Config
   </td>
   <td>(Optional) Configure the sink connector. For detailed descriptions about configurations of the supported sink connectors, see [StreamNative Hub](<a href="https://hub.streamnative.io/">https://hub.streamnative.io/</a>). If you do not configure the source connector, the connector is created with default configurations. 
   </td>
  </tr>
</table>

4. Click **Create**.

# Delete connectors

This section describes how to delete source and sink connectors.

## Delete source connectors

To delete a source connector, follow these steps.

1. From the left navigation pane, click **Connector** > **Source**. 
2. Click the target source connector and then scroll down the page.
3. Click **Delete Source**. 
  
   ![](../../image/delete-source.png)

4. Enter the source connector name and then click **Confirm**.

## Delete sink connectors

To delete a sink connector, follow these steps.

1. From the left navigation pane, click **Connector** > **Sink**. 
2. Click the target sink connector and then scroll down the page. 
3. Click **Delete Sink**. A dialog box is displayed.

   ![](../../image/delete-sink.png)

4. Enter the sink connector name and then click **Confirm**.

