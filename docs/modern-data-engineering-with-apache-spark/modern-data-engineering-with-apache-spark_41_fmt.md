# spec

接下来讨论最重要的配置：Pod 规范（`spec`）块本身。如前所述，Pod 为一个或多个容器封装了隔离环境，因此 `PodSpec` 被拆分为多个部分，以便对共享资源进行通用配置，同时包含容器特定的配置。你会在清单 15-2 的 `docker-compose.yaml` 和清单 15-3 的 Redis Pod 规范中看到容器规范中的一些重叠之处。最大的区别在于资源请求和限制的精细控制，以及端口配置。

现在让这个 Pod 投入使用吧。

## 调度 Redis Pod

使用 `kubectl` 我们可以应用 `redis-pod-basic.yaml`，让 Kubernetes 调度我们的 Pod。然后我们最终能够达到与理想世界状态的一致。

```bash
% kubectl apply -f redis-pod-basic.yaml
```

如果一切顺利，你会看到 `kubectl` 输出 `pod/redis-basic` 已创建的消息。接下来让我们找到我们的 Pod。

## 列出、描述和定位 Pod

在命令行中，对 `pods` 资源调用 `get` 命令。

```bash
% kubectl get pods
```

此时你可能感到困惑，为什么没有显示任何结果（在默认命名空间中未找到任何资源）。这是因为 Kubernetes（默认情况下）只会列出来自单个命名空间的资源，而这个命名空间恰好是默认命名空间。通过添加 `explicit` 命名空间或包含 `--all-namespaces` 标志，你可以获得期望的结果。

```bash
% kubectl get pods -n data-services
NAME           READY   STATUS    RESTARTS   AGE
redis-basic    1/1     Running   0          44m
```

现在你可以找到你的 Pod，还可以通过对 Pod 资源使用 Pod 名称并调用 `describe` 动词来获取有关 Pod 本身的更多信息。

```bash
% kubectl describe pod/redis-basic
```

调用 `describe` 的结果可用于获取有关 Pod 本身的详细信息，包括启动日期、容器、节点、IP 地址、当前观测到的 Pod 状态等。

最后，在未来你可能运行了许多 Pod 的情况下，并且想要找到一个特定的 Pod（或一组 Pod），你可以依靠 Pod 资源的元数据标签来找到你要找的内容（跨一个或多个命名空间）。清单 15-4 在所有命名空间中执行通用搜索，以找到任何具有匹配标签（`app=redis`）且类型不是 `pv`（`type!=pv`）的 Pod。

```bash
% kubectl get pods \
--all-namespaces \
-l app=redis,type!=pv
清单 15-4
使用标签选择器跨所有命名空间定位 Pod
```

清单 15-4 中的查询将返回所有匹配的 Pod。在我们的案例中，目前只有一个。

```bash
NAMESPACE      NAME          READY   STATUS    RESTARTS   AGE
data-services  redis-basic   1/1     Running   0          78s
```

我们的下一个任务就是简单地进入新的 Redis Pod，并使用 `redis-cli` 执行几个命令。

## 在 Redis Pod 上执行命令

就像你已经习惯于使用 `docker exec` 命令一样，你可以直接将你的技能迁移到 Kubernetes 和 `kubectl` 上。清单 15-5 中的命令将连接到 `redis-basic` Pod 并启动 `redis-cli`。

```bash
% kubectl exec \
-n data-services \
-it redis-basic \
-- redis-cli
清单 15-5
使用 kubectl exec 连接到正在运行的 Pod
```

设置并获取一个键，以确保 Redis 按预期工作。

```bash
127.0.0.1:6379> set app redis-basic
OK
127.0.0.1:6379> get app
"redis-basic"
```

退出进程（Command+C），这样我们就可以删除 Pod。

## 删除 Redis Pod

你可能已经知道需要什么命令。就像命名空间一样，你可以简单地对 Pod 资源使用 `delete` 动词，通过名称删除 Pod。

```bash
% kubectl delete -n data-services pod/redis-basic
```

那么为什么要费劲做所有这些工作，最后却要删除呢？答案在于我们对特定服务的期望。在现实世界中，考虑到 Pod 几乎可能随时被杀死（由于任何云原生基础设施的正常生命周期），你最终需要一个可靠的机制来确保你的所有服务（包括 Spark 应用程序）能够以一致的方式部署、监控和恢复，且只需有限或无需人工干预。

接下来我们将研究 Kubernetes 的部署资源，学习如何创建一致且可靠的部署。

## 部署与处理故障时自动恢复

在上一节中，你学会了如何配置 Pod 规范。使用简单的 Pod 模板，你可以在 `data-services` 命名空间中调度和运行 Pod。通过这个过程，你学习了如何使用 Pod 的元数据装饰 Pod（使用标签进行标签选择），以及如何配置一个简单的单容器规范。利用你运行 `redis-basic` Pod 所学到的技能，接下来你将学习如何使用 Kubernetes 的部署资源创建受管部署。

### 创建 Kubernetes 部署

*部署*是一种特殊的 K8s 资源，它引入了针对给定 Pod 规范管理和运行可靠部署的扩展能力。作为一项服务管理可靠性，Kubernetes 集群将监督部署中配置和包含的资源，确保诸如节点故障等常见问题不会导致你的服务停滞不前。

创建一个名为 `deploy-redis-memory.yaml` 的新文件，并将清单 15-6 中的部署配置内容添加到文件中。然后保存它。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
name: redis-memory
namespace: data-services
labels:
app: redis-memory
type: canary
spec:
replicas: 1
strategy:
rollingUpdate:
maxUnavailable: 100%
type: RollingUpdate
selector:
matchLabels:
app: redis
type: memory
template:
metadata:
labels:
app: redis
type: memory
spec:
containers:
- name: redis
image: redis:6.2
volumeMounts:
- name: redis-storage
mountPath: /data
imagePullPolicy: Always
ports:
- containerPort: 6379
protocol: TCP
resources:
limits:
memory: "1G"
cpu: "2000m"
requests:
memory: "500Mi"
cpu: "1000m"
volumes:
- name: redis-storage
emptyDir:
medium: Memory
清单 15-6
部署为基于 Pod 的服务提供运行时一致的行为，包括故障时自动恢复，以及处理复制服务滚动更新的开箱即用能力
```

你会注意到，清单 15-6 中的初始部署配置包装了清单 15-3 中引入的 Pod 规范，并做了两个小改动以引入基于 Pod 的本地 `卷` 存储。`卷` 是 Pod 中的共享资源，但不是立即可用于所有容器，每个容器必须使用 `volumeMounts` 声明式地添加卷。将卷挂载（或附加）到容器还带来了额外的好处，即允许每个容器创建自定义挂载路径。

```yaml
volumeMounts:
- name: redis-storage
mountPath: /data
```

稍后我们将介绍 `volumes` 和 `volumeMounts`，但现在请注意，`volumes` 规范是在与 `containers` 规范同级的级别上配置的，而 `volumeMounts` 是在每个容器的基础上配置的。现在让我们创建部署。

### 部署与资源管理

使用清单 15-6 中的部署配置，通过应用配置来创建并启动您的部署。

```bash
% kubectl apply -f deploy-redis-memory.yaml
```

现在部署已经创建成功，让我们来点有趣的尝试。与其详细解析所有部署细节，不如通过以下四个步骤来探究部署：定位、连接、观察和删除。

#### 定位新部署的 Pod

使用本章学到的任意技术，找到您新部署的 `redis-memory` pod。定位后，您会注意到该 pod 的名称比之前更长。例如，我创建的 pod 名称为 `redis-memory-78d89d7c87-zchc2`。如果您想了解原因，可以查看部署元数据。

```
% kubectl get deployment/redis-memory -n data-services
```

现在，连接到被管理的 pod。

### 连接到被管理的 Pod

连接到被管理的 Redis pod，并重复清单 15-5 中的步骤。在断开与 pod 的连接前，请记得检查键是否已保存。

### 观察 Pod 状态与行为

在第二个终端窗口（最好与当前终端会话并排显示）中，运行以下命令。

```
% kubectl get pods -n data-services --watch
```

通过观察 pod，您将能够实时查看目标命名空间中所有 pod 的状态变化。

### 删除被管理的 Pod

回到第一个终端窗口，删除 Redis pod。

```
% kubectl delete -n data-services pod/redis-memory-78d89d7c87-zchc2
```

会发生什么？

您会观察到，在删除被管理 pod 的瞬间，几乎实时就会有一个新 pod 被调度来替换已删除的 pod。

清单 15-7 中的输出显示了我集群上观察到的状态变化消息。输出显示了我的初始 pod（`redis-memory-78d89d7c87-zchc2`）从 `Running` 状态变为 `Terminating` 状态。同时，一个新 pod 被创建，以确保我们始终至少有一个副本，因为我们的部署声明了期望副本数：1。替换的 pod 最初处于 `Pending` 状态，然后变为 `ContainerCreating`，最后变为 `Running`。所有这些过程在本地环境下不到五秒内完成。除非删除部署，否则 Kubernetes 将确保您的 pod 永远运行。

```
NAME                            READY   STATUS              RESTARTS   AGE
redis-memory-78d89d7c87-zchc2   1/1     Running             0          35m
redis-memory-78d89d7c87-zchc2   1/1     Terminating         0          36m
redis-memory-78d89d7c87-p4hzl   0/1     Pending             0          0s
redis-memory-78d89d7c87-p4hzl   0/1     Pending             0          0s
redis-memory-78d89d7c87-p4hzl   0/1     ContainerCreating   0          0s
redis-memory-78d89d7c87-zchc2   0/1     Terminating         0          36m
redis-memory-78d89d7c87-p4hzl   1/1     Running             0          4s
redis-memory-78d89d7c87-zchc2   0/1     Terminating         0          36m
redis-memory-78d89d7c87-zchc2   0/1     Terminating         0          36m
```

清单 15-7 当被管理的 Pod（位于有状态副本集之后）终止时观察到的状态变化

现在来说说坏消息。虽然部署控制器能够恢复已删除的 pod，但您会发现 Redis 数据库中没有任何键。您可以使用 `redis-cli` 自行验证。这是为什么呢？Pod 不应该是一致的和可靠的吗？

事实上，这完全是设计使然。为了声明每个部署是幂等的，并一致地支持运行时容错，pod 及其底层容器必须使用不可变约定。否则，当 pod 终止或遭遇不可恢复故障时，对任何 pod 本地资源（例如向 Redis 数据库添加新键）的运行时更改（变更）将会丢失。鉴于按照约定，pod 需要能够在集群中快速移动，因此最佳实践是由 Kubernetes 管理外部配置、密钥以及存储，以确保一致性。

让我们通过学习如何在部署之间持久化数据，来完成 Kubernetes 的实践之旅。然后，我们将动手实践 Apache Spark 的 Kubernetes 资源管理器。

## 在部署之间持久化数据

Kubernetes 基于 `volumes` 和 `volumeMounts` 的概念，为文件系统（和内存）存储提供了丰富的抽象层。在上一节（清单 15-6）中您已经了解了这两个概念，现在您将学习如何启用能够抵御 pod 故障的持久化存储。

首先，我们将使用节点级存储，它在 Kubernetes 节点上持久存在，并在 pod 终止时防止数据丢失。值得注意的是，如果您删除 minikube 虚拟机，节点级数据也会随之丢失。在命令行中，通过 `ssh` 登录到 minikube，然后创建一个位于 `/mnt/data` 的空目录。

```
% minikube ssh
$ sudo mkdir -p /mnt/data
```

完成此步骤后，我们可以继续创建持久卷资源。

> **警告**
> 在托管的生成环境 Kubernetes 集群（如 EKS/AKS/GKE）中，通常不需要 `ssh` 访问。请考虑限制 `ssh` 访问或完全关闭它，以防止恶意行为者干扰您的 Kubernetes 集群。

### 持久卷（PV）

容器在运行时进行的本地更改，会在 pod 因正常生命周期事件（如全新部署）而终止、失败或以其他方式被替换（重新调度）时消失。正如您所见，这还包括添加到本地 pod 环境中的任何数据库记录。

幸运的是，可以使用底层 Kubernetes 节点的本地文件系统存储，或采用更松耦合的实践，即附加托管的云网络文件系统存储，来为 Kubernetes 集群配置可靠的存储。以主流云厂商的产品为例：亚马逊的[弹性块存储](https://aws.amazon.com/ebs/)、微软的[AzureFile](https://docs.microsoft.com/en-us/azure/storage/files/)/[AzureDisk](https://azure.microsoft.com/en-us/services/storage/disks/)以及谷歌的[持久磁盘](https://cloud.google.com/persistent-disk)。持久卷将存储提供者与实际消费者隔离开来，使得应用能够请求存储，并由 Kubernetes 负责查找并声明与存储请求匹配的资源。

#### 配置持久卷（PV）

创建一个名为 `persistent-volume.yaml` 的文件，并添加清单 15-8 所示的 `PersistentVolume` 资源配置。随后，可将此模板应用于 Kubernetes 集群，以在 minikube 集群的单个节点上物理创建一个持久卷。

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: general-pv-volume
  namespace: data-services
  labels:
    type: local
spec:
  storageClassName: manual
  capacity:
    storage: 2Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/mnt/data"
```

清单 15-8 为 Redis 创建 PersistentVolume (`persistent-volume.yaml`)

清单 15-8 中的资源配置会在 minikube 节点的 `/mnt/data` 目录下创建一个容量为 2GB 的卷（并将其隔离）。应用此配置以创建新资源，然后使用 `persistentvolume`（`pv`）资源类型的 `get` 和 `describe` 命令查看已创建的资源，深入了解这一新的资源类型。

##### 创建持久卷

```
% kubectl apply -f persistent-volume.yaml
```

##### 查看持久卷

```
% kubectl get pv general-pv-volume
NAME               CAPACITY   ACCESS MODES   RECLAIM POLICY
general-pv-volume  2Gi        RWO            Retain
```

##### 描述持久卷的详细信息

```
% kubectl describe pv general-pv-volume
```

有了 `general-pv-volume` 后，下一步是创建一个声明策略，通过 `PersistentVolumeClaims` 资源来管理对卷的读写访问。

## 持久卷声明（PVC）

PV 是集群中特定于存储的资源。PVC 作为一种资源，用于声明 PV 的一部分（或全部）。正如一个节点可以被分割以运行不同规模的工作负载（由 Pod 规范控制），涉及 CPU 核心和内存一样，PVC 代表了一种对特定存储空间的请求，并带有特定的访问模式，以管理对底层存储资源的独占或共享读写访问。

### 配置持久卷声明（PVC）

使用清单 15-9 中的配置，创建一个名为 `persistent-volume-claim.yaml` 的新文件。

```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: redis-pv-claim
  namespace: data-services
spec:
  storageClassName: manual
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 500Mi
清单 15-9
创建 PersistentVolumeClaim (persistent-volume-claim.yaml)
```

清单 15-9 中的 PVC 配置了一个名为 `redis-pv-claim` 的声明，请求至少 500 Mi（兆字节）的存储空间，并采用 `ReadWriteOnce` 访问模式。此访问模式允许一个 Pod 声明对绑定的 PV 进行独占访问，这符合 Redis 服务所需的正确访问模式。你可能也注意到，PVC 完全未提及实际的 PV（这是因为存储供应和存储消费是松耦合的；具体细节被抽象化了）。接下来，创建 PVC 资源，然后使用 `persistentvolumeclaim (pvc)` 资源类型的 `get` 和 `describe` 命令进一步了解你所创建的内容。

#### 创建持久卷声明（PVC）

```
% kubectl apply -f persistent-volume-claim.yaml
```

#### 使用 Get 查看 PVC

检查 PVC 的状态，可以查看声明是否已绑定到某个 PV，包括绑定卷的名称。

```
% kubectl get pvc -n data-services
NAME             STATUS   VOLUME              CAPACITY
redis-pv-claim   Bound    general-pv-volume   2Gi
```

#### 查看 PV

由于 PV 与 PVC 的关系是双向的，你也可以通过 PV 来发现 PVC 声明及其状态（引用）。

```
% kubectl get pv redis-pv-volume
NAME              RECLAIM STATUS CLAIM
general-pv-volume Retain  Bound  data-services/redis-pv-claim
```

**提示：** Kubernetes 会使用 PVC 中的 `resources:requests:storage` 值来尝试找到匹配的 PV。在我们的例子中，尽管我们只请求了 500Mi，但实际上声明了 2Gi。如果你感兴趣，我建议你进一步了解 PV 的动态供应。

### 使用数据持久化增强 Redis 部署

现在，让我们将 PV 和 PVC 付诸实践，并修改之前清单 15-6 中的 Redis 部署。结果发现，我们只需要修改 Pod 规范中的卷配置即可。打开你之前的部署配置文件，将卷类型从 `emptyDir` 修改为 `persistentVolumeClaim`（如清单 15-10 所示）。

```
apiVersion: apps/v1
kind: Deployment
...
spec:
  template:
    spec:
      volumes:
        - name: redis-storage
          persistentVolumeClaim:
            claimName: redis-pv-claim
清单 15-10
使用持久卷声明来持久化数据
```

保存部署的更改并应用更新后的部署。你的 Redis 数据库中保存的任何数据（流对象、键等）都将以有状态且一致的方式在多次部署之间持久保留。按照清单 15-6 的流程执行一遍，然后删除你的 Pod。观察你的 Redis 服务是否在你离开时的状态下重新启动。

## Kubernetes 服务与 DNS

作为最后一步，让我们为 Redis Pod 部署添加一个 Service 定义。Kubernetes 中的服务支持在单个命名空间范围内（默认情况下）进行服务发现，例如，允许你在 `data-services` 命名空间内使用 Redis，该命名空间可用来支持 Airflow 或作为 MySQL 的缓存层。

### 创建服务

创建一个名为 `redis-service.yaml` 的文件，并添加清单 15-11 中的以下 Service 配置。然后使用 `kubectl apply -f` 应用该配置。

```
apiVersion: v1
kind: Service
metadata:
  name: redis-service
  namespace: data-services
spec:
  selector:
    app: redis
    type: memory
  type: ClusterIP
  ports:
    - name: tcp-port
      port: 6379
      targetPort: 6379
      protocol: TCP
清单 15-11
为 Redis 定义 Kubernetes Service 资源
```

清单 15-11 中的 Redis 服务定义创建了一个动态服务，指向与 `Service` 选择器（`app=redis,type=memory`）匹配的零个或多个 Pod 组上的端口 6379。这种抽象使你能够定义一个松耦合的服务，该服务依靠 Kubernetes 使用选择器动态填充端点。绑定的端点是与选择条件匹配的 Pod 子集的 Pod IP 地址。

### 查看服务（SVC）

```
kubectl get services -n data-services
NAME            TYPE        CLUSTER-IP     PORT(S)    AGE
redis-service   ClusterIP   10.106.24.62   6379/TCP   8d
```

我们将在第 2 部分中再次讨论服务。现在，让我们先回顾一下我们所学的内容。

### 第一部分：总结

本章上半部分向你介绍了在 Kubernetes 上运行云原生应用所需的许多核心组件、概念和 API。如前所述，在 Kubernetes 上运行 Apache Spark（使用官方的 [Spark Kubernetes 资源管理器](http://spark.apache.org/docs/latest/running-on-kubernetes.html)）能够抽象化（并保护你免受）启动 K8s 所需的大量运行时细节和复杂性的影响。然而，当从概念验证转向生产环境时，始终有必要不仅为工具，而且为其运行的环境建立坚实的基础。这就是我们逐步讲解如何设置和配置持久化 Redis 部署的原因。

作为后续练习，请将 MySQL 和 MinIO 引入你的本地 Kubernetes 集群，以测试你在本章前半部分学到的内容。你可以参考本书前面的课程，查阅你所需的一切，包括使用 `docker-compose` 文件和初始化步骤。发挥你的创造力来解决这些问题，并在构建心智模型的过程中，通过这个过程更仔细地审视哪些方法有效，哪些无效。

现在，我们期待已久的时刻到了——是时候学习如何在 Kubernetes 上部署你的 Apache Spark 应用了。

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