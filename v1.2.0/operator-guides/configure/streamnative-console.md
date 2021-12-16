---
title: 配置 StreamNative 控制台
id: streamnative-console
category: operator-guides
---

StreamNative 控制台是基于 Web 的工具，用于配置和管理 Apache Pulsar。使用控制台，可以查看环境和相关集群的状况，并配置和管理实例、租户、命名空间和主题。

下表列出了在部署 StreamNative Platform 之前，需要为 StreamNative 控制台配置的项目。

| 项目 | 描述 | 默认值  |
| --- | --- | --- |
| `DEFAULT_ORGANIZATION` | 组织名称。不支持特殊字符，例如句号（。）、斜线（/）、美元符号（$）等。 | N/A |
| `INSTANCE_NAME` | 实例名称。不支持特殊字符，例如句号（。）、斜线（/）、美元符号（$）等。 | N/A |
| `GOOGLE_OAUTH2_ENABLED` | 谷歌 OAuth2 登录设置。 <br> - `true`：支持使用谷歌账户登录 StreamNative 控制台。<br>- `false`：不支持使用谷歌账户登录 StreamNative 控制台。这种情况下，可以使用用户名和密码登录到 StreamNative 控制台。 | `false` |
| `GOOGLE_CLIENT_ID` | 应用程序的客户端 ID。关于如何创建谷歌 OAuth 客户端 ID 的详细信息，请参见[创建授权凭证](https://developers.google.com/identity/protocols/oauth2/web-server#creatingcred)。 | N/A |
| `GOOGLE_CLIENT_SECRET` | 应用程序的客户端 secret。                                     | N/A |
| `GOOGLE_RESTRICT_DOMAIN_SUFFIXES_NAME` | 域名限制。如果设置该项，则只有符合该规则的域名才能登录到 StreamNative 控制台。 | N/A |
| `GOOGLE_REDIRECT_URI` | 用户完成授权流程后，API 服务器将用户重定向到的地址。         | N/A |
| `REDIRECT_SCHEME` | 重定向 scheme。每个 URI 以一个 scheme 名称开始，该名称指该 scheme 中分配标识符的规范。例如，如果前端域名是 `http://frontend-host:9527`，那么 `REDIRECT_SCHEME` 就是 `http`。 | N/A |
| `REDIRECT_HOST` | 重定向 URI 的主机。例如，如果前端域名是 `http://frontend-host:9527`，那么 `REDIRECT_HOST` 就是 `frontend-host`。 | N/A |
| `REDIRECT_PORT` | 重定向 URI 的端口 ID。例如，如果前端域名是 `http://frontend-host:9527`，那么 `REDIRECT_PORT` 就是 `9527`。 | N/A |
