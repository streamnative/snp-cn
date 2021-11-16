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

### Install StreamNative Platform using original images within operators

This section describes how to install StreamNative Platform using original images within the operators.

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

### Install StreamNative Platform using customized images

This section describes how to install StreamNative Platform using customized images.

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

   1. Inspect the chart used for installing the Vault operator and save the contents of `values.yaml` file as a local YAML file.

      This example saves the `values.yaml` file as `value_vault-operator.yaml` file.

        ```
        helm inspect values banzaicloud-stable/vault-operator  > value_vault-operator.yaml
        ```

   2. Update the image setting in the `value_vault-operator.yaml` file.

      You can specify the location and version of the image using the `image.bankVaultsRepository` and `image.tag` parameters. This example specifies the Vault image published to the [Docker Hub](https://hub.docker.com/) by Banzai Cloud.

      ```yaml
      image:
        bankVaultsRepository: banzaicloud/bank-vaults
        repository: banzaicloud/vault-operator
        # tag: ""
        pullPolicy: IfNotPresent
        imagePullSecrets: []  # global.imagePullSecrets is also supported
      ```

    3. Install the Vault operator with the customized image.

        ```
        helm upgrade --install -f value_vault-operator.yaml vault-operator banzaicloud-stable/vault-operator -n $NAMESPACE
        ```

4. Install the cert-manager.

    The cert-manager is a native [Kubernetes](https://kubernetes.io/) certificate management controller. It helps issue certificates from [HashiCorp Vault](https://www.vaultproject.io/). The cert-manager ensures that certificates are valid and up-to-date, and attempts to renew certificates at a configured time before expiry.

    The cert-manager requires a number of CRD resources to be installed into your cluster as part of installation. To automatically install and manage the CRDs as part of your Helm release, you must add the `--set installCRDs=true` flag to your Helm installation command.

    1. Inspect the chart used for installing the cert-manager and save the contents of `values.yaml` file as a local YAML file.

        This example saves the `values.yaml` file as `value_cert-manager.yaml` file.

        ```
        helm inspect values jetstack/cert-manager  > value_cert-manager.yaml
        ```

   2. Update the image settings in the `value_cert-manager.yaml` file.

        You can specify the location and version of images using the `image.repository` and `image.tag` parameters. StreamNative mirrors the cert-manager images to the [Docker Hub](https://hub.docker.com/). This example specifies the cert-manager images published to the [Docker Hub](https://hub.docker.com/) by StreamNative.

        ```yaml
        image:
          repository: streamnative/cert-manager-controller
          # You can manage a registry with
          # registry: quay.io
          # repository: jetstack/cert-manager-controller

          # Override the image tag to deploy by setting this variable.
          # If no value is set, the chart's appVersion will be used.
          tag: v1.4.0

          # Setting a digest will override any tag
          # digest: sha256:0e072dddd1f7f8fc8909a2ca6f65e76c5f0d2fcfb8be47935ae3457e8bbceb20
          pullPolicy: IfNotPresent

          image:
            repository: streamnative/cert-manager-webhook
            # You can manage a registry with
            # registry: quay.io
            # repository: jetstack/cert-manager-webhook

            # Override the image tag to deploy by setting this variable.
            # If no value is set, the chart's appVersion will be used.
            tag: v1.4.0

            # Setting a digest will override any tag
            # digest: sha256:0e072dddd1f7f8fc8909a2ca6f65e76c5f0d2fcfb8be47935ae3457e8bbceb20

            pullPolicy: IfNotPresent

          image:
            repository: streamnative/cert-manager-cainjector
            # You can manage a registry with
            # registry: quay.io
            # repository: jetstack/cert-manager-cainjector

            # Override the image tag to deploy by setting this variable.
            # If no value is set, the chart's appVersion will be used.
            tag: v1.4.0

            # Setting a digest will override any tag
            # digest: sha256:0e072dddd1f7f8fc8909a2ca6f65e76c5f0d2fcfb8be47935ae3457e8bbceb20

            pullPolicy: IfNotPresent
        ```

    3. Install the cert-manager with the customized images.

        ```
        helm upgrade --install -f value_cert-manager.yaml  cert-manager jetstack/cert-manager -n $NAMESPACE --set installCRDs=true
        ```

5. Install the Pulsar operator.

    1. Inspect the chart used for installing the Pulsar operator and save the contents of `values.yaml` file as a local YAML file.

        This example saves the `values.yaml` file as `value_pulsar-operator.yaml` file.

        ```
        helm inspect values streamnative/pulsar-operator  > value_pulsar-operator.yaml
        ```

    2. Update the image setting in the `value_pulsar-operator.yaml` file.

        You can specify the location and version of images using the `images.repository` and `images.tag` parameters. This example specifies the images for ZooKeeper, BookKeeper, and Pulsar published to the [Docker Hub](https://hub.docker.com/) by StreamNative.

        ```yaml
        images:
          zookeeper:
            repository: streamnative/zookeeper-operator
            tag: v0.7.0-rc5
            pullPolicy: IfNotPresent
          bookkeeper:
            repository: streamnative/bookkeeper-operator
            tag: v0.6.12
            pullPolicy: IfNotPresent
          pulsar:
            repository: streamnative/pulsar-operator
            tag: 0.7.0-rc6
            pullPolicy: IfNotPresent
        ```
        
   3. Install the Pulsar operator with the customized YAML file.

        ```
        helm upgrade --install pulsar-operator -f value_pulsar-operator.yaml streamnative/pulsar-operator -n $NAMESPACE
        ```

6. Install the FunctionMesh operator.

   [Function Mesh](/concepts/functionmesh-concepts.md) is a serverless and purpose-built framework for orchestrating multiple [Pulsar Functions](/concepts/pulsar-function-concepts.md) and [Pulsar IO connectors](/concepts/pulsar-io-concepts.md) for stream processing applications.

    1. Inspect the chart used for installing the FunctionMesh operator and save the contents of `values.yaml` file as a local YAML file.

        This example saves the `values.yaml` file as `value_function-mesh-operator.yaml` file.

        ```
        helm inspect values streamnative/function-mesh-operator  > value_function-mesh-operator.yaml
        ```

   2. Update the image setting in the `value_function-mesh-operator.yaml` file.

        You can specify the location and version of the image using the `operatorImage` parameter. This example specifies the FunctionMesh image published to the [Docker Hub](https://hub.docker.com/) by StreamNative.

        ```yaml
        # operatorImage is Function Mesh Operator image
        operatorImage: streamnative/function-mesh:v0.1.6-rc1
        imagePullPolicy: IfNotPresent
        imagePullSecrets: []
        ```
      
    3. Install the FunctionMesh operator with the customized YAML file.

        ```
        helm upgrade --install function-mesh -f value_function-mesh-operator.yaml streamnative/function-mesh-operator -n $NAMESPACE
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

    - AWS S3

        ```
        wget https://sn-products.s3.amazonaws.com/snp/v1.2.0/streamnative_platform-v1.2.0.tar.gz
        ```

    - Alibaba Cloud

        ```
        wget https://downloads-streamnative-cloud.oss-cn-beijing.aliyuncs.com/sn-products/snp/v1.2.0/streamnative_platform-v1.2.0.tar.gz
        ```

2. Extract the package and use the `cd` command to switch to the target directory.

    ```
    tar zxvf streamnative_platform-v1.2.0.tar.gz
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

>**Note**
>
> If you want to delete the PVCs or the Secret, you need to delete them together. Otherwise, you will fail to reinstall the StreamNative Platform. It is recommended that you exercise caution when deleting the PVCs or the Secret because some of your significant information cannot be restored once you have deleted them.