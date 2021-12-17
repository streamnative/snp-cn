---
title: 使用 WebSocket API 连接 Pulsar 集群
id: connect-websocket
category: user-guides
---

本示例介绍了如何使用 WebSocket API 客户端连接到 Pulsar 集群，然后向 Pulsar 集群生产和从 Pulsar 集群消费消息。

> **注意**
> 
> 要使用 WebSocket API 连接 Pulsar 集群，需要提前开启 WebSocket 服务。

1. 连接到 Pulsar 集群。

    ```python
    import websocket, base64, json

    # TOPIC = 'ws://CLUSTER_HOST:9090/ws/v2/consumer/persistent/public/default/test/sub'
    TOPIC = 'wss://CLUSTER_HOST:9443/ws/v2/consumer/persistent/public/default/test/sub'
    token = "YOUR_TOKEN"
    header = ["Authorization:Bearer" + token]
    ws = websocket.create_connection(TOPIC, header=header)
    while True:
        msg = json.loads(ws.recv())
        if not msg: break
        print("Received: {} - payload: {}".format(msg, base64.b64decode(msg['payload'])))
        # Acknowledge successful processing
        ws.send(json.dumps({'messageId' : msg['messageId']}))
        
    ws.close()
    ```
    将 `CLUSTER_HOST` 参数替换为集群的域名。

2. 创建消费者并使用消费者来消费消息。

    ```python
    import websocket, base64, json
    
    # TOPIC = 'ws://CLUSTER_HOST:9090/ws/v2/consumer/persistent/public/default/test/sub'
    TOPIC = 'wss://CLUSTER_HOST:9443/ws/v2/consumer/persistent/public/default/test/sub'
    token = "YOUR_TOKEN"
    header = ["Authorization:Bearer " + token]
    ws = websocket.create_connection(TOPIC, header=header)
    while True:
        msg = json.loads(ws.recv())
        if not msg: break
        print("Received: {} - payload: {}".format(msg, base64.b64decode(msg['payload'])))
        # Acknowledge successful processing
        ws.send(json.dumps({'messageId' : msg['messageId']}))
        
    ws.close()
    ```

3. 创建生产者并使用生产者来生产消息。

    ```python
    import websocket, base64, json
    
    # TOPIC = 'ws://CLUSTER_HOST:9090/ws/v2/producer/persistent/public/default/test'
    TOPIC = 'wss://CLUSTER_HOST:9443/ws/v2/producer/persistent/public/default/test'
    
    token = "YOUR_TOKEN"
    
    header = ["Authorization:Bearer " + token]
    
    ws = websocket.create_connection(TOPIC, header=header)
    
    # Send one message as JSON
    ws.send(json.dumps({
    'payload' : base64.b64encode('Hello World'),
    'properties': {
        'key1' : 'VALUE1',
        'key2' : 'VALUE2'
    },
    'context' : 5
    }))
    
    response =  json.loads(ws.recv())
    if response['result'] == 'ok':
        print('Message published successfully')
    else:
        print('Failed to publish message:', response)
    ws.close()
    ```

> **注意**
> 
> 如使用 Web Socket Secure（WSS）协议，应使用端口 `9443`。
