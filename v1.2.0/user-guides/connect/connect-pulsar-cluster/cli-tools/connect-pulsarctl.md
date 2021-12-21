---
title: 使用 pulsarctl CLI 连接到 Pulsar 集群
id: connect-pulsarctl
category: user-guides
---

`pulsarctl` 是一个 CLI（命令行工具），用于管理 Pulsar 集群。

如下示例显示了如何使用 `pulsarctl` CLI（命令行工具）连接到 Pulsar 集群，并列出了该集群中的租户。

```shell script
pulsarctl \
    --admin-service-url WEB_SERVICE_URL \
    --token AUTH_PARAMS \
    tenants list
```

根据[准备连接 Pulsar 集群](/user-guides/connect/connect-pulsar-cluster/connect-prepare.md)中的描述，设置 `--admin-service-url` 和 `--token` 参数。

关于如何使用 pulsarctl  CLI（命令行工具）管理 Pulsar 组件的详细信息，参见 [pulsarctl 命令参考](https://docs.streamnative.io/pulsarctl/v2.7.0.7/)。
