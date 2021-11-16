---
title: Package Pulsar Functions
id: function-package
category: user-guides
---

After developing and testing your Pulsar Functions , you need to package it so that it can be submitted to a Pulsar cluster. This document describes how to package Java, Python, and Go Functions.

# Prerequisites

- Apache Pulsar 2.8.0 or higher

- Function Mesh v0.1.3 or higher

# Package Java Functions

You can package Java Functions to NAR/JAR packages or Docker images.

## Build Java Functions packages

To package a Function in Java, follow these steps.

1. Create a new Maven project with a `pom.xml` file. In the following code sample, the value of `mainClass` is your package name.

    ```Java
    &lt;?xml version="1.0" encoding="UTF-8"?>
    &lt;project xmlns="http://maven.apache.org/POM/4.0.0"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
        &lt;modelVersion>4.0.0&lt;/modelVersion>
        &lt;groupId>java-function&lt;/groupId>
        &lt;artifactId>java-function&lt;/artifactId>
        &lt;version>1.0-SNAPSHOT&lt;/version>
        &lt;dependencies>
            &lt;dependency>
                &lt;groupId>org.apache.pulsar&lt;/groupId>
                &lt;artifactId>pulsar-functions-api&lt;/artifactId>
                &lt;version>2.6.0&lt;/version>
            &lt;/dependency>
        &lt;/dependencies>
        &lt;build>
            &lt;plugins>
                &lt;plugin>
                    &lt;artifactId>maven-assembly-plugin&lt;/artifactId>
                    &lt;configuration>
                        &lt;appendAssemblyId>false&lt;/appendAssemblyId>
                        &lt;descriptorRefs>
                            &lt;descriptorRef>jar-with-dependencies&lt;/descriptorRef>
                        &lt;/descriptorRefs>
                        &lt;archive>
                        &lt;manifest>
                            &lt;mainClass>org.example.test.ExclamationFunction&lt;/mainClass>
                        &lt;/manifest>
                    &lt;/archive>
                    &lt;/configuration>
                    &lt;executions>
                        &lt;execution>
                            &lt;id>make-assembly&lt;/id>
                            &lt;phase>package&lt;/phase>
                            &lt;goals>
                                &lt;goal>assembly&lt;/goal>
                            &lt;/goals>
                        &lt;/execution>
                    &lt;/executions>
                &lt;/plugin>
                &lt;plugin>
                    &lt;groupId>org.apache.maven.plugins&lt;/groupId>
                    &lt;artifactId>maven-compiler-plugin&lt;/artifactId>
                    &lt;configuration>
                        &lt;source>8&lt;/source>
                        &lt;target>8&lt;/target>
                    &lt;/configuration>
                &lt;/plugin>
            &lt;/plugins>
        &lt;/build>
    &lt;/project>
    ```

2. Write a Java Functions.

    ```java
    package org.example.test;
    import java.util.function.Function;
    public class ExclamationFunction implements Function&lt;String, String> {
        @Override
        public String apply(String s) {
            return "This is my function!";
        }
    }
    ```

3. Package the Java Functions.

    ```bash
    mvn package
    ```

    After the Java Function is packaged, a `target` directory is created automatically. Open the `target` directory to check if there is a JAR package similar to `java-function-1.0-SNAPSHOT.jar`.

Then, you can push the package into a package management system, define Functions CRDs by specifying the uploaded Functions package and submit the Functions to a Pulsar cluster.

## Build Docker images

To build a Docker image, follow these steps.

1. Package your Pulsar function. For details, see [package Pulsar functions](#package-java-functions).

2. Define a `Dockerfile`.

    This example shows how to define a `Dockerfile` with a JAR package (`example-function.jar`) of the Java Functions.

    ```dockerfile
    # Use pulsar-functions-java-runner since we pack Java function
    FROM streamnative/pulsar-functions-java-runner:2.7.1
    # Copy function JAR package into /pulsar directory  
    COPY example-function.jar /pulsar/
    ```

Then, you can push the Functions Docker image into an image registry (such as the [Docker Hub](https://hub.docker.com/), or any private registry) and use the Functions Docker image to configure and submit the Functions to a Pulsar cluster.

# Package Python Functions

You can package Python Functions to external packages (one Python file or ZIP file) or Docker images.

## Build Python Functions packages

This section describes how to build packages for Python Functions.

Python Function supports One Python file or ZIP file.

- One Python file

- ZIP file

### One Python file

   This example shows how to package a Python Functions with the **one Python file**.

    ```python
    from pulsar import Function //  import the Function module from Pulsar
    # The classic ExclamationFunction that appends an exclamation at the end
    # of the input
    class ExclamationFunction(Function):
      def __init__(self):
        pass
      def process(self, input, context):
        return input + '!'
    ```

    In this example, when you write a Python Functions, you need to inherit the Function class and implement the `process()` method.

    The `process()` method mainly has two parameters:

    - `input` represents your input.

    - `context` represents an interface exposed by the Pulsar Function. You can get the attributes in the Python Functions based on the provided context object.

### ZIP file

    To package a Python Functions with the **ZIP file** in Python, you need to prepare a ZIP file. The following is required when packaging the ZIP file of the Python Function.

    Assume the zip file is named as `func.zip`, unzip the `func.zip` folder:

    ```text
        "func/src"
        "func/requirements.txt"
        "func/deps"
    ```

    Take [exclamation.zip](https://github.com/apache/pulsar/tree/master/tests/docker-images/latest-version-image/python-examples) as an example. The internal structure of the example is as follows.

    ```text
    .
    ├── deps
    │   └── sh-1.12.14-py2.py3-none-any.whl
    └── src
        └── exclamation.py
    ```

Then, you can push the package into a package management system, define Functions CRDs by specifying the uploaded Functions package and submit the Functions to a Pulsar cluster.

## Build Docker images

To build a Docker image, follow these steps.

1. Package your Python Functions. For details, see [package Pulsar functions](#package-python-functions).

2. Define a `Dockerfile`.

    This example shows how to define a `Dockerfile` with a JAR package (`example-function.jar`) of the Python Functions.

    ```dockerfile

    # Use pulsar-functions-python-runner since we pack python function

    FROM streamnative/pulsar-functions-python-runner:2.7.1

    # Copy function JAR package into /pulsar directory  

    COPY example-function.jar /pulsar/

    ```

Then, you can push the Functions Docker image into an image registry (such as the [Docker Hub](https://hub.docker.com/), or any private registry) and use the Functions Docker image to configure and submit the Functions to a Pulsar cluster.

# Package Go Functions

You can package Go Functions to external packages or Docker images.

## Build Go Functions packages

To package Go Functions in Go, follow these steps.

1. Write a Go Functions.

    Currently, Go Functions can be **only** implemented using SDK and the interface of the Functions is exposed in the form of SDK. Before using the Go Functions, you need to import `github.com/apache/pulsar/pulsar-function-go/pf`. 

    ```go
    import (
        "context"
        "fmt"
        "github.com/apache/pulsar/pulsar-function-go/pf"
    )
    func HandleRequest(ctx context.Context, input []byte) error {
        fmt.Println(string(input) + "!")
        return nil
    }
    func main() {
        pf.Start(HandleRequest)
    }
    ```

    When writing a Go Functions, remember that

    - In `main()`, you **only** need to register the Functions name to `Start()`. **Only** one Functions name is received in `Start()`. 

    - Go Functions use Go reflection, which is based on the received Functions name, to verify whether the parameter list and returned value list are correct. The parameter list and returned value list **must be** one of the following sample Functions:

      ```go
       func ()
       func () error
       func (input) error
       func () (output, error)
       func (input) (output, error)
       func (context.Context) error
       func (context.Context, input) error
       func (context.Context) (output, error)
       func (context.Context, input) (output, error)
       ```

    You can use the context to connect to the Go Functions.

    ```go
    if fc, ok := pf.FromContext(ctx); ok {
        fmt.Printf("function ID is:%s, ", fc.GetFuncID())
        fmt.Printf("function version is:%s\n", fc.GetFuncVersion())
    }
    ```

2. Build the Go Functions.

    ```bash
    go build &lt;your Go Function filename>.go 
    ```

Then, you can push the package into a package management system, define Functions CRDs by specifying the uploaded Functions package and submit the Functions to a Pulsar cluster.

## Build Docker images

To build a Docker image, follow these steps.

1. Package your Go Functions. For details, see [package Pulsar functions](#package-go-functions).

2. Define a `Dockerfile`.

    This example shows how to define a `Dockerfile` with a JAR package (`example-function.jar`) of the Go function.

    ```dockerfile
    # Use pulsar-functions-go-runner since we pack Go function
    FROM streamnative/pulsar-functions-go-runner:2.7.1
    # Copy function JAR package into /pulsar directory  
    COPY example-function.jar /pulsar/
    ```

Then, you can push the Functions Docker image into an image registry (such as the [Docker Hub](https://hub.docker.com/), or any private registry) and use the Functions Docker image to configure and submit the Go functions to a Pulsar cluster.
