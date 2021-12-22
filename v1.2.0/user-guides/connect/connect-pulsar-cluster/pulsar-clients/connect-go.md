---
title: 使用 Go 客户端连接 Pulsar 集群
id: connect-go
category: user-guides
---

本示例介绍了如何使用 Go 客户端连接到 Pulsar 集群，然后向 Pulsar 集群生产和从 Pulsar 集群消费消息。

1. 连接到 Pulsar 集群。

    ```go
    package main
    
    import (
        "github.com/apache/pulsar-client-go/pulsar"
        "log"
    )
    
    func main() {
        client, err := pulsar.NewClient(pulsar.ClientOptions{
            URL:            "SERVICE_URL",
            Authentication: pulsar.NewAuthenticationToken(AUTH_PARAMS),
        })
        if err != nil {
            log.Fatal(err)
        }
        defer client.Close()
    }
    ```

    按照[准备连接 Pulsar 集群](/user-guides/connect/connect-pulsar-cluster/connect-prepare.md)中的描述，设置 `SERVICE_URL` 和 `AUTH_PARAMS` 参数。

2. 创建 Go 消费者并使用 Go 消费者来消费消息。

    ```go
    func main() {
    client, err := pulsar.NewClient(pulsar.ClientOptions{
        URL:            SERVICE_URL,
        Authentication: pulsar.NewAuthenticationToken(AUTH_PARAMS),
    })
    if err != nil {
        log.Fatal(err)
    }
    defer client.Close()


    consumer, err := client.Subscribe(pulsar.ConsumerOptions{
        Topic:            "topic-1",
        SubscriptionName: "my-sub",
        Type:             pulsar.Shared,
    })
    if err != nil {
        log.Fatal(err)
    }
    defer consumer.Close()
    
    for i := 0; i < 10; i++ {
        msg, err := consumer.Receive(context.Background())
        if err != nil {
            log.Fatal(err)
        }
    
        fmt.Printf("Received message msgId: %v -- content: '%s'\n",
            msg.ID(), string(msg.Payload()))
    
        consumer.Ack(msg)
    }
    
    if err := consumer.Unsubscribe(); err != nil {
        log.Fatal(err)
    }
    }
    ```

3. 创建 Go 生产者并使用 Go 生产者来生产消息。

    ```
    func main() {
        client, err := pulsar.NewClient(pulsar.ClientOptions{
            URL:            SERVICE_URL,
            Authentication: pulsar.NewAuthenticationToken(AUTH_PARAMS),
        })
        if err != nil {
            log.Fatal(err)
        }
        defer client.Close()
    
        producer, err := client.CreateProducer(pulsar.ProducerOptions{
            Topic: "topic-1",
        })
        if err != nil {
            log.Fatal(err)
        }
        defer producer.Close()
    
        for i := 0; i < 10; i++ {
            if msgId, err := producer.Send(context.Background(), &pulsar.ProducerMessage{
                Payload: []byte(fmt.Sprintf("hello-%d", i)),
            }); err != nil {
                log.Fatal(err)
            } else {
                fmt.Printf("Published message: %v \n", msgId)
            }
        }
    }
    ```
