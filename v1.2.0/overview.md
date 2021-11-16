---
title: Overview
id: overview
category: overview
---

This section introduce the following content:
- [What is StreamNative Platform](#what-is-streamnative-platform)
- [Why use StreamNative Platform?](#why-use-streamnative-platform)
- [What is Apache Pulsar](#what-is-apache-pulsar)
- [StreamNative Platform vs Apache Pulsar: key features](#streamnative-platform-vs-apache-pulsar-key-features)

# What is StreamNative Platform

StreamNative Platform is a cloud-native messaging and event-streaming platform that enables you to build a real-time application and data infrastructure for both real-time and historical events. Founded by the original developers of [Apache Pulsar](https://pulsar.apache.org/en/) and [Apache BookKeeper](https://bookkeeper.apache.org/), [StreamNative](https://streamnative.io/) offers a complete, self-managed platform for continuously streaming data across your organization to power rich customer experiences and data-driven operations. You can deploy StreamNative Platform on-premise or in-cloud.

Powered by Apache Pulsar, StreamNative Platform makes it easy to build mission-critical messaging and streaming applications and real-time data pipelines by integrating data from multiple sources into a single, central messaging and event streaming platform for your company. StreamNative Platform lets you focus on how to maximize business value from real-time data rather than worrying about the underlying mechanisms such as how data is messaged between various systems and how data is stored reliably for processing. 

Specifically, StreamNative Platform simplifies:

- Publishing-and-consuming messages using popular messaging protocols (including Apache Pulsar and Apache Kafka)
- Connecting various data sources to Pulsar
- Building real-time applications with Pulsar
- Integrating your data processing infrastructure with Pulsar
- Securing, monitoring, and managing your Pulsar deployments

Most importantly, StreamNative Platform enables you to:

- Deploy and manage Pulsar in your private cloud environment
- Deploy and manage your platform as a cloud-native system on Kubernetes
- Monitor the health and performance of Pulsar clusters using dedicated tools such as [Pulsar detector](/operator-guides/configure/backup-restore/detector.md) and StreamNative console.

![](../../image/sn-platform-architecture.png)

Out of the box, the latest features of Pulsar and the add-on StreamNative features are shipped in each StreamNative Platform release.

# Why use StreamNative Platform?
With StreamNative Platform, you can enjoy the following benefits:

- **Infinite productivity**: Empower more developers to use Pulsar and improve the efficiency to build event streaming applications.

- **Efficient operations at scale**: Minimize operational complexity and ensure high performance and scalability as event streaming grows in your organization.

- **Production-stage**: Implement event streaming in production with enterprise-level functionalities.

- **Flexible deployment**: Deploy on-premises, in private or public cloud, from bare-metal to Kubernetes, or leverage a fully managed cloud service with StreamNative Platform.

- **Committer-driven expertise**: Leverage the rich experience of Pulsar experts and committers to support you throughout the application development lifecycle.

# What is Apache Pulsar

At the core of StreamNative Platform is [Apache Pulsar](https://pulsar.apache.org), an open source distributed cloud-native messaging and streaming platform. 

The key capabilities of Apache Pulsar are:

- Publish and subscribe to event streams at scale
- Store infinite event streams in a reliable and scalable way with [Apache BookKeeper](https://bookkeeper.apache.org) and tiered storage
- Process and query event streams using serverless functions

Apache Pulsar has received global adoption from [major technology companies](https://pulsar.apache.org/en/powered-by/) such
as Verizon Media, Narvar, Overstock, Nutanix, Yahoo! JAPAN, Tencent, OVHCloud, and Clever Cloud, who rely on its ability to deliver on performance, scalability, and resiliency. Pulsar adoption is  driven by the market’s increased demand for real-time, data-enabled technologies, from asynchronous applications to core business applications to ETL.

The key components of Pulsar are:

- [Brokers](https://pulsar.apache.org/docs/en/concepts-architecture-overview/#brokers)

    Brokers form the messaging and serving tier for Pulsar.

- [Bookies](https://bookkeeper.apache.org)

    Bookies form the storage tier for Pulsar.

- [Functions Workers](https://pulsar.apache.org/docs/en/functions-overview/)

    Functions Workers form the serverless processing tier for Pulsar.

- [Proxy](https://pulsar.apache.org/docs/en/next/administration-proxy/)

    Pulsar proxy is an optional gateway that you can run in front of the brokers in a Pulsar cluster.

- [ZooKeeper](https://zookeeper.apache.org/)

    Pulsar relies on ZooKeeper for a wide variety of configuration-related and coordination-related tasks.

- Client APIs
    - [Producer](https://pulsar.apache.org/docs/en/concepts-messaging/#producers) API: allows an application to publish a stream of events to one or more Pulsar topics.
    - [Consumer](https://pulsar.apache.org/docs/en/concepts-messaging/#consumers) API: allows an application to subscribe to one or more topics and receive the stream of events produced to them.
    - [Functions](https://pulsar.apache.org/docs/en/next/functions-overview/) API: allows an application to create, deploy, and manage serverless functions that consume events from one or more Pulsar topics, apply a user-supplied processing logic to each event, and publish the results of the computation to another topic.
    - [Connector](https://pulsar.apache.org/docs/en/next/io-overview/) API: allows an application to create, deploy, and manage connectors that interact with external systems, such as Apache Cassandra, Aerospike, and many others.
    - [Admin API](https://pulsar.apache.org/docs/en/admin-api-overview/): allows an application to manage Pulsar resources (for example, namespaces, topics, functions, connectors, and so on) in a programmable way.

# StreamNative Platform vs Apache Pulsar: key features

Each release of StreamNative Platform includes the latest release of Pulsar as well as additional tools and services that make it easier to build and manage a cloud-native messaging and event-streaming platform. StreamNative Platform delivers both community and commercially licensed features that complement and enhance your Pulsar deployment.

The following table lists features supported in Apache Pulsar and StreamNative Platform.

| Features | Apache Pulsar | StreamNative Platform |
|----------|---------------|-----------------------|
|Pulsar Functions| Yes     | Yes                   |
|Pulsar IO       | Yes     | Yes                   |
|Geo-replication | Yes     | Yes                   |
|Multi-tenancy   | Yes     | Yes                   |
|Transactions    | Yes     | Yes                   |
|Pulsar Manager  | Yes     | Yes                   |
|StreamNative Console: upgraded Pulsar Manager|    | Yes    |
|Monitoring Stack: Integrated Dashboard and alerting rules for Pulsar based on Prometheus/Grafana/Alertmanager|    | Yes    |
|Multi-Cluster Management: Manage multiple Pulsar clusters at the same time| | Yes                   |
|[pulsarctl](https://docs.streamnative.io/pulsarctl/v2.7.0.7/): a CLI tool to manage Pulsar clusters       |         | Yes                   |
|[Function Mesh](/concepts/functionmesh-concepts.md): a serverless framework to run Pulsar Functions and Connectors on Kubernetes   |         | Yes                   |
|[KoP](/concepts/kop-concepts.md): enables you to migrate your existing Kafka applications and services to Pulsar without modifying the code |   | Yes  |
|[Backup and restore](/operator-guides/configure/backup-restore-metadata-tool.md): a tool to backup Pulsar cluster's metadata, and restore cluster to a backup point |         | Yes         |
|[Pulsar detector](/operator-guides/configure/detector.md): a new tool to monitor the health of Pulsar | | Yes  |

[Get started](/quickstart.md) with StreamNative Platform.