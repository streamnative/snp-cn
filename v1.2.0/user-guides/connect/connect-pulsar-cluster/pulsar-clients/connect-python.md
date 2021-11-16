---
title: Connect to Pulsar cluster using Python client
id: connect-python
category: user-guides
---

This example shows how to use the Python client to connect to a Pulsar cluster and then produce and consume messages to and from the Pulsar cluster.

1. Connect to the Pulsar cluster.

    ```python
    import os
    from pulsar import Client, AuthenticationToken
    client = Client(os.environ.get('SERVICE_URL'), authentication=AuthenticationToken(os.environ.get('AUTH_PARAMS')))

    client.close()
    ```

    Set the `SERVICE_URL` and `AUTH_PARAMS` parameters based on the descriptions in the [prepare to connect to a Pulsar cluster](/user-guides/connect/connect-pulsar-cluster/connect-prepare.md).

2. Create a Python consumer and use the Python consumer to consume messages.

    ```python
    import os
    from pulsar import Client, AuthenticationToken

    client = Client(os.environ.get('SERVICE_URL'), authentication=AuthenticationToken(os.environ.get('AUTH_PARAMS')))

    consumer = client.subscribe('my-topic', 'my-subscription')

    while True:
        msg = consumer.receive()
        try:
            print("Received message '{}' id='{}'".format(msg.data(), msg.message_id()))
            # Acknowledge successful processing of the message
            consumer.acknowledge(msg)
        except:
            # Message failed to be processed
            consumer.negative_acknowledge(msg)
    ```

3. Create a Python producer and use the Python producer to produce messages.

    ```python
    import os
    from pulsar import Client, AuthenticationToken

    client = Client(os.environ.get('SERVICE_URL'), authentication=AuthenticationToken(os.environ.get('AUTH_PARAMS')))

    producer = client.create_producer('my-topic')

    for i in range(10):
        producer.send(('Hello-%d' % i).encode('utf-8'))
        print('send msg "hello-%d"' % i)

    client.close()
    ```
