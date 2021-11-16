---
title: Plan for StreamNative Platform deployment
id: sn-plan
category: operator-guides
---

This document describes recommended options for deploying StreamNative Platform.

# Deployment workflow

In general, the workflow for manually installing StreamNative Platform and deploying a Pulsar cluster consists of these steps:

> **Note**
>
> Review the prerequisites for StreamNative Platform deployment. For details, see [prerequisites](#prerequisites).

1. Prepare your Kubernetes environment.

2. Configure StreamNative Platform.

3. Deploy StreamNative Platform.

4. Manage StreamNative Platform.

5. Monitor StreamNative Platform.

# Prerequisites

This section lists minimum requirements for installing StreamNative Platform.

## Software requirements

This table lists software requirements for installing StreamNative Platform.

<table>
  <tr>
   <td>Software name
   </td>
   <td>Version
   </td>
  </tr>
  <tr>
   <td>Docker
   </td>
   <td>Docker CE v18.09.6
   </td>
  </tr>
  <tr>
   <td>Kubernetes
   </td>
   <td>v1.16 or higher
   </td>
  </tr>
  <tr>
   <td>CentOS
   </td>
   <td>v7.6 and kernel v3.10.0-957 or higher
   </td>
  </tr>
</table>

## Hardware requirements

This table lists the minimum hardware requirements on a 3-node Kubernetes cluster for test purposes. You can customize your Kubernetes node configuration based on your production environment requirements.

| Item | Value |
| --- | --- |
| CPU | 4 cores |
| Memory | 16 GB|
| Disk | 50 GB |

## Required tools

* [kubectl](https://kubernetes.io/docs/tasks/tools/#kubectl) v1.16 or higher.
* [Helm](https://helm.sh/docs/intro/install/) v3.0.0 or higher.
* [pulsarctl](https://github.com/streamnative/pulsarctl#install) v2.8.0 CLI tool.

## Configure local PVs and storage classes

If you deploy a local Kubernetes cluster, you need to configure the local PVs and storage classes for persisting data to your local storage. For details, see [local PVs and storage classes](/operator-guides/configure/storage.md#local-pvs-and-storage-classes).

# Docker registry

You can get all the Docker images required for deploying StreamNative Platform from a Docker registry and then deploy it on to your Kubernetes cluster. 

> **Note**
>
> By default, all publicly-available Docker images are hosted on Docker Hub from the `streamnative` repositories.

If you choose to use your own Docker registry and repositories, you must pull the images from the `streamnative` repositories and upload them to your Docker registry repositories.

## Preload Docker images

If you have limited access to the Internet, you can preload the docker images before installing StreamNative Platform. A Docker image contains application code, libraries, tools, dependencies and other files needed to make an application run. When you run an image, it can become one or many instances of a container. For supported Docker images and their download links, see [here](/operator-guides/operator-reference/sn-version-matrix.md).

You can use the [`docker load`](https://docs.docker.com/engine/reference/commandline/load/) command to upload these images to your Docker registry repositories.

# Storage

You must provide dynamic persistent storage for all StreamNative Platform components. If you deploy a local Kubernetes cluster, you need to configure the local PVs and storage class for persisting data to your local storage. For details, see [configure storage](/operator-guides/configure/storage.md).

# Security

This section includes Kubernetes security and StreamNative Platform security.

## Kubernetes security

With Kubernetes Role-based access control (RBAC) and namespaces, you can deploy StreamNative Platform in one of two ways:

- (Recommended) provide access to provision and manage StreamNative Platform resources in one specific namespace.

- Provide access to provision and manage StreamNative Platform resources across all namespaces in the Kubernetes cluster.

## StreamNative Platform security

StreamNative Platform supports the following processes to enforce security.

- Authentication

- Authorization

- Network encryption

- Configuration Secrets

For production deployments, StreamNative Platform recommends the following security mechanisms:

- Enable SSL/Plain for Kafka client authentication.

- Enable JSON Web Token (JWT) authentication for Pulsar client authentication.

- Enable TLS for network encryption for both internal traffic between StreamNative Platform components and external traffic from clients to StreamNative Platform components.

# Networking

StreamNative Platform provides two ways for client applications to access StreamNative Platform components that are deployed in the same Kubernetes cluster or in a different cluster.

- If StreamNative Platform components are deployed in the same Kubernetes cluster, you can access them through the `ClusterIP` mode.

- If StreamNative Platform components are deployed outside the Kubernetes cluster, you can access them through the `NodePort` or `LoadBalancer` mode.

For details, see [configure networking](/operator-guides/configure/networking.md).