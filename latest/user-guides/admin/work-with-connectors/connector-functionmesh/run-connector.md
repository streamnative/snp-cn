---
title: Run connectors
id: run-connector
category: user-guides
---

This document describes how to create a connector using Fuction Mesh. 

# Prerequisites

- Deploy and connect to a [Kubernetes cluster](https://kubernetes.io/).
- Deploy a [Pulsar cluster](/operator-guides/deploy/sn-deploy.md#deploy-pulsar-clusters) in the Kubernetes cluster.
- Install the Function Mesh Operator and CRD into the Kubernetes cluster.

# Procedures

You can create them based on how you package them.

1. Define a connector by using a YAML file and save the YAML file.

   - This example defines a YAML file for a sink connector named `my-sink-sample` connector by specifying the connector package URL and the YAML file is saved as `sink-sample.yaml`.

       ```yaml
      apiVersion: compute.functionmesh.io/v1alpha1
      kind: Sink
      metadata:
        name: my-sink-package-sample
        namespace: default
      spec:
        image: streamnative/pulsar-functions-java-runner:2.7.1 # using java function runner
        className: org.example.MySink
        forwardSourceMessageProperty: true
        MaxPendingAsyncRequests: 1000
        replicas: 1
        maxReplicas: 1
        logTopic: persistent://public/default/logging-connector-logs
        input:
          topics:
          - persistent://public/default/input
          typeClassName: java.lang.String
        sinkConfig:
          myconfig: "test-config"
        pulsar:
          pulsarConfig: "test-pulsar"
        java:
          extraDependenciesDir: random-dir/
          jar: /pulsar/connectors/pulsar-io-my-sink.nar # the package will download as this filename.
          jarLocation: sink://public/default/my-sink@1.0 # connector package URL
       ```

   - This example defines a YAML file for a sink connector named `my-sink-package-sample` connector by specifying the connector Docker image URL and the YAML file is saved as `sink-sample.yaml`.

     ```yaml
      apiVersion: compute.functionmesh.io/v1alpha1
      kind: Sink
      metadata:
        name: my-sink-sample
        namespace: default
      spec:
        image: myorg/pulsar-io-my-sink:2.7.1 # using self built image
        forwardSourceMessageProperty: true
        MaxPendingAsyncRequests: 1000
        replicas: 1
        maxReplicas: 1
        logTopic: persistent://public/default/logging-connector-logs
        input:
          topics:
          - persistent://public/default/input
          typeClassName: java.lang.String
        sinkConfig:
          myconfig: "test-config"
        pulsar:
          pulsarConfig: "test-pulsar"
        java:
          extraDependenciesDir: random-dir/
          jar: /pulsar/connectors/pulsar-io-my-sink.nar # the NAR location in image.
          jarLocation: "" # leave empty since we will not download package from Pulsar Packages
     ```

2. Apply the YAML file to create the sink connector.

	```bash
	kubectl apply -f /path/to/sink-sample.yaml
	```

3. Verify that  the sink connector is created successfully.

	```bash
	kubectl get all
	```

	**Output**
	```
	NAME                                READY   STATUS      RESTARTS   AGE
	pod/my-sink-sample              1/1     Running     0          77s
	```

	From the output you can see that the sink connector is in `Running` status, which indicates that the sink connector is created successfully.
