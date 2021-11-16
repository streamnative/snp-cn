---
title: Configure StreamNative Console
id: streamnative-console
category: operator-guides
---

The StreamNative Console is a web-based tool for configuring and managing Apache Pulsar. You can view your environments and associated clusters, and configure and manage instances, tenants, namespaces, and topics.

This table lists items to be configured for StreamNative Console before deploying StreamNative Platform.

| Item | Description | Default value |
| --- | --- | --- |
| `DEFAULT_ORGANIZATION` | The organization name. Does not support special characters (such as period (.), slash (/), dollar sign ($) etc.). | N/A |
| `INSTANCE_NAME` | The instance name. Does not support special characters (such as period (.), slash (/), dollar sign ($) etc.). | N/A |
| `GOOGLE_OAUTH2_ENABLED` | Google OAuth2 login setting. <br> - `true`: Supports logging in to StreamNative Console using your Google account. <br>- `false`: Does not support logging in to StreamNative Console using your Google account. In this case, you can log in to StreamNative Console using the username and password.| `false` |
| `GOOGLE_CLIENT_ID` | The client ID for your application. For details about how to create the Google OAuth client ID, see [create authorization credentials](https://developers.google.com/identity/protocols/oauth2/web-server#creatingcred). | N/A |
| `GOOGLE_CLIENT_SECRET` | The client secret for your application. | N/A |
| `GOOGLE_RESTRICT_DOMAIN_SUFFIXES_NAME` | The domain name restriction. If it is set, only the domain names that meet this rule can log in to the StreamNative Console. | N/A |
| `GOOGLE_REDIRECT_URI` | The address to which the API server redirects the user after the user completes the authorization flow. | N/A |
| `REDIRECT_SCHEME` | The redirect scheme. Each URI begins with a scheme name that refers to a specification for assigning identifiers within that scheme. For example, if the front-end domain is `http://frontend-host:9527`, then the `REDIRECT_SCHEME` is `http`. | N/A |
| `REDIRECT_HOST` | The host of the redirect URI. For example, if the front-end domain is `http://frontend-host:9527`, then the `REDIRECT_HOST` is `frontend-host`. | N/A |
| `REDIRECT_PORT` | The port ID of the redirect URI. For example, if the front-end domain is `http://frontend-host:9527`, then the `REDIRECT_PORT` is `9527`. | N/A |
