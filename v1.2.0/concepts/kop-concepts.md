---
title: KoP
id: kop-concepts
category: concepts
---

Kafka on Pulsar (KoP) brings the native Apache Kafka protocol support to Apache Pulsar by introducing a Kafka protocol handler on Pulsar brokers. By adding the KoP protocol handler to your existing Pulsar cluster, you can now migrate your existing Kafka applications and services to Pulsar without modifying the code. This enables Kafka applications to leverage Pulsar’s powerful features, such as:

- Streamlined operations with enterprise-grade multi-tenancy
- Simplified operations with a rebalance-free architecture
- Infinite event stream retention with Apache BookKeeper and tiered storage
- Serverless event processing with Pulsar Functions

KoP, implemented as a Pulsar protocol handler plugin with the protocol name "kafka", is loaded when Pulsar broker starts. It is believed that providing a native Kafka protocol support on Apache Pulsar helps reduce the barriers for people adopting Pulsar to achieve their business success. By integrating two popular event streaming ecosystems, KoP unlocks new use cases. You can leverage advantages from each ecosystem and build a truly unified event streaming platform with Apache Pulsar to accelerate the development of real-time applications and services.

KoP implements the Kafka wire protocol on Pulsar by leveraging the existing components (such as topic discovery, the distributed log library - ManagedLedger, cursors and so on) that Pulsar already has. To use KoP, you can download the StreamNative Platform, and [get it started](/quickstart.md). 

The following figure illustrates how we add the Kafka protocol support on Pulsar.

![](../../../image/kop-architecture.png)

# Topic

In Kafka, all topics are stored in one flat namespace. But in Pulsar, topics are organized in hierarchical multi-tenant namespaces. KoP introduces a setting `kafkaNamespace` in the broker configuration, which allows the administrator to map Kafka topics to Pulsar topics.

To let Kafka users leverage the multi-tenant feature of Apache Pulsar, a Kafka user can specify a Pulsar tenant and namespace as its SASL (Simple Authentication and Security Layer) username when it uses SASL authentication mechanism to authenticate a Kafka client.

## Topic lookup

KoP uses the same topic lookup approach for the Kafka request handler and the Pulsar request handler. The request handler does topic discovery to look up all the ownerships for the requested topic partitions and responds with the ownership information as part of Kafka `TopicMetadata` back to Kafka clients.

# Message

Both a Kafka message and a Pulsar message have the key, value, timestamp, and headers. (In Pulsar，The `headers` is called `properties`.) KoP converts these fields automatically between Kafka messages and Pulsar messages.

## Message ID and offset

In Kafka, each message is assigned with an offset once the message is successfully produced to a topic partition. In Pulsar, each message is assigned with a `MessageID`. The message ID consists of `ledger-id`, `entry-id`, and `batch-index` components. KoP uses the same approach in Pulsar-Kafka wrapper to convert a Pulsar `MessageID` to an offset and vice versa.

## Produce Messages

When the Kafka request handler receives produced messages from a Kafka client, it converts Kafka messages to Pulsar messages by mapping the fields (such as the key, value, timestamp and headers) one by one, and uses the ManagedLedger append API to append those converted Pulsar messages to BookKeeper. Converting Kafka messages to Pulsar messages allows existing Pulsar applications to consume messages produced by Kafka clients.

## Consume Messages

When the Kafka request handler receives a consumer request from a Kafka client, it opens a non-durable cursor to read the entries starting from the requested offset. The Kafka request handler converts the Pulsar messages back to Kafka messages to allow existing Kafka applications to consume the messages produced by Pulsar clients.

# Group coordinator & offset management

The most challenging part is to implement the group coordinator and offset management. Pulsar does not have a centralized group coordinator for assigning partitions to consumers of a consumer group or managing offsets for each consumer group. In Pulsar, the partition assignment is managed by the broker on a per-partition basis, and the offset management is done by storing the acknowledgements in cursors by the owner broker of that partition.

It is difficult to align the Pulsar model with the Kafka model. Therefore, to be fully compatible with Kafka clients, KoP implements the Kafka group coordinator by storing the coordinator group changes and offsets in a system topic called `public/kafka/__offsets` in Pulsar. 
This bridges the gap between Pulsar and Kafka and allows people to use existing Pulsar tools and policies to manage subscriptions and monitor Kafka consumers. KoP adds a background thread in the implemented group coordinator to periodically synchronize offset updates from the system topic to Pulsar cursors. Therefore, a Kafka consumer group is effectively treated as a Pulsar subscription. All the existing Pulsar tools can be used for managing Kafka consumer groups as well.
