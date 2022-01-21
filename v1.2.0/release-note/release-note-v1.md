---
title: 版本说明 V1.0.0
id: release-note-v1
category: release-note
---


StreamNative 发布了基于 Apache Pulsar 的云原生批流融合数据平台。StreamNative Platform 1.0 为在私有环境中部署和自我管理 Apache Pulsar 提供了完整的声明式 API 驱动体验。

StreamNative Platform 为你和你的团队提供如下功能：

- 在用户选择的私有环境中进行部署和管理，降低运营成本
- 运行安全、可靠、生产就绪的批流融合数据平台，减少风险和昂贵的资源投资
- 稳定运行消息流工作负载 
- 可扩展以满足业务需求，高效利用资源

该版本主要包括如下亮点：
- StreamNative Platform 支持 Apache Pulsar 2.8.0。
- 功能：
  - 事务
  - [KoP](/concepts/kop-concepts.md)
  - [Function Mesh](/concepts/functionmesh-concepts.md)
- 安全性：
  - 基于 Token 和 [TLS](/operator-guides/configure/security/tls-proxy.md) 的安全集群。
  - 支持不同服务账户的[授权](/operator-guides/configure/security/security-auth.md)。
  - 支持[审计日志](/operator-guides/configure/security/audit-log.md)，记录集群管理、生产和消费动作。
- 监控
  - 配备集成仪表板的 [Grafana](/operator-guides/sn-monitor.md)
  - [Prometheus](/operator-guides/configure/monitoring/prometheus.md)
  - [Alertmanager](/operator-guides/configure/monitoring/alertmanager.md)
- 管理
  - StreamNative 控制台：新 UI 提供全新用户体验。
  - pulsarctl CLI（命令行工具）
- [备份和还原工具](/operator-guides/configure/backup-restore-metadata-tool.md)
- Pulsar E2E/SLA 延迟监控工具

有关 StreamNative Platform V1.0.0 版本的更多信息，可查阅[版本日志](https://streamnative.io/en/blog/release/2021-06-16-streamnative-platform)。