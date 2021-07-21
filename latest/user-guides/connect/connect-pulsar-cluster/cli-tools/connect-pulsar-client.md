---
title: Connect to Pulsar cluster using pulsar-client CLI tool
id: connect-pulsar-client
category: user-guides
---

`pulsar-client` is a CLI tool that is used to produce and consume messages to and from pulsar topics.

 This example shows how to connect to a Pulsar cluster using the pulsar-client CLI tool and produce 10 messages to the `test-topic` topic.

    ```shell script
    ./bin/pulsar-client \
        --url SERVICE_URL \
        --auth-plugin org.apache.pulsar.client.impl.auth.AuthenticationToken \
        --auth-params token:AUTH_PARAMS \
        produce test-topic -m "test-message" -n 10
    ```

Set the `--url` and `--auth-params` parameters based on descriptions in the [prepare to connect to a Pulsar cluster](/user-guides/connect/connect-pulsar-cluster/connect-prepare.md).
