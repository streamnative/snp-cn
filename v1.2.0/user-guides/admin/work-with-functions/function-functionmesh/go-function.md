---
title: 运行 Go 函数
id: go-function
category: user-guides
---


本文介绍了如何使用 Function Mesh 来创建 Go 函数。

# 先决条件

- Apache Pulsar 2.8.0 或更高版本

- Function Mesh v0.1.3 或更高版本

# 步骤

按照如下步骤操作，使用 Function Mesh 创建 Go 函数：

1. 使用 YAML 文件定义 Go 函数，并保存 YAML 文件。

   - 如下示例显示了如何使用名为 `function://my-tenant/my-ns/my-function@0.1` 的 JAR 包将 `go-function-sample` 函数发布到 Pulsar 集群。

        ```yaml
        apiVersion: compute.functionmesh.io/v1alpha1
        kind: Function
        metadata:
          name: go-function-sample
          namespace: default
        spec:
          image: streamnative/pulsar-functions-go-runner:2.7.1 # using go function runner
          className: exclamation_function.ExclamationFunction
          forwardSourceMessageProperty: true
          MaxPendingAsyncRequests: 1000
          replicas: 1
          maxReplicas: 5
          logTopic: persistent://public/default/logging-function-logs
          input:
            topics:
            - persistent://public/default/go-function-input-topic
            typeClassName: java.lang.String
          output:
            topic: persistent://public/default/go-function-output-topic
            typeClassName: java.lang.String
          pulsar:
            pulsarConfig: "test-pulsar"
          golang:
            go: go_func_all
            goLocation: ""
            # use package name:
            # goLocation: function://public/default/nul-test-go-function@v1
            # to be delete & use admission hook
        ```

   - 如下示例显示了如何使用 Docker 镜像将 `go-function-sample` 函数发布到 Pulsar 集群。

      ```yaml
      apiVersion: compute.functionmesh.io/v1alpha1
      kind: Function
      metadata:
        name: go-function-sample
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
          - persistent://public/default/go-function-input-topic
          typeClassName: java.lang.String
        output:
          topic: persistent://public/default/go-function-output-topic
          typeClassName: java.lang.String
        pulsar:
          pulsarConfig: "test-pulsar"
        golang:
          go: go_func_all
          goLocation: ""
          # use package name:
          # goLocation: function://public/default/nul-test-go-function@v1
          # to be delete & use admission hook
      ```

2. 使用 YAML 文件来创建 Go 函数。

    ```bash
    kubectl apply -f /path/to/YAML/file
    ```

3. 检查 Go 函数是否创建成功。

    ```bash
    kubectl get all
    ```