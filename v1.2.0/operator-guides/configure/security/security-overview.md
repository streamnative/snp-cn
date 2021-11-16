---
title: Security Overview
id: security-overview
category: operator-guides
---

StreamNative Platform security model uses a flexible design and supports three security options: authentication, authorization, and network encryption. 

- StreamNative Platform authentication provides the identity for a client. It makes sure that everyone accessing the system is who they claim to be.
- StreamNative Platform authorization determines what actions the identity can perform. It protects privacy by clearly defining what authorized personnel have access to and whether they can modify data or system behavior.
- StreamNative Platform encryption ensures data is transferred and stored safely. It keeps data from being deciphered by unauthorized entities. StreamNative Platform supports enabling TLS for StreamNative Platform components and load balancer.

By default, the state of the Pulsar cluster is wide open and anyone can access it. Any client can communicate to the Pulsar cluster using a plain text service URLs and any connected clients are effectively acting as “super-users” able to perform all actions. This means your messages are coming in clear text without authentication. Your cluster is not secure.

Without enabling TLS and authorization security on StreamNative Platform, you had better not expose your Pulsar cluster on the public internet.

This guide covers:
- [Authentication and Authorization](/operator-guides/configure/security/security-auth.md)
- [Enable TLS on StreamNative Platform components](/operator-guides/configure/security/network-encryption/tls-proxy.md)
- [Enable TLS on load balancer](/operator-guides/configure/security/network-encryption/tls-load-balancer.md)
- [Audit log](/operator-guides/configure/security/audit-log.md)