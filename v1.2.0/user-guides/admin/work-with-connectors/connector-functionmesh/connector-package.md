---
title: Package self-built connectors
id: connector-package
category: user-guides
---

StreamNative provides ready-to-use Docker images for Pulsar built-in connectors and StreamNative-managed connectors. These images are public at the [Docker Hub](https://hub.docker.com/), with the image name in a format of `streamnative/pulsar-io-CONNECTOR-NAME:TAG`, such as [`streamnative/pulsar-io-hbase:2.7.1`](https://hub.docker.com/r/streamnative/pulsar-io-hbase). 

However, for self-built connectors, you need to package them to NAR/JAR packages or Docker images. Then, you can push the package into a package management system or Docker registry, define connector CRDs by specifying the package URL or Docker image URL and create the connector in a Pulsar cluster.

## Build NAR packages

This section describes how to package a Pulsar connector to a NAR package. **NAR** stands for NiFi Archive, which is a custom packaging mechanism used by [Apache NiFi](https://nifi.apache.org/), to provide a bit of Java `ClassLoader` isolation.

To build a connector to a NAR package, follow these steps.

1. Build a NAR package for a connector by using the [nifi-nar-maven-plugin](https://mvnrepository.com/artifact/org.apache.nifi/nifi-nar-maven-plugin).

2. Include this [nifi-nar-maven-plugin](https://mvnrepository.com/artifact/org.apache.nifi/nifi-nar-maven-plugin) in the Maven project for your connector.

```xml
<plugins>
<plugin>
    <groupId>org.apache.nifi</groupId>
    <artifactId>nifi-nar-maven-plugin</artifactId>
    <version>1.2.0</version>
</plugin>
</plugins>
```

3. Create a `resources/META-INF/services/pulsar-io.yaml` file with the following contents.

```yaml
name: connector name
description: connector description
sourceClass: fully qualified class name (only if source connector)
sinkClass: fully qualified class name (only if sink connector)
```

4. (Optional) If you use the [Gradle NiFi plugin](https://github.com/sponiro/gradle-nar-plugin), you need to create a directive to ensure that your `pulsar-io.yaml` is copied to the NAR file correctly.

# Build uber JAR packages

You can create an **uber JAR** that contains all the connector's JAR files and other resource files.

You can use the [maven-shade-plugin](https://maven.apache.org/plugins/maven-shade-plugin/examples/includes-excludes.html) to create an uber JAR as below.

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

# Build Docker images

To build a Docker image, follow these steps.

1. Package your connector to a [NAR package](#nar-package) or [JAR package](#uber-jar-package).

2. Define a `Dockerfile`.

    This example shows how to define a `Dockerfile` with a NAR package called `pulsar-io-example.nar`.

    ```dockerfile
    # Use pulsar-functions-java-runner since we pack Pulsar Connector written in Java
    FROM streamnative/pulsar-functions-java-runner:2.7.1
    # Copy NAR file into /pulsar/connectors/ directory  
    COPY pulsar-io-example.nar /pulsar/connectors/
    ```

3. Build your connector Docker image packaged with your connector NAR or JAR package.

Then, you can push the connector Docker image into an image registry (such as the [Docker Hub](https://hub.docker.com/), or any private registry) and use the connector Docker image to configure and submit the connector to Function Mesh.
