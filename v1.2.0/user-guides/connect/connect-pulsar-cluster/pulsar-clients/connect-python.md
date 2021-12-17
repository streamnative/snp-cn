---
title: 使用 Python 客户端连接 Pulsar 集群
id: connect-python
category: user-guides
---

本示例介绍了如何使用 Python 客户端连接到 Pulsar 集群，然后向 Pulsar 集群生产和从 Pulsar 集群消费消息。

1. 连接到 Pulsar 集群。

    ```python
    import os
    from pulsar import Client, AuthenticationToken
    client = Client(os.environ.get('SERVICE_URL'), authentication=AuthenticationToken(os.environ.get('AUTH_PARAMS')))
    
    client.close()
    ```

    按照[准备连接 Pulsar 集群](/user-guides/connect/connect-pulsar-cluster/connect-prepare.md)中的描述，设置 `SERVICE_URL` 和 `AUTH_PARAMS` 参数。

2. 创建 Python 消费者并使用 Python 消费者来消费消息。

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

3. 创建 Python 生产者并使用 Python 生产者来生产消息。

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
