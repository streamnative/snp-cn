---
title: Connect to Pulsar cluster using Go client
id: connect-go
category: user-guides
---

This example shows how to use the Go client to connect to a Pulsar cluster and then produce and consume messages to and from the Pulsar cluster.

1. Connect to the Pulsar cluster.

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

    Set the `SERVICE_URL` and `AUTH_PARAMS` parameters based on the descriptions in the [prepare to connect to a Pulsar cluster](/user-guides/connect/connect-pulsar-cluster/connect-prepare.md).

2. Create a Go consumer and use the Go consumer to consume messages.

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

3. Create a Go producer and use the Go producer to produce messages.

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
