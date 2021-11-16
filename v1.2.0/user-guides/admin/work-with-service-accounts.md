---
title: Work with service accounts
id: work-with-service-accounts
category: user-guides
---

Service accounts are created for automation purposes, such as to authenticate service accounts which operate on your clusters. This document describes how to create, check, authenticate, and delete service accounts using the StreamNative Console.

# Create service accounts

To create a service account, follow these steps.

1. From the left navigation pane, click **Manage** > **Service Accounts**.

2. Click **Create Service Account** and a dialog box is displayed.
   
   ![](../../image/create-service-account.png)

3. Configure whether to grant the service account the Admin role for accessing to a namespace or tenant.

4. Enter a name for the service account and then click **Confirm**.

# View service account information

To view details about the service account, follow these steps.

1. From the left navigation pane, click **Manage** > **Service Accounts**.

2. Select the **TOKEN** tab to see details about the service account.

    <table>
    <tr>
    <td>
    Field
    </td>
    <td>Description
    </td>
    </tr>
    <tr>
    <td>Name
    </td>
    <td>The name of the service account.
    </td>
    </tr>
    <tr>
    <td>Pulsar Role Name 
    </td>
    <td>The email address of the service account. 
    </td>
    </tr>
    <tr>
    <td>Token
    </td>
    <td>The token for the service account. This field is only available on the <strong>TOKEN</strong> tab.
    </td>
    </tr>
    <tr>
    <td>Organization
    </td>
    <td>The organization where the service account is created.
    </td>
    </tr>
    <tr>
    <td>Status
    </td>
    <td>The status of the service account.
    </td>
    </tr>
    <tr>
    <td>Admin
    </td>
    <td>Whether the service account is an administrator of a namespace or tenant.
    </td>
    </tr>
    <tr>
    <td>Actions
    </td>
    <td>The actions that can be performed on the service account. Currently, it only supports deleting the service account.
    </td>
    </tr>
    </table>

# Authenticate service accounts

You can use a token to authenticate a service account. To authenticate service accounts, follow these steps.

1. From the left navigation pane, click **Manage** > **Service Accounts**.

2. Select the **TOKEN** tab and then click the **Download** link of the token or click the **Copy** icon to download or copy the token of the service account to your local directory.

   ![](../../image/download-token.png)

Then, you can use the token to authenticate the service account when connecting to a Pulsar cluster.

# Delete service accounts

To delete a service account, follow these steps.

1. From the left navigation pane, click **Manage** > **Service Accounts**.

2. Click the **Delete** icon in the **Actions** column. A dialog box is displayed, asking _Are you sure you want to delete this service account?_

   ![](../../image/delete-service-accounts.png)

3. Click **Confirm**.