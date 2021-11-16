---
title: Connect to Pulsar cluster using pulsarctl CLI tool
id: connect-pulsarctl
category: user-guides
---

`pulsarctl` is a CLI tool that is used to manage Pulsar clusters.

This example shows how to use the `pulsarctl` CLI tool to connect to a Pulsar cluster and then list tenants available for this cluster.

```shell script
pulsarctl \
    --admin-service-url WEB_SERVICE_URL \
    --token AUTH_PARAMS \
    tenants list
```

Set the `--admin-service-url` and `--token` parameters based on descriptions in the [prepare to connect to a Pulsar cluster](/user-guides/connect/connect-pulsar-cluster/connect-prepare.md).

For details about how to use the pulsarctl CLI tool to manage Pulsar components, see [pulsarctl command reference](https://docs.streamnative.io/pulsarctl/v2.7.0.7/).
