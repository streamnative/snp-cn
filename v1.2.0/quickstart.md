---
title: 快速上手
id: quickstart
category: quickstart
---

本文档将指导你如何使用 Kubernetes 的 operator 快速安装并运行 StreamNative Platform，具体步骤如下：

- 在 Kubernetes 上使用 operator 安装 StreamNative Platform。
- 启动和停止 StreamNative Platform。
- 使用 pulsarctl CLI（命令行工具）创建租户、命名空间和主题。
- 使用 pulsar-client 生产和消费事件。
- 使用 Kafka 客户端生产和消费事件。
- 验证 Pulsar 和 Kafka 之间的互操作性。
- 使用 Prometheus 和 Grafana 监控 StreamNative Platform 的状态。

# 先决条件

- 安装 Kubernetes 服务器 v1.16 或更高版本。
- 安装 [kubectl](https://kubernetes.io/docs/tasks/tools/#kubectl) v1.16 或更高版本。
- 安装 [Helm](https://helm.sh/docs/intro/install/) 3.0 或更高版本。
- 安装 [pulsarctl](https://github.com/streamnative/pulsarctl#install) 2.8.0 或更高版本。
- 部署一个 Kubernetes 集群。

# 步骤 1：安装 StreamNative Platform

> **注意**
>
> 本步骤使用 operator 的原始镜像。如要使用自定义镜像安装 StreamNative Platform，请参阅[使用自定义镜像安装 StreamNative Platform]（/operator-guides/deploy/sn-deploy.md#install-streamnative-platform-using-customized-images）。

1. 安装 StreamNative 仓库。

    ```
    helm repo add streamnative https://charts.streamnative.io
    helm repo add banzaicloud-stable https://kubernetes-charts.banzaicloud.com
    helm repo add jetstack https://charts.jetstack.io
    helm repo update
    ```

2. 创建 Kubernetes 命名空间，并为 Kubernetes 命名空间添加环境变量。

    ```
    kubectl create namespace KUBERNETES_NAMESPACE
    export NAMESPACE=KUBERNETES_NAMESPACE
    ```

3. 安装 Vault operator。
   
    Vault operator 用于在 Kubernetes 上创建和维护高可用的 Vault 集群。通过使用 Vault operator，用户可以轻松地为应用程序部署和管理 Vault 集群。

    ```
    helm upgrade --install vault-operator banzaicloud-stable/vault-operator -n $NAMESPACE
    ```

4. 安装 cert-manager。

   cert-manager 是本地 [Kubernetes](https://kubernetes.io/) 证书管理控制器。用于从 [HashiCorp Vault](https://www.vaultproject.io/) 发布证书。cert-manager 可以确保证书是有效的和最新的。在设定的时间，cert-manager 会更新证书，以免证书过期。

   在安装 cert-manager 时，需要在 Helm 安装命令中添加 `--set installCRDs=true` 参数，从而将 cert-manager 所需的 CRD 资源一并安装到集群中。

    ```
    helm upgrade --install cert-manager jetstack/cert-manager -n $NAMESPACE --set installCRDs=true
    ```

5. 安装 Pulsar operator。
   
   Pulsar operator 用于管理 Pulsar 组件，包括 Pulsar broker、BookKeeper、ZooKeeper 和 Pular proxy。

    ```
    helm upgrade --install pulsar-operator streamnative/pulsar-operator -n $NAMESPACE
    ```

6. 安装 Function Mesh operator。

    Function Mesh operator 用于配置和管理 Pulsar IO 连接器和 Pulsar Functions。

   [Function Mesh](/concepts/functionmesh-concepts.md) 作为无服务器（Serverless）框架，协调多个 [Pulsar Functions](/concepts/pulsar-function-concepts.md) 和 [Pulsar IO 连接器](/concepts/pulsar-io-concepts.md)，以支持流处理应用程序。

    ```
    helm upgrade --install function-mesh streamnative/function-mesh-operator -n $NAMESPACE 
    ```

7.  部署 Pulsar 集群。

    1. 定义 Pulsar 集群的配置文件。

        - [点此查看](https://raw.githubusercontent.com/streamnative/examples/master/platform/values_cluster.yaml)配置 Pulsar 集群的 YAML 文件示例。

        - 初次部署，建议使用默认的 `values.yaml`配置，并更改存储部分，其余可保持不变。以最小改动，让集群在当前环境运行起来，稍后可方便的通过 `upgrade` 命令，再根据需求快速扩容。
        - 在配置存储时，对于公有云可参考对应 StorageClass 创建手册，对于简单测试，可使用 [local-path-provisioner](https://github.com/rancher/local-path-provisioner/tree/master) 实现一个本地的 StorageClass。注意，如果使用了StorageClass，`local_storage`配置需要注释掉，这两个存储配置，只能同时存在一个。
        - 默认镜像仓库如果在本地因网络限制下载速度较慢，可寻找本地代理源下载。如果阻塞在镜像下载，可反复多次下载。
    
    2. 使用 YAML 文件部署 Pulsar 集群。
    
        ```
        helm install -f /path/to/pulsar-cluster/file.yaml $RELEASE_NAME streamnative/sn-platform --set initialize=true
        
        helm install -f /path/to/pulsar-cluster/file.yaml $RELEASE_NAME streamnative/sn-platform --set initialize=true -n $NAMESPACE --version 1.4.1
        ```
        - 建议每次安装和升级都显示的增加上版本号，放置误操作升级到最新版本。
    
8.  检查 Pulsar 集群
    
    使用 `helm list` 查看 chart 信息。
    
    ```shell
     helm list -n $NAMESPACE
    ```
    
    使用 `kubectl get pods` 查看 pods 运行情况。
    
    ```shell
    kubectl get pods -n $NAMESPACE
    ```
    
    使用 `kubectl describe pod` 查看 pod 描述（以其中一个 Zookeeper 节点为例）。
    
    ```shell
    kubectl describe pod $RELEASE_NAME-zookeeper-0 -n $NAMESPACE
    ```

9.  更新 Pulsar 集群     

    当有新的参数和配置需要应用到集群，可以使用 `helm upgrade` 升级。建议每次更新前先使用 `diff upgrade` 检查更改的配置是否符合预期，待确认且无报错后，再执行 `upgrade` 命令，在集群中生效配置。
    
    ```shell
    # diff upgrade
    helm diff upgrade -f /path/to/pulsar-cluster/file.yaml $RELEASE_NAME streamnative/sn-platform --set initialize=true -n $NAMESPACE --version 1.4.1 --debug
    
    # upgrade
    helm upgrade -f /path/to/pulsar-cluster/file.yaml $RELEASE_NAME streamnative/sn-platform --set initialize=true -n $NAMESPACE --version 1.4.1 --debug
    ```
    

# 步骤 2：创建 Pulsar 租户/命名空间/主题

pulsarctl 是 Pulsar 的命令行工具（CLI）。你可以使用 `pulsarctl` 来创建租户、命名空间和主题。

1. 登录 pulsarctl 并创建租户。

    ```bash
    pulsarctl \
    --admin-service-url WEB_SERVICE_URL \
    --token AUTH_PARAMS \
    tenants create TENANT_NAME
    ```

     将 `WEB_SERVICE_URL` 替换为你 Pulsar 集群的 Web 服务 URL。将 `AUTH_PARAMS` 替换为从 StreamNative 控制台获得的令牌。详情请见[准备连接 Pulsar 集群](/user-guides/connect/connect-pulsar-cluster/connect-prepare.md)。

3. 创建命名空间。

    ```bash
    pulsarctl namespaces create NAMESPACE_NAME -c CLUSTER_NAME
    ```

4. 创建主题。

    ```bash
    pulsarctl topics create TOPIC_NAME
    ```

5. 列出所有主题。

    ```bash
    pulsarctl topics list NAMESPACE_NAME
    ```

# 步骤 3：使用 pulsar-client 生产和消费事件  

StreamNative Platform 支持 Pulsar 官方的所有客户端。你可以使用 pulsar-client 命令行工具（CLI）来创建生产者和消费者，以模拟简单的生产和消费模型。

1. 进入 `toolset` Pod。

    StreamNative Platform 提供了一个 `toolset` Pod 以方便用户使用 Pulsar 命令行工具（CLI），如 pulsar-client。你可以通过 `kubectl exec` 命令连接到这个 Pod，从而直接使用官方的 Pulsar 命令行工具（CLI）。

    ```
    kubectl exec -n KUBERNETES_NAMESPACE -it RELEASE_NAME-sn-platform-toolset-0 -- bash
    ```

2. 创建消费者。

    ```bash
    pulsar-client consume -s SUBSCRIPTION_NAME TOPIC_NAME  -n 0
    ```

3. 创建生产者。

    ```bash
    pulsar-client produce TOPIC_NAME  -m "---------hello streamnative platform-------" -n 10
    ```

   * 在生产者这边，可以看到消息已经成功生产。

        ```shell
        23:04:25.652 [main] INFO  org.apache.pulsar.client.cli.PulsarClientTool - 10 messages successfully produced
        ```

   * 在消费者这边，可以收到以下消息。

        ```shell
        ----- got message -----
        ---------hello streamnative platform-------
        ----- got message -----
        ---------hello streamnative platform-------
        ----- got message -----
        ---------hello streamnative platform-------
        ----- got message -----
        ---------hello streamnative platform-------
        ----- got message -----
        ---------hello streamnative platform-------
        ----- got message -----
        ---------hello streamnative platform-------
        ----- got message -----
        ---------hello streamnative platform-------
        ----- got message -----
        ---------hello streamnative platform-------
        ----- got message -----
        ---------hello streamnative platform-------
        ----- got message -----
        ---------hello streamnative platform-------
        ```

# 步骤 4：使用 Kafka 客户端生产和消费事件

通过  Kafka on Pulsar (KoP) ， StreamNative Platform 中的 Pulsar broker  就可以支持原生的 Kafka 协议。因此，无需修改代码，就可以将现有的 Kafka 应用程序和服务迁移到 Apache Pulsar 上。

目前，StreamNative Platform 支持 [Kafka 客户端 v1.0.0 - v2.6.0](https://github.com/streamnative/kop/tree/master/integrations#readme)。

1. 在命名空间上给 Admin 角色授予生产和消费权限。

    ```bash
    pulsarctl namespaces grant-permission --role ROLE_NAME --actions produce,consume NAMESPACE_NAME
    ```

2. 运行 Kafka 镜像。

    ```
    kubectl run kafka --rm -it -n KUBERNETES_NAMESPACE --image bitnami/kafka -- bash
    ```

3. 启动 Kafka 生产者，并由 Kafka 生产者发送一条消息。

    ```
    kafka-console-producer.sh \
    --producer-property security.protocol=SASL_PLAINTEXT \
    --producer-property sasl.mechanism=PLAIN \
    --producer-property 'sasl.jaas.config=org.apache.kafka.common.security.scram.ScramLoginModule required username="TENANT/NAMESPACE" password="token:'YOUR_TOKEN'";' \
    --broker-list RELEASE_NAME-sn-platform-broker:9092 --topic TOPIC_NAME
    ```

    以下是需要配置的安全选项。

    | 选项 |  描述 | 默认值 |
    | --- | --- | --- |
    | `username`| Kafka 客户端连接到 Pulsar 集群的用户名。该用户名会被设为 Pulsar 租户和命名空间的名称，Kafka 主题也将存储在此 Pulsar 租户和命名空间中。 | `public/default` |
    | `password` | Kafka 客户端连接到 Pulsar 集群的密码。设置为从 StreamNative 控制台获得的令牌。详情请见[准备连接 Pulsar 集群](/user-guides/connect/connect-pulsar-cluster/connect-prepare.md)。 | N/A |

4. 打开一个新的终端窗口，进入 Kafka Pod。

    ```
    kubectl exec -it -n KUBERNETES_NAMESPACE kafka -- bash
    ```

5. 启动 Kafka 消费者。

    ```
    kafka-console-consumer.sh \
    --consumer-property security.protocol=SASL_PLAINTEXT \
    --consumer-property sasl.mechanism=PLAIN \
    --consumer-property 'sasl.jaas.config=org.apache.kafka.common.security.scram.ScramLoginModule required username="TENANT/NAMESPACE" password="token:'YOUR_TOKEN'";' \
    --bootstrap-server sn-platform-broker:9092 --topic TOPIC_NAME 
    ```

# 步骤 5：验证 Pulsar 和 Kafka 间的互操作性

在完成[步骤 3 ](#步骤-3使用-pulsar-client-生产和消费事件)和[步骤 4 ](#步骤-4使用-kafka-客户端生产和消费事件)后，Pulsar 生产者、Pulsar 消费者、Kafka 生产者和 Kafka 消费者都可正常运行。Pulsar 消费者可以从Pulsar 生产者那里获得消息，Kafka 消费者也可以从 Kafka 生产者那里接收消息。下面将进一步验证 Pulsar 和 Kafka 之间的互操作性。

1. 启动 Pulsar 消费者。

   1. 进入 `toolset` Pod.

       ```
       kubectl exec -n KUBERNETES_NAMESPACE -it RELEASE_NAME-sn-platform-toolset-0 -- bash
       ```

   2. 创建 Pulsar 消费者。

       ```bash
       pulsar-client consume -s SUBSCRIPTION_NAME TOPIC_NAME  -n 0
       ```

2. 启动 Kafka 消费者。

   1. 进入 Kafka Pod。

        ```
        kubectl exec -it -n KUBERNETES_NAMESPACE kafka -- bash
        ```

   2. 创建 Kafka  消费者。

       ```
       kafka-console-consumer.sh \
       --consumer-property security.protocol=SASL_PLAINTEXT \
       --consumer-property sasl.mechanism=PLAIN \
       --consumer-property 'sasl.jaas.config=org.apache.kafka.common.security.scram.ScramLoginModule required username="TENANT/NAMESPACE" password="token:'YOUR_TOKEN'";' \
       --bootstrap-server sn-platform-broker:9092 --topic TOPIC_NAME 
       ```

3. 启动 Kafka 生产者。

   1. 进入 Kafka Pod.

        ```
        kubectl exec -it -n KUBERNETES_NAMESPACE kafka -- bash
        ```

   2. 启动 Kafka 生产者并发送一条消息。

        ```
        kafka-console-producer.sh \
        --producer-property security.protocol=SASL_PLAINTEXT \
        --producer-property sasl.mechanism=PLAIN \
        --producer-property 'sasl.jaas.config=org.apache.kafka.common.security.scram.ScramLoginModule required username="TENANT/NAMESPACE" password="token:'YOUR_TOKEN'";' \
        --broker-list RELEASE_NAME-sn-platform-broker:9092 --topic TOPIC_NAME
        > message-for-both-pulsar-and-kafka-client
        ```

        你可以同时接收来自 Pulsar 消费者和 Kafka 消费者的消息。

        * Pulsar 消费者端

            **输出**

            ```shell
            ----- got message -----
            message-for-both-pulsar-and-kafka-client
            ```

        *  Kafka 消费者端

            **输出**

            ```shell
            > message-for-both-pulsar-and-kafka-client
            ```

4. 启动 Pulsar 生产者并发送消息 _message-from-pulsar-producer_。

    ```bash
    pulsar-client producer -m "message-from-pulsar-producer" TOPIC_NAME
    ```

   你可以同时接收来自 Pulsar 消费者和 Kafka 消费者的消息。

   * Pulsar 消费者端

       **输出**

       ```shell
       ----- got message -----
       message-from-pulsar-producer
       ```

   * Kafka 消费者端

       **输出**

       ```shell
       > message-from-pulsar-producer
       ```

# 步骤 6：使用 StreamNative 控制台管理 Pulsar 集群

StreamNative 控制台是基于 Web 的图形化界面（GUI）管理工具，用于管理和监控 Pulsar。

本节介绍了如何用 StreamNative 控制台管理 Pulsar 集群，包括创建和管理租户、命名空间和主题。

1. 登录 StreamNative 控制台。详情请参见[此处](/user-guides/admin/login-console.md)。

2. 创建租户。关于 StreamNative 控制台中租户相关操作的完整列表，请参见[此处](/user-guides/admin/work-with-tenants.md#work-with-tenants-using-streamnative-console)。

   1. 从左边的导航窗格，点击**租户**。

   2. 点击**创建租户**，出现一个对话框。

   3. 配置租户，然后点击**确定**。

3. 创建命名空间。关于 StreamNative 控制台中命名空间相关操作的完整列表，请参见[此处](/user-guides/admin/work-with-namespaces.md#work-with-namespaces-using-streamnative-console)。

   1. 从左边的导航窗格，点击**命名空间**。

   2. 点击**创建命名空间**，出现一个对话框。

   3. 为命名空间输入一个名称，然后点击**确定**。命名空间的名称最多包含 40 个字符，支持小写字母（a-z），数字字符（0-9），以及特殊字符连字符（-）。

4. 创建主题。关于 StreamNative 控制台中主题相关操作的完整列表，请参见[此处](/user-guides/admin/work-with-topics.md#work-with-topics-using-streamnative-console)。

   1. 从左边的导航窗格，点击**主题**。

   2. 点击**添加主题**，出现一个对话框。

   3. 配置主题，然后点击**确定**。

# 步骤 7：使用 Prometheus 和 Grafana 监控状态

本节介绍了如何使用 Prometheus 和 Grafana 来监控 Pulsar 的状态。

## Prometheus

Prometheus 是一个开源的系统监控和告警工具包，用于收集和存储 Pulsar 相关的指标。

1. 暴露 Prometheus 服务。

    要访问 Prometheus，需要先暴露 Prometheus 服务。

    ```bash
    kubectl expose service PROMETHEUS_SERVICE_NAME --type=LoadBalancer --name=PROMETHEUS_NAME --port=9090 -n KUBERNETES_NAMESPACE
    ```

2.  获取 Prometheus 服务的外部 IP 地址。

    ```bash
    kubectl get service -n KUBERNETES_NAMESPACE
    ```

    **输出**

    `sn-prometheus` is exposed and the external IP is `{PROM-EXTERNAL-IP}`.

    ```shell
    NAME                                        TYPE           CLUSTER-IP     EXTERNAL-IP        PORT
    sn-prometheus                               LoadBalancer   10.12.3.133    {PROM-EXTERNAL-IP} 9090:32556/TCP               9s
    ```

3. 访问 Prometheus 网站： http://{PROM-EXTERNAL-IP}:9090/targets.

   现在你可以用 Prometheus 来查看所有 Pulsar 组件的指标了。

## Grafana

Apache Pulsar Grafana 仪表板是一个开源的可视化工具，包含独有的 Graphite 目标解析器，可以轻松地进行指标和函数编辑，用于将不同监测指标的时间序列数据进行可视化显示。

1. 暴露 Grafana 服务。

    要访问 Grafana，需要先暴露 Grafana 服务。

    ```bash
    kubectl expose service GRAFANA_SERVICE_NAME  --type=LoadBalancer --name=GRAFANA_NAME --port=3000 -n KUBERNETES_NAMESPACE
    ```

2. 获取 Grafana 服务的外部 IP 地址。

    ```bash
    kubectl get service -n KUBERNETES_NAMESPACE
    ```

    **输出**

    `sn-grafana` is exposed and the external IP is `{GRAFANA-EXTERNAL-IP}`.

    ```shell
    NAME                                        TYPE           CLUSTER-IP     EXTERNAL-IP            PORT(S)                      AGE
    sn-grafana                                  LoadBalancer   10.12.5.204    {GRAFANA-EXTERNAL-IP}  3000:30307/TCP               5s
    ```

3. 访问 Grafana 网站：http://{GRAFANA-EXTERNAL-IP}:3000，并使用用下面的默认账户密码登录。

   * 帐户：pulsar
   * 密码：pulsar

   现在你可以用 Grafana 仪表板查看组件的详细指标了，如 bookie、JVM、消息、节点、代理、ZooKeeper、Pulsar 主题等。

# 步骤 8：卸载

本节介绍如何卸载 Pulsar 集群、Istio 以及 StreamNative Platform。

- 执行以下命令卸载 Pulsar 集群：

    ```
    helm uninstall $PULSAR_CLUSTER
    ```

- 执行以下命令卸载 Istio：

    ```
    istioctl x uninstall --purge
    ```

- 执行以下命令卸载 StreamNative Platform：

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
