---
title: Configure networking
id: networking
category: operator-guides
---

StreamNative Platform provides the `ingress` component to expose following services.

- Pulsar Proxy
- StreamNative Console
- Grafana

# Access types

You can configure Pulsar service with different types according to the scenarios, such as `ClusterIP`, `NodePort` and `LoadBalancer`, and use different access methods for different types.

- `ClusterIP` exposes services through the internal IP of the cluster. When selecting this type of service, you can only access it within the cluster by the following methods:

  - ClusterIP + ServicePort
  - Service domain name (`${serviceName}.${namespace}`) + ServicePort

- `LoadBalancer`: clients connect to Pulsar proxies, Grafana, StreamNative Console using the Kubernetes provider’s load balancer. 
- `NodePort`: If there is no LoadBalancer, you can choose to expose the service through NodePort. NodePort exposes services through the node's IP address and static port. You can access a `NodePort` service from outside of the cluster by requesting NodeIP + NodePort

Here is an example about obtaining the Pulsar services information by running the following command.

**Example**

```
kubectl get services pulsar-broker -n snpe
NAME                         TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)                       AGE
pulsar-broker           ClusterIP      None             <none>        8080:31062/TCP,6650:32198/TCP             2m22s
```

The above example describes the information of the Pulsar broker service in the `snpe` namespace. The type is `ClusterIP`. The ServicePort is `8080` and `6650`. The `NodePort` is `31062` and `32198`.

# Access Pulsar cluster using load balancer

To allow external access to the Pulsar cluster using load balancers, follow these steps.

1. Set the following in the Pulsar configuration YAML file.

    ```
    ## Ingresses for exposing Pulsar services
    ingress:
    ## Ingresses for exposing pulsar service publicly
    proxy:
        enabled: true
        type:
        tls:
        # If you enable proxy tls, you should enable this too.
        enabled: true
        annotations:
        # If you use aws lb, recommend to add this
        service.beta.kubernetes.io/aws-load-balancer-type: nlb
    ```

2. Apply the new configuration.

    ```
    helm upgrade -f /path/to/your/file.yaml CLUSTER_NAME $PULSAR_CHART/
    ```

3. Get the external IP address or the URL. 

    ```
    kubectl get svc/RELEASE_NAME-sn-platform-proxy-ingress -n KUBERNETES_NAMESPACE
    ```

    Then you can access the Pulsar cluster through the `proxy-ingress` HOST/IP address. The relative port should be `8080/6650` for non-TLS access and `443/6651` for TLS access.

# Access StreamNative Console and Grafana using load balancer

StreamNative Platform provides the same interface for the client accessing StreamNative Console and Grafana services.

To allow external access to the StreamNative Console and Grafana using load balancers, follow these steps.

1. Set the following in the Pulsar configuration YAML file.

    ```
    ingress:
  controller:
    enabled: true
    annotations: 
      # If you use aws lb, recommend to add this
      service.beta.kubernetes.io/aws-load-balancer-type: nlb
  control_center:
    enabled: true
    # Set external domain of the load balancer of ingress controller
    # external_domain: 
    # external_domain_scheme: https://
    ```

2. Apply the new configuration.

    ```
    helm upgrade -f /path/to/your/file.yaml CLUSTER_NAME $PULSAR_CHART/
    ```

3. Get the external IP address or the URL. 

    ```
    kubectl get svc/RELEASE_NAME-sn-platform-nginx-ingress-controller -n KUBERNETES_NAMESPACE
    ```

    You can access StreamNative Console and Grafana through `nginx-ingress-controller` HOST/IP address. The relative port is `80`.
    - StreamNative Console URL: `http://[nginx-ingress-HOST]/`
    - Grafana URL: `http://[nginx-ingress-HOST]/grafana`
