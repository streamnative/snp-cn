---
title: Log in to StreamNative Console
id: login-console
category: user-guides
---

StreamNative Console is a web-based GUI management and monitoring tool that helps Pulsar users manage tenants, namespaces, topics, subscriptions, and so on.

After [deploying StreamNative Platform](/operator-guides/deploy/sn-deploy.md) and a [Pulsar cluster](/operator-guides/deploy/sn-deploy.md#deploy-pulsar-clusters), you can log in to StreamNative Console to manage and monitor Pulsar resources.

To log in to StreamNative Console, follow these steps.

1. Execute the following command to expose the StreamNative Console service to a localhost address.

   ```
   kubectl port-forward RELEASE_NAME-pulsar-streamnative-console-0 9527:9527 -n KUBERNETES_NAMESPACE
   ```

2. Visit the StreamNative Console at http://localhost:9527.

3. Enter the username and user password and then click **Login** to log in to StreamNative Console. 

   By default, an `admin` user is created for StreamNative Console. You can execute the following command to get the password.

   ```
   kubectl get secret RELEASE_NAME-sn-platform-vault-console-admin-passwd -o=jsonpath='{.data.password}' -n KUBERNETES_NAMESPACE | base64 --decode; echo
   ```

![](../../image/log-in-console.png)

After logging in to StreamNative Console, you can manage the following resources:

- [Service accounts](/user-guides/admin/work-with-service-accounts.md)
- [Users](/user-guides/admin/work-with-users.md)
- [Tenants](/user-guides/admin/work-with-tenants.md)
- [Namespaces](/user-guides/admin/work-with-namespaces.md)
- [Topics](/user-guides/admin/work-with-topics.md)
