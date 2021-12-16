---
title: 配置 Prometheus
id: prometheus
category: operator-guides
---

[Prometheus](https://prometheus.io) 是 StreamNative Platform 中的一个组件，用于从 Pulsar 集群收集指标。默认情况下，Prometheus 在 StreamNative Platform 是启用状态。如需禁用，可以在 Pulsar 集群的 YAML 配置文件中设置 `monitoring.prometheus: false`。

在 Pulsar 集群的 YAML 配置文件中，可以为 Prometheus 配置 CPU、内存和磁盘。然后，使用 `helm upgrade` 命令来重新启动 StreamNative Platform，使更新生效。

```
prometheus:
  # keep request = limit to keep this container in guaranteed class
  resources:
    requests:
      cpu: 
      memory: 
  volumes:
    data:
      name: 
      size: 
```
