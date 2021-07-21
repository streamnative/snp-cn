---
title: Prepare to connect to a Pulsar cluster
id: connect-prepare
category: user-guides
---

Before connecting to a Pulsar cluster, you need to get the Web service URL of the target Pulsar cluster and the token used for authentication.

* Get the Web service URL.

StreamNative Platform does not expose any external IP address to your Pulsar cluster. To get the Web service URL, you must request that your Kubernetes cluster administrator generate a Web Service URL for your Pulsar cluster. For details about how to expose the Web Service URL, see [here](/operator-guides/configure/networking.md).

* Get the token of a service account.

  1. From the left navigation pane of StreamNative Console, click **Service Accounts**.

  2. Click the **Copy** icon in the **Token** column to copy the token to your local directory.

      > **Note**
      >
      > Before getting the token of a service account, verify that the service account is authorized as a super user or an admin of the tenants and namespaces.