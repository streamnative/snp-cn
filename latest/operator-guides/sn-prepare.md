---
title: Prepare Kubernetes cluster
id: sn-prepare
category: operator-guides
---

This document describes the required tasks to prepare your Kubernetes cluster for deploying StreamNative Platform. To perform these tasks, you must have the  appropriate Kubernetes cluster-level permissions.

# Create a Kubernetes namespace for StreamNative Platform

1. Create a Kubernetes namespace to deploy StreamNative Platform into:

    ```
    kubectl create namespace KUBERNETES_NAMESPACE 
    ```

2. (Optional) Set the new namespace as the current namespace.

    ```
    kubectl config set-context --current --namespace=KUBERNETES_NAMESPACE
    ```

After setting the new namespace as the current namespace, you do not need to add the `--namespace` flag in the subsequent `kubectl` commands as the commands assume the current namespace.