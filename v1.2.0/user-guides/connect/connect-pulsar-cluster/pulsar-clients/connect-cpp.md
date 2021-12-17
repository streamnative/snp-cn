---
title: 使用 C++ 客户端连接 Pulsar 集群
id: connect-cpp
category: user-guides
---

本示例介绍了如何使用 C++ 客户端连接到 Pulsar 集群，然后向 Pulsar 集群生产和从 Pulsar 集群消费消息。

1. 连接到 Pulsar 集群。

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

    按照[准备连接 Pulsar 集群](/user-guides/connect/connect-pulsar-cluster/connect-prepare.md)中的描述，设置 `SERVICE_URL` 和 `AUTH_PARAMS` 参数。

2. 创建 C++ 消费者并使用 C++ 消费者来消费消息。

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

3. 创建 C++ 生产者并使用 C++ 生产者来生产消息。

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
