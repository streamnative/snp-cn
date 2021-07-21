---
title: Upgrade StreamNative Platform
id: sn-upgrade
category: operator-guides
---

This document describes how to upgrade StreamNative Platform.

# Upgrade StreamNative Platform with Internet access

To upgrade StreamNative Platform with Internet access, follow these steps.

1. Get the latest version of the Vault operator, cert-manager operator, Pulsar operator, and FunctionMesh operators.

    ```
    helm repo update
    ```

2. Use the `helm upgrade` command to upgrade these operators one by one.

- Vault operator

    ```
    helm upgrade --install vault-operator banzaicloud-stable/vault-operator -n K8S_NAMESPACE_NAME
    ```

- cert-manager operator

    ```
    helm upgrade --install cert-manager jetstack/cert-manager -n K8S_NAMESPACE_NAME --set installCRDs=true
    ```	

- Pulsar operator

    ```
    helm upgrade --install pulsar-operator streamnative/pulsar-operator -n $K8S_NAMESPACE_NAME
    ```

- FunctionMesh operator
 
    ```
    helm upgrade --install function-mesh streamnative/function-mesh-operator -n $K8S_NAMESPACE_NAME
    ```

# Upgrade streamNative Platform without Internet access

When a new version of StreamNative Platform is available, you can upgrade the StreamNative Platform.

1. Use the `wget` command to download the latest StreamNative Platform installation package.

2. Extract the package and use the `cd` command to switch to the target directory.

    ```
    tar zxvf streamnative_platform-vx.y.z.tar.gz
    cd streamnative-platform
    ```

3. Add the environment variable for the Kubernetes namespace of StreamNative Platform.

    ```
    export NAMESPACE=KUBERNETES_NAMESPACE
    ```

4. Install StreamNative Platform.

    ```
    sh install.sh
    ```
