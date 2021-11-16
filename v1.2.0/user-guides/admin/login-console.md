---
title: Log in to StreamNative Console
id: login-console
category: user-guides
---

StreamNative Console is a web-based GUI management and monitoring tool that helps Pulsar users manage tenants, namespaces, topics, subscriptions, and so on.

After [deploying StreamNative Platform](/operator-guides/deploy/sn-deploy.md) and a [Pulsar cluster](/operator-guides/deploy/sn-deploy.md#deploy-pulsar-clusters), you can log in to StreamNative Console to manage and monitor Pulsar resources.

# Log in to StreamNative Console using usernames and passwords

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

# Log in to StreamNative Console using Google accounts

This section describes how to log in to StreamNative Console using a Google accounts.

## Prerequisites

To log in to StreamNative Console using a Google account, you need to enable Google OAuth2 login and configure related parameters. For details, see [configure StreamNative Console](/operator-guides/configure/streamnative-console.md).

To log in to StreamNative Console using a Google account, follow these steps.

1. Execute the following command to expose the StreamNative Console service to a localhost address.

   ```
   kubectl port-forward RELEASE_NAME-pulsar-streamnative-console-0 9527:9527 -n KUBERNETES_NAMESPACE
   ```

2. Visit the StreamNative Console at http://localhost:9527.

3. Click **Log in With Google**.

   ![](../../image/google-login.png)

4. Enter your Email and then click **Next**.

5. Enter your password and then click **Next**.

6. (Optional) If Two-Factor Authentication (2FA) is enabled, enter the verification code and then click **Next**.

   ![](../../image/verification-code.png)

Then, you will be directed to the StreamNative Console GUI. And, you can manage the following resources:

- [Service accounts](/user-guides/admin/work-with-service-accounts.md)
- [Users](/user-guides/admin/work-with-users.md)
- [Tenants](/user-guides/admin/work-with-tenants.md)
- [Namespaces](/user-guides/admin/work-with-namespaces.md)
- [Topics](/user-guides/admin/work-with-topics.md)
