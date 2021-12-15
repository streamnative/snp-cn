---
title: 侦查器
id: detector
category: operator-guides
---

Pulsar 侦查器监控 Pulsar 集群的健康状况。当需要监控某个集群或跨集群从消息发送到消息被消费的分布延迟，就可以使用 Pulsar 侦查器。Pulsar 侦查器能够轻松地与现有的侦查工具（如 Prometheus）集成，并在 Grafana 中查看关键指标。

在 StreamNative Platform 中，Pulsar 侦查器是默认启用的。要查看 Pulsar 集群的健康状况，可以在 Grafana 的 pulsar_detector 仪表板中进行检查。参见如下示例。

![](../image/pulsar-detector.png)

如果要禁用该工具，可以在 `${PLATFORM_HOME}/pulsar/values.yaml` 文件中将 `pulsar_detector` 设置为 `false`。

# 指标 

Pulsar 侦查器支持以下指标。可以在 Grafana 中查看指标的完整列表。 

| 名称                                          | 类型   | 描述 |
| ------------------------------------------ | ---------  | --------------- |
|pulsar_detector_e2e_latency_ms| 总结  | 从消息发送到消息被消费的分布延迟。|
|pulsar_detector_publish_latency_ms| 总结  | 消息发送的分布延迟。|
|pulsar_detector_geo_latency_ms| 总结 | 从消息发送到消息被消费的分布延迟（跨集群）。|
|pulsar_sla_messaging_up | 标尺 | 消息发布服务是否正在运行。|
|pulsar_sla_webservice_up| 标尺 | Webservice 是否正在运行。值 `1` 表示网络服务正在运行。值 `0` 表示 webservice 没有运行。|
