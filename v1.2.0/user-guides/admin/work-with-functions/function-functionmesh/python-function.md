---
title: Run Python Functions
id: python-function
category: user-guides
---

This document describes how to create a Python Function using Function Mesh.

# Prerequisites

- Apache Pulsar 2.8.0 or higher

- Function Mesh v0.1.3 or higher

# Procedures

To create a Python Function using Function Mesh, follow these steps.

1. Define a Python Functions by using a YAML file and save the YAML file.

   - This example shows how to publish a `python-function-sample` Functions to a Pulsar cluster by using a JAR package called `function://my-tenant/my-ns/my-function@0.1`.

        ```yaml
        apiVersion: compute.functionmesh.io/v1alpha1
        kind: Function
        metadata:
          name: python-function-sample
          namespace: default
        spec:
          image: streamnative/pulsar-functions-python-runner:2.7.1 # using python function runner
          className: exclamation_function.ExclamationFunction
          forwardSourceMessageProperty: true
          MaxPendingAsyncRequests: 1000
          replicas: 1
          maxReplicas: 5
          logTopic: persistent://public/default/logging-function-logs
          input:
            topics:
            - persistent://public/default/python-function-input-topic
            typeClassName: java.lang.String
          output:
            topic: persistent://public/default/python-function-output-topic
            typeClassName: java.lang.String
          pulsar:
            pulsarConfig: "test-pulsar"
          python:
              py: exclamation_function.py
              pyLocation: ""
              # use package name:
              # pyLocation: function://public/default/nul-py-test-function@v1
          # to be delete & use admission hook
        ```

   - This example shows how to publish a `python-function-sample` Functions to a Pulsar cluster by using a Docker image.

      ```yaml
      apiVersion: compute.functionmesh.io/v1alpha1
      kind: Function
      metadata:
        name: python-function-sample
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
          - persistent://public/default/python-function-input-topic
          typeClassName: java.lang.String
        output:
          topic: persistent://public/default/python-function-output-topic
          typeClassName: java.lang.String
        pulsar:
          pulsarConfig: "test-pulsar"
        python:
            py: exclamation_function.py
            pyLocation: ""
            # use package name:
            # pyLocation: function://public/default/nul-py-test-function@v1
            # to be delete & use admission hook
      ```

2. Apply the YAML file to create the Python Function.

    ```bash
    kubectl apply -f /path/to/YAML/file
    ```

3. Check whether the Python Function is created successfully.

    ```bash
    kubectl get all
    ```
