---
title: Configure Alertmanager
id: alertmanager
category: operator-guides
---

[Alertmanager](https://prometheus.io/docs/alerting/alertmanager/) is a component in StreamNative Platform and a component of Prometheus. Alertmanager handles alerts sent by StreamNnative components, such as the Prometheus server. It takes care of deduplicating, grouping, and routing them to the correct receiver integration, such as email, PagerDuty, or OpsGenie. It also takes care of silencing and inhibition of alerts.

By default, Alertmanager is enabled with StreamNative Platform. To disable it, you can set `monitoring.alert_manager: false` in the Pulsar cluster configuration YAML file.

# Configure Alertmanager

You can configure the requested CPU and memory, resolve time, and alert rules for the Alertmanager in the Pulsar cluster configuration YAML file. Then, you can use the `helm upgrade` command to restart the StreamNative Platform to make updates effective.

```
alert_manager:
  resources:
    requests:
      memory: 
      cpu: 
  config:
    global:
      resolve_timeout: 
  rules:
    groups:
      - name: 
        rules:
```

# Configure alerting rules

Alerting rules allow you to define alert conditions based on Prometheus expression language expressions and to send notifications about firing alerts to an external service. Whenever the alert expression results in one or more vector elements at a given point in time, the alert counts as active for these elements label sets.

For more information about alert rules, see [here](https://prometheus.io/docs/prometheus/latest/configuration/alerting_rules/).

This example shows how to configure alert rules with ZooKeeper.

```shell
- name: zookeeper
        rules:
          - alert: HighWatchers
            expr: zookeeper_server_watches_count{job="zookeeper"} > 1000000
            for: 5m
            labels:
              status: warning
            annotations:
              summary: "Watchers of Zookeeper server is over than 1000k."
              description: "Watchers of Zookeeper server {{ $labels.kubernetes_pod_name }} is over than 1000k, current value is {{ $value }}."

          - alert: HighEphemerals
            expr: zookeeper_server_ephemerals_count{job="zookeeper"} > 10000
            for: 5m
            labels:
              status: warning
            annotations:
              summary: "Ephemeral nodes of Zookeeper server is over than 10k."
              description: "Ephemeral nodes of Zookeeper server {{ $labels.kubernetes_pod_name }} is over than 10k, current value is {{ $value }}."

          - alert: HighConnections
            expr: zookeeper_server_connections{job="zookeeper"} > 10000
            for: 5m
            labels:
              severity: page
            annotations:
              summary: "Connections of Zookeeper server is over than 10k."
              description: "Connections of Zookeeper server {{ $labels.kubernetes_pod_name }} is over than 10k, current value is {{ $value }}."

          - alert: HighDataSize
            expr: zookeeper_server_data_size_bytes{job="zookeeper"} > 2147483648
            for: 5m
            labels:
              severity: page
            annotations:
              summary: "Data size of Zookeeper server is over than 2GB."
              description: "Data size of Zookeeper server {{ $labels.instance }} is over than 2GB, current value is {{ $value }}."

          - alert: HighRequestThroughput
            expr: sum(irate(zookeeper_server_requests{job="zookeeper"}[30s])) by (type) > 1000
            for: 5m
            labels:
              status: warning
            annotations:
              summary: "Request throughput on Zookeeper server is over than 1000 in 5m."
              description: "Request throughput of {{ $labels.type}} on Zookeeper server {{ $labels.instance }} is over than 1k, current value is {{ $value }}."

          - alert: HighRequestLatency
            expr: zookeeper_server_requests_latency_ms{job="zookeeper", quantile="0.99"} > 100
            for: 5m
            labels:
              severity: page
            annotations:
              summary: "Request latency on Zookeeper server is over than 100ms."
              description: "Request latency {{ $labels.type }} in p99 on Zookeeper server {{ $labels.instance }} is over than 100ms, current value is {{ $value }} ms."
```

