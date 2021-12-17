---
title: 使用 Node.js 客户端连接 Pulsar 集群
id: connect-nodejs
category: user-guides
---

本示例介绍了如何使用 Node.js 客户端连接到 Pulsar 集群，然后向 Pulsar 集群生产和从 Pulsar 集群消费消息。

1. 连接到 Pulsar 集群。

    ```
    const Pulsar = require('pulsar-client');
    
    const auth_params = process.env.AUTH_PARAMS;
    const service_url = process.env.SERVICE_URL;
    
    (async () => {
        const auth = new Pulsar.AuthenticationToken({
            token: AUTH_PARAMS,
        });
    
        // Create a client
        const client = new Pulsar.Client({
            serviceUrl: service_url,
            authentication: auth,
            operationTimeoutSeconds: 30,
        });
    
        await client.close();
    })();
    ```

    按照[准备连接 Pulsar 集群](/user-guides/connect/connect-pulsar-cluster/connect-prepare.md)中的描述，设置 `SERVICE_URL` 和 `AUTH_PARAMS` 参数。

2. 创建 Node.js 消费者并使用 Java 消费者来消费消息。

    ```
    const Pulsar = require('pulsar-client');

    const auth_params = process.env.AUTH_PARAMS;
    const service_url = process.env.SERVICE_URL;

    (async () => {
    const auth = new Pulsar.AuthenticationToken({
        token: auth_params,
    });

    // Create a client
    const client = new Pulsar.Client({
        serviceUrl: service_url,
        authentication: auth,
        operationTimeoutSeconds: 30,
    });

    // Create a consumer
    const consumer = await client.subscribe({
        topic: 'persistent://public/default/my-topic',
        subscription: 'sub1',
        subscriptionType: 'Shared',
        ackTimeoutMs: 10000,
    });

    // Receive messages
    for (let i = 0; i < 10; i += 1) {
        const msg = await consumer.receive();
        console.log(msg.getData().toString());
        consumer.acknowledge(msg);
    }

    await consumer.close();
    await client.close();
    })();
    ```

3. 创建 Node.js 生产者并使用 Java 生产者来生产消息。

    ```
    const Pulsar = require('pulsar-client');
    
    const auth_params = process.env.AUTH_PARAMS;
    const service_url = process.env.SERVICE_URL;
    
    (async () => {
    const auth = new Pulsar.AuthenticationToken({
        token: auth_params,
    });
    
    // Create a client
    const client = new Pulsar.Client({
        serviceUrl: service_url,
        authentication: auth,
        operationTimeoutSeconds: 30,
    });
    
    // Create a producer
    const producer = await client.createProducer({
        topic: 'persistent://public/default/my-topic',
        sendTimeoutMs: 30000,
        batchingEnabled: true,
    });
    
    // Send messages
    for (let i = 0; i < 10; i += 1) {
        const msg = `my-message-${i}`;
        producer.send({
            data: Buffer.from(msg),
        });
        console.log(`Sent message: ${msg}`);
    }
    await producer.flush();
    
    await producer.close();
    await client.close();
    })();
    ```

