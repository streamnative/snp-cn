---
title: Scale Pulsar clusters
id: scale-cluster
category: operator-guides
---

This document describes how to resize the Pulsar cluster based on the demand of your workload. When demand is high, you can  add nodes to scale up the cluster. When demand is low, you can scale the Pulsar cluster back down.

You can resize a Pulsar cluster by increasing or decreasing the number of Pulsar components, including the Pulsar brokers, BookKeeper, ZooKeeper, and Pulsar Proxy. In most cases, you only need to add or remove the Pulsar broker and BookKeeper nodes.

# Scale up Pulsar clusters

It takes a few steps to add new nodes to a Pulsar cluster:

1. Define the configuration for each of the new nodes.
2. Provision storage, networking, and compute resources to the nodes.
3. Update the Pulsar cluster with the defined configurations and provisioned resources.
4. Reassign traffic across the cluster so that the new brokers share the load.
You should observe that the cluster’s overall performance improves.

> **Note**
> 
> By default, StreamNative Platform supports automatic load balancing.

To scale up the Pulsar cluster, follow these steps.

1. Increase the number of nodes by setting the `replicaCount` option with a greater value in the Pulsar CRD file.

    ```
    broker:
    component: broker
    replicaCount: 9 # In this example, scaling up from 5
    ```

2. Update the Pulsar cluster with the new settings.

    ```
    helm upgrade -f PULSAR_CRD_FILE PULSAR_CLUSTER PULSAR_CHART/
    ```

# Scale down Pulsar clusters

To scale down the Pulsar cluster, follow these steps.

1. Decrease the number of nodes by setting the `replicaCount` option with a greater value in the Pulsar CRD file.

    ```
    broker:
    component: broker
    replicaCount: 3 # In this example, scaling down from 5
    ```

2. Update the Pulsar cluster with the new settings.

    ```
    helm upgrade -f PULSAR_CRD_FILE PULSAR_CLUSTER PULSAR_CHART/
    ```

3. Wait an appropriate time for traffic to be replicated to other existing nodes. The time varies on the cluster size and the number of topics.