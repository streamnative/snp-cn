---
title: 使用 Java 客户端连接 Pulsar 集群
id: connect-java
category: user-guides
---

本示例介绍了如何使用 Java 客户端连接到 Pulsar 集群，然后向 Pulsar 集群生产和从 Pulsar 集群消费消息。

1. 连接到 Pulsar 集群。

    ```java
    package io.streamnative.examples.oauth2;
    
    import org.apache.pulsar.client.api.AuthenticationFactory;
    import org.apache.pulsar.client.api.PulsarClient;
    
    public class ConnectByToken {
        public static void main(String[] args) throws Exception {
    
            PulsarClient client = PulsarClient.builder()
                    .serviceUrl("SERVICE_URL")
                    .authentication(AuthenticationFactory.token("AUTH_PARAMS"))
                    .build();
    
            client.close();
        }
    }
    ```

    按照[准备连接 Pulsar 集群](/user-guides/connect/connect-pulsar-cluster/connect-prepare.md)中的描述，设置 `SERVICE_URL` 和 `AUTH_PARAMS` 参数。

2. 创建 Java 消费者并使用 Java 消费者来消费消息。

    ```java
    public class SampleConsumer {
        public static void main(String[] args) throws Exception {
            String serviceUrl = "SERVICE_URL";
            String topic = "persistent://public/default/topic-1";
            String token = "YOUR_TOKEN";
    
            PulsarClient client = PulsarClient.builder()
                .serviceUrl(serviceUrl)
                .authentication(AuthenticationFactory.token(token))
                .build();
    
            Consumer<byte[]> consumer = client.newConsumer(Schema.BYTES)
                .topic(topic)
                .subscriptionName("sub")
                .subscriptionInitialPosition(SubscriptionInitialPosition.Earliest)
                .subscribe();
    ```


            for (int i = 0; i < 10; i++) {
                Message<byte[]> msg = consumer.receive();
                consumer.acknowledge(msg);
                System.out.println("Receive message " + new String(msg.getData()));
            }
    
            consumer.close();
            client.close();
        }
    }
    ```

3. 创建 Java 生产者并使用 Java 生产者来生产消息。

    ```java
    public class SampleProducer {
        public static void main(String[] args) throws Exception {
            String serviceUrl = "SERVICE_URL";
            String topic = "persistent://public/default/topic-1";
            String token = "YOUR_TOKEN";
    
        PulsarClient client = PulsarClient.builder()
            .serviceUrl(serviceUrl)
            .authentication(AuthenticationFactory.token(token))
            .build();
    
        Producer<byte[]> producer = client.newProducer().topic(token).create();
        
    ```


            for (int i = 0; i < 10; i++) {
                String message = "my-message-" + i;
                MessageId msgID = producer.send(message.getBytes());
                System.out.println("Publish " + "my-message-" + i + " and message ID " + msgID);
            }
    
            producer.close();
            client.close();
        }
    }
    ```