---
title: Connect to Pulsar cluster using Java client
id: connect-java
category: user-guides
---

This example shows how to use the Java client to connect to a Pulsar cluster and then produce and consume messages to and from the Pulsar cluster.

1. Connect to the Pulsar cluster.

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

    Set the `SERVICE_URL` and `AUTH_PARAMS` parameters based on the descriptions in the [prepare to connect to a Pulsar cluster](/user-guides/connect/connect-pulsar-cluster/connect-prepare.md).

2. Create a Java consumer and use the Java consumer to consume messages.

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

3. Create a Java producer and use the Java producer to produce messages.

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