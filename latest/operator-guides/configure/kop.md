---
title: Configure KoP
id: kop
category: operator-guides
---

Kafka on Pulsar (KoP) brings the native Apache Kafka protocol support to Apache Pulsar by introducing a Kafka protocol handler on Pulsar brokers. By adding the KoP protocol handler to your existing Pulsar cluster, you can now migrate your existing Kafka applications and services to Pulsar without modifying the code.

To enable KoP access outside a Kubernetes cluster, you need to install [Istio]((https://istio.io/latest/about/service-mesh/)) and enable TLS.

# Install Istio for KoP access

[Istio](https://istio.io/latest/about/service-mesh/) is an open source service mesh that layers transparently onto existing distributed applications. It provides a uniform and more efficient way to secure, connect, and monitor services. StreamNative Platform uses the Istio service to expose the KoP services.

> **Note**
> 
> StreamNative Platform exposes port 9093 for the Istio service.

1. Download Istio and switch to the target directory.

    ```
    curl -L https://istio.io/downloadIstio | sh -
    cd istio-*
    ```

2. Download the Istio configuration file and install Istio.

    ```
    wget https://raw.githubusercontent.com/streamnative/examples/master/platform/istio.yaml
    bin/istioctl install -y -f ./istio.yaml
    ```

# Enable KoP

To enable KoP, follow these steps.

1. Enable TLS for KoP. for details, see [Enable TLS on KoP](/operator-guides/configure/security/network-encryption/tls-proxy.md#enable-tls-on-kop-with-manuallygenerated-certificates).

2. Set the following in the Pulsar configuration YAML file.

    ```
    # The domain name for accessing KoP outside from the Kuberbetes cluster.
    advertisedDomain: "kop.sn.dev"
    kop:
      enabled: true
    istio:
      gateway:
        selector:
          # set your istio gateway label here
          istio: "ingressgateway"
    ```

3. Apply the new configuration.

    ```
    helm upgrade -f /path/to/your/file.yaml CLUSTER_NAME $PULSAR_CHART/
    ```
