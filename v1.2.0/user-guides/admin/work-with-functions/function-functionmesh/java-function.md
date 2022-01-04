---
title: 运行 Java 函数
id: java-function
category: user-guides
---

本文介绍了如何使用 Function Mesh 来创建 Java 函数。

# 先决条件

- Apache Pulsar 2.8.0 或更高版本

- Function Mesh v0.1.3 或更高版本

# 步骤

按照如下步骤操作，使用 Function Mesh 创建 Java 函数：

1. 使用 YAML 文件定义 Java 函数，并保存 YAML 文件。

   - 如下示例显示了如何使用名为 `function://my-tenant/my-ns/my-function@0.1` 的 JAR 包将 `java-function-sample` 函数发布到 Pulsar 集群。

        ```yaml
        apiVersion: compute.functionmesh.io/v1alpha1
        kind: Function
        metadata:
          name: java-function-sample
          namespace: default
        spec:
          image: streamnative/pulsar-functions-java-runner:2.7.1 # using java function runner
          className: exclamation_function.ExclamationFunction
          forwardSourceMessageProperty: true
          MaxPendingAsyncRequests: 1000
          replicas: 1
          maxReplicas: 5
          logTopic: persistent://public/default/logging-function-logs
          input:
            topics:
            - persistent://public/default/java-function-input-topic
            typeClassName: java.lang.String
          output:
            topic: persistent://public/default/java-function-output-topic
            typeClassName: java.lang.String
          pulsar:
            pulsarConfig: "test-pulsar"
          java:
            extraDependenciesDir: random-dir/
            jar: my-function.jar # the package will download as this filename.
            jarLocation: function://my-tenant/my-ns/my-function@0.1 # function package URL
        ```

   - 如下示例显示了如何使用 Docker 镜像将 `java-function-sample` 函数发布到 Pulsar 集群。

      ```yaml
      apiVersion: compute.functionmesh.io/v1alpha1
      kind: Function
      metadata:
        name: java-function-sample
        namespace: default
      spec:
        image: streamnative/example-function-image:latest # using function image here
        className: exclamation_function.ExclamationFunction
        forwardSourceMessageProperty: true
        MaxPendingAsyncRequests: 1000
        replicas: 1
        maxReplicas: 5
        logTopic: persistent://public/default/logging-function-logs
        input:
          topics:
          - persistent://public/default/java-function-input-topic
          typeClassName: java.lang.String
        output:
          topic: persistent://public/default/java-function-output-topic
          typeClassName: java.lang.String
        pulsar:
          pulsarConfig: "test-pulsar"
        java:
          extraDependenciesDir: random-dir/
          jar: /pulsar/example-function.jar # the package location in image
          jarLocation: "" # leave empty since we will not download package from Pulsar Packages
      ```

2. 使用 YAML 文件来创建 Java 函数。

    ```bash
    kubectl apply -f /path/to/YAML/file
    ```

3. 检查 Java 函数是否创建成功。

    ```bash
    kubectl get all
    ```
