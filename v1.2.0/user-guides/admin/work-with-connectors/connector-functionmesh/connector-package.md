---
title: 打包自建连接器
id: connector-package
category: user-guides
---

StreamNative 为 Pulsar 内置连接器和 StreamNative 管理的连接器提供了即用型 Docker 镜像。这些镜像公开存放在 [Docker Hub](https://hub.docker.com/)，镜像名称格式为 `streamnative/pulsar-io-CONNECTOR-NAME:TAG`，如 [`streamnative/pulsar-io-hbase:2.7.1`](https://hub.docker.com/r/streamnative/pulsar-io-hbase)。

但是，对于自建连接器，需要将它们打包成 NAR/JAR 包或 Docker 镜像。然后将包推送到包管理系统或 Docker registry，通过指定包的 URL 或 Docker 镜像的 URL 来定义连接器 CRD，并在 Pulsar 集群中创建连接器。

## 构建 NAR 包

本节介绍了如何如何将 Pulsar 连接器打包成 NAR 包。**NAR** 代表 NiFi Archive，这是 [Apache NiFi](https://nifi.apache.org/) 使用的一种自定义打包机制，用于提供一定的 Java `ClassLoader` 隔离。

按照如下步骤，构建连接到 NAR 包的连接器：

1. 使用 [nifi-nar-maven-plugin](https://mvnrepository.com/artifact/org.apache.nifi/nifi-nar-maven-plugin) 为连接器构建 NAR 包。

2. 将这个 [nifi-nar-maven-plugin](https://mvnrepository.com/artifact/org.apache.nifi/nifi-nar-maven-plugin) 包括到连接器的 Maven 项目中。

```xml
<plugins>
<plugin>
    <groupId>org.apache.nifi</groupId>
    <artifactId>nifi-nar-maven-plugin</artifactId>
    <version>1.2.0</version>
</plugin>
</plugins>
```

3. 创建一个 `resources/META-INF/services/pulsar-io.yaml` 文件，包含如下内容：

```yaml
name: connector name
description: connector description
sourceClass: fully qualified class name (only if source connector)
sinkClass: fully qualified class name (only if sink connector)
```

4. （可选）如果使用了 [Gradle NiFi 插件](https://github.com/sponiro/gradle-nar-plugin)，则需要创建指令来确保 `pulsar-io.yaml` 被正确地复制到 NAR 文件中。

# 构建 uber JAR 包 

可以创建一个 **uber JAR**，将所有连接器的 JAR 文件和其他资源文件包括到 **uber JAR** 里面。

可以使用 [maven-shade-plugin](https://maven.apache.org/plugins/maven-shade-plugin/examples/includes-excludes.html) 来创建一个 uber JAR，如下所示：

```xml
<plugin>
<groupId>org.apache.maven.plugins</groupId>
<artifactId>maven-shade-plugin</artifactId>
<version>3.1.1</version>
<executions>
    <execution>
    <phase>package</phase>
    <goals>
        <goal>shade</goal>
    </goals>
    <configuration>
        <filters>
        <filter>
            <artifact>*:*</artifact>
        </filter>
        </filters>
    </configuration>
    </execution>
</executions>
</plugin>
```

# 构建 Docker 镜像

按照如下步骤，构建 Docker 镜像：

1. 将连接器打包成 [NAR 包](#nar-package) 或 [JAR 包](#uber-jar-package)。

2. 定义一个 `Dockerfile`。

    如下示例显示了如何使一个用名为 `pulsar-io-example.nar` 的 NAR 包定义  `Dockerfile` 。

    ```dockerfile
    # Use pulsar-functions-java-runner since we pack Pulsar Connector written in Java
    FROM streamnative/pulsar-functions-java-runner:2.7.1
    # Copy NAR file into /pulsar/connectors/ directory  
    COPY pulsar-io-example.nar /pulsar/connectors/
    ```

3. 构建连接器 Docker 镜像，与连接器 NAR 或 JAR 包打包在一起。

然后，将连接器 Docker 镜像推送到镜像 registry（例如 [Docker Hub](https://hub.docker.com/) 或任何私有 registry）中，并使用连接器 Docker 镜像来配置连接器，以及将连接器提交到 Function Mesh。
