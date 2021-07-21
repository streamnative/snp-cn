---
title: Pub/Sub
id: pub-sub-concepts
category: concepts
---

Pulsar is built on the [publish-subscribe](https://en.wikipedia.org/wiki/Publish%E2%80%93subscribe_pattern) pattern (often abbreviated to pub-sub). In this pattern, [producers](#producers) publish messages to [topics](#topics). [Consumers](#consumers) [subscribe](#subscriptions) to those topics, process incoming messages, and send an acknowledgement when processing is complete.

When a subscription is created, Pulsar persists all messages, even if the consumer is disconnected. Retained messages are discarded only when a consumer acknowledges that those messages are processed successfully.

# Tenants

Pulsar was created from the ground up as a multi-tenant system. To support multi-tenancy, Pulsar has a concept of tenants. Tenants can be spread across clusters and can each have their own authentication and authorization scheme applied to them. They are also the administrative unit at which storage quotas, message TTL, and isolation policies can be managed.

For details about how to create tenants, see [work with tenant](/user-guides/admin/work-with-tenants.md).

# Namespaces

A namespace represents an administrative unit within a tenant. The configuration policies set on a namespace apply to all the topics created in that namespace. You can create multiple namespaces for a tenant using the StreamNative Console, REST API or the pulsar-admin CLI tool.

For details about how to create namespaces, see [work with namespace](/user-guides/admin/work-with-namespaces.md).

## Permissions

In Pulsar, permissions are managed at the namespace level (within tenants and clusters). You can grant permissions to specific users for lists of operations such as `produce` and `consume`. In addition, you can revoke permissions from specific users, which means that those users cannot access the specified namespace.

## Backlog quotas

Backlogs are sets of unacknowledged messages for a topic that have been stored by bookies. Pulsar stores all unacknowledged messages in backlogs until they are processed and acknowledged.
You can use the backlog quotas to control the allowable size of backlogs at the namespace level. You can set the following items for a backlog quota:

- an allowable size threshold for each topic in the namespace
- a retention policy that determines which action the broker takes if the threshold is exceeded.

The following table lists available retention policies.
| Policy | Action |
| --- | --- |
| `producer_request_hold` | The broker holds but does not persist producers' request payload. |
| `producer_exception` | The broker disconnects from the client by throwing an exception. |
| `consumer_backlog_eviction` | The broker begins discarding backlog messages. |
## Bundle

For assignment, a namespace is sharded into a list of bundles, with each bundle comprising a portion of the overall hash range of the namespace. A bundle is a virtual group of topics that belong to the same namespace. A namespace bundle is defined as a range between two 32-bit hashes, such as 0x00000000 and 0xffffffff. By default, four bundles are supported for each namespace.

Since the load for topics in a bundle might change over time, one bundle can be split in two bundles by brokers. Then, the new smaller bundle is reassigned to different brokers. By default, the newly split bundles are immediately offloaded to other brokers to facilitate the traffic distribution.

## Dispatch rate

Dispatch rate refers to the number of messages dispatched per second by topics for a namespace. Dispatch rate can be restricted by number of messages per second (`msg-dispatch-rate`) or by number of bytes of messages per second (`byte-dispatch-rate`). Dispatch rate is in second and it can be configured with `dispatch-rate-period`. By default, `msg-dispatch-rate` and `byte-dispatch-rate` are both set to -1, which indicates that throttling is disabled.

# Topics

As in other pub-sub systems, topics in Pulsar are named channels for transmitting messages from producers to consumers. Pulsar supports persistent and non-persistent topics. By default, a persistent topic is created if you do not specify a topic type. With persistent topics, all messages are durably persisted on disks (if the broker is not standalone, messages are durably persisted on multiple disks). whereas data for non-persistent topics is not persisted to storage disks.

Topic names are URLs that have a well-defined structure:

```http
{persistent|non-persistent}://tenant/namespace/topic
```

Topic name component | Description
:--------------------|:-----------
`persistent` / `non-persistent` | This identifies the type of topic. Pulsar supports persistent and [non-persistent](#non-persistent-topics) topics. By default, a persistent topic is created if you do not specify the topic type. With persistent topics, all messages are durably persisted on disks (if the broker is not standalone, messages are durably persisted on multiple disks), whereas data for non-persistent topics is not persisted to storage disks.
`tenant`             | The topic tenant within the instance. Tenants are essential to multi-tenancy in Pulsar, and spread across clusters.
`namespace`          | The administrative unit of the topic, which acts as a grouping mechanism for related topics. Most topic configuration is performed at the [namespace](#namespaces) level. Each tenant has one or multiple namespaces.
`topic`              | The final part of the name. Topic names have no special meaning in a Pulsar instance.

You do not need to explicitly create topics in Pulsar. If a client writes or receives messages to/from a topic that does not yet exist, Pulsar creates that topic under the namespace provided in the [topic name](#topics) automatically. If no tenant or namespace is specified when a client creates a topic, the topic is created in the default tenant and namespace. You can also create a topic in a specified tenant and namespace, such as `persistent://my-tenant/my-namespace/my-topic`. `persistent://my-tenant/my-namespace/my-topic` means the `my-topic` topic is created in the `my-namespace` namespace of the `my-tenant` tenant.

## Non-persistent topics

Non-persistent topics are topics on which messages are never persisted to disk and live only in memory. When using non-persistent delivery, killing a Pulsar broker or disconnecting a subscriber to a topic means that all in-transit messages are lost on that non-persistent topic. In non-persistent topics, brokers immediately deliver messages to all connected subscribers without persisting them in BookKeeper.

### Performance

Non-persistent messaging is usually faster than persistent messaging because brokers don't persist messages and immediately send acks back to the producer as soon as that message is delivered to connected brokers. Producers thus see comparatively low publish latency with non-persistent topics.

## Partitioned topics

Normal topics are served only by a single broker, which limits the maximum throughput of the topic. Partitioned topics are a special type of topic that are handled by multiple brokers, thus allowing for higher throughput.
A partitioned topic is actually implemented as N internal topics, where N is the number of partitions. When publishing messages to a partitioned topic, each message is routed to one of several brokers. The distribution of partitions across brokers is handled automatically by Pulsar.
### Routing modes

When publishing to partitioned topics, you must specify a *routing mode*. The routing mode determines which partition---that is, which internal topic---each message should be published to.

Mode     | Description 
:--------|:------------
`RoundRobinPartition` | If no key is provided, the producer will publish messages across all partitions in round-robin fashion to achieve maximum throughput. Please note that round-robin is not done per individual message but rather it's set to the same boundary of batching delay, to ensure batching is effective. While if a key is specified on the message, the partitioned producer will hash the key and assign the message to a particular partition. This is the default mode. 
`SinglePartition`     | If no key is provided, the producer will randomly pick one single partition and publish all the messages into that partition. While if a key is specified on the message, the partitioned producer will hash the key and assign the message to a particular partition.
`CustomPartition`     | Use custom message router implementation that will be called to determine the partition for a particular message.

### Ordering guarantee

The ordering of messages is related to routing mode and the key of the message. Usually, users would want an ordering of the Per-key-partition guarantee.

If there is a key attached to message, the messages will be routed to corresponding partitions based on the [hashing scheme](#hashing-scheme), when using either `SinglePartition` or `RoundRobinPartition` mode.

Ordering guarantee | Description | Routing Mode and Key
:------------------|:------------|:------------
Per-key-partition  | All the messages with the same key will be in order and be placed in the same partition. | Use either `SinglePartition` or `RoundRobinPartition` mode, and Key is provided by each message.
Per-producer       | All the messages from the same producer will be in order. | Use `SinglePartition` mode, and no Key is provided for each message.

### Hashing scheme

Hashing scheme is an enum that represents sets of standard hashing functions available when choosing the partition to use for a particular message.

There are 2 types of standard hashing functions available: `JavaStringHash` and `Murmur3_32Hash`. 
The default hashing function for producers is `JavaStringHash`.
Please pay attention that `JavaStringHash` is not useful when producers can be from different multiple language clients. In this  case, it is recommended to use `Murmur3_32Hash`.

## Dead letter topics

Dead letter topics enable you to consume new messages when some messages cannot be consumed successfully by a consumer. In this mechanism, messages that fail to be consumed are stored in a separate topic, which is called dead letter topic. You can decide how to handle messages in the dead letter topic.

Dead letter topics depend on message re-delivery. Messages are redelivered either due to [acknowledgement timeout](#acknowledgement-timeout) or [negative acknowledgement](#negative-acknowledgement). If you are going to use negative acknowledgement on a message, make sure it is negatively acknowledged before the acknowledgement timeout. 

> **Note**    
> Currently, dead letter topics are enabled in the [Shared](#shared) and [Key_Shared](#key_shared) subscription modes.

## Retry letter topics

For many online business systems, a message is re-consumed due to an exception that occurs in the business logic processing. To configure the delay time for re-consuming the failed messages, you can configure the producer to send messages to both the business topic and the retry letter topic, and enable automatic retry on the consumer. When automatic retry is enabled on the consumer, a message is stored in the retry letter topic if the messages are not consumed, and therefore the consumer automatically consumes the failed messages from the retry letter topic after a specified delay time.

## Schema

Pulsar has a built-in schema registry that enables clients to upload data schemas on a per-topic basis. Those schemas dictate which data types are recognized as valid for that topic. Pulsar schema enables you to use language-specific types of data when constructing and handling messages from simple types like `string` to more complex application-specific types.

# Messages

Messages are the basic unit of Pulsar. The following table lists the components of messages.

Component | Description
:---------|:-------
Value / data payload | The data carried by the message. All Pulsar messages contain raw bytes, although message data can also conform to data schemas.
Key | Messages are optionally tagged with keys, which is useful for things like topic compaction.
Properties | An optional key/value map of user-defined properties.
Producer name | The name of the producer who produces the message. If you do not specify a producer name, the default name is used. 
Sequence ID | Each Pulsar message belongs to an ordered sequence on its topic. The sequence ID of the message is its order in that sequence.
Publish time | The timestamp of when the message is published. The timestamp is automatically applied by the producer.
Event time | An optional timestamp attached to a message by applications. For example, applications attach a timestamp on when the message is processed. If nothing is set to event time, the value is `0`. 
TypedMessageBuilder | It is used to construct a message. You can set message properties such as the message key, message value with `TypedMessageBuilder`. </br> When you set `TypedMessageBuilder`, set the key as a string. If you set the key as other types, for example, an AVRO object, the key is sent as bytes, and it is difficult to get the AVRO object back to the consumer.

## Message compression

You can compress messages published by producers during transportation. Pulsar currently supports the following types of compression:

* [LZ4](https://github.com/lz4/lz4)
* [ZLIB](https://zlib.net/)
* [ZSTD](https://facebook.github.io/zstd/)
* [SNAPPY](https://google.github.io/snappy/)

## Message retention and expiry

By default, Pulsar brokers immediately delete all messages that have been acknowledged by a consumer, and persistently store all unacknowledged messages in a message backlog.

All message retention and expiry is managed at the [namespace](#namespaces) level. The diagram below illustrates message retention and expiry.

![Message retention and expiry](../../image/retention-expiry.png)

### Message retention

Message retention enables you to store messages that have been acknowledged by a consumer. With message retention, shown at the top, a retention policy applied to all topics in a namespace dicates that some messages are durably stored in Pulsar even though they've already been acknowledged. Acknowledged messages that are not covered by the retention policy are deleted. Without a retention policy, *all* of the acknowledged messages would be deleted.

### Message expiry

Message expiry enables you to set a time to live (TTL) for messages that have not yet been acknowledged

With message expiry, shown at the bottom, some messages are deleted, even though they have not been acknowledged, because they've expired according to the TTL applied to the namespace (for example because a TTL of 5 minutes has been applied and the messages haven't been acknowledged but are 10 minutes old).

## Message deduplication

Message duplication occurs when a message is persisted by Pulsar more than once. Message deduplication is an optional Pulsar feature that prevents unnecessary message duplication by processing each message only once, even if the message is received more than once. Message deduplication is handled at the namespace level or the topic level. 

The following diagram illustrates what happens when message deduplication is disabled and enabled:

![Pulsar message deduplication](../../image/message-deduplication.png)

Message deduplication is disabled in the scenario shown at the top. Here, a producer publishes message 1 on a topic; the message reaches a Pulsar broker and is persisted to BookKeeper. The producer then sends message 1 again (in this case due to some retry logic), and the message is received by the broker and stored in BookKeeper again, which means that duplication has occurred.

In the second scenario at the bottom, the producer publishes message 1, which is received by the broker and persisted, as in the first scenario. When the producer attempts to publish the message again, however, the broker knows that it has already seen message 1 and thus does not persist the message.

### Producer idempotency

The other available approach to message deduplication is to ensure that each message is *only produced once*. This approach is typically called **producer idempotency**. The drawback of this approach is that it defers the work of message deduplication to the application. In Pulsar, this is handled at the broker level, so you do not need to modify your Pulsar client code. Instead, you only need to make administrative changes. For details, see Managing message deduplication.

### Deduplication and effectively-once semantics

Message deduplication makes Pulsar an ideal messaging system to be used in conjunction with Stream Processing Engines (SPEs) and other systems seeking to provide effectively-once processing semantics. Messaging systems that do not offer automatic message deduplication require the SPE or other system to guarantee deduplication, which means that strict message ordering comes at the cost of burdening the application with the responsibility of deduplication. With Pulsar, strict ordering guarantees come at no application-level cost.

## Delayed message delivery

Delayed message delivery enables you to consume a message later rather than immediately. In this mechanism, a message is stored in BookKeeper, `DelayedDeliveryTracker` maintains the time index(time -> messageId) in memory after being published to a broker, and it is delivered to a consumer once the specific delayed time is passed.  

Delayed message delivery only works in [Shared](#shared) subscription mode. In [Exclusive](#exclusive) and [Failover](#failover) subscription modes, the delayed message is dispatched immediately.

The diagram below illustrates the concept of delayed message delivery:

![Delayed Message Delivery](../../image/message_delay.png)

A broker saves a message without any check. When a consumer consumes a message, if the message is set to delay, then the message is added to `DelayedDeliveryTracker`. A subscription checks and gets timeout messages from `DelayedDeliveryTracker`.

# Producers

A producer is a process that attaches to a topic and publishes messages to a Pulsar broker.

## Send modes

Producers send messages to brokers synchronously (sync) or asynchronously (async).

| Mode       | Description |            
|:-----------|-----------|
| Sync send  | The producer waits for an acknowledgement from the broker after sending every message. If the acknowledgment is not received, the producer treats the sending operation as a failure.                                                                                                                                                                                    |
| Async send | The producer puts a message in a blocking queue and returns immediately. The client library sends the message to the broker in the background. If the queue is full (you can configure the maximum size), the producer is blocked or fails immediately when calling the API, depending on arguments passed to the producer. |

## Access mode

You can have different types of access modes on topics for producers.

|Access mode | Description
|---|---
`Shared`|Multiple producers can publish on a topic. <br><br>This is the **default** setting.
`Exclusive`|Only one producer can publish on a topic. <br><br>If there is already a producer connected, other producers trying to publish on this topic get errors immediately.<br><br>The “old” producer is evicted and a “new” producer is selected to be the next exclusive producer if the “old” producer experiences a network partition with the broker.
`WaitForExclusive`|If there is already a producer connected, the producer creation is pending (rather than timing out) until the producer gets the `Exclusive` access.<br><br>The producer that succeeds in becoming the exclusive one is treated as the leader. Consequently, if you want to implement the leader election scheme for your application, you can use this access mode.

> **Note**
>
> Once an application creates a producer with the `Exclusive` or `WaitForExclusive` access mode successfully, the instance of the application is guaranteed to be the **only one writer** on the topic. Other producers trying to produce on this topic get errors immediately or have to wait until they get the `Exclusive` access. 

# Consumers

A consumer is a process that attaches to a topic via a subscription and then receives messages.

A consumer sends a flow permit request to a broker to get messages. There is a queue at the consumer side to receive messages pushed from the broker. You can configure the queue size with the `receiverQueueSize` parameter. The default size is `1000`). Each time `consumer.receive()` is called, a message is dequeued from the buffer.  

## Receive modes

Messages are received from brokers either synchronously (sync) or asynchronously (async).

| Mode          | Description                                                                                                                                                                                                   |
|:--------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Sync receive  | A sync receive is blocked until a message is available.                                                                                                                                                  |
| Async receive | An async receive returns immediately with a future value—for example, a [`CompletableFuture`](http://www.baeldung.com/java-completablefuture) in Java—that completes once a new message is available. |

## Acknowledgement

When a consumer consumes a message successfully, the consumer sends an acknowledgement request to the broker. This message is permanently stored, and then deleted only after all the subscriptions have acknowledged it. If you want to store the message that has been acknowledged by a consumer, you need to configure the [message retention policy](#message-retention-and-expiry).

For a batch message, if batch index acknowledgement is enabled, the broker maintains the batch index acknowledgement status and tracks the acknowledgement status of each batch index to avoid dispatching acknowledged messages to the consumer. When all indexes of the batch message are acknowledged, the batch message is deleted. 

Messages are acknowledged either one by one or cumulatively. With cumulative acknowledgement, the consumer only needs to acknowledge the last message it received. All messages in the stream up to (and including) the provided message will not be re-delivered to that consumer.

Messages can be acknowledged in the following two ways:

- Messages are acknowledged individually. With individual acknowledgement, the consumer needs to acknowledge each message and send an acknowledgement request to the broker.
- Messages are acknowledged cumulatively. With cumulative acknowledgement, the consumer only needs to acknowledge the last message it received. All messages in the stream up to (and including) the provided message are not re-delivered to that consumer.

> **Note**
> 
> Cumulative acknowledgement cannot be used in the [Shared subscription mode](#shared), because the Shared subscription mode involves multiple consumers who have access to the same subscription. In the Shared subscription mode, messages are acknowledged individually.

### Acknowledgement timeout

If a message is not consumed successfully, and you want to trigger the broker to redeliver the message automatically, you can adopt the unacknowledged message automatic re-delivery mechanism. Client tracks the unacknowledged messages within the entire `acktimeout` time range, and sends a `redeliver unacknowledged messages` request to the broker automatically when the acknowledgement timeout is specified.

> **Note**
> 
> - If batching is enabled, other messages and the unacknowledged messages in the same batch are redelivered to the consumer.
> - Prefer negative acknowledgements over acknowledgement timeout. Negative acknowledgement controls the re-delivery of individual messages with more precision, and avoids invalid redeliveries when the message processing time exceeds the acknowledgement timeout.

## Negative acknowledgement

When a consumer does not consume a message successfully at a time, and wants to consume the message again, the consumer sends a negative acknowledgement to the broker, and then the broker redelivers the message.

Messages are negatively acknowledged one by one or cumulatively, which depends on the consumption subscription mode.

In the exclusive and failover subscription modes, consumers only negatively acknowledge the last message they receive.

In the shared and key_shared subscription modes, you can negatively acknowledge messages individually.

Be aware that negative acknowledgment on ordered subscription types, such as Exclusive, Failover and Key_Shared, can cause failed messages to arrive consumers out of the original order.

> **Note**
> 
> If batching is enabled, other messages and the negatively acknowledged messages in the same batch are redelivered to the consumer.

## Subscriptions

A subscription is a named configuration rule that determines how messages are delivered to consumers. Four subscription modes are available in Pulsar: [Exclusive](#exclusive), [Shared](#shared), [Failover](#failover), and [Key_Shared](#key_shared).

### Exclusive

In *exclusive* mode, only a single consumer is allowed to attach to the subscription. If multiple consumers subscribe to a topic using the same subscription, an error occurs.

In the diagram below, only **Consumer A-0** is allowed to consume messages.

> Exclusive mode is the default subscription mode.

![Exclusive subscriptions](../../image/pulsar-exclusive-subscriptions.png)

### Failover

In *failover* mode, multiple consumers can attach to the same subscription. A master consumer is picked for non-partitioned topics or each partition of partitioned topics and receives messages. When the master consumer disconnects, all (non-acknowledged and subsequent) messages are delivered to the next consumer in line.

For partitioned topics, the broker sorts consumers by priority level and lexicographical order of consumer name. Then, the broker tries to evenly assign topics to consumers with the highest priority level.

For non-partitioned topics, brokers pick consumers in the order they subscribe to the non partitioned topic.

In the diagram below, **Consumer-B-0** is the master consumer while **Consumer-B-1** would be the next consumer in line to receive messages if **Consumer-B-0** is disconnected.

![Failover subscriptions](../../image/pulsar-failover-subscriptions.png)

### Shared

In Shared or *round robin* mode, multiple consumers can attach to the same subscription. Messages are delivered in a round robin distribution across consumers, and any given message is delivered to only one consumer. When a consumer disconnects, all the messages that were sent to it and not acknowledged will be rescheduled for sending to the remaining consumers.

In the diagram below, **Consumer-C-1** and **Consumer-C-2** are able to subscribe to the topic, but **Consumer-C-3** and others could as well.

> **Limitations of Shared mode**
> 
> When using Shared mode, be aware that:
> * Message ordering is not guaranteed.
> * You cannot use cumulative acknowledgment with Shared mode.

![Shared subscriptions](../../image/pulsar-shared-subscriptions.png)

### Key_Shared

In *Key_Shared* mode, multiple consumers can attach to the same subscription. Messages are delivered in a distribution across consumers and messages with the same key or same ordering key are delivered to only one consumer. No matter how many times the message is re-delivered, it is delivered to the same consumer. When a consumer connected or disconnected, it will cause a served consumer change for some key of message.

> **Limitations of Key_Shared mode**
> When you use Key_Shared mode, be aware that:
> * You need to specify a key or `orderingKey` for messages.
> * You cannot use cumulative acknowledgment with Key_Shared mode.
> * Your producers should disable batching or use a key-based batch builder.

![Key_Shared subscriptions](../../image/pulsar-key-shared-subscriptions.png)

## Multi-topic subscriptions

When a consumer subscribes to a Pulsar topic, by default it subscribes to one specific topic, such as `persistent://public/default/my-topic`. However, Pulsar consumers can simultaneously subscribe to multiple topics. You can define a list of topics in two ways:

* On the basis of a [**regular expression**](https://en.wikipedia.org/wiki/Regular_expression) (regex), for example `persistent://public/default/finance-.*`
* Explicitly define a list of topics.

> **Note**
> 
> When subscribing to multiple topics by regex, all topics must be in the same [namespace](#namespaces).

When subscribing to multiple topics, the Pulsar client automatically makes a call to the API to discover the topics that match the regex pattern/list, and then subscribe to all of them. If any of the topics do not exist, the consumer auto-subscribes to them once the topics are created.

> **No ordering guarantees across multiple topics**
> 
> When a producer sends messages to a single topic, all messages are guaranteed to be read from that topic in the same order. However, these guarantees do not hold across multiple topics. So when a producer sends messages to multiple topics, the order in which messages are read from those topics is not guaranteed to be the same.

## Consumerless subscriptions and their corresponding modes

When a subscription has no consumers, its subscription mode is undefined. A subscription's mode is defined when a consumer connects to the subscription, and the mode can be changed by restarting all consumers with a different configuration.
