---
title: 重启 Pulsar 集群
id: restart-cluster
category: operator-guides
---

你可能会遇到需要重启 Pulsar 集群的情况，例如在 Kubernetes Secret 中更新许可证或是更改凭证。

按照如下步骤重启 Pulsar 集群：

1. 找到要重启的 Pulsar 集群所对应的 StatefulSet 名称。

    ```
    kubectl get statefulset --namespace KUBERNETES_NAMESPACE
    ```

2. 使用在上一步获取的 StatefulSet 名称重启 Pulsar 集群。

    ```
    kubectl rollout restart statefulset/STATEFULSET_NAME --namespace KUBERNETES_NAMESPACE
    ```