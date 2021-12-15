---
title: 在 StreamNative Platform 组件上启用 TLS
id: tls-proxy
category: operator-guides
---

StreamNative Platform 支持传输层安全（TLS）协议，一种行业标准的加密协议，以保护 StreamNative Platform 组件的网络通信。

TLS 依靠密钥和证书来建立可信任连接。可以在 Pulsar proxy、控制中心和 KoP broker 来终止 TLS 。本节介绍如何为 StreamNative Platform 组件启用 TLS 加密。

StreamNative Platform 支持以下机制来启用 TLS 加密：

- 自动生成证书：用 [cert-manager](https://cert-manager.io/docs/) 自动生成证书。
- 手动生成证书：由用户生成私钥、公钥和证书颁发机构。

对于不需要使用自己的服务器端证书的场景，我们建议使用自动生成证书的方式。

# 使用 cert-manager 启用 TLS

Cert-manager 将证书和证书签发者作为资源类型添加到 Kubernetes 集群中，并简化了获取、更新和使用这些证书的过程。

在 StreamNative 平台中，cert-manager 支持从内部签发者和公共签发者授权证书。以下部分介绍了如何使用内部签发者和公共签发者生成证书，然后启用 TLS 加密。

::: tabs

 @@@ 内部签发者

要使用内部签发者生成证书（自签名），请按如下步骤操作：

1. 为 JKS cert 创建密码密钥。 

	```
	kubectl create secret generic cert-jks-passwd --from-literal=password=passwd -n KUBERNETES_NAMESPACE
	```

2. 在 Pulsar proxy 上启用 TLS。可以在 YAML 文件中将 `tls`、`proxy`、`internal_issuer` 和相关参数设置为 `true`，如下所示：

	```
	tls:

 	 enabled: true
 	 proxy:
 	   enabled: true
 	
 	certs:
 	 internal_issuer:
 	   enabled: true
 	
 	broker:
 	  # set the domain for accessing KoP 
 	  advertisedDomain: "messaging.pulsar.example.local"
 	  kop:
 	    enabled: true
 	    tls:
 		  enabled: true
 	
 	domain:
 	  enabled: true
 	  suffix: pulsar.example.local
 	
 	ingress:
 	  proxy:
 	  enabled: true
 	  tls:
 	    enabled: true
 	control_center:
 	  enabled: true
 	  tls:
 	    enabled: true
 	```
 	
 	Cert-manager 自动为自签名证书配置“certName”。

3. 重新启动 Pulsar proxy 使更改生效。

    ```
    helm upgrade -f /path/to/your/file.yaml CLUSTER_NAME $PULSAR_CHART/
    ```
    
    Cert-manager 自动生成所有证书并将它们附加到 Kubernetes pod。

4. 将 TLS CA 证书下载到本地目录。`ca.crt` 用于连接 Pulsar proxy。`truststore.jks` 用于 Kafka 客户端连接 KoP。

    ```
    kubectl get secret CLUSTER_NAME-tls-proxy -o=jsonpath='{.data.ca\.crt}' -n KUBERNETES_NAMESPACE | base64 --decode -o ca.crt
    kubectl get secret CLUSTER_NAME-tls-proxy -o=jsonpath='{.data.truststore\.jks}' -n KUBERNETES_NAMESPACE | base64 --decode -o truststore.jks
    ```

@@@

@@@ 公共签发者

要使用公共签发者（公共签名，let's encrypt）生成证书，请按如下步骤操作：

1. 为 JKS cert 创建密码密钥。

	```
	kubectl create secret generic cert-jks-passwd --from-literal=password=passwd -n KUBERNETES_NAMESPACE
	```

2. 在 [Amazon Route 53](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/Welcome.html) 中创建托管区域并获取托管 ID。

3. 在 Pulsar proxy 上启用 TLS。可以在 YAML 文件中将 `tls`、`proxy`、`public_issuer` 和相关参数设置为 `true`，如下所示：

    ```
    tls:
      enabled: true
      proxy:
        enabled: true
        untrustedCa: false

    certs:
      internal_issuer:
        enabled: false
      public_issuer:
        enabled: true
      issuers:
        acme:
          email: certs-support@streamnative.io
          server: https://acme-v02.api.letsencrypt.org/directory
          solver: route53
          solvers:
            route53:
              region: us-west-2
              hostedZoneID: [YOUR_HOSTED_ZONE_ID]

    ## Domain requested from External DNS
    domain:
      enabled: true
      ## Cert-manager generates a wildcard certificate for this domain.
      suffix: pulsar.example.local

    broker:
     # set the domain for accessing KoP 
     # Use messaging as a prefix
     # Use the domain to access kop
     advertisedDomain: "messaging.pulsar.example.local"
     kop:
       enabled: true
       tls:
         enabled: true

    ingress:
      proxy:
        enabled: true
     # Use the domain to access proxy
        external_domain: data.pulsar.example.local
        type: LoadBalancer
        tls:
          enabled: true
        annotations:
          service.beta.kubernetes.io/aws-load-balancer-type: nlb
      controller:
        enabled: true
        annotations: 
          service.beta.kubernetes.io/aws-load-balancer-type: nlb
      control_center:
        enabled: true
        tls:
          enabled: true
        # Use the domain to access control center
        external_domain: admin.pulsar.example.local
        external_domain_scheme: https://
    ```
	Cert-manager 自动为自签名证书配置 `certName`。

4. 重新启动 Pulsar proxy 使更改生效。

    ```
    helm upgrade -f /path/to/your/file.yaml CLUSTER_NAME $PULSAR_CHART/
    ```
    
    Cert-manager 自动生成所有证书并将它们附加到 Kubernetes pod。

@@@

:::

# 使用手动生成的证书在 Pulsar proxy 上启用 TLS

要在 Pulsar proxy 上启用 TLS（不使用 cert-manager），就需要先手动生成证书。

要在 Pulsar proxy 上启用 TLS，请完成以下步骤：

1. 创建 TLS 证书文件。详情参见[操作说明](#手动创建-TLS-证书)。

2. 创建 secret。

    ```
    kubectl create secret generic RELEASE_NAME-sn-platform-proxy-tls -n KUBERNETES_NAMESPACE \
      --from-file=tls.crt=$(PWD)/cert/broker.cert.pem \
      --from-file=tls.key=$(PWD)/cert/broker.key-pk8.pem \
      --from-file=ca.crt=$(PWD)/cert/ca.cert.pem
    ```

3. 在 Pulsar proxy 上启用 TLS。可以把 `tls` 和 `proxy` 都设置为 `true`，并设置 `tlsSecretName`。

    ```
    tls:
      enabled: true
      proxy:
       enabled: true
    proxy:
      tlsSecretName: "RELEASE_NAME-sn-platform-proxy-tls"
    ```

4. 重新启动 Pulsar proxy。

    ```
    helm upgrade -f /path/to/your/file.yaml CLUSTER_NAME $PULSAR_CHART/
    ```

5. 从 proxy-ingress 服务获取 proxy URL。运行如下命令以显示外部 IP 地址：

    ```
    kubectl get svc/RELEASE_NAME-sn-platform-proxy-ingress -n KUBERNETES_NAMESPACE
    ```
    
6. 在 `config/client.conf` 文件中更改客户端配置。

    启用 TLS 后，连接协议会相应变化，例如 `http://` 变成 `https://`，`pulsar://` 变成 `pulsar+ssl://`。

    ```
    webServiceUrl=https://[PROXY_INGRESS_ADDRESS]/
    brokerServiceUrl=pulsar+ssl://[PROXY_INGRESS_ADDRESS]:6651/
    tlsAllowInsecureConnection=false
    tlsEnableHostnameVerification=false
    tlsTrustCertsFilePath=[PATH_TO]/ca.cert.pem
    ```
7. 用 pulsar-client 生产和消费数据来验证配置。

## 手动创建 TLS 证书

为 Pulsar 创建 TLS 证书包括创建证书颁发机构（CA）、服务器端证书和客户端证书。

可以按照以下步骤设置证书颁发机构（CA），或参阅[本指南](https://jamielinux.com/docs/openssl-certificate-authority/index.html)了解详细信息。

### 创建证书颁发机构（CA）

1. 为证书颁发机构（CA）创建证书。

    > **注意**      
    > 可以使用 CA 来签署 broker 和客户端证书。这确保了双方相互信任。应该将 CA 存储在一个安全的位置（理想情况是完全断网、与外界隔离并且完全加密的）。

2. 输入以下命令，为 CA 创建一个目录，并将 OpenSSL 配置文件放在该目录中。

    > **提示**      
    > 如果需要修改配置文件中公司名称和部门的默认值，可以将 CA 目录的位置导出到环境变量 CA_HOME 中。配置文件使用此环境变量来查找 CA 所需的文件和目录。

    ```bash
    mkdir my-ca
    cd my-ca
    wget https://raw.githubusercontent.com/apache/pulsar/master/site2/website/static/examples/openssl.cnf
    export CA_HOME=$(pwd)
    ```

3. 输入以下命令，来创建必要的目录、密钥和证书。

    ```bash
    mkdir certs crl newcerts private
    chmod 700 private/
    touch index.txt
    echo 1000 > serial
    openssl genrsa -aes256 -out private/ca.key.pem 4096
    chmod 400 private/ca.key.pem
    openssl req -config openssl.cnf -key private/ca.key.pem \
        -new -x509 -days 7300 -sha256 -extensions v3_ca \
        -out certs/ca.cert.pem
    chmod 444 certs/ca.cert.pem
    ```

4. 根据提示进行回答。    
   CA 相关文件存储在 `./my-ca` 目录中。

   * `certs/ca.cert.pem` 是分发给所有相关方的公共证书。 
   * `private/ca.key.pem` 是私钥。只有在为 broker 或客户端签署新证书时才需要这个秘钥。必须安全地保护此私钥。

### 生成服务器端证书

服务器端证书是由同一个证书机构生成的。创建了 CA 证书之后，就可以创建证书请求并使用 CA 对其进行签名。

> **提示**     
> 通用名称应与 broker 相匹配。还可以使用通配符来匹配一组 broker 的主机名，例如，`*.broker.usw.example.com`。这样确保了多台机器可以重复使用相同的证书。但是，当无法匹配主机名时，你可以禁用客户端的 TLS 主机名验证。

要创建证书，请按如下步骤操作：

1. 生成密钥。

    ```bash
    openssl genrsa -out broker.key.pem 2048
    ```

    该密钥为 [PKCS 8](https://en.wikipedia.org/wiki/PKCS_8) 格式。可以用下面的命令对其进行转换：

    ```bash
    openssl pkcs8 -topk8 -inform PEM -outform PEM \
          -in broker.key.pem -out broker.key-pk8.pem -nocrypt
    ```

2. 生成证书申请。

    ```bash
    openssl req -config openssl.cnf \
        -key broker.key.pem -new -sha256 -out broker.csr.pem
    ```

3. 用 CA 进行签名。

    ```bash
    openssl ca -config openssl.cnf -extensions server_cert \
       -days 1000 -notext -md sha256 \
        -in broker.csr.pem -out broker.cert.pem
    ```

现在，你已经得到了证书 `broker.cert.pem` 和密钥 `broker.key-pk8.pem`。可以使用证书和密钥以及 `ca.cert.pem` 来为 broker 和 proxy 节点配置 TLS 传输加密。

# 使用手动生成的证书在 KoP 上启用 TLS

要在 KoP 上启用 TLS（不使用 cert-manager），首先要为 KoP 生成 SSL 密钥和证书，然后为 KoP 证书和密码创建 Kubernetes Secret。

## 为 KoP 生成 SSL 密钥和证书 

要通过 SSL 连接到 KoP，需完成如下步骤：

1. 生成密钥和证书。

    ```
    keytool -keystore server.keystore.jks -alias localhost -validity <validity> -keyalg RSA -genkey
    ```

    上述命令中，需要指定两个参数：

    - `keystore`：存储证书的文件。Keystore（密钥库）包含证书的私钥，应该保存在一个安全的地方。
    - `validity`：证书到期前的剩余天数。

2. 生成证书颁发机构（CA）。

   1. 生成 CA。

      CA 是指用于签署其他证书的公-私密钥对和证书。

      ```
      openssl req -new -x509 -keyout ca-key -out ca-cert -days <validity>
      ```

   2. 将生成的 CA 添加到 broker 的 truststore（信任库）中，这样 broker 就可以信任这个 CA。

      ```
      keytool -keystore server.truststore.jks -alias CARoot -import -file ca-cert
      ```

   3. 将生成的 CA 添加到客户端的 truststore（信任库）中。

      ```
      keytool -keystore client.truststore.jks -alias CARoot -import -file ca-cert
      ```

3. 对证书进行签名。

    1. 将证书从 keystore（密钥库）中导出。

        ```
        keytool -keystore server.keystore.jks -alias localhost -certreq -file cert-file
        ```

    2. 用 CA 对证书签名。

        ```
        openssl x509 -req -CA ca-cert -CAkey ca-key -in cert-file -out cert-signed -days <validity> -CAcreateserial -passin pass:<ca-password>
        ```

    3. 将 CA 的证书和已签名的证书都导入到 broker 的 keystore（密钥库）中。

        ```
        keytool -keystore server.keystore.jks -alias CARoot -import -file ca-cert
        keytool -keystore server.keystore.jks -alias localhost -import -file cert-signed
        ```

参数的定义如下：

- keystore：储存证书的文件
- ca-cert：CA 的证书
- ca-key：CA 的私钥
- ca-password：CA 的口令
- cert-file：导出的未签名的服务器端证书
- cert-signed：已签名的服务器端证书

## 为 KoP 证书和密码创建 Kubernetes Secret

Kubernetes Secret 用于存储和管理敏感信息，如密码、token 和密钥。相比于在 Pod 定义或容器镜像中逐字存储，在 Secret 中存储机密信息更加安全和灵活。

1. 为 KoP 证书创建 Kubernetes Secret。

    ```
    kubectl create secret generic kop-secret -n KUBERNETES_NAMESPACE --from-file=keystore.jks=$(PWD)/cert/server.keystore.jks --from-file=truststore.jks=$(PWD)/cert/server.truststore.jks
    ```

2. 为 KoP 证书密码创建 Kubernetes Secret。

    ```
    kubectl create secret generic kop-keystore-password --from-literal=password=<ca-password> -n KUBERNETES_NAMESPACE
    
    ```

## 配置 YAML 文件

按如下步骤操作启用 KoP 和 TLS：

1. 在 Pulsar 的 YAML 配置文件中设置 KoP 参数。

    ```
    # The domain name for accessing KoP outside from the Kuberbetes cluster.
    advertisedDomain: "kop.sn.dev"
    kop:
      enabled: true
      tls:
        enabled: true
        # create a secret with keystore.jks and truststore.jks for KoP TLS security
        certSecretName: "kop-secret"
        # create a secret for keystore and truststore cert
        passwordSecretRef:
          key: password
          name: kop-keystore-password
    ```

2. 使新配置生效。

    ```
    helm upgrade -f /path/to/your/file.yaml CLUSTER_NAME $PULSAR_CHART/
    ```
