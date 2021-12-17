---
title: 使用 Grafana 仪表板监控 StreamNative Platform 
id: sn-monitor
category: operator-guides
---

所有 StreamNative Platform 组件都暴露了可被 Prometheus 抓取的指标。通过配置 Grafana，就可以将这些指标在仪表板中可视化。

本文介绍了如何用 Grafana 仪表板监控 StreamNative Platform。

# 在浏览器中打开 Grafana 

StreamNative Platform 支持[暴露 Grafana 服务](/operator-guides/configure/monitoring/grafana.md#expose-grafana-service)。可以通过以下 URL 访问 Grafana 仪表板。

![](../../image/login-grafana.png)

# 使用 Grafana 仪表板

Grafana 提供了以下仪表板，包括 broker、bookie、ZooKeeper、代理（proxy）、主题、消息等。 

- *概览*：显示 Pulsar 集群的总体健康状况。
- *消息指标*：显示与 Pulsar 消息相关的指标（如生产者、消费者、消息 backlog 等）。
- *代理（proxy）指标*：如果在 Pulsar 集群中运行了代理（proxy），则会显示与 Pulsar proxy 相关的指标。
- *Bookie 指标*：显示与 Bookie 相关的指标。
- *ZooKeeper*：显示与 ZooKeeper 集群相关的指标。
- *JVM 指标*：显示 Pulsar 集群中所有组件（如 proxy、broker、bookie 等）的 JVM 相关指标。
- *Functions 指标*：显示与 Pulsar Functions 相关的指标。
- *自动恢复*：显示与自动恢复相关的指标。
- *容器*：显示与容器相关的指标。 

*系统指标*显示在*节点指标*仪表板中，某些部分显示在*概览*仪表板中。

下图显示了如何使用 Grafana 仪表板监控 Pulsar broker。

![](../../image/broker-dashboard.png)

| 编号 | 动作 |
| --- | --- |
| 1 | 从下拉列表中选择一个 Broker 仪表板。    |
| 2 | 选择监控所有 broker 或某个特定的 broker。 |
| 3 | 配置收集指标的时间段。                  |
| 4 | 配置刷新仪表板的时间间隔。 |