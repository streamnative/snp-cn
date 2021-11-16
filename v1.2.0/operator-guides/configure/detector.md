---
title: Detector
id: detector
category: operator-guides
---

The Pulsar detector monitors the health of Pulsar clusters. When you need to monitor the distribution latency from message sending to message consumption in one cluster or across clusters, you can use the Pulsar detector. The Pulsar detector enables you to easily integrate with existing monitoring tools like Prometheus, and view the critical metrics in Grafana.

Pulsar detector is enabled by default in StreamNative Platform. To view the health status of your Pulsar cluster, you can check it in your pulsar_detector dashboard in Grafana. The following is an example. 

![](../image/pulsar-detector.png)

 To disable the tool, you can set `pulsar_detector` to `false` in your `${PLATFORM_HOME}/pulsar/values.yaml` file. 

# Metrics

Pulsar detector supports the following metrics. You can view the complete list of metrics in Grafana. 

| Name                                          | Type     | Description |
| ------------------------------------------ | ---------  | --------------- |
|pulsar_detector_e2e_latency_ms| Summary  |  The distribution latency from message sending to message consumption.  |
|pulsar_detector_publish_latency_ms| Summary  | The distribution latency of message sending. |
|pulsar_detector_geo_latency_ms| Summary | The distribution latency from message sending to message consumption across clusters. |
|pulsar_sla_messaging_up | Gauge | Whether the message publishing service is running. |
|pulsar_sla_webservice_up| Gauge | Whether the webservice is running. The value `1` means the webservice is running. The value `0` means the webservice is not running. |
