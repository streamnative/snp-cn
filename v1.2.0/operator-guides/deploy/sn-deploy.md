---
title: 在原生 Kubernetes 集群上部署 StreamNative Platform
id: sn-deploy
category: operator-guides
---
创建 Kubernetes 集群后，便可以部署 StreamNative Platform。

# 安装 StreamNative Platform

本节介绍了如何在在线和离线模式下安装 StreamNative Platform。

## 在互联网条件下安装 StreamNative Platform

本节介绍了在接入互联网条件下，如何安装 StreamNative Platform。

### 使用 operator 内的原始镜像安装 StreamNative Platform

本节介绍了如何使用 operator 内的原始镜像安装 StreamNative Platform。

1. 安装 StreamNative 仓库。

    ```
    helm repo add streamnative https://charts.streamnative.io
    helm repo add banzaicloud-stable https://kubernetes-charts.banzaicloud.com
    helm repo add jetstack https://charts.jetstack.io
    helm repo update
    ```

2. 创建 Kubernetes 命名空间，并为其添加环境变量。

    ```
    kubectl create namespace KUBERNETES_NAMESPACE
    export NAMESPACE=KUBERNETES_NAMESPACE
    ```

3. 安装 Vault operator。
   
    Vault operator 用于在 Kubernetes 上创建和维护高可用的 Vault 集群，使用户可以轻松地为其应用程序部署和管理 Vault 集群。

    ```
    helm upgrade --install vault-operator banzaicloud-stable/vault-operator -n $NAMESPACE
    ```

4. 安装 cert-manager。

    Cert-manager 是原生的 [Kubernetes](https://kubernetes.io/) 证书管理控制器。用于从 [HashiCorp Vault](https://www.vaultproject.io/) 签发证书。Cert-manager 可以确保证书有效且是最新版本。在设定的时间，cert-manager 会更新证书，以免证书过期。

    在安装 cert-manager 时，需要在 Helm 安装命令中添加 `--set installCRDs=true` 参数，从而将 cert-manager 所需的 CRD 资源一并安装到集群中。

    ```
    helm upgrade --install cert-manager jetstack/cert-manager -n $NAMESPACE --set installCRDs=true
    ```

5. 安装 Pulsar operator。

    ```
    helm upgrade --install pulsar-operator streamnative/pulsar-operator -n $NAMESPACE
    ```

6. 安装 Function Mesh operator。

   [Function Mesh](/concepts/functionmesh-concepts.md) 作为无服务器（Serverless）框架，协调多个 [Pulsar Functions](/concepts/pulsar-function-concepts.md) 和 [Pulsar IO 连接器](/concepts /pulsar-io-concepts.md)，以支持流处理应用程序。

    ```
    helm upgrade --install function-mesh streamnative/function-mesh-operator -n $NAMESPACE 
    ```

7. 设置环境变量 `PULSAR_CHART`。

    ```
    export PULSAR_CHART=streamnative/sn-platform
    ```

### 使用自定义镜像安装 StreamNative Platform

本节介绍了如何使用自定义镜像安装 StreamNative Platform。

1. 安装 StreamNative 仓库。

    ```
    helm repo add streamnative https://charts.streamnative.io
    helm repo add banzaicloud-stable https://kubernetes-charts.banzaicloud.com
    helm repo add jetstack https://charts.jetstack.io
    helm repo update
    ```

2. 创建 Kubernetes 命名空间，并为其添加环境变量。

    ```
    kubectl create namespace KUBERNETES_NAMESPACE
    export NAMESPACE=KUBERNETES_NAMESPACE
    ```

3. 安装 Vault operator。
   
    Vault operator 用于在 Kubernetes 上创建和维护高可用的 Vault 集群，使用户可以轻松地为其应用程序部署和管理 Vault 集群。

   1. 检查用于安装 Vault operator 的 chart 并将 `values.yaml` 文件的内容保存为本地 YAML 文件。
   
      本示例将 `values.yaml` 文件保存为 `value_vault-operator.yaml` 文件。

        ```
        helm inspect values banzaicloud-stable/vault-operator  > value_vault-operator.yaml
        ```

   2. 更新 `value_vault-operator.yaml` 文件中的镜像设置。 
   
      可以使用 `image.bankVaultsRepository` 和 `image.tag` 参数指定镜像的位置和版本。本示例指定了 Banzai Cloud 发布到 [Docker Hub](https://hub.docker.com/) 的 Vault 镜像。

      ```yaml
      image:
        bankVaultsRepository: banzaicloud/bank-vaults
        repository: banzaicloud/vault-operator
        # tag: ""
        pullPolicy: IfNotPresent
        imagePullSecrets: []  # global.imagePullSecrets is also supported
      ```
   
    3. 使用自定义镜像安装 Vault operator。
   
        ```
        helm upgrade --install -f value_vault-operator.yaml vault-operator banzaicloud-stable/vault-operator -n $NAMESPACE
        ```

4. 安装 cert-manager。

   Cert-manager 是原生的 [Kubernetes](https://kubernetes.io/) 证书管理控制器。用于从 [HashiCorp Vault](https://www.vaultproject.io/) 签发证书。Cert-manager 可以确保证书有效且是最新版本。在设定的时间，cert-manager 会更新证书，以免证书过期。

   在安装 cert-manager 时，需要在 Helm 安装命令中添加 `--set installCRDs=true` 参数，从而将 cert-manager 所需的 CRD 资源一并安装到集群中。

   1. 检查用于安装 cert-manager 的 chart 并将 `values.yaml` 文件的内容保存为本地 YAML 文件。

       本示例将 `values.yaml` 文件保存为 `value_cert-manager.yaml` 文件。

       ```
       helm inspect values jetstack/cert-manager  > value_cert-manager.yaml
       ```

   2. 更新 `value_cert-manager.yaml` 文件中的镜像设置。 

        可以使用 `image.repository` 和 `image.tag` 参数指定镜像的位置和版本。StreamNative 将 cert-manager 镜像创建到 [Docker Hub](https://hub.docker.com/) 上。本示例指定了使用 StreamNative 发布到 [Docker Hub](https://hub.docker.com/) 的 cert-manager 镜像。

        

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

    3. 使用自定义镜像安装 cert-manager。

        ```
        helm upgrade --install -f value_cert-manager.yaml  cert-manager jetstack/cert-manager -n $NAMESPACE --set installCRDs=true
        ```

5. 安装 Pulsar operator。

   1. 检查用于安装 Pulsar operator 的 chart 并将 `values.yaml` 文件的内容保存为本地 YAML 文件。

       本示例将 `values.yaml` 文件保存为 `value_pulsar-operator.yaml` 文件。

       ```
       helm inspect values streamnative/pulsar-operator  > value_pulsar-operator.yaml
       ```

   2. 更新 `value_pulsar-operator.yaml` 文件中的镜像设置。

       可以使用 `images.repository` 和 `images.tag` 参数指定镜像的位置和版本。本示例中指定了由 StreamNative 发布到 [Docker Hub](https://hub.docker.com/) 的 ZooKeeper、BookKeeper 和 Pulsar 的镜像。

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
       
   3. 使用定制的 YAML 文件安装 Pulsar operator。

        ```
        helm upgrade --install pulsar-operator -f value_pulsar-operator.yaml streamnative/pulsar-operator -n $NAMESPACE
        ```

6. 安装 FunctionMesh operator。

   [Function Mesh](/concepts/functionmesh-concepts.md) 作为无服务器（Serverless）框架，协调多个 [Pulsar Functions](/concepts/pulsar-function-concepts.md) 和 [Pulsar IO 连接器](/concepts /pulsar-io-concepts.md)，以支持流处理应用程序。

    1. 检查用于安装 FunctionMesh operator 的 chart 并将 `values.yaml` 文件的内容保存为本地 YAML 文件。

        本示例将 `values.yaml` 文件保存为 `value_function-mesh-operator.yaml` 文件。

        ```
        helm inspect values streamnative/function-mesh-operator  > value_function-mesh-operator.yaml
        ```

   2. 更新 `value_function-mesh-operator.yaml` 文件中的镜像设置。

        可以使用 `operatorImage` 参数指定镜像的位置和版本。本示例指定了 StreamNative 发布到 [Docker Hub](https://hub.docker.com/) 的 Function Mesh 镜像。

        ```yaml
        # operatorImage is Function Mesh Operator image
        operatorImage: streamnative/function-mesh:v0.1.6-rc1
        imagePullPolicy: IfNotPresent
        imagePullSecrets: []
        ```
      
    3. 使用自定义的 YAML 文件安装 Function Mesh operator。

        ```
        helm upgrade --install function-mesh -f value_function-mesh-operator.yaml streamnative/function-mesh-operator -n $NAMESPACE
        ```

7. 设置环境变量 `PULSAR_CHART`。

    ```
    export PULSAR_CHART=streamnative/sn-platform
    ```

## 在无互联网条件下安装 StreamNative Platform

本节介绍了在无互联网条件下，如何安装 StreamNative Platform。

> **注意**
> 
> 在无互联网条件下安装 StreamNative Platform ，需要预加载所需的 Docker 镜像。预加载 Docker 镜像的方法请参见[预加载 Docker 镜像](/operator-guides/sn-plan.md#preload-docker-images)。

### 使用 `install.sh` 脚本安装 StreamNative Platform

要使用脚本来安装 StreamNative Platform，请完成如下步骤：

1. 使用 `wget` 命令下载 StreamNative Platform 的 `.tar.gz` 包。

    - AWS S3

        ```
        wget https://sn-products.s3.amazonaws.com/snp/v1.2.0/streamnative_platform-v1.2.0.tar.gz
        ```

    - 阿里云

        ```
        wget https://downloads-streamnative-cloud.oss-cn-beijing.aliyuncs.com/sn-products/snp/v1.2.0/streamnative_platform-v1.2.0.tar.gz
        ```

2. 解压包并使用 `cd` 命令切换到目标目录。

    ```
    tar zxvf streamnative_platform-v1.2.0.tar.gz
    cd streamnative-platform
    ```

3. 为 StreamNative Platform 的 Kubernetes 命名空间添加环境变量。

    ```
    export NAMESPACE=KUBERNETES_NAMESPACE
    ```

4. 安装 StreamNative Platform。

    ```
    sh install.sh
    ```

5. 设置环境变量 `PULSAR_CHART`。

    ```
    export PULSAR_CHART=[PATH_TO_PULSAR_CHART]
    ```

### 手动安装 StreamNative Platform

按照如下步骤来手动安装 StreamNative Platform：

1. 创建一个 Kubernetes 命名空间。

    ```
    kubectl create namespace KUBERNETES_NAMESPACE
    ```

2. 安装 Vault operator。

    ```
    helm upgrade --install vault-operator VAULT_OPERATOR_NAME/ -n KUBERNETES_NAMESPACE
    ```

3. 安装 cert-manager。

    ```
    helm upgrade --install cert-manager CERT_MANAGER_NAME/ -n KUBERNETES_NAMESPACE --set installCRDs=true
    ```

4. 安装 Pulsar operator。

   1. 定义一个 YAML 文件并应用 CRD 来部署 Pulsar 资源。

       ```
       kubectl apply -f /path/to/pulsar-operator/file.yaml -n KUBERNETES_NAMESPACE 
       ```

   2. 安装 Pulsar operator。

       ```
       helm upgrade --install pulsar-operators PULSAR_OPERATOR_NAME/ -n KUBERNETES_NAMESPACE
       ```

5. 安装 Function Mesh operator。

    1. 定义一个 YAML 文件并应用 CRD 来部署 Pulsar 资源。

        ```
        kubectl apply -f /path/to/functionmesh-operator/file.yaml -n KUBERNETES_NAMESPACE
        ```

    2. 安装 Function Mesh operator。

        ```
        helm upgrade --install function-mesh FUNCTIONMESH_OPERATOR_NAME/ -n KUBERNETES_NAMESPACE
        ```

6. 设置环境变量 `PULSAR_CHART`。

    ```
    export PULSAR_CHART=[PATH_TO_PULSAR_CHART]
    ```

# 部署 Pulsar 集群

按照如下步骤来部署 Pulsar 集群：

1. 为 Pulsar Chart 目录、Pulsar 集群名称和 Kubernetes 命名空间添加环境变量。

    ```
    # If you use offline version, export PULSAR_CHART first.
    export PULSAR_CHART=/path/to/pulsar/chart
    # Define your pulsar cluster name
    export RELEASE_NAME=PULSAR_CLUSTER
    # Define the k8s namespace to install the pulsar cluster
    export NAMESPACE=KUBERNETES_NAMESPACE
    ```

2. 为 Pulsar 集群创建 Kubernetes 命名空间。

    ```
    kubectl create namespace $NAMESPACE
    ```

3. 定义一个 Pulsar 集群配置文件。

    参看[此处](https://raw.githubusercontent.com/streamnative/examples/master/platform/values_cluster.yaml) YAML 文件示例，该 YAML 文件用于配置 Pulsar 集群。

4. 使用 YAML 文件来创建 Pulsar 集群。

    ```
    helm install -f /path/to/pulsar/file.yaml $RELEASE_NAME $PULSAR_CHART --set initialize=true --set namespace=$NAMESPACE
    ```

5. （可选）更新 Pulsar 集群。

    要更新 Pulsar 集群，可以更新 YAML 文件，然后执行 `helm upgrade` 命令。

    ```
    helm upgrade -f /path/to/pulsar/file.yaml $RELEASE_NAME $PULSAR_CHART
    ```

# 卸载

本节介绍了如何卸载 Pulsar 集群和 StreamNative Platform。

- 执行以下命令卸载 Pulsar 集群：

    ```
    helm uninstall $PULSAR_CLUSTER
    ```

- 执行以下命令卸载 StreamNative Platform：

    - 如果你是通过离线方式安装的 StreamNative Platform，运行以下命令：

        ```
        sh [StreamNative_HOME]/uninstall.sh
        ```

    - 如果你是通过在线方式安装的 StreamNative Platform，运行以下命令：

        ```
        export NAMESPACE=KUBERNETES_NAMESPACE
        helm uninstall vault-operator -n $NAMESPACE
        helm uninstall cert-manager -n $NAMESPACE
        helm uninstall pulsar-operator -n $NAMESPACE
        helm uninstall function-mesh -n $NAMESPACE
        ```

>**注意**
>
> 如想删除 PVC 或 Secret，需要将它们同时删除。否则，你将无法重新安装 StreamNative Platform。建议谨慎删除 PVC 或 Secret，因为一旦删除，一些重要信息将无法恢复。