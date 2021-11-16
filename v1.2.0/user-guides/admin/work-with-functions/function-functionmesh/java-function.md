---
title: Run Java Functions
id: java-function
category: user-guides
---

This document describes how to create a Java Function using Function Mesh.

# Prerequisites

- Apache Pulsar 2.8.0 or higher

- Function Mesh v0.1.3 or higher

# Procedures

To create a Java Function using Function Mesh, follow these steps.

1. Define Java Functions by using a YAML file and save the YAML file.

   - This example shows how to publish a `java-function-sample` Functions to a Pulsar cluster by using a JAR package called `function://my-tenant/my-ns/my-function@0.1`.

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

   - This example shows how to publish a `java-function-sample` Functions to a Pulsar cluster by using a Docker image.

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

2. Apply the YAML file to create the Java Functions.

    ```bash
    kubectl apply -f /path/to/YAML/file
    ```

3. Check whether the Java Functions is created successfully.

    ```bash
    kubectl get all
    ```
