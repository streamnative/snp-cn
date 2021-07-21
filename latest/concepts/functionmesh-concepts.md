---
title: Function Mesh
id: functionmesh-concepts
category: concepts
---

Function Mesh is a Kubernetes operator that enables users to run Pulsar Functions and Pulsar connectors natively on Kubernetes, unlocking the full power of Kubernetes’ features and resources.

By providing a serverless framework that enables users to organize a collection of Pulsar Functions and connectors, Function Mesh simplifies the process of creating complex streaming jobs. Function Mesh is a valuable tool for users who are seeking cloud-native serverless streaming solutions.

# Concepts

Function Mesh enables you to build event streaming applications leveraging your familiarity with [Apache Pulsar](https://pulsar.apache.org/) and modern stream processing technologies. Three concepts are foundational to build an application: _streams_, _functions_, and _connectors_.

## Stream

A stream is a partitioned, immutable, append-only sequence for events that represents a series of historical facts. For example, the events of a stream could model a sequence of financial transactions, like "Jack sent $100 to Alice", followed by "Alice sent $50 to Bob". 

A stream is used for connecting **functions** and **connectors**. 

Topics in a messaging system are usually used for presenting the streams. The streams in Function Mesh are implemented by using topics in Apache Pulsar.

Figure 1 illustrates a **stream**.

![Stream](../../image/stream.png)

Figure 1. A stream that connects a **function** and a **connector**

## Function

A **function** is a lightweight event processor that consumes messages from one or more input streams, applies a user-supplied processing logic to one or multiple messages, and produces the results of the processing logic to another stream.

The **functions** in Function Mesh are implemented based on Pulsar Functions.

Figure 2 illustrates a **function**.

![Function](../../image/function.png)

Figure 2. A function that consumes messages from one or more input streams and produces the results to another output stream

## Connector

A **connector** is a processor that ingresses or egresses events from and to **streams**. There are two types of connectors in Function Mesh:

- *Source Connector (aka Source)*: a processor that ingests events from an external data system into a **stream**.
- *Sink Connector (aka Sink)*: a processor that egresses events from **streams** to an external data system.

The **connectors** in Function Mesh are implemented based on Pulsar IO connectors. The available Pulsar I/O connectors can be found at [StreamNative Hub](https://hub.streamnative.io/).

## FunctionMesh

A **FunctionMesh** (aka Mesh) is a collection of **functions** and **connectors** connected by **streams** that are orchestrated together for achieving powerful stream processing logics.

All the **functions** and **connectors** in a **FunctionMesh** share the same lifecycle. They are started when a **FunctionMesh** is created and terminated when the mesh is destroyed. All the event processors are long running processes. They are auto-scaled based on the workload by the Function Mesh controller.

A **FunctionMesh** can be either a Directed Acyclic Graph (DAG) or a cyclic graph of functions and/or connectors connected with streams. Figure 5 illustrates a **FunctionMesh** of a Debezium source connector, an enrichement function, and an Elastic sink connector.

![Function Mesh](../../image/function-mesh.png)

Figure 5. A FunctionMesh is a collection of functions and/or connectors connected with streams

# API

Function Mesh APIs build on existing Kubernetes APIs, so that Function Mesh resources are compatible with other Kubernetes-native resources, and can be managed by cluster administrators using existing Kubernetes tools.

Common languages and frameworks that include Kubernetes-friendly tooling work smoothly with Function Mesh to reduce the time spent solving common deployment issues.

The foundational concepts described above are delivered as Kubernetes Custom Resource Definitions (CRDs), which can be configured by a cluster administrator for developing event streaming applications.

The available Function Mesh CRDs are:

- [**Function**](/user-guides/admin/work-with-functions/function-functionmesh.md#function-crd): The `Function` resource automatically manages the whole lifecycle of a Pulsar Function.
- [**Source**](/user-guides/admin/work-with-connectors/connector-functionmesh/connector-crd.md#source-crd-configurations): The `Source` resource automatically manages the whole lifecyle of a Pulsar Source connector.
- [**Sink**](/user-guides/admin/work-with-connectors/connector-functionmesh/connector-crd.md#sink-crd-configurations): The `Sink` resource automatically manages the whole lifecycle of a Pulsar Sink connector.
- **FunctionMesh**: The `FunctionMesh` resource automatically manages the whole lifecycle of your event streaming application. It controls the creation of other objects to ensure that the **functions** and **connectors** defined in your mesh are running and they are connected via the defined **streams**. 

# How Function Mesh works

Function Mesh is designed to run Pulsar Functions and connectors natively on Kubernetes. Instead of using the pulsar-admin CLI tool and sending function requests to Pulsar clusters, you can use `kubectl` to submit a Function Mesh CRD directly to Kubernetes clusters. The corresponding Mesh operator installed inside Kubernetes launches functions and connectors individually, organize scheduling, and load balance them together.

Function Mesh works in this way, and both the metadata and running state are stored directly in Kubernetes.

1. Create Pulsar Functions, source, sink, and Function Mesh `.yaml` files as CRDs.

2. The Functions controller receives CRDs from Kubernetes service and then schedules the individual pod for running function/connector instances.

3. The running function/connector instances run with the configured Pulsar cluster and topics.

The following diagram illustrates how Function Mesh works.

![Function Mesh](../../image/function-mesh-overview.png)
