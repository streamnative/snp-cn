---
title: Release note V1.1
id: release-note-v1-1
category: release-note
---
StreamNative Platform 1.1 is now available.

# General Changes

- Support deploying StreamNative Platform on AWS using [Terraform](https://www.terraform.io/). With this terraform module, you can provision an eks cluster with StreamNative Platform installed. For details,see [Deploy StreamNative Platform on AWS](/operator-guides/deploy/deploy-snp-aws.md).
- Support generating self-signed or public-signed certificates automatically using [cert-manager](https://cert-manager.io/docs/). For details, see [Enable TLS on StreamNative Platform components](/operator-guides/configure/security/network-encryption/tls-proxy.md).
- Support enabling TLS with certificates managed by the AWS Certificate Manager (ACM) on the AWS Load Balancer. For details, see [enable TLS on load balancer](/operator-guides/configure/security/network-encryption/tls-load-balancer.md)
- Support provisioning the domain name to AWS Route53 with the [ExternalDNS](https://github.com/kubernetes-sigs/external-dns).
