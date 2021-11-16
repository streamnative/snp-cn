---
title: Enable TLS on load balancer
id: tls-load-balancer
category: operator-guides
---

StreamNative Platform supports enabling TLS with [AWS Certificate Manager (ACM)](https://docs.aws.amazon.com/acm/latest/userguide/acm-overview.html). When you want to perform TLS termination at the load balancer, you can use certificates with ACM. ACM handles the complexity of creating, storing, and renewing public and private SSL/TLS X.509 certificates and keys that protect your AWS websites and applications.

The load balancer offloads traffic to the backend service via TCP protocols.

* One load balancer for Pulsar proxy with ports `6651/443`
  * The DNS name is `data.pulsar.example.local`
* One load balancer for nginx ingress controller with port `443`
  * The DNS name is `admin.pulsar.example.local`
* One load balancer for istio ingress( to KoP brokers ) with port `9093`
  * The DNS name is `messaging.pulsar.example.local`

> **Note**     
> Enabling TLS with ACM is not applicable to KoP, since KoP needs TLS Server Name Indication (SNI) to route traffic that requires TLS termination on the broker side rather than the load balancer side. 

To use certificates with ACM, complete the following steps.

1. [Request a public certificate from ACM](https://docs.aws.amazon.com/acm/latest/userguide/gs-acm-request-public.html) for the following domain names, and get the Amazon Resource Names (ARNs) for the certificate.

	```
	*.pulsar.example.local
	```

2. In the YAML file, enable domain, configure the annotations, and use the ARN obtained above as shown. 

   ```
   tls:
     enabled: false
     proxy:
       enabled: false

   ## Domain requested from External DNS
   domain:
     enabled: true
     suffix: pulsar.example.local

   ## Ingresses for exposing Pulsar services
   ingress:
     proxy:
       enabled: true
       external_domain: data.pulsar.example.local
       type: LoadBalancer
       tls:
         enabled: true
       annotations:
         external-dns.alpha.kubernetes.io/hostname: "data.pulsar.example.local"
         service.beta.kubernetes.io/aws-load-balancer-cross-zone-load-balancing-enabled: "true"
         service.beta.kubernetes.io/aws-load-balancer-type: nlb
         service.beta.kubernetes.io/aws-load-balancer-ssl-cert: "[ARN OF THE CERT]"
         # Enable this if the service is only accessed from the Amazon Virtual Private Cloud (Amazon VPC).
         #service.beta.kubernetes.io/aws-load-balancer-internal: 0.0.0.0/0
       extraSpec:
         externalTrafficPolicy: Local
   ## Ingresses for exposing monitoring/management services publicly
   controller:
     enabled: true
     annotations:
       external-dns.alpha.kubernetes.io/hostname: "admin.pulsar.example.local"
       service.beta.kubernetes.io/aws-load-balancer-ssl-cert: "[ARN OF THE CERT]"
       service.beta.kubernetes.io/aws-load-balancer-cross-zone-load-balancing-enabled: "true"
     extraSpec:
       externalTrafficPolicy: Local
     # flag whether to terminate the tls at the loadbalancer level
     tls:
       termination: true
   control_center:
     enabled: true
     tls:
       enabled: true
     # Set external domain for the load balancer of the ingress controller
     external_domain: admin.pulsar.example.local
     external_domain_scheme: https://
     ```

3. Apply the changes by restarting the Pulsar proxy.

    ```
    helm upgrade -f /path/to/your/file.yaml CLUSTER_NAME $PULSAR_CHART/
    ```
