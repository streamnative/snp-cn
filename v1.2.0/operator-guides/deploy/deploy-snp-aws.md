---
title: Deploy StreamNative Platform on AWS
id: deploy-snp-aws
category: operator-guides
---


The Amazon Elastic Kubernetes Service (EKS) is the AWS service for deploying, managing, and scaling containerized applications with Kubernetes. This document describes how to deploy StreamNative Platform on an Amazon EKS cluster using [Terraform](https://www.terraform.io/). 

# Prerequisites

The tutorial assumes some basic familiarity with Kubernetes and `kubectl` but does not assume any pre-existing deployment.

It also assumes that you are familiar with the usual Terraform plan/apply workflow.

For this tutorial, you need to perform the following operations before deploying StreamNative Platform on an EKS cluster:

- Install [Terraform](https://learn.hashicorp.com/tutorials/terraform/install-cli?in=terraform/aws-get-started).
- Install [AWS CLI version 2](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html).  
- Install [kubectl](https://kubernetes.io/docs/tasks/tools/#kubectl) v1.16 or higher.
- Install [Helm](https://helm.sh/docs/intro/install/) 3.0 or higher.

# Install StreamNative Platform on AWS 

This section describes how to deploy StreamNative Platform on an Amazon EKS cluster using Terraform.

1. Define an EKS cluster configuration file with pre-defined StreamNative Platform.

    [Here](https://github.com/streamnative/terraform-aws-cloud/blob/master/examples/root-example/main.tf) is an example of the `main.tf` file used for configuring the EKS cluster.

2. Get AWS credentials.

    Terraform uses the AWS provider to interact with the many resources supported by AWS. You must configure the AWS provider with the proper credentials for authentication before you can use it. For detailed information, see [here](https://registry.terraform.io/providers/hashicorp/aws/latest/docs#authentication).

3. Deploy an EKS cluster with the predefined StreamNative Platform.

   1. Initialize the Terraform module in the directory containing your own version of the `main.tf` file.

		```
		terraform init
		```

	2. Review the planned actions.

		```
		terraform plan
		```
	3. Apply the configuration.

		```
		terraform apply
		```
    
		Your terminal output should indicate the plan is running and what resources will be created. Confirm the apply with a `yes`. This process should take approximately 15-20 minutes.

4. Verify that StreamNative Platform is deployed successfully.

   - Verify that the BookKeeper Controller Manager, Pulsar Controller Manage, ZooKeeper Controller Manage, and the Vault Controller Manage run properly.

       ```
       kubectl --kubeconfig /path/to/sn-platform-cluster-config get po -n sn-system
       ```

       **Output**
       ```
       NAME                                                            READY   STATUS    RESTARTS   AGE
       prometheus-operator-987955d64-4wq67                             1/1     Running   0          3d5h
       pulsar-operator-bookkeeper-controller-manager-d5d7b4668-q256l   1/1     Running   0          30h
       pulsar-operator-pulsar-controller-manager-8f7fd4569-6jh2p       1/1     Running   0          30h
       pulsar-operator-zookeeper-controller-manager-6cff55bfb7-bngtf   1/1     Running   0          30h
       vault-operator-5655985464-hjlmv                                 1/1     Running   0          3d5h
       ```

   - Verify that the cert-manager and ExternalDNS run properly.

       ```
       kubectl --kubeconfig ~/.kube/sn-ibex-test-config get po -n kube-system
       ```

       **Output**
       ```
       NAME                                                        READY   STATUS    RESTARTS   AGE
       cert-manager-7b95979bb-7vgth                                1/1     Running   0          2d1h
       cert-manager-cainjector-69d885bf55-fczz8                    1/1     Running   0          2d1h
       cert-manager-webhook-7d8f545f9b-p5jpg                       1/1     Running   0          2d1h
       external-dns-795b8cf977-v8h6t                               1/1     Running   0          23h
       ```

   From the above outputs, you can see that all components are running well, it indicates that StreamNative Platform has been installed successfully.

# Deploy Pulsar clusters

To deploy a Pulsar cluster, follow these steps.

1. Add the environment variables for the Pulsar Chart directory, Pulsar cluster name, and Kubernetes namespace.

    ```
    # Define your pulsar cluster name
    export RELEASE_NAME=PULSAR_CLUSTER
    # Define the k8s namespace to install the pulsar cluster
    export NAMESPACE=KUBERNETES_NAMESPACE
    ```

2. Define a Pulsar cluster configuration file.

    [Here](https://github.com/streamnative/examples/blob/master/platform/values_cluster.yaml) is an example of the YAML file used for configuring the Pulsar cluster on StreamNative Platform.

3. Use the `helm install` command to deploy a Pulsar cluster.

    ```
    helm install \
    --namespace $NAMESPACE \
    $RELEASE_NAME \
    --repo https://charts.streamnative.io sn-platform \
    --values /path/to/pulsar/file.yaml \
    --set initialize=true
    --kubeconfig=/path/to/sn-platform-cluster-config
    ```

    This table lists options used for deploying a Pulsar cluster.
    | Option | Description |
    | --- | --- |
    | `namespace` | The Kubernetes namespace. |
    | `repo` | The URL of the StreamNative Platform Pulsar chart. |
    | `values` | The URL of the YAML file used for configuring the Pulsar cluster. |
    | `kubeconfig` | The URL of the file that is used to configure access to the EKS cluster. |

    > **Note**
    >
    > If this is your first time installing the Helm chart, you must override the initialized values to `true` (setting the `--set initialize=true` option).

# Clean up resources

Remember to destroy any resources you create once you do not need the StreamNative Platform and Pulsar cluster. Execute the `terraform destroy` command and confirm with `yes` in your terminal.

```
terraform destroy
```

> **Note**
> 
> If some resources (like the authentication ConfigMap and namespaces) cannot be removed, you can use the `terraform state rm ` command to remove them from the `terraform.tfstate` file and then use the `terraform destroy` command to remove all resources.
