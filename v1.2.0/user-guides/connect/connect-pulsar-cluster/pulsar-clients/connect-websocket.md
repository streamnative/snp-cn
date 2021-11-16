---
title: Connect to Pulsar cluster using WebSocket API
id: connect-websocket
category: user-guides
---

This example shows how to use the WebSocket API to connect to a Pulsar cluster and then produce and consume messages to and from the Pulsar cluster.

> **Note**
> 
> To use the WebSocket API to connect to a Pulsar cluster, you need to enable the WebSocket service in advance.

1. Connect to the Pulsar cluster.

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
    Replace the `CLUSTER_HOST` parameter with the domain name of the cluster.

2. Create a consumer and use the consumer to consume messages.

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

3. Create a producer and use the producer to produce messages.

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

> **Note**
> 
> If Web Socket Secure (WSS) protocol is used, you should use the port `9443`.
