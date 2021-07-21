---
title: Audit log
id: audit-log
category: operator-guides
---

Audit logs capture, protect, and preserve authorization activity in Pulsar clusters, tenants, namespaces, and topics. Once Pulsar is up and running within a large team, it’s critical to keep an eye on who is touching data and what they’re doing with it. Structured audit logs provide an easy way to track user/application access, so you can identify potential anomalies and bad actors.

Structured audit logs enable you to capture audit logs in a set of dedicated Pulsar topics, either on a local or a remote cluster, including:

- Capture low-volume, management-related activities, such as creating or deleting tenants, namespaces or topics (enabled by default).
- Capture high-volume activities, such as produce, consume, and acknowledge events (you can enable when needed).

You can use Pulsar integrated tools, like Pulsar Functions, Pulsar SQL, and Flink SQL, to process and analyze the audit events stored in the Pulsar topics. Additionally, you can offload audit events to external data lakes or data warehouses (like Snowflake or Databricks) for analysis using Pulsar IO connectors.

# Configure audit log

The audit logger writes the audit log into a Pulsar topic using the following default settings:

- The topic name of the audit log is `persistent://sn/system/audit_log_all`.
- The audit log captures only events in the `Management` category.

To customize your Pulsar audit log, you can set configurations in the `PULSAR_PREFIX_snAuditLogConfig` section in the `values.yaml` file.

For example, to write the audit logs into different Pulsar topics based on permission settings, you can add the following configurations in the `PULSAR_PREFIX_snAuditLogConfig` section. And then events are written to `persistent://sn/system/audit_log_allowed` if they succeed in permission, and written to `persistent://sn/system/audit_log_denied` if they fail in permission.
  
```
PULSAR_PREFIX_brokerInterceptors: "audit-log"
PULSAR_PREFIX_brokerInterceptorsDirectory: "./interceptors"
PULSAR_PREFIX_snAuditLogConfig: >
      {"defaultTopics":{"allowed":"persistent://sn/system/audit_log_allowed","denied":"persistent://sn/system/audit_log_denied"}}

```

To capture [more audit log events](#event-type), add the `captured` setting in the `PULSAR_PREFIX_snAuditLogConfig` section.

To write events of different categories to separate topics, add the `routes` setting in the `PULSAR_PREFIX_snAuditLogConfig` section. 

The following example captures audit log events in the `Management` and `Produce` categories. Events in the `Produce` category are written to `persistent://sn/system/audit_log_produce_allowed` if they succeed in permission, and written to `persistent://sn-system/audit/audit_log_produce_denied` if they fail in permission.

```
PULSAR_PREFIX_brokerInterceptors: "audit-log"
PULSAR_PREFIX_brokerInterceptorsDirectory: "./interceptors"
PULSAR_PREFIX_snAuditLogConfig: >
      {"captured":{"principal://User:bob":{"srn://cluster=.*/tenant=.*/namespace=.*/topic=.*": {"category":"Management|Produce","eventType":".*"}}},”routes”:{"srn://cluster=.*/tenant=.*/namespace=.*/topic=.*":{"Produce":{"allowed":"persistent://sn/system/audit_log_produce_allowed","denied":"persistent://sn/system/audit_log_produce_denied"}}},defaultTopics":{"allowed":"persistent://sn/system/audit_log_allowed","denied":"persistent://sn/system/audit_log_denied"}}
```

When you finish configuration, upgrade your cluster with the following command:

```
helm upgrade  -f /path/to/your/file.yaml CLUSTER_NAME $PULSAR_CHART/
```

Your Pulsar broker restarts automatically after the setting, and then you can create a new namespace and check the audit log in the topic created for the audit log.

# Verify audit log

The audit log topic stores the audit log messages. To verify the audit log configuration, complete the following steps:

1. Create a new namespace in the `public` tenant.

    ```
    bin/pulsar-admin namespaces create public/audit_log
    ```

2. Check the audit log in the `persistent://sn/system/audit_log_all` topic.

    ```
    bin/pulsar-admin topics peek-messages -n 1 -s audit persistent://sn/system/audit_log_all
    ```

    If you see similar audit logs below, your audit log configuration works.

    ```json
    {"id":"11c5296d-bf17-431a-80be-79ba66ba8a35","specVersion":"0.1","category":"Management","time":"2021-06-15T04:58:41.710Z","eventType":"CreateNamespace","resourceInfo":{"resourceType":"Namespace","cluster":"RELEASE_NAME-sn-platform","tenant":"public","namespace":"audit_log"},"authenticationInfo":{"role":"admin"},"authorizationInfo":{"granted":true,"superUserAuthorization":true},"requestInfo":{"metadata":{"clientAddress":"10.225.14.43","uri":"/admin/v2/namespaces/public/audit_log","method":"PUT"}},"responseInfo":{"responseType":"SUCCESS","responseCode":204}}
    ```

# Event type

Each audit log event includes information about the event, event time, and permission status. The supported audit event types include:

Event type | Description | Category | Captured by default
|---|---|---|---
`ListClusters` | List Pulsar clusters | Describe | false
`GetCluster` | Get cluster info | Describe | false
`CreateCluster` | Create Pulsar cluster | Management | true
`UpdateCluster` | Update Pulsar cluster info | Management | true
`ListTenants` | List Pulsar tenants | Describe | false
`GetTenant` | Get tenant info | Describe | false
`CreateNamespace` | Create a new namespace | Management | true
`NewProducer` | A new producer attach the topic | Produce | false
`CloseProducer` | Close a producer | Produce | false
`Produce` | Produce messages to the broker | Produce | false
`NewConsumer` | A new consumer attach the topic | Consume | false
`CloseConsumer` | Close a consumer | Consume | false
`Consume` | Consume messages from a topic | Consume | false
`Acknowledge` | Acknowledge the message by the consumer | Consume | false