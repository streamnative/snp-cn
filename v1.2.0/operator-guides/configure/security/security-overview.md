---
title: 安全概览
id: security-overview
category: operator-guides
---

StreamNative Platform 安全模型具有灵活的设计，支持三种安全选项：身份验证、授权和网络加密。

- StreamNative Platform 身份验证为客户端提供身份，确保访问系统的每个人的身份的正确性。
- StreamNative Platform 授权决定了对应身份可以执行的操作。通过明确定义授权人员可以访问的内容以及他们是否可以修改数据或系统行为来保护隐私性。
- StreamNative Platform 加密确保了数据可以被安全地传输和存储，防止数据被未经授权的实体所破译。StreamNative Platform 支持为 StreamNative Platform 组件和负载均衡器启用 TLS。

默认情况下，Pulsar 集群的状态是完全开放的，任何人都可以访问。任何客户端都可以使用纯文本服务 URL 与 Pulsar 集群通信，并且任何连接的客户端都有效地充当“超级用户”来执行所有操作。这意味着你的消息会以明文的形式发送，且无需身份验证。你的集群是不安全的。

如果不在 StreamNative Platform 上启用 TLS 和授权安全，最好不要将 Pulsar 集群暴露在公共互联网上。

本指南包括如下内容：
- [认证和授权](/operator-guides/configure/security/security-auth.md)
- [在 StreamNative Platform 组件上启用 TLS](/operator-guides/configure/security/network-encryption/tls-proxy.md)
- [在负载平衡器上启用 TLS](/operator-guides/configure/security/network-encryption/tls-load-balancer.md)
- [审计日志](/operator-guides/configure/security/audit-log.md)