---
title: Configure Prometheus
id: prometheus
category: operator-guides
---

[Prometheus](https://prometheus.io) is a component in StreamNative Platform and is used to  collect metrics from the Pulsar cluster. By default, Prometheus is enabled with StreamNative Platform. To disable it, you can set `monitoring.prometheus: false` in the Pulsar cluster configuration YAML file.

You can configure the CPU, memory, and disk for Prometheus in the Pulsar cluster configuration YAML file. Then, you can use the `helm upgrade` command to restart the StreamNative Platform to make updates effective.

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
