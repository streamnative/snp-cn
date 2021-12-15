---
title: 认证和授权
id: security-auth
category: operator-guides
---

StreamNative Platform 支持使用基于 [JSON Web Tokens](https://jwt.io/introduction/) 的 token 进行认证。关于如何生成 token 的详细信息，请参见[准备连接到 Pulsar 集群](/user-guides/connect/connect-pulsar-cluster/connect-prepare.md)。

认证和授权在 StreamNative Platform 中是默认启用的。当需要定制配置时，可以在自己的集群的 helm 配置文件对其进行配置。

要禁用认证和授权，需在 broker 和代理（proxy）端将 `authentication` 和 `authorization` 设置为 `false`，然后所有客户端就都可以连接到集群来生产和消费消息。