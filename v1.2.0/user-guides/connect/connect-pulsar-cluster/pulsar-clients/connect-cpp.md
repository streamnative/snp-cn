---
title: Connect to Pulsar cluster using C++ client
id: connect-cpp
category: user-guides
---

This example shows how to use the C++ client to connect to a Pulsar cluster and then produce and consume messages to and from the Pulsar cluster.

1. Connect to the Pulsar cluster.

    ```cpp
    #include <iostream>
    #include <pulsar/Client.h>

    using namespace pulsar;

    int main() {
        ClientConfiguration config;
        config.setAuth(AuthToken::createWithToken("AUTH_PARAMS"));
        Client client("SERVICE_URL", config);

        client.close();
    }
    ```

    Set the `SERVICE_URL` and `AUTH_PARAMS` parameters based on the descriptions in the [prepare to connect to a Pulsar cluster](/user-guides/connect/connect-pulsar-cluster/connect-prepare.md).

2. Create a C++ consumer and use the C++ consumer to consume messages.

    ```cpp
    int main(int argc, char *argv[]) {
        ClientConfiguration config;
        std::string oauthParams = argv[2];

        config.setAuth(pulsar::AuthOauth2::create(oauthParams));

        Client client(argv[1], config);

        Consumer consumer;
        Result result = client.subscribe("persistent://public/default/my-topic", "consumer-1", consumer);
        if (result != ResultOk) {
            std::cout << "Failed to subscribe: " << result << "\n";
            return -1;
        }

        Message msg;
        while (true) {
            consumer.receive(msg);
            std::cout << "Received: " << msg << "  with payload '" << msg.getDataAsString() << "'" << "\n";

            consumer.acknowledge(msg);
        }
    }
    ```

3. Create a C++ producer and use the C++ producer to produce messages.

    ```cpp
    int main(int argc, char *argv[]) {
        ClientConfiguration config;
        std::string oauthParams = argv[2];

        config.setAuth(pulsar::AuthOauth2::create(oauthParams));

        Client client(argv[1], config);

        Producer producer;
        Result result = client.createProducer("persistent://public/default/my-topic", producer);
        if (result != ResultOk) {
            std::cout << "Error creating producer: " << result << "\n";
            return -1;
        }

        // Send synchronously
        Message msg = MessageBuilder().setContent("content").build();
        Result res = producer.send(msg);
        std::cout << "Message sent: " << res << "\n";

        client.close();
    }
    ```
