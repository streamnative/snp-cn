---
title: 版本说明 V1.1
id: release-note-v1-1
category: release-note
---
StreamNative Platform 1.1 现已发布。

# 一般变更

- 支持使用 [Terraform](https://www.terraform.io/) 在 AWS 上部署 StreamNative Platform。使用 terraform 模块，可以提供安装了 StreamNative Platform 的 EKS 集群。详情参见[在 AWS 上部署 StreamNative Platform](/operator-guides/deploy/deploy-snp-aws.md)。
- 支持使用 [cert-manager](https://cert-manager.io/docs/) 自动生成自签名或公共签名证书。详情参见[在 StreamNative Platform 组件上启用 TLS](/operator-guides/configure/security/network-encryption/tls-proxy.md)。
- 支持在 AWS 负载均衡器上使用由 AWS Certificate Manager（ACM）管理的证书启用 TLS。详情参见[在负载均衡器上启用 TLS](/operator-guides/configure/security/network-encryption/tls-load-balancer.md)。
- 支持使用 [ExternalDNS](https://github.com/kubernetes-sigs/external-dns) 向 AWS Route53 配置域名。
