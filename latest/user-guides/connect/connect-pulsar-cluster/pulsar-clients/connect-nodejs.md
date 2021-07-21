---
title: Connect to Pulsar cluster using Node.js client
id: connect-nodejs
category: user-guides
---

This example shows how to use the Node.js client to connect to a Pulsar cluster and then produce and consume messages to and from the Pulsar cluster.

1. Connect to the Pulsar cluster.

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

    Set the `SERVICE_URL` and `AUTH_PARAMS` parameters based on the descriptions in the [prepare to connect to a Pulsar cluster](/user-guides/connect/connect-pulsar-cluster/connect-prepare.md).

2. Create a Node.js consumer and use the Java consumer to consume messages.

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

3. Create a Node.js producer and use the Java producer to produce messages.

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

