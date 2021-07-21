---
title: Work with tenants
id: work-with-tenants
category: user-guides
---

Pulsar was created from the ground up as a multi-tenant system. To support multi-tenancy, Pulsar has a concept of tenants. Tenants can be spread across clusters and can each have their own authentication and authorization scheme applied to them.

This document describes how to create and manage tenants for a cluster using the pulsarctl CLI tool or the StreamNative Console.

# Work with tenants using pulsarctl CLI tool

You can create, update, and delete tenants using the pulsarctl CLI tool. For a full list of supported operations on tenants, see [pulsarctl command reference](https://docs.streamnative.io/pulsarctl/v2.7.0.7/#-em-update-em--32).

Before using the pulsarctl CLI tool, you need to connect to a Pulsar cluster. For details, see [here](/user-guides/connect/connect-pulsar-cluster/cli-tools/connect-pulsarctl.md).

## Create tenants

After creating a Pulsar cluster, you can create tenants for the Pulsar cluster. When creating a tenant, you need to use `-cluster` or `-c` option to specify the target cluster for the tenant.

This example shows how to create a tenant named `example-tenant` for the `example-cluster` cluster with the admin role. If you do not configure the admin role when creating the tenant, you cannot perform follow-up operations on the target tenant, such as updating or deleting tenants.

**Input**

```bash
pulsarctl tenants create example-tenant -r bot@matrix.auth.streamnative -c example-cluster
```

**Output**

```shell
Create tenant example-tenant successfully
```

## Update tenants

When you want to add more admin roles for a tenant, you can use the `pulsarctl tenants update` command to update the target tenant.

This example shows how to update the admin role for `example-tenant`.

**Input**

```bash
pulsarctl tenants update --admin-roles (bot) --admin-roles (bot1) example-tenant -c example-cluster
```

**Output**

```bash
Update tenant example-tenant successfully
```

## Delete tenants

When you want to remove a tenant from a Pulsar cluster, you can delete it. If a tenant is associated with any resources, you cannot delete the tenant. In this case, you must delete its associated resources first.

This example shows how to delete `example-tenant`.

**Input**

```
pulsarctl tenants delete example-tenant
```

**Output**

```
Delete tenant example-tenant successfully
```

# Work with tenants using StreamNative Console

You can create, update, and delete tenants using the StreamNative Console.

## Create tenants

To create a tenant using the StreamNative Console, follow these steps.

1. From the left navigation pane, click **Tenants**.

2. Click **New Tenant** and a dialog box is displayed.
   
   ![](../../image/create-tenant.png)

3. Configure the tenant, as listed below.

    <table>
    <tr>
    <td>
    Field
    </td>
    <td>Descrption
    </td>
    </tr>
    <tr>
    <td>Tenants
    </td>
    <td>
    Enter a name for the tenant. The tenant name is a string of characters, supporting lowercase letters (a-z), numeric characters (0-9), and the special character hyphen (_).
    </td>
    </tr>
    <tr>
    <td>Allowed Clusters
    </td>
    <td>
    Select the cluster (from the list) to which the tenant belongs.
    </td>
    </tr>
    <tr>
    <td>Admin Roles
    </td>
    <td>
    Select one or more users or service accounts as the administrators for the tenant. 
    </td>
    </tr>
    </table>

4. Click **Confirm**.

## Update tenants

When you want to create namespaces for a tenant or add or delete admin roles for a tenant, you can update the tenant. To update a tenant using the StreamNative Console, follow these steps.

1. From the left navigation pane, click **Tenants**.

2. To enter the page for editing the tenant, do one of the followings.

   - Click the **Edit** icon in the **Actions** column.
   - Click the link of the tenant name.

   ![](../../image/edit-tenant.png)

3. Select the **NAMESPACES** tab and then click **New Namespace** to add more namespaces for the tenant. For details about how to create a namespace, see [create namespace](/user-guides/admin/work-with-namespaces.md#create-namespaces).

4. Select the **CONFIGURATION** tab to update tenant configurations.

   * To delete administrators, click the **Delete** icon of an existing administrator.
   * To add administrators, click **New Role** and then select one or more users or service accounts from the drop-down list.

## Delete tenants

To delete a tenant using the StreamNative Console, follow these steps.

1. From the left navigation pane, click **Tenants**.

2. the **Edit** icon in the **Actions** column or click the link of the tenant name and a page is displayed.

3. Click **Delete Tenant** and a dialog box is displayed, asking “_Are you sure you want to delete this?_”

   ![](../../image/delete-tenant.png)

4. Enter the tenant name and then click **Confirm**.