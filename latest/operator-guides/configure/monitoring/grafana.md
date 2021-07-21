---
title: Configure Grafana
id: grafana
category: operator-guides
---

[Grafana](https://grafana.com/) is a component in StreamNative Platform. It provides observability for StreamNative Platform. Grafana provides dashboards including brokers, bookies, zookeeper, proxy, topic, and messages. 

By default, Grafana is enabled with StreamNative Platform. To disable it, you can set `monitoring.grafana: false` in the Pulsar cluster configuration YAML file.

# Configure Grafana

You can configure the requested and limited CPU and memory for Grafana in the Pulsar cluster configuration YAML file. Then, you can use the `helm upgrade` command to restart the StreamNative Platform to make updates effective.

```
grafana:
  resources:
    # keep request = limit to keep this container in guaranteed class
    requests:
      cpu: 
      memory: 
    limits:
      cpu: 
      memory: 
```

# Expose Grafana service

StreamNative Platform supports exposing the Grafana service using the [LoadBalancer](/operator-guides/configure/networking.md#loadbalancer) or [NodePort](/operator-guides/configure/networking.md#nodeport). 

1. Enable Grafana and configure the external domain in the Pulsar cluster configuration YAML file.

    ```
    Ingress:
        controller:
        enabled: true
    control_center:
        enabled: true
        # Set external domain of the load balancer of ingress controller
        # external_domain: 
        # external_domain_scheme: https://
    ```

2. Execute the following command to get the external IP address of the Grafana service.

    ```
    kubectl get svc/RELEASE_NAME-sn-platform-nginx-ingress-controller -n KUBERNETES_NAMESPACE
    ``` 

3. Visit `http://EXTERNAL_IP/grafana` in your browser and log in to it by using the username and password. By default, both the username and password are `pulsar`. 

> **Note**
> 
> Ensure to change the password once you log in to the Grafana Dashboard.