## 第二部分：在 Kubernetes 上部署 Spark Structured Streaming 应用

在你的 Kubernetes 集群启动并运行，并且你已划分出 `data-services` 命名空间后，我们将深入探讨为我们的 Apache Spark 环境提供支持所需的额外 Kubernetes 资源。

**动手实践内容**

本章第二部分的章节材料位于 `ch-15/spark-on-kubernetes` 目录下。完整的操作指南可在 `README` 文件中找到。在 `data-services` 命名空间中运行 MinIO 和 MySQL 的完整解决方案可以为你接下来的探索做好准备。

### Spark 命名空间、应用环境和基于角色的访问控制

要使你的 Apache Spark 应用能够运行，需要一些基础资源。在本节中，我们将定义`spark-apps`命名空间，然后向你介绍角色、角色绑定和服务账户。

## 定义 Spark 应用命名空间

清单 15-12 中的命令为我们的 Spark 应用创建了一个名为`spark-apps`的新命名空间。创建此命名空间。

```sh
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Namespace
metadata:
labels:
kubernetes.io/metadata.name: spark-apps
name: spark-apps
EOF
```

*清单 15-12：定义 Spark 应用命名空间*

定义了`spark-apps`命名空间后，下一步是建立一些规则来管理我们 Spark 应用的运行时能力。就像在现实世界中，你不会把你家的钥匙随便给一个陌生人一样，这在 Kubernetes 世界中也是如此。`Pod`具有已知的身份，这些身份由不可伪造的、基于`Pod`与 Kubernetes API 之间令牌/证书交换来控制认证和授权的双向 TLS 管理。`Pod`可以承担特定的身份，并获得由服务账户、角色和角色绑定资源管理的特殊资源和访问权限。

## 基于角色的访问控制 (RBAC)

就像在 SQL 系统中使用`GRANT`和`REVOKE`来建立权限一样，基于角色的访问控制 (RBAC) 用于为你的`Pod`以及访问你 K8s 集群的真实用户定义通用能力和权限。

### 角色

Kubernetes 中的角色用于在 API 级别定义通用权限（例如创建、修改或销毁资源）。

```sh
% kubectl get roles -n {namespace}
```

角色定义了一系列规则，这些规则在特定命名空间内对资源进行管理，并且是动词级别的。

**注意：** 你也可以定义管理集群级别（而不仅仅是特定命名空间）访问权限的规则。这些被称为`clusterroles`：`% kubectl get clusterroles`

### 服务账户

服务账户在 Kubernetes API 中提供了一种身份机制。这相当于在 Amazon Web Services 上使用 IAM 权限。服务账户为特定资源提供基于令牌的授权访问。默认情况下，每个命名空间都会生成一个名为`default`的通用服务账户。除非指定了不同的服务账户，否则此默认身份将自动与在每个命名空间中创建的任何`Pod`关联。

```sh
% kubectl get sa -n {namespace}
```

### 角色绑定

角色绑定将用户、组或服务账户连接到一个特定的角色。

```sh
% kubectl get rolebindings -n {namespace}
```

现在，我们来设置`spark-controller`服务账户，以及相关联的角色和角色绑定。

## 创建 Spark Controller 服务账户、角色和角色绑定

考虑到`Pod`在命名空间内运行，并且我们希望我们的 Spark Structured Streaming 应用能够自我管理——在必要时添加和移除执行器`Pod`——我们需要创建一个特殊的服务账户（`spark-controller`），该账户可以绑定到我们的 Spark 应用的`Pod`上。这将授予`Pod`自我创建和销毁以及管理命名空间内其他资源的能力（这样我们就不必操心了）。

清单 15-13 中的多部分资源定义在`spark-apps`命名空间内生成三个新资源。角色（`spark-app-controller`）、服务账户（`spark-controller`）和角色绑定（`spark-app-controller-binding`）。

```yaml
cat <<EOF | kubectl apply -f -
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
namespace: spark-apps
name: spark-app-controller
rules:
- apiGroups:
- ""
resources:
- pods
- services
- configmaps
verbs:
- list
- create
- delete
- get
- list
- patch
- update
- watch

apiVersion: v1
kind: ServiceAccount
metadata:
name: spark-controller
namespace: spark-apps

apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
name: spark-app-controller-binding
namespace: spark-apps
subjects:
- apiGroup: ""
kind: ServiceAccount
name: spark-controller
roleRef:
apiGroup: rbac.authorization.k8s.io
kind: Role
name: spark-app-controller
EOF
```

*清单 15-13：使用多部分 `---` 分隔符可以创建多个 K8s 资源*

清单 15-13 中的技术使你能够提供一个声明了一组资源的单一文件。依赖多部分资源定义模式可确保每个隔离部署的不同需求都能以一致的方式得到满足。

**提示：** 对于复杂部署，或用于引导整个环境，你可以依赖多资源配置，如 Spark 引导配置（清单 15-13）。这样做，你可以确保所有部署依赖项（资源）在部署之前都可用，并以正确的顺序应用到 K8s 集群中。关键在于一致性。K8s 社区已经采用了两种流行的解决方案——[helm](https://helm.sh/) 和 [kustomize](https://kustomize.io/)——来简化部署复杂性的管理。Helm 就像 K8s 的包管理器，使得复杂部署可以通过命令行轻松安装和更新。而 Kustomize 则是一个无模板的配置管理器，可以轻松地将特定环境的配置合并（覆盖）到通用部署资源之上。该解决方案使你能够为开发、预发布和生产环境配置部署，同时重用通用配置以防止配置漂移的问题。

现在，你已经应用了环境脚手架，是时候重新审视第 13 章中的通用 Structured Streaming 应用，并为其在 K8s 集群中的部署做准备。

## Redis Streams Kubernetes 应用

在第十章 10 中，你了解了如何将 Redis Streams 与 Structured Streaming 结合使用，将其作为一种轻量级（低开销）的流式数据源。最初构建该应用是为了向你展示如何可靠地读取无界的`CoffeeOrder`事件流，控制应用触发过程（作为有状态批处理或流式微批处理），并使用检查点维护应用状态。最终成果是一个简单但一致的流式应用，它能够将`CoffeeOrder`对象读取并写入数据仓库（MinIO）的分布式表中。

在第十三章 13 中，我们利用 Spark Structured Streaming 生成基于时间（窗口化）的聚合，从而简化了创建可靠流式分析数据的复杂流程。我们将基于第十三章中的通用聚合应用进行构建，并创建一个可在`minikube`集群内部署和管理的新容器。

首先，在你喜欢的 IDE 中打开`ch-15/applications/redis-streams-k8s`应用，或者直接打开源代码以便跟随学习。

### 构建 Redis Streams 应用

我们不会修改应用的源代码；它已经准备好进行构建。从容器化的角度来看，只有少数几个小的调整值得指出。

首先，`Dockerfile`已被调整，用于将`user_jars`目录的内容复制到最终容器中，如清单 15-14 所示。这样做是为了保持一致性，确保没有意外缺失的依赖项或运行时问题。

```dockerfile
FROM newfrontdocker/${spark_base_version}
ARG app_jar
USER 1000
# 复制并重命名
COPY --chown=1000 ${app_jar} /opt/spark/app/jars/redis-streams-k8s.jar
# 复制应用配置
COPY --chown=1000 conf /opt/spark/app/conf
COPY --chown=1000 user_jars /opt/spark/app/user_jars
```

*清单 15-14：修改 Dockerfile 以打包任何额外的运行时 JAR 和基础应用配置*

如前所述，修改 `Dockerfile` 是为了生成一个一致的容器，其中包含所有必需的运行时 JAR 和通用的基线配置。容器中附带的基线配置包括来自第十三章 13 的 `TypeSafe` 配置、`fairscheduler.xml` 文件（在上一章中介绍）以及两个可执行文件（`spark-submit.sh` 和 `spark-submit-lite.sh`），用于在 Kubernetes 集群内部提交应用。

> **提示：** 如果你需要类似的通用依赖项，可以将它们打包到你的基础 Spark 容器镜像中。这样，每个应用就不必重复执行自备 JAR 的过程。只需确保添加通用场景所需的依赖，以便镜像体积尽可能小。

### 构建应用容器

第二个更改是针对容器构建过程。你需要使用 `minikube` Docker 环境构建本地应用镜像。如清单 15-15 所示，这里增加了一个小步骤，本质上就是一个单行的 `eval` 语句。

```bash
1.   sbt clean assembly
2.   eval $(minikube docker-env)
3.   docker build . -t mde/redis-streams-k8s:1.0.0
```

*清单 15-15：构建容器以在 minikube 环境中使用*

现在你已经构建好了容器，下一步就是部署它。

> **提示：** 在本地测试时，使用清单 15-15 中的技巧可以更快地迭代你的应用。另一种方式是将镜像构建并推送到 DockerHub 或亚马逊 ECR 等其他容器注册服务。

### 在 Kubernetes 上部署 Redis Streams 应用

回顾整本书中运行各种 Spark 应用的过程，我们经常通过 `docker-compose` 使用挂载的配置和运行时资源（JAR、`hive-site.xml` 中的密钥以及配置）来增强运行时环境。为了实现特定容器间的通信，我们还使用了内部 Docker 网络（`mde`）。将类似的策略应用于 Kubernetes 上的 Spark 应用是完全合理的。

在本章前面，你了解了*服务*和*卷*（持久卷和临时卷）。为准备部署，你将添加一种新的服务类型，以简化与 `data-services` 命名空间中运行服务的跨命名空间 DNS 解析。你还将学习如何外部化环境变量、部署特定配置以及密钥，例如 `hive-site.xml` 和 MinIO 的访问/密钥。我们将把注意力转向使用超越传统文件存储（目录和单个文件）的卷。

**运行代码**

在终端中，将你的工作目录切换到 `ch-15/spark-on-kubernetes`。

#### 外部化依赖管理

有两种重要的资源类型，它们的作用类似于卷（并且可以挂载到 Pod 上），但提供了额外的专门功能。它们分别称为 `ConfigMap` 和 `Secret`，你将在部署中依赖它们。

#### 使用 ConfigMap 进行外部配置

`ConfigMap` 资源提供了一种解决方案，用于存储在命名空间范围内、具有唯一标识的数据集合（文件、二进制数据等）。`ConfigMap` 对象通常用于提供共享的环境变量（例如，用于 `dev`、`stage` 和 `production` 环境），以及提供隔离的（特定于部署）的应用资源。

让我们创建一个 `ConfigMap`，它可以用作我们的默认 Spark 应用配置（`spark-defaults.conf`）。在你的终端中（在 `spark-on-kubernetes` 目录内），运行以下命令：

```bash
kubectl create configmap spark-redis-streams-conf \
--from-file=spark-redis-streams/config \
-n spark-apps
```

神奇的是，`config/spark-defaults.conf` 文件的内容会被编码到你的 `spark-redis-streams-conf` 资源中。要查看内容，你可以运行以下命令：

```bash
kubectl get configmap spark-redis-streams-conf \
-n spark-apps \
-o yaml
```

输出结果实质上只是向你展示了 `spark-defaults.conf` 的内容。

#### Secrets

众所周知，你不应该将用户名、密码或敏感数据提交到 Git（或你正在使用的任何版本控制系统）中，否则你可能会无意中泄露敏感数据。`Secret` 资源类型允许你（在你的命名空间内）存储敏感数据，这些数据保证通过加密（HTTPS）通道传输，并在内部存储为 base-64 编码的字符串。

### 安全存储 Hive Metastore 配置

清单 15-16 中的命令对我们一直用于访问基于 MySQL 的 Hive Metastore 的 `hive-site.xml` 文件进行了编码，并将其封装到一个名为 `hivesite-admin` 的密钥资源中，该资源可以在运行时安全地挂载到我们的 Spark 应用 Pod 上。

```
cat 

javax.jdo.option.ConnectionURL
jdbc:mysql://mysql-service.spark-apps.svc.cluster.local:3306/metastore

javax.jdo.option.ConnectionDriverName
org.mariadb.jdbc.Driver

javax.jdo.option.ConnectionUserName
dataeng

javax.jdo.option.ConnectionPassword
dataengineering_user

EOF
清单 15-16
将 hive-site.xml 封装为密钥，用于应用部署
```

你会注意到 `hive-site.xml` 存储在 `stringData` 块中。对于像 `hive-site.xml` 和 `spark-defaults.conf` 这样的二进制文件，你不能使用简单的键/值对，这使得资源定义的长度更长。

在此处，也值得指出的是，`hive-site.xml` 中的 `ConnectionURL` 指向的服务域名目前并不存在于 `spark-apps` 命名空间中。

```
jdbc:mysql://mysql-service.spark-apps.svc.cluster.local:3306/metastore
```

我们稍后将添加一个 `ExternalService` 资源，为 `data-services` MySQL 服务提供别名。

**提示：** 如果你尚未尝试过在 K8s 上设置 MySQL 并希望获得解决方案，请使用 `ch-15/spark-on-kubernetes/data-services/README.md` 进行设置。

现在我们已经将 `hive-site.xml` 编码为密钥，我们一直在处理的另一个敏感信息是 MinIO 的访问密钥和秘密密钥。幸运的是，这是一个更简单的定义，使用了键/值对。

### 安全存储 MinIO 访问参数

清单 15-17 中的资源定义对管理用户名和密码信息进行了编码。创建 `minio-access` 资源。请记住，密钥是在你的应用程序源代码之外管理的，因此你不必担心意外将密钥提交到 GitHub。只需引用已知的密钥并将其挂载到你的 Pod 上，以便在容器中使用即可。

```
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Secret
metadata:
name: minio-access
namespace: spark-apps
type: Opaque
stringData:
access: minio
secret: minio_admin
EOF
清单 15-17
创建 minio-access 键/值对密钥，用于 Spark 应用部署
```

创建好这两个密钥后，你现在可以使用 `volumes` 和 `volumeMounts` 将它们用于你的部署规范中。

### 查看命名空间中的密钥

如果你想查看已编码的密钥，可以使用以下命令：

```bash
% kubectl get secrets -n {namespace}
% kubectl get secret/{name} -n {namespace} -o yaml
```

接下来要做的事情是添加运行 Spark Structured Streaming 应用所需的 `ExternalService` 配置。

### 跨命名空间 DNS 和网络策略

有趣的事实：命名空间为跨命名空间通信提供了天然的保护屏障。要实现命名空间之间的交互，例如在实际的生产 Kubernetes 集群中，你需要使用一种名为 `ExternalName` 的特殊服务配置以及 `NetworkPolicy` 来创建策略。这两者结合使用，可以使在隔离命名空间中运行的应用能够向另一个命名空间中运行的服务发出请求。例如，使你的 Spark 应用能够与 `data-services` 命名空间通信。

当在 `minikube` 上运行时，我们在这里走个流程，因为网络在我们的单节点上是共享的。让我们定义指向 `data-services` `MySQL` 服务的服务指针（如清单 15-18 所示）。

```
cat <<EOF | kubectl apply -f -
kind: Service
apiVersion: v1
metadata:
name: mysql-service
namespace: spark-apps
spec:
type: ExternalName
externalName: mysql-service.data-services.svc.cluster.local
ports:
- port: 3306
name: tcp-port
EOF
```

清单 15-18 ExternalName 服务为你的命名空间内部提供了一个本地完全限定域名（FQDN），该域名解析到另一个命名空间中的外部服务。

为什么要使用这个呢？随着时间的推移，内部管理的服务经常会跨团队转移所有权。如果新团队接手某些服务的所有权并切换命名空间，你可以更新单个资源并迁移你的应用，或者在不同命名空间之间链接 `ExternalName` 资源。

最终，随着规模的扩大，你会发现自己最终会迁移到像 `Istio` 这样的服务网格，但由于你刚开始接触，请记住保持简单（已经有太多新组件和服务需要理清了）。

作为后续练习，请遵循清单 15-18 的模式，为 `Redis` 和 `MinIO` 创建额外的策略。准备好后，就可以进行部署了。

# 使用跳板 Pod 控制部署

首先，您将通过一个简单的"跳板"（jump）Pod 中的 `mde/redis-streams-k8s:1.0.0` 容器来部署 Spark 应用程序。如果一开始就全速推进，很容易遗漏某些内容并陷入所谓的"崩溃循环"（crash loop）。您可能会发现，在首次采用任何新的部署模式后，对后续几个应用重复这种缓慢、受控的部署流程是必要的。此过程在调试应用时也很有帮助，因为在将所有内容自动化到正式的有状态部署之前，您可以先检查常见的故障点，例如缺失的配置。

从 `ch-15/spark-on-kubernetes/spark-redis-streams` 目录下，打开 `deployment-manual.yaml`（如列表 15-19 所示）查看 Pod 规范。请特别关注 `volumes` 和 `volumeMounts` 环境变量，以了解 `Secrets` 和 `ConfigMap` 是如何注入到 Pod 中的，以及外部 DNS 值是如何注入到规范中的。

```yaml
apiVersion: v1
kind: Pod
metadata:
name: spark-redis-streams-app
namespace: spark-apps
labels:
app: spark-redis-streams
type: canary
version: "1.0.0"
spec:
restartPolicy: OnFailure
# 跳板 Pod 以 root 身份运行
securityContext:
runAsUser: 0
runAsGroup: 0
serviceAccountName: spark-controller
volumes:
- name: spark-submit-conf
configMap:
name: spark-redis-streams-conf
- name: hive
secret:
secretName: hivesite-admin
containers:
- name: redis-streams-app
env:
- name: SPARK_CONTAINER_IMAGE
value: mde/redis-streams-k8s:1.0.0
- name: REDIS_SERVICE_HOST
value: redis-service.spark-apps.svc.cluster.local
- name: REDIS_SERVICE_PORT
value: "6379"
- name: MINIO_SERVICE_HOST
value: minio-service.spark-apps.svc.cluster.local
- name: MINIO_SERVICE_PORT
value: "9000"
- name: MINIO_ACCESS_KEY
valueFrom:
secretKeyRef:
name: minio-access
key: access
- name: MINIO_SECRET_KEY
valueFrom:
secretKeyRef:
name: minio-access
key: secret
volumeMounts:
- mountPath: /opt/spark/conf/spark-defaults.conf
name: spark-submit-conf
subPath: spark-defaults.conf
readOnly: false
- mountPath: /opt/spark/conf/hive-site.xml
name: hive
subPath: hive-site.xml
readOnly: true
image: mde/redis-streams-k8s:1.0.0
imagePullPolicy: Never
command: ["tail", "-f", "/dev/null"]
resources:
limits:
memory: "2000Mi"
cpu: "500m"
requests:
memory: "1000Mi"
cpu: "250m"
```
列表 15-19：Spark 跳板 Pod（`spark-redis-streams-app`）规范

列表 15-20 中的 Pod 规范安全地挂载了您应用到 `spark-apps` 命名空间的密钥和配置。我们使用挂起的 `tail` 命令来保持 Pod 运行（例如在本章第一部分与 Redis 服务交互时）。这允许您 `exec` 进入 `redis-streams-app` 容器进程并运行应用程序。

通过执行以下命令创建（并调度）Pod：

```bash
% kubectl apply -f deployment-manual.yaml
```

一个 `pod/spark-redis-streams-app` 即被创建。现在，我们可以 `exec` 进入 Pod 并触发应用程序。

## 从跳板 Pod 触发 Spark Submit

使用 `exec` 命令跳转到跳板 Pod，以启动实际的 Spark 应用程序。为了便于理解，可以将跳板 Pod 视为驻留在 K8s 集群中的一个经过身份验证的进程，从而允许用户（使用 `spark-controller` 服务账户）部署应用程序。

```bash
% kubectl exec --stdin --tty pod/spark-redis-streams-app \
-n spark-apps \
-- bash
```

从 `exec` 进程中，您可以使用我们预先打包在容器内的 `spark-submit.sh` 可执行文件，将应用程序提交到 Kubernetes 集群。它位于以下路径：`/opt/spark/app/conf/spark-submit.sh`。

**提示**
在您首次启动应用程序之前，我始终建议您打开第二个终端窗口，以观察 `spark-submit` 进程如何生成 Pod。您也可以使用 `spark-submit` 进程生成的日志来观察 Spark Kubernetes 资源管理器的操作。
```bash
kubectl get pods -n spark-apps --watch
```

在 `spark-redis-streams-app` Pod 的 `exec` 进程中，运行以下命令。

```bash
% /opt/spark/app/conf/spark-submit.sh
```

当应用程序启动并切换到 `Running` 状态时，您刚刚触发的 `spark-submit` 进程将定期调用 K8s API 来检查驱动状态（本质上与您通过 `kubectl` 进程观察命名空间内 Pod 的行为相同）。

有趣的是：这是您第一次使用您的服务账户，它使您能够即时创建新资源。这允许您将控制权委托给驱动程序应用程序进程。要查看您创建的资源，请调用 `kubectl get pods -n spark-apps`。

```
NAME                                                 READY  STATUS    RESTARTS  AGE
spark-redis-stream-aggs-app-cf14ec7d5490f37d-exec-1  1/1    Running   0         29m
spark-redis-stream-aggs-app-cf14ec7d5490f37d-exec-2  1/1    Running   0         29m
spark-redis-streams-app                              1/1    Running   0         33m
spark-redis-streams-app-driver                       1/1    Running   0         29m
```

您随时可以退出该进程。由于 `spark-submit.sh` 创建的是基于集群的部署，您将返回到 `exec` 进程。（我们在第 14 章中讨论过集群部署，但作为复习，这意味着驱动程序应用程序进程与执行器一起在集群中运行，而不是作为外部资源进行控制。）

**注意**
在 `spark-redis-streams-app` Pod 上运行 `ls -l /opt/spark/app/conf`，您会注意到存在第二个 `spark-submit*` 文件，名为 `spark-submit-lite.sh`。这第二个配置文件提供了运行聚合应用程序所需的最简配置示例。由于第二个示例不可执行（缺少 `-x` 权限），您可以使用 `bash /opt/spark/app/conf/spark-submit-lite.sh` 来运行该应用程序。

## 连接部署的各个点

由环境变量控制的 `spark-submit.sh` 可执行文件如列表 15-20 所示。您会看到重要的配置设置（仅基于 Pod 规范的覆盖项），这些设置通常用于修改应用程序在不同 K8s 集群（非生产环境与生产环境）中运行时的配置。您使用新的 `checkpointLocation` 和输出路径来运行应用程序，这样在部署替换应用程序（或金丝雀版本）时，就不会破坏应用程序的状态。

# Kubernetes 上的 Spark Structured Streaming 应用程序

```bash
#!/usr/bin/env bash
K8S_MASTER=${K8S_MASTER:-k8s://https://${KUBERNETES_SERVICE_HOST}:${KUBERNETES_SERVICE_PORT_HTTPS}}
...
$SPARK_HOME/bin/spark-submit \
--master ${K8S_MASTER} \
--deploy-mode cluster \
--class "com.coffeeco.data.SparkStatefulAggregationsApp" \
--conf "spark.kubernetes.driver.pod.name=${SPARK_APP_NAME}-driver" \
--conf "spark.kubernetes.authenticate.driver.serviceAccountName=${K8S_SERVICE_ACCOUNT_NAME}" \
--conf "spark.redis.host=${REDIS_SERVICE_HOST}" \
--conf "spark.redis.port=${REDIS_SERVICE_PORT}" \
--conf "spark.hadoop.fs.s3a.endpoint=${MINIO_SERVICE_HOST}:${MINIO_SERVICE_PORT}" \
--conf "spark.hadoop.fs.s3a.access.key=${MINIO_ACCESS_KEY}" \
--conf "spark.hadoop.fs.s3a.secret.key=${MINIO_SECRET_KEY}" \
--conf "spark.kubernetes.container.image=${SPARK_CONTAINER_IMAGE}" \
--conf "spark.kubernetes.container.image.pullPolicy=${SPARK_CONTAINER_PULL_POLICY}" \
--conf "spark.sql.warehouse.dir=${S3_BASE_PATH}/warehouse" \
--conf "spark.app.sink.options.checkpointLocation=${S3_BASE_PATH}/apps/spark-redis-streams-app/1.0.0" \
--conf "spark.app.sink.options.path=${S3_BASE_PATH}/warehouse/silver/coffee_order_aggs" \
local:///opt/spark/app/jars/redis-streams-k8s.jar
```

列表 15-20 `spark-submit.sh` 可执行文件

现在，让我们换个话题，学习如何查看与 `Spark` 驱动程序应用程序日志和 `Spark Driver UI` 相关的运行时信息。

### 查看驱动程序日志

通过调用 `spark-submit` 生成的驱动程序 `Pod`（而非启动 `Pod` 本身）上，可以获取应用程序日志。这种间接性是因为我们使用集群部署模式来部署应用程序。

```bash
% kubectl logs spark-redis-streams-app-driver -f \
-n spark-apps
```

现在，您可以通过 `-f` 参数实时跟踪驱动程序日志的更新。如果您想转储日志，请省略 `-f` 参数，这样您将看到存储在 `kubelet` 中驱动程序 `Pod` 的所有可用日志。（`Pod` 中容器的日志存在内存限制，因此 `-f` 选项允许您在处理流式应用程序时读取所有日志。）

### 查看驱动程序 UI

与查看驱动程序日志的过程类似，您可以连接到远程驱动程序 `Pod`，并使用一个名为 `port-forward` 的简单 `kubectl` 命令来转发 `Spark UI`。

```bash
% kubectl port-forward {spark-driver-pod} 4040:4040 \
-n spark-apps
```

其精妙之处在于，只有您组织中能够访问您命名空间的成员才能使用 `Spark UI`。在此示例中，只有您可以访问自己的 `minikube` 集群。

### 发送 CoffeeOrder 事件

打开一个新的终端窗口，并通过 `exec` 命令连接到本章第一部分中的 `Redis` 服务 `Pod`。

```bash
% kubectl exec -it redis-deployment-pv-{hash} \
-n data-services \
-- redis-cli
```

然后，向 `CoffeeOrder` 的流中添加一些记录。

```bash
% xadd com:coffeeco:coffee:v1:orders  * timestamp 1637548381179 orderId ord123 storeId st1 customerId ca100 numItems 6 price 48.00
% xadd com:coffeeco:coffee:v1:orders  * timestamp 1637548458800 orderId ord124 storeId st1 customerId ca101 numItems 3 price 36.00
% xadd com:coffeeco:coffee:v1:orders  * timestamp 1637720977241 orderId ord125 storeId st2 customerId ca102 numItems 1 price 4.99
% xadd com:coffeeco:coffee:v1:orders  * timestamp 1637783601557 orderId ord126 storeId st1 customerId ca103 numItems 2 price 10.99
```

您应该会在驱动程序日志中看到一些活动（打开一个窗口并跟踪日志），或者前往驱动程序 `UI` 查看 `Spark Structured Streaming UI` 或 `Spark SQL` 微批次。当您准备好并确认应用程序正在运行（并处理数据）后，即可删除驱动程序和跳板 `Pod`。

### 删除驱动程序和跳板 Pod

当您删除 `Spark Structured Streaming` 驱动程序时，与该驱动程序关联的所有资源也会被删除。这些资源会通过 `ownerReferences` 自动为您管理。当您完成应用程序运行，或者需要在事故期间关闭系统时，`Kubernetes` 将负责删除由 `spark-submit` 进程生成的任何资源，包括自动生成的 `configMaps`、驱动程序 `Pod` 服务以及执行器 `Pod`。

```bash
% kubectl delete pod/redis-streams-app-driver -n spark-apps
pod "redis-streams-app-driver" deleted
```

虽然驱动程序 `Pod` 及其所有资源现在都已移除，但您的跳板 `Pod` 仍然保留。这是因为您启动了跳板 `Pod`，而您触发的 `spark-submit` 进程生成了驱动程序及相关配置和密钥。当您准备好关闭跳板 `Pod` 时，只需同样将其删除即可。

### 自动化部署与自动恢复

当您对应用程序的稳定性感到满意（一切按预期运行）时，可以应用简单的 `deployment-redis-streams.yaml` 文件来部署上一节中涵盖的所有内容。

```bash
% kubectl apply -f ch-15/spark-on-kubernetes/spark-redis-streams/deployment-redis-streams.yaml
service/redis-service unchanged
configmap/spark-redis-streams-conf unchanged
secret/hivesite-admin configured
secret/minio-access configured
deployment.apps/spark-redis-streams-app created
```

恭喜！您刚刚在 `Kubernetes` 上部署了一个幂等的 `Spark Structured Streaming` 应用程序。

### 第 2 部分：总结

本章的后半部分让您付诸实践，并让您掌控在 `Kubernetes` 上运行 `Spark Structured Streaming` 应用程序。您学习了如何创建可靠的有状态部署，以及如何使用服务别名（外部网络服务类型）在命名空间之间进行通信。本章是一个开端，教您如何充分利用完全容器化的 `Apache Spark` 应用程序。既然您已读完本书，还有许多方向可以探索。在结论之后，我还提供了一些额外的思考内容。敬请继续阅读！

## 结论

首先，感谢您达到这个重要的里程碑，并将本书坚持读完。我希望本书中传授的经验能帮助您以不同的视角思考您的工作、与您共事的工程师、您所效力（或拥有）的公司、您所影响的系统，以及如何优化您作为数据工程师的每日时间投入产出。本书基于我在 Twilio 公司任职时的真实经历、众多研讨会（内部和外部举办）、在 Spark 办公时间（同样在 Twilio）提供指导和调试应用程序的无数时光，以及过去六年与 Apache Spark 打交道并使其与不同数据系统和服务协同工作的无数其他经验教训。我认识到，通往流式处理卓越之路并非总是一帆风顺。但是，只要有时间、耐心，并能够使用本地数据平台或直接在基于 Spark 的单元测试中本地验证假设，您就能以更少的时间和麻烦解决最复杂的问题。

请记住，几乎所有数据问题，无论大小，都可以分解成更小、更易于处理的问题，然后再重新组合起来。花时间去理解手头的问题，制定一个行动计划，并朝着最优解决方案努力。如果问题需要一个更通用的解决方案以实现广泛复用，那就大胆去做。最后，请记住在其中寻找乐趣，并享受您所做的工作。
