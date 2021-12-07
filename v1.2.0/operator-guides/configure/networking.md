---
title: 配置联网
id: networking
category: operator-guides
---

StreamNative Platform 提供了 `ingress` 组件提供以下服务。 

- Pulsar Proxy
- StreamNative 控制台
- Grafana

# 访问类型

你可以根据场景配置不同类型的 Pulsar 服务，如 `ClusterIP`、`NodePort` 和 `LoadBalancer`，并对不同类型使用不同的访问方法。

- `ClusterIP ` 通过集群的内部 IP 暴露服务。当选择这种类型的服务时，只能通过以下方法在集群内访问服务。
  - ClusterIP + ServicePort
  - 服务域名 (`${serviceName}.${namespace}`) + ServicePort
  
- `LoadBalancer`：客户端使用 Kubernetes 服务商的负载均衡器连接到 Pulsar proxy、Grafana、StreamNative 控制台。
- `NodePort`：如果没有 LoadBalancer，可以选择通过 NodePort 暴露服务。NodePort 通过节点的 IP 地址和静态端口来暴露服务。可以通过 NodeIP + NodePort 从集群外部访问 `NodePort` 服务。

以下是运行命令获得 Pulsar 服务信息的例子。 

**例子**

```
kubectl get services pulsar-broker -n snpe
NAME                         TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)                       AGE
pulsar-broker           ClusterIP      None             <none>        8080:31062/TCP,6650:32198/TCP             2m22s
```

上面的例子描述了 `snpe` 命名空间中 Pulsar broker 服务的信息。类型是 `ClusterIP`。服务端口是 `8080` 和 `6650`。`NodePort` 是`31062` 和 `32198`。

# 使用负载均衡器访问 Pulsar 集群

要实现通过负载均衡器从外部访问 Pulsar 集群，需完成以下步骤：

1. 在 Pulsar 的 YAML 配置文件中设置以下内容。

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

2. 让新配置生效。

    ```
    helm upgrade -f /path/to/your/file.yaml CLUSTER_NAME $PULSAR_CHART/
    ```

3. 获得外部 IP 地址或 URL。 

    ```
    kubectl get svc/RELEASE_NAME-sn-platform-proxy-ingress -n KUBERNETES_NAMESPACE
    ```

    然后，通过 `proxy-ingress` HOST/IP 地址访问 Pulsar 集群。非 TLS 访问的端口为 `8080/6650`，TLS 访问的端口为 `443/6651`。

#  使用负载均衡器访问 StreamNative 控制器和 Grafana

StreamNative Platform 为客户端访问 StreamNative 控制台和 Grafana 服务提供了相同的接口。

要实现通过负载均衡器从外部访问 StreamNative 控制台和 Grafana，需完成以下步骤：

1. 在 Pulsar 的 YAML 配置文件中设置以下内容。

    ```
    ## Ingresses for exposing monitoring/management services publicly
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

2. 让新配置生效。

    ```
    helm upgrade -f /path/to/your/file.yaml CLUSTER_NAME $PULSAR_CHART/
    ```

3. 获得外部 IP 地址或 URL。 

    ```
    kubectl get svc/RELEASE_NAME-sn-platform-nginx-ingress-controller -n KUBERNETES_NAMESPACE
    ```

    可以通过 `nginx-ingress-controller` 的 HOST/IP 地址访问 StreamNative 控制台和 Grafana。相关端口为 `80`。 
    - StreamNative 控制台 URL：`http://[nginx-ingress-HOST]/`
    - Grafana URL：`http://[nginx-ingress-HOST]/grafana`
