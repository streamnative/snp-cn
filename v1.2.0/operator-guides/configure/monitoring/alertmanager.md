---
title: 配置 Alertmanager
id: alertmanager
category: operator-guides
---

[Alertmanager](https://prometheus.io/docs/alerting/alertmanager/) 是 StreamNative Platform 的组件，也是 Prometheus 的组件。Alertmanager 处理由 StreamNative 组件发送的告警，例如 Prometheus 服务器。它负责去重、分组，并将它们路由到正确的接收集成端，如电子邮件、PagerDuty 或 OpsGenie。它还负责处理告警静默和抑制。

默认情况下，Alertmanager 在 StreamNative Platform 中是自动启用的。 如果要禁用 Alertmanager，你可以在 Pulsar 集群的 YAML 配置文件中设置 `monitoring.alert_manager: false`。

# 配置 Alertmanager

可以在 Pulsar 集群的 YAML 配置文件中为 Alertmanager 配置需要的 CPU 和内存、解析时间和告警规则。然后，使用 `helm upgrade` 命令重新启动 StreamNative Platform 使变更生效。

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

# 配置告警规则

告警规则允许你根据表达式（Prometheus 表达式语言）定义告警条件，并将有关触发告警的通知发送到外部服务。一旦告警表达式在给定时间点产生一个或多个向量元素时，告警就会因为这些元素标签集而激活。

关于告警规则的更多信息，参见[这里](https://prometheus.io/docs/prometheus/latest/configuration/alerting_rules/)。

如下示例显示了如何为 ZooKeeper 配置告警规则。

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

