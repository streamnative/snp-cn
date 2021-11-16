---
title: Configure storage
id: storage
category: operator-guides
---

# Local PVs and storage classes

> **Note**
> 
> If you deploy a local Kubernetes cluster, you need to configure the local [PersistentVolume (PV)](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) and storage classes for persisting data to your local storage.

Pulsar cluster components such as the BookKeeper and ZooKeeper require the persistent storage of data. To persist data in Kubernetes, you need to use PVs. A PV contains the details of the storage that is available for use by the Pulsar cluster. A PV can be provisioned by an administrator statically or dynamically using [StorageClass](https://kubernetes.io/docs/concepts/storage/storage-classes/). A StorageClass provides a way for administrators to describe the "classes" of storage they offer. Different classes might map to Quality-of-Service (QoS) levels, to backup policies, or to arbitrary policies determined by the cluster administrators.

PVs and Pods are bound by [PersistentVolumeClaim (PVC)](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistentvolumeclaims). A PersistentVolumeClaim (PVC) is a request for storage by a user. It is similar to a Pod. Pods consume node resources and PVCs consume PV resources.

To configure the local PVs and storage classes, follow these steps.

1. Preallocate local storage in each cluster node.

    The example creates five Solid State Drive (SSD) and Hybrid Hard Drive (HDDs) volumes respectively.

    > **Note**
    > 
    > This code example is just for the test environment. You can configure your local storage based on your production environment.

    ```
    #!/bin/bash
    for i in $(seq 1 5); do
      mkdir -p /mnt/ssd-bind/vol${i}
      mkdir -p /mnt/ssd/vol${i}
      mount --bind /mnt/ssd-bind/vol${i} /mnt/ssd/vol${i}
    done
    for i in $(seq 1 5); do
      mkdir -p /mnt/hdd-bind/vol${i}
      mkdir -p /mnt/hdd/vol${i}
      mount --bind /mnt/hdd-bind/vol${i} /mnt/hdd/vol${i}
    done
    ```

2. Install the local volume provisioner.

    > **Note**
    >
    > The local volume provisioner manages the PV lifecycle for pre-allocated disks by detecting and creating PVs for each local disk on the host, and then cleaning up the disks when released. It does not support dynamic provisioning.

    1. Define a YAML file to configure the local volume provisioner.

        [Here](https://github.com/streamnative/examples/tree/master/platform) is an example of the YAML file used for configuring the local volume provisioner.

    2. Apply the YAML file to install the local volume provisioner.

        ```
        kubectl apply -f /path/to/local-volume-provisioner/file.yaml
        ```

3. Verify that the local volume provisioner is created successfully.

     ```
     kubectl get po -n KUBERNETES_NAMESPACE |grep local-volume
     ```

4. Verify that all PVs are created successfully.

     ```
     kubectl get pv
     ```

5. Verify that all storage classes are created successfully.

     ```
     kubectl get storageclasses
    ```

# Kubernetes default StorageClass

If you do not provide the `spec.storageClassName` in the CR, the Pulsar operator uses the default storage class.

Use the following command to get the name of the current default storage class:

```
kubectl get sc
```

To use the Kubernetes default storage class, it is recommended to set the following properties on the default StorageClasses.

- `volumeBindingMode: WaitForFirstConsumer`
- `reclaimPolicy: Retain`
- `allowVolumeExpansion: true` (required field for the production deployments)