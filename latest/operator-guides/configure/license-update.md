---
title: Update license key
id: license-update
category: operator-guides
---

You can use StreamNative Platform for a 30-day trial period without a license key. After 30 days, StreamNative Platform requires a license key. The license can be provided through a Kubernetes Secret.

To update the license key for StreamNative Platform, follow these steps.

1. Apply for your `license.key` file from StreamNative.

2. Create a Kubernetes Secret for the `license.key` file.

    For example, with the `license.key` file in the current directory.

    ```
    kubectl create secret generic streamnative-platform-license \
    --from-file=license.key=./license.key \
    --namespace <namespace>
    ```

3. Set the Secret name in your YAML configuration file. 
    
    ```
    license:
    secretName: streamnative-platform-license
    ```
4. Roll the Pulsar broker to apply the license change.

   1. Find the name of the StatefulSet corresponding to the Pulsar broker.

       ```
       kubectl get statefulset --n KUBERNETES_NAMESPACE
       ```
   2. Use the StatefulSet name, <name-of-statefulset>, from the previous step, roll the cluster.
       
       ```
       kubectl rollout restart statefulset/STATEFULSET_NAME> \
       --namespace KUBERNETES_NAMESPACE
   ```
