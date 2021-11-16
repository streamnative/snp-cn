---
title: Authentication and Authorization
id: security-auth
category: operator-guides
---

StreamNative Platform supports authentication using tokens based on [JSON Web Tokens](https://jwt.io/introduction/). For details on how to generate token, see [prepare to connect to a Pulsar cluster](/user-guides/connect/connect-pulsar-cluster/connect-prepare.md).

Authentication and authorization are enabled by default in StreamNative Platform. When you need to customize configuration, you can configure them in your own cluster helm config file.

To disable authentication and authorization, you need to set `authentication` and `authorization` to `false` on the broker and proxy side, and then all clients can connect to the cluster to produce and consume messages.