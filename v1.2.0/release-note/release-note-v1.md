---
title: Release note V1.0.0
id: release-note-v1
category: release-note
---


StreamNative releases a cloud-native, unified messaging and streaming platform powered by Apache Pulsar. StreamNative Platform 1.0 provides a complete, declarative API-driven experience for deploying and self-managing Apache Pulsar in your private environments.

StreamNative platform enables you and your team to:

- Deploy and manage in the private environment of your choice to reduce operational costs
- Run a secure, reliable, and production-ready messaging and streaming platform to reduce risk and costly resource investments
- Run messaging and streaming workloads consistently 
- Scale to meet business demands with efficient use of resources

This release includes the following major highlights.
- StreamNative Platform supports Apache Pulsar 2.8.0.
- Functionalities
  - Transactions
  - [KoP](/concepts/kop-concepts.md)
  - [Function Mesh](/concepts/functionmesh-concepts.md)
- Security
  - Secure clusters based on Token and [TLS](/operator-guides/configure/security/tls-proxy.md).
  - Support [authorization](/operator-guides/configure/security/security-auth.md) for different service accounts.
  - Support [audit log](/operator-guides/configure/security/audit-log.md) for cluster management, produce and consume actions.
- Monitor
  - [Grafana](/operator-guides/sn-monitor.md) with integrated Dashboards
  - [Prometheus](/operator-guides/configure/monitoring/prometheus.md)
  - [AlertManager](/operator-guides/configure/monitoring/alertmanager.md)
- Manage
  - StreamNative Console: provide new user experience with new UI.
  - pulsarctl CLI tool
- [Backup and restore tools](/operator-guides/configure/backup-restore-metadata-tool.md)
- Pulsar E2E/SLA latency monitoring tool

For more information about the StreamNative Platform V1.0.0 release, check out the [release blog](https://streamnative.io/en/blog/release/2021-06-16-streamnative-platform).