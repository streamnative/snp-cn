---
title: 打包 Pulsar Functions
id: function-package
category: user-guides
---

对 Pulsar Functions 进行开发和测试后，需要将其打包，以便将其提交到 Pulsar 集群。本文介绍了如何打包 Java、Python 和 Go 函数。

# 先决条件

- Apache Pulsar 2.8.0 或更高版本

- Function Mesh v0.1.3 或更高版本

# 打包 Java 函数

可以将 Java 函数打包到 NAR/JAR 包或 Docker 镜像。

## 构建 Java 函数包

按照如下步骤操作，构建 Java 的函数包：

1. 使用 `pom.xml` 文件创建一个新的 Maven 项目。下面的代码示例中，`mainClass` 的值是包的名字。

    ```Java
    <?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>
        <groupId>java-function</groupId>
        <artifactId>java-function</artifactId>
        <version>1.0-SNAPSHOT</version>
        <dependencies>
            <dependency>
                <groupId>org.apache.pulsar</groupId>
                <artifactId>pulsar-functions-api</artifactId>
                <version>2.6.0</version>
            </dependency>
        </dependencies>
        <build>
            <plugins>
                <plugin>
                    <artifactId>maven-assembly-plugin</artifactId>
                    <configuration>
                        <appendAssemblyId>false</appendAssemblyId>
                        <descriptorRefs>
                            <descriptorRef>jar-with-dependencies</descriptorRef>
                        </descriptorRefs>
                        <archive>
                        <manifest>
                            <mainClass>org.example.test.ExclamationFunction</mainClass>
                        </manifest>
                    </archive>
                    </configuration>
                    <executions>
                        <execution>
                            <id>make-assembly</id>
                            <phase>package</phase>
                            <goals>
                                <goal>assembly</goal>
                            </goals>
                        </execution>
                    </executions>
                </plugin>
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <configuration>
                        <source>8</source>
                        <target>8</target>
                    </configuration>
                </plugin>
            </plugins>
        </build>
    </project>
    ```

2. 编写 Java 函数。

    ```java
    package org.example.test;
    import java.util.function.Function;
    public class ExclamationFunction implements Function<String, String> {
        @Override
        public String apply(String s) {
            return "This is my function!";
        }
    }
    ```

3. 打包 Java 函数。

    ```bash
    mvn package
    ```

    Java 函数打包后，会自动创建一个 `target`  目录。打开 `target` 目录，查看是否有类似 `java-function-1.0-SNAPSHOT.jar` 的 JAR 包。

然后，将包推送到包管理系统中，通过指定上传的函数包来定义函数 CRD，并将这些函数提交到 Pulsar 集群。

## 构建 Docker 镜像

按照如下步骤，创建 Docker 镜像：

1. 打包 Java 函数。详细信息参见[打包 Java 函数](#打包-java-函数)。

2. 定义 `Dockerfile`。

    下面的例子显示了如何使用 Java 函数的 JAR 包 (`example-function.jar`) 来定义 `Dockerfile`。

    ```dockerfile
    # Use pulsar-functions-java-runner since we pack Java function
    FROM streamnative/pulsar-functions-java-runner:2.7.1
    # Copy function JAR package into /pulsar directory  
    COPY example-function.jar /pulsar/
    ```

然后，将函数 Docker 镜像推送到镜像 registry（如 [Docker Hub](https://hub.docker.com/)，或任意私有的 registry），并使用函数 Docker 镜像来配置和提交函数提交到 Pulsar 集群。

# 打包 Python 函数 

可以将 Python 函数打包成外部包（单个 Python 文件或 ZIP 文件）或 Docker 镜像。

## 构建 Python 函数包

本节介绍了如何为 Python 函数构建包。

Python 函数支持单个 Python 文件或 ZIP 文件。

- 单个 Python 文件

- ZIP 文件

### 单个 Python 文件

如下示例介绍了如何使用 **单个 Python 文件**打包 Python 函数：

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

在这个示例中，在编写 Python 函数时，需要继承函数类，并使用 `process()` 方法。

这个 `process()` 方法主要有两个参数：
    
- `input` 表示输入。
  
- `context` 表示 Pulsar Function 暴露的接口。可以基于提供的上下文对象获取 Python 函数的属性。

### ZIP 文件

要在 Python 中使用 **ZIP 文件**来打包 Python 函数，需要准备一个 ZIP 文件。打包 Python 函数的 ZIP 文件，需要满足下面的要求。

假设 ZIP 文件的文件名是 `func.zip`，将它解压到 `func.zip` 文件夹：
    

    ```text
        "func/src"
        "func/requirements.txt"
        "func/deps"
    ```

可以使用 [exclamation.zip](https://github.com/apache/pulsar/tree/master/tests/docker-images/latest-version-image/python-examples) 作为示例。示例文件的内部结构如下：
    ```text
    .
    ├── deps
    │   └── sh-1.12.14-py2.py3-none-any.whl
    └── src
        └── exclamation.py
    ```

然后，将包推送到包管理系统中，通过指定上传的函数包来定义函数 CRD，并将这些函数提交到 Pulsar 集群。

## 构建 Docker 镜像

按照如下步骤操作构建 Docker 镜像。

1. 打包 Python 函数。细节参见[打包 Python 函数](#打包-python-函数)。

2. 定义 `Dockerfile`。

    下面的例子显示了如何使用 Python 函数的 JAR 包 (`example-function.jar`) 来定义 `Dockerfile`。

    ```dockerfile
    
    # Use pulsar-functions-python-runner since we pack python function
    
    FROM streamnative/pulsar-functions-python-runner:2.7.1
    
    # Copy function JAR package into /pulsar directory  
    
    COPY example-function.jar /pulsar/
    
    ```

然后，将函数 Docker 镜像推送到镜像 registry（如 [Docker Hub](https://hub.docker.com/)，或任意私有的 registry），并使用函数 Docker 镜像来配置和提交函数到 Pulsar 集群。

# 打包 Go 函数

可以将 Go 函数打包到外部包或者 Docker 镜像。

## 构建 Go 函数包

按照如下步骤，在 Go 中打包 Go 函数：

1. 编写 Go 函数。

    目前 Go 函数**仅**可使用 SDK 实现，并且函数的接口以 SDK 的形式公开。使用 Go 函数前，需要先导入 `github.com/apache/pulsar/pulsar-function-go/pf`。

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

    在编写 Go 函数时，需要注意如下几点： 

    - 在 `main()` 中，**只**需要将函数名称注册到 `Start()`。在 `Start()`  中**只**接收函数名。

    - Go 函数使用 Go 反射，根据接收到的函数名，来验证参数列表和返回值列表是否正确。参数列表和返回值列表**必须**是以下示例函数之一：

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

    可以使用上下文连接到 Go 函数。

    ```go
    if fc, ok := pf.FromContext(ctx); ok {
        fmt.Printf("function ID is:%s, ", fc.GetFuncID())
        fmt.Printf("function version is:%s\n", fc.GetFuncVersion())
    }
    ```

2. 构建 Go 函数。

    ```bash
    go build <your Go Function filename>.go 
    ```

然后，将包推送到包管理系统中，通过指定上传的函数包来定义函数 CRD，并将这些函数提交到 Pulsar 集群。

## 构建 Docker 镜像

按照如下步骤，构建 Docker 镜像：

1. 打包 Go 函数。详情参见[打包 Go 函数](#打包-go-函数)。

2. 定义 `Dockerfile`。

    如下示例显示了如何使用 Go 函数的 JAR 包（`example-function.jar`）定义 `Dockerfile`。

    ```dockerfile
    # Use pulsar-functions-go-runner since we pack Go function
    FROM streamnative/pulsar-functions-go-runner:2.7.1
    # Copy function JAR package into /pulsar directory  
    COPY example-function.jar /pulsar/
    ```

然后，将函数 Docker 镜像推送到镜像 registry（如 [Docker Hub](https://hub.docker.com/)，或任意私有的 registry），并使用函数 Docker 镜像来配置和提交函数提交到 Pulsar 集群。
