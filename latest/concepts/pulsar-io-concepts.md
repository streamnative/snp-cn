---
title: Pulsar connectors
id: pulsar-io-concepts
category: concepts
---

Messaging systems are most powerful when you can easily use them with external systems like databases and other messaging systems.

**Pulsar IO connectors** enable you to easily create, deploy, and manage connectors that interact with external systems, such as [Apache Cassandra](https://cassandra.apache.org) and [Aerospike](https://www.aerospike.com).

A **connector** is a processor that ingresses or egresses events from and to **streams**. Pulsar IO connectors consists **source** and **sink**. This diagram illustrates the relationship between source, Pulsar, and sink.

![Pulsar IO connector](../../image/pulsar-io.png)

## Source

A source is an application that ingests data from an external system into Pulsar. Common sources include other messaging systems and firehose-style data pipeline APIs.

For a complete list of source connectors, see [source connector](https://hub.streamnative.io/).

## Sink

A sink is an application that egresses data from Pulsar to an external system. Common sinks include other messaging systems, as well as SQL and NoSQL databases.

For a complete list of sink connectors, see [sink connector](https://hub.streamnative.io/).

## Processing guarantee

Processing guarantees are used to handle errors when writing messages to Pulsar topics. Pulsar connectors and Functions use the same processing guarantees.

| Delivery semantics | Description |
| ------------------| ------- |
| `at-most-once` | Each message sent to a connector is processed at most once. Therefore, there is a chance that the message is not processed. |
| `at-least-once`  | Each message sent to a connector is processed more than once. Therefore, there is a chance that the message is processed redundantly. |
| `effectively-once` | Each message sent to a connectors is processed only once and has one output associated with it.|

Processing guarantees for connectors do not just rely on Pulsar guarantee but also **relate to external systems**, that is, **the implementation of source and sink**. For the source, Pulsar ensures that writing messages to Pulsar topics respects to the processing guarantees. It is under Pulsar's control. For the sink, the processing guarantees rely on the sink implementation. If the sink implementation does not handle retries in an idempotent way, the sink does not respect to the processing guarantees.