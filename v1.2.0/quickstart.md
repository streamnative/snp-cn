---
title: Get Started
id: quickstart
category: quickstart
---

This document guides you through every step of installing and running StreamNative Platform with operators on Kubernetes quickly. It includes the following tasks:

- Install the StreamNative Platform on Kubernetes using operators.
- Start and stop StreamNative Platform.
- Create tenants, namespaces, and topics using the pulsarctl CLI tool.
- Produce and consume events using pulsar-client.
- Produce and consume events using Kafka client.
- Verify interoperability between Pulsar and Kafka.
- Monitor StreamNative Platform status with Prometheus and Grafana.

# Prerequisites

- Kubernetes server v1.16 or higher
- Install [kubectl](https://kubernetes.io/docs/tasks/tools/#kubectl) v1.16 or higher.
- Install [Helm](https://helm.sh/docs/intro/install/) 3.0 or higher.
- Install [pulsarctl](https://github.com/streamnative/pulsarctl#install) 2.8.0 or higher.
- Deploy a Kubernetes cluster.

# Step 1: Install StreamNative Platform

> **Note**
>
> This step uses the original images within the operators. If you want to use customized images to install StreamNative Platform, see [install StreamNative Platform using customized image](/operator-guides/deploy/sn-deploy.md#install-streamnative-platform-using-customized-images).

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
   
   The Pulsar operator is used to manage Pulsar components, including the Pulsar broker, BookKeeper, ZooKeeper, and Pular proxy.

    ```
    helm upgrade --install pulsar-operator streamnative/pulsar-operator -n $NAMESPACE
    ```

6. Install the FunctionMesh operator.

    The FunctionMesh operator is used to configure and manage Pulsar IO connectors and Pulsar Functions.

   [Function Mesh](/concepts/functionmesh-concepts.md) is a serverless and purpose-built framework for orchestrating multiple [Pulsar Functions](/concepts/pulsar-function-concepts.md) and [Pulsar IO connectors](/concepts/pulsar-io-concepts.md) for stream processing applications.

    ```
    helm upgrade --install function-mesh streamnative/function-mesh-operator -n $NAMESPACE 
    ```

7.  Deploy a Pulsar cluster.

    1. Define a Pulsar cluster configuration file. 

        [Here](https://raw.githubusercontent.com/streamnative/examples/master/platform/values_cluster.yaml) is an example of the YAML file used for configuring the Pulsar cluster.

    2. Deploy the Pulsar cluster using the YAML file.

        ```
        helm install -f /path/to/pulsar-cluster/file.yaml $RELEASE_NAME streamnative/sn-platform --set initialize=true
        ```

# Step 2: Create Pulsar tenants/namespaces/topics

pulsarctl is a Command-Line Interface (CLI) tool for Pulsar. In this section, you can use the `pulsarctl` CLI tool to create tenants, namespaces, and topics.

1. Log in to pulsarctl and create a tenant.

    ```bash
    pulsarctl \
    --admin-service-url WEB_SERVICE_URL \
    --token AUTH_PARAMS \
    tenants create TENANT_NAME
    ```

    Replace `WEB_SERVICE_URL` with the Web service URL of your Pulsar cluster. Replace `AUTH_PARAMS` with the token that you get from StreamNative Console. For details, see [prepare to connect to a Pulsar cluster](/user-guides/connect/connect-pulsar-cluster/connect-prepare.md).

2. Create a tenant.

    ```bash
    pulsarctl tenants create TENANT_NAME
    ```

3. Create a namespace.

    ```bash
    pulsarctl namespaces create NAMESPACE_NAME -c CLUSTER_NAME
    ```

4. Create a topic.

    ```bash
    pulsarctl topics create TOPIC_NAME
    ```

5. List all the topics.

    ```bash
    pulsarctl topics list NAMESPACE_NAME
    ```

# Step 3: Use pulsar-client to produce and consume events 

StreamNative Platform supports all the official Pulsar clients. You can use the pulsar-client CLI tool to create producers and consumers to simulate a simple production and consumption model.

1. Enter the `toolset` Pod.

    To facilitate the usage of the official Pulsar CLI tools, such as pulsar-client, StreamNative Platform provides a `toolset` Pod, which you can use to the `kubectl exec` command to connect to, to directly use the official Pulsar CLI tools.

    ```
    kubectl exec -n KUBERNETES_NAMESPACE -it RELEASE_NAME-sn-platform-toolset-0 -- bash
    ```

2. Create a consumer.

    ```bash
    pulsar-client consume -s SUBSCRIPTION_NAME TOPIC_NAME  -n 0
    ```

3. Create a producer.

    ```bash
    pulsar-client produce TOPIC_NAME  -m "---------hello streamnative platform-------" -n 10
    ```

   * From the producer side, you can see that the messages have been produced successfully.

        ```shell
        23:04:25.652 [main] INFO  org.apache.pulsar.client.cli.PulsarClientTool - 10 messages successfully produced
        ```

   * From the consumer side, you can receive the following messages.

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

# Step 4: Use Kafka client to produce and consume events

StreamNative Platform brings native Kafka protocol support to Pulsar brokers using Kafka on Pulsar (KoP). Therefore, you can migrate your existing Kafka applications and services to Apache Pulsar without modifying the codes.

Currently, StreamNative Platform supports [Kafka Client v1.0.0 - v2.6.0](https://github.com/streamnative/kop/tree/master/integrations#readme).

1. Grant produce and consume permission to the Admin role on the namespace.

    ```bash
    pulsarctl namespaces grant-permission --role ROLE_NAME --actions produce,consume NAMESPACE_NAME
    ```

2. Run the Kafka image.

    ```
    kubectl run kafka --rm -it -n KUBERNETES_NAMESPACE --image bitnami/kafka -- bash
    ```

3. Start a Kafka producer and send a message from the Kafka producer.

    ```
    kafka-console-producer.sh \
    --producer-property security.protocol=SASL_PLAINTEXT \
    --producer-property sasl.mechanism=PLAIN \
    --producer-property 'sasl.jaas.config=org.apache.kafka.common.security.scram.ScramLoginModule required username="TENANT/NAMESPACE" password="token:'YOUR_TOKEN'";' \
    --broker-list RELEASE_NAME-sn-platform-broker:9092 --topic TOPIC_NAME
    ```

    Here are security-related options to be configured.

    | Option |  Description | Default ｜
    | --- | --- | --- |
    | `username`| The username for the Kafka client connecting to the Pulsar cluster.  It is set to the name of the Pulsar tenant and namespace (`TENANT_NAME/NAMESPACE_NAME`) where Kafka topics are stored. | `public/default` |
    | `password` | The password for the Kafka client connecting to the Pulsar cluster. It is set to the token that you get from StreamNative Console. For details, see [prepare to connect to a Pulsar cluster](/user-guides/connect/connect-pulsar-cluster/connect-prepare.md).| N/A |

4. Open a new terminal window and enter the Kafka Pod.

    ```
    kubectl exec -it -n KUBERNETES_NAMESPACE kafka -- bash
    ```

5. Start a Kafka consumer.

    ```
    kafka-console-consumer.sh \
    --consumer-property security.protocol=SASL_PLAINTEXT \
    --consumer-property sasl.mechanism=PLAIN \
    --consumer-property 'sasl.jaas.config=org.apache.kafka.common.security.scram.ScramLoginModule required username="TENANT/NAMESPACE" password="token:'YOUR_TOKEN'";' \
    --bootstrap-server sn-platform-broker:9092 --topic TOPIC_NAME 
    ```

# Step 5: Verify interoperability between Pulsar and Kafka

As shown in the [step 3](#step-3-use-pulsar-client-to-produce-and-consume-events) and [step 4](#step-4-use-kafka-client-to-produce-and-consume-events), Pulsar producer, Pulsar consumer, Kafka producer, and Kafka consumer run normally. The Pulsar consumer can get the message from the Pulsar producer and the Kafka consumer can receive the message from the Kafka producer. This section further verifies the interoperability between Pulsar and Kafka.

1. Start a Pulsar consumer.

   1. Enter the `toolset` Pod.

       ```
       kubectl exec -n KUBERNETES_NAMESPACE -it RELEASE_NAME-sn-platform-toolset-0 -- bash
       ```

   2. Create a Pulsar consumer.

       ```bash
       pulsar-client consume -s SUBSCRIPTION_NAME TOPIC_NAME  -n 0
       ```

2. Start a Kafka consumer.

   1. Enter the Kafka Pod.

        ```
        kubectl exec -it -n KUBERNETES_NAMESPACE kafka -- bash
        ```

   2. Start a Kafka consumer.

       ```
       kafka-console-consumer.sh \
       --consumer-property security.protocol=SASL_PLAINTEXT \
       --consumer-property sasl.mechanism=PLAIN \
       --consumer-property 'sasl.jaas.config=org.apache.kafka.common.security.scram.ScramLoginModule required username="TENANT/NAMESPACE" password="token:'YOUR_TOKEN'";' \
       --bootstrap-server sn-platform-broker:9092 --topic TOPIC_NAME 
       ```

3. Start a Kafka producer.

   1. Enter the Kafka Pod.

        ```
        kubectl exec -it -n KUBERNETES_NAMESPACE kafka -- bash
        ```

   2. Start a Kafka producer and send a message.

        ```
        kafka-console-producer.sh \
        --producer-property security.protocol=SASL_PLAINTEXT \
        --producer-property sasl.mechanism=PLAIN \
        --producer-property 'sasl.jaas.config=org.apache.kafka.common.security.scram.ScramLoginModule required username="TENANT/NAMESPACE" password="token:'YOUR_TOKEN'";' \
        --broker-list RELEASE_NAME-sn-platform-broker:9092 --topic TOPIC_NAME
        > message-for-both-pulsar-and-kafka-client
        ```

        At the same time, you can receive messages from both Pulsar and Kafka consumers.

        * From Pulsar consumer side

            **Output**

            ```shell
            ----- got message -----
            message-for-both-pulsar-and-kafka-client
            ```

        * From Kafka consumer side

            **Output**

            ```shell
            > message-for-both-pulsar-and-kafka-client
            ```

4. Start a Pulsar producer and send the message _message-from-pulsar-producer_.

    ```bash
    pulsar-client producer -m "message-from-pulsar-producer" TOPIC_NAME
    ```

   At the same time, you can receive messages from both Pulsar and Kafka consumers.

   * From Pulsar consumer side

       **Output**

       ```shell
       ----- got message -----
       message-from-pulsar-producer
       ```

   * From Kafka consumer side

       **Output**

       ```shell
       > message-from-pulsar-producer
       ```

# Step 6: Use StreamNative Console to manage Pulsar cluster

The StreamNative Console is a web-based GUI management tool for managing and monitoring Pulsar. 

This section describes how to manage a Pulsar cluster, including creating and managing tenants, namespaces, and topics. 

1. Log in to the StreamNative Console. For details, see [here](/user-guides/admin/login-console.md).

2. Create a tenant. For a full list of operations available for tenants on the StreamNative Console, see [here](/user-guides/admin/work-with-tenants.md#work-with-tenants-using-streamnative-console).

   1. From the left navigation pane, click **Tenants**.

   2. Click **New Tenant** and a dialog box is displayed.

   3. Configure the tenant and then click **Confirm**.

3. Create a namespace. For a full list of operations available for namespaces on the StreamNative Console, see [here](/user-guides/admin/work-with-namespaces.md#work-with-namespaces-using-streamnative-console).

   1. From the left navigation pane, click **Namespaces**.

   2. Click **Create Namespace**. A dialog box is displayed.

   3. Enter a name for the namespace and then click **Confirm**. The namespace name is a string of up to 40 characters, supporting lowercase letters (a-z), numeric characters (0-9), and the special character hyphen (-).

4. Create a topic. For a full list of operations available for topics on the StreamNative Console, see [here](/user-guides/admin/work-with-topics.md#work-with-topics-using-streamnative-console).

   1. From the left navigation pane, click **Topics**.

   2. Click **New Topic** and a dialog box is displayed.

   3. Configure the topic and then click **Confirm**.

# Step 7: Use Prometheus and Grafana to monitor status

This section describes how to use the Prometheus and Grafana to monitor Pulsar status.

## Prometheus

Prometheus is an open-source system monitoring and alerting toolkit, which is used to collect and store Pulsar related metrics.

1. Expose Prometheus service.

    Before accessing the Prometheus website, you need to expose Prometheus service.

    ```bash
    kubectl expose service PROMETHEUS_SERVICE_NAME --type=LoadBalancer --name=PROMETHEUS_NAME --port=9090 -n KUBERNETES_NAMESPACE
    ```

2. Get the external IP address of Prometheus service.

    ```bash
    kubectl get service -n KUBERNETES_NAMESPACE
    ```

    **Output**

    `sn-prometheus` is exposed and the external IP is `{PROM-EXTERNAL-IP}`.

    ```shell
    NAME                                        TYPE           CLUSTER-IP     EXTERNAL-IP        PORT
    sn-prometheus                               LoadBalancer   10.12.3.133    {PROM-EXTERNAL-IP} 9090:32556/TCP               9s
    ```

3. Navigate to the Prometheus website at http://{PROM-EXTERNAL-IP}:9090/targets.

   Then you can use Prometheus to view metrics of all Pulsar components.

## Grafana

Apache Pulsar Grafana dashboard is an open-source visualization tool, containing a unique Graphite target parser that enables easy metric and function editing, which is used to visualize time series data of different monitoring indexes.

1. Expose Grafana service.

    Before accessing Grafana, you need to expose Grafana service. 

    ```bash
    kubectl expose service GRAFANA_SERVICE_NAME  --type=LoadBalancer --name=GRAFANA_NAME --port=3000 -n KUBERNETES_NAMESPACE
    ```

2. Get the external IP address of Grafana service.

    ```bash
    kubectl get service -n KUBERNETES_NAMESPACE
    ```

    **Output**

    `sn-grafana` is exposed and the external IP is `{GRAFANA-EXTERNAL-IP}`.

    ```shell
    NAME                                        TYPE           CLUSTER-IP     EXTERNAL-IP            PORT(S)                      AGE
    sn-grafana                                  LoadBalancer   10.12.5.204    {GRAFANA-EXTERNAL-IP}  3000:30307/TCP               5s
    ```

3. Navigate to the Grafana website at http://{GRAFANA-EXTERNAL-IP}:3000 and log into using the default credentials as below.

   * Account: pulsar
   * Password: pulsar

    Now you can see the Grafana dashboard showing detailed metrics of components, such as bookie, JVM, messaging, node, proxy, Zookeeper, pulsar topics and so on.

# Step 8: Uninstallation

This section describes how to uninstall Pulsar cluster, and Istio, and StreamNative Platform,

- Execute the following command to uninstall the Pulsar cluster.

    ```
    helm uninstall $PULSAR_CLUSTER
    ```

- Execute the following command to uninstall the Istio.

    ```
    istioctl x uninstall --purge
    ```

- Execute the following command to uninstall the StreamNative Platform.

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