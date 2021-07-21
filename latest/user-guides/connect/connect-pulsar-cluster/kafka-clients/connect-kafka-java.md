---
title: Connect to Pulsar cluster using Kafka Java client
id: connect-kafka-java
category: user-guides
---

StreamNative Platform brings native Kafka protocol support to Pulsar brokers using Kafka on Pulsar (KoP). Therefore, you can migrate your existing Kafka applications and services to Apache Pulsar without modifying the codes.

This example shows how to use the Kafka Java client to connect to a Pulsar cluster and then produce and consume messages to and from the Pulsar cluster.

Currently, StreamNative Platform supports [Kafka Client v1.0.0 - v2.6.0](https://github.com/streamnative/kop/tree/master/integrations#readme).

# Prerequisites

- Install Java 1.8.0 or higher version.
- Install Istio. For details, see [install Istio for KoP access](/operator-guides/sn-plan.md#install-istio-for-kop-access).

# Procedures

1. Grant produce and consume permission to the Admin role on the namespace.

    ```bash
    pulsarctl namespaces grant-permission --role ROLE_NAME --actions produce,consume NAMESPACE_NAME
    ```

2. Configure [KoP SSL connection](/operator-guides/sn-plan.md#generate-ssl-key-and-certificates-for-KoP) and enable KoP.

    KoP starts up together with the Pulsar broker. To facilitate the usage of KoP, KoP is enabled by default. You can set the following options based on your KoP SSL connection.

    ```
    broker:
    # set your own domain for accessing kop outside from the cluster
    advertisedDomain: ""
    kop:
        tls:
        # create a secret with keystore.jks and truststore.jks for kop tls security
        certSecretName: "kop-secret"
        # create a secret for keystore and truststore cert
        passwordSecretRef:
            key: password
            name: kop-keystore-password
    ```

3. Get the external IP address of the Istio gateway service.

    ```
    kubectl get svc/istio-ingressgateway -n KUBERNETES_NAMESPACE
    ```

4. Install Kafka client.

    1. Download the [Kafka client 2.2.0](https://kafka.apache.org/downloads). In this example, Kafka 2.2.0 is used because the default configuration of the producer's `retries` parameter is changed from `0` to `2147483647` since Kafka 2.1.0.

        ```bash
        curl -O https://archive.apache.org/dist/kafka/2.2.0/kafka_2.11-2.2.0.tgz
        ```

    2. Extract the downloaded package.

        ```bash
        tar -xf kafka_2.11-2.2.0.tgz
        ```

5. Prepare a file named `client-ssl.properties`. The file contains the following information.

    ```
    security.protocol=SASL_SSL
    ssl.truststore.location=client.truststore.jks
    ssl.truststore.password=client
    ssl.endpoint.identification.algorithm=
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.scram.ScramLoginModule required username="USER_NAME" password="token:[YOUR_TOKEN]";
    ```

6. Run Kafka client.

   1. Enter the Kafka client directory.

       ```bash
       cd kafka_2.11-2.2.0
       ```     

   2. Start a Kafka producer and send a message from the Kafka producer..

       ```bash
       kafka-console-producer.sh --broker-list kop_service_url:9093 --topic TOPIC_NAME --producer.config client-ssl.properties
       > message-for-kafka-client
       ```

   3. Start a Kafka consumer.

       ```bash
       kafka-console-consumer.sh --bootstrap-server kop_service_url:9093 --topic TOPIC_NAME --consumer.config client-ssl.properties
       ```

       At the same time, you can receive the message `message-for-kafka-client`.
