---
title: Restart Pulsar clusters
id: restart-cluster
category: operator-guides
---

You may encounter situations when it is necessary to restart a Pulsar cluster. For example,  to apply a license change or to apply a credentials change in a Kubernetes Secret.

To restart a Pulsar cluster, follow these steps.

1. Find the name of the StatefulSet corresponding to the Pulsar cluster that you want to restart.

    ```
    kubectl get statefulset --namespace KUBERNETES_NAMESPACE
    ```

2. Restart the Pulsar cluster by using the StatefulSet name obtained from the previous step.

    ```
    kubectl rollout restart statefulset/STATEFULSET_NAME --namespace KUBERNETES_NAMESPACE
    ```