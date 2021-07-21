---
title: Deploy StreamNative Platform on native Kubernetes clusters
id: sn-deploy
category: operator-guides
---
After creating a Kubernetes cluster, you can deploy the StreamNative Platform.

# Install StreamNative Platform

This section describes how to install StreamNative Platform in online and offline mode.

## Install StreamNative Platform with Internet access

This section describes how to install StreamNative Platform online.

1. Install the StreamNative repositories.

    ```
    helm repo add streamnative https://charts.streamnative.io
    helm repo add banzaicloud-stable https://kubernetes-charts.banzaicloud.com
    helm repo add jetstack https://charts.jetstack.io
    helm repo update
    ```

2. Create a Kubernetes namespace and add the environment variable for the Kubernetes namespace.

    ```
    kubectl create namespace KUBERNETES_NAMESPACE
    export NAMESPACE=KUBERNETES_NAMESPACE
    ```

3. Install the Vault operator.
   
    The Vault operator creates and maintains highly-available Vault clusters on Kubernetes, allowing users to easily deploy and manage Vault clusters for their applications.

    ```
    helm upgrade --install vault-operator banzaicloud-stable/vault-operator -n $NAMESPACE
    ```

4. Install the cert-manager.

    The cert-manager is a native [Kubernetes](https://kubernetes.io/) certificate management controller. It helps issue certificates from [HashiCorp Vault](https://www.vaultproject.io/). The cert-manager ensures that certificates are valid and up-to-date, and attempts to renew certificates at a configured time before expiry.

    The cert-manager requires a number of CRD resources to be installed into your cluster as part of installation. To automatically install and manage the CRDs as part of your Helm release, you must add the `--set installCRDs=true` flag to your Helm installation command.

    ```
    helm upgrade --install cert-manager jetstack/cert-manager -n $NAMESPACE --set installCRDs=true
    ```

5. Install the Pulsar operator.

    ```
    helm upgrade --install pulsar-operator streamnative/pulsar-operator -n $NAMESPACE
    ```

6. Install the FunctionMesh operator.

   [Function Mesh](/concepts/functionmesh-concepts.md) is a serverless and purpose-built framework for orchestrating multiple [Pulsar Functions](/concepts/pulsar-function-concepts.md) and [Pulsar IO connectors](/concepts/pulsar-io-concepts.md) for stream processing applications.

    ```
    helm upgrade --install function-mesh streamnative/function-mesh-operator -n $NAMESPACE 
    ```

7. Set the environment variable `PULSAR_CHART`.

    ```
    export PULSAR_CHART=streamnative/sn-platform
    ```

## Install StreamNative Platform without Internet access

This section describes how to install StreamNative Platform offline.

> **Note**
> 
> When installing StreamNative Platform without Internet access, you need to preload required Docker images. For details about how to preload Docker images, see [preload Docker images](/operator-guides/sn-plan.md#preload-docker-images).

### Install StreamNative Platform using `install.sh` scripts

To install StreamNative Platform using scripts, follow these steps.

1. Use the `wget` command to download the `.tar.gz` package of StreamNative Platform.

    ```
    wget https://downloads-streamnative-cloud.oss-cn-beijing.aliyuncs.com/snpe/streamnative_platform-v1.0.0.tar.gz
    ```

2. Extract the package and use the `cd` command to switch to the target directory.

    ```
    tar zxvf streamnative_platform-v1.0.0.tar.gz
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

5. Set the environment variable `PULSAR_CHART`.

    ```
    export PULSAR_CHART=[PATH_TO_PULSAR_CHART]
    ```

### Install StreamNative Platform manually

To install StreamNative Platform manually, follow these steps.

1. Create a Kubernetes namespace.

    ```
    kubectl create namespace KUBERNETES_NAMESPACE
    ```

2. Install the Vault operator.

    ```
    helm upgrade --install vault-operator VAULT_OPERATOR_NAME/ -n KUBERNETES_NAMESPACE
    ```

3. Install the cert-manager.

    ```
    helm upgrade --install cert-manager CERT_MANAGER_NAME/ -n KUBERNETES_NAMESPACE --set installCRDs=true
    ```

4. Install the Pulsar operator.

   1. Define a YAML file and apply the CRDs to deploy the Pulsar resources.

       ```
       kubectl apply -f /path/to/pulsar-operator/file.yaml -n KUBERNETES_NAMESPACE 
       ```

   2. Install the Pulsar operator.

       ```
       helm upgrade --install pulsar-operators PULSAR_OPERATOR_NAME/ -n KUBERNETES_NAMESPACE
       ```

5. Install the FunctionMesh operator.

    1. Define a YAML file and apply the CRDs to deploy the Pulsar resources.

        ```
        kubectl apply -f /path/to/functionmesh-operator/file.yaml -n KUBERNETES_NAMESPACE
        ```

    2. Install the FunctionMesh operator.

        ```
        helm upgrade --install function-mesh FUNCTIONMESH_OPERATOR_NAME/ -n KUBERNETES_NAMESPACE
        ```

6. Set the environment variable `PULSAR_CHART`.

    ```
    export PULSAR_CHART=[PATH_TO_PULSAR_CHART]
    ```

# Deploy Pulsar clusters

To deploy a Pulsar cluster, follow these steps.

1. Add the environment variables for the Pulsar Chart directory, Pulsar cluster name, and Kubernetes namespace.

    ```
    # If you use offline version, export PULSAR_CHART first.
    export PULSAR_CHART=/path/to/pulsar/chart
    # Define your pulsar cluster name
    export RELEASE_NAME=PULSAR_CLUSTER
    # Define the k8s namespace to install the pulsar cluster
    export NAMESPACE=KUBERNETES_NAMESPACE
    ```

2. Create a Kubernetes namespace for your Pulsar cluster.

    ```
    kubectl create namespace $NAMESPACE
    ```

3. Define a Pulsar cluster configuration file.

    [Here](https://raw.githubusercontent.com/streamnative/examples/master/platform/values_cluster.yaml) is an example of the YAML file used for configuring the Pulsar cluster.

4. Apply the YAML file to create a Pulsar cluster.

    ```
    helm install -f /path/to/pulsar/file.yaml $RELEASE_NAME $PULSAR_CHART --set initialize=true --set namespace=$NAMESPACE
    ```

5. (Optional) Update your Pulsar cluster.

    You can update your Pulsar cluster by updating the YAML file and then execute the `helm upgrade` command.

    ```
    helm upgrade -f /path/to/pulsar/file.yaml $RELEASE_NAME $PULSAR_CHART
    ```

# Uninstallation

This section describes how to uninstall Pulsar cluster and StreamNative Platform.

- Execute the following command to uninstall the Pulsar cluster.

    ```
    helm uninstall $PULSAR_CLUSTER
    ```

- Execute the following command to uninstall the StreamNative Platform.

    - If you install StreamNative Platform in an offline way, you can run the following command.

        ```
        sh [StreamNative_HOME]/uninstall.sh
        ```

    - If you install StreamNative Platform in an online way, you can run the following commands.

        ```
        export NAMESPACE=KUBERNETES_NAMESPACE
        helm uninstall vault-operator -n $NAMESPACE
        helm uninstall cert-manager -n $NAMESPACE
        helm uninstall pulsar-operator -n $NAMESPACE
        helm uninstall function-mesh -n $NAMESPACE
        ```
