# 使用跳板 Pod 控制部署

首先，您将通过一个简单的"跳板"（jump）Pod 中的 `mde/redis-streams-k8s:1.0.0` 容器来部署 Spark 应用程序。如果一开始就全速推进，很容易遗漏某些内容并陷入所谓的"崩溃循环"（crash loop）。您可能会发现，在首次采用任何新的部署模式后，对后续几个应用重复这种缓慢、受控的部署流程是必要的。此过程在调试应用时也很有帮助，因为在将所有内容自动化到正式的有状态部署之前，您可以先检查常见的故障点，例如缺失的配置。

从 `ch-15/spark-on-kubernetes/spark-redis-streams` 目录下，打开 `deployment-manual.yaml`（如列表 15-19 所示）查看 Pod 规范。请特别关注 `volumes` 和 `volumeMounts` 环境变量，以了解 `Secrets` 和 `ConfigMap` 是如何注入到 Pod 中的，以及外部 DNS 值是如何注入到规范中的。

```
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
列表 15-19
Spark 跳板 Pod（spark-redis-streams-app）规范
```

列表 15-20 中的 Pod 规范安全地挂载了您应用到 `spark-apps` 命名空间的密钥和配置。我们使用挂起的 `tail` 命令来保持 Pod 运行（例如在本章第一部分与 Redis 服务交互时）。这允许您 `exec` 进入 `redis-streams-app` 容器进程并运行应用程序。

通过执行以下命令创建（并调度）Pod：

```
% kubectl apply -f deployment-manual.yaml
```

一个 `pod/spark-redis-streams-app` 即被创建。现在，我们可以 `exec` 进入 Pod 并触发应用程序。

## 从跳板 Pod 触发 Spark Submit

使用 `exec` 命令跳转到跳板 Pod，以启动实际的 Spark 应用程序。为了便于理解，可以将跳板 Pod 视为驻留在 K8s 集群中的一个经过身份验证的进程，从而允许用户（使用 `spark-controller` 服务账户）部署应用程序。

```
% kubectl exec --stdin --tty pod/spark-redis-streams-app \
-n spark-apps \
-- bash
```

从 `exec` 进程中，您可以使用我们预先打包在容器内的 `spark-submit.sh` 可执行文件，将应用程序提交到 Kubernetes 集群。它位于以下路径：`/opt/spark/app/conf/spark-submit.sh`。

**提示**

在您首次启动应用程序之前，我始终建议您打开第二个终端窗口，以观察 `spark-submit` 进程如何生成 Pod。您也可以使用 `spark-submit` 进程生成的日志来观察 Spark Kubernetes 资源管理器的操作。

`kubectl get pods -n spark-apps --watch`

在 `spark-redis-streams-app` Pod 的 `exec` 进程中，运行以下命令。

```
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

```
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
列表 15-20
spark-submit.sh 可执行文件
```

现在，让我们换个话题，学习如何查看与 Spark 驱动程序应用程序日志和 Spark Driver UI 相关的运行时信息。



#### 查看驱动程序日志

通过调用 `spark-submit` 生成的驱动程序 Pod（而非启动 Pod 本身）上，可以获取应用程序日志。这种间接性是因为我们使用集群部署模式来部署应用程序。

```
% kubectl logs spark-redis-streams-app-driver -f \
-n spark-apps
```

现在，您可以通过 `-f` 参数实时跟踪驱动程序日志的更新。如果您想转储日志，请省略 `-f` 参数，这样您将看到存储在 `kubelet` 中驱动程序 Pod 的所有可用日志。（Pod 中容器的日志存在内存限制，因此 `-f` 选项允许您在处理流式应用程序时读取所有日志。）

#### 查看驱动程序 UI

与查看驱动程序日志的过程类似，您可以连接到远程驱动程序 Pod，并使用一个名为 `port-forward` 的简单 `kubectl` 命令来转发 Spark UI。

```
% kubectl port-forward {spark-driver-pod} 4040:4040 \
-n spark-apps
```

其精妙之处在于，只有您组织中能够访问您命名空间的成员才能使用 Spark UI。在此示例中，只有您可以访问自己的 minikube 集群。

#### 发送 CoffeeOrder 事件

打开一个新的终端窗口，并通过 `exec` 命令连接到本章第一部分中的 Redis 服务 Pod。

```
% kubectl exec -it redis-deployment-pv-{hash} \
-n data-services \
-- redis-cli
```

然后，向 `CoffeeOrder` 的流中添加一些记录。

```
% xadd com:coffeeco:coffee:v1:orders  * timestamp 1637548381179 orderId ord123 storeId st1 customerId ca100 numItems 6 price 48.00
% xadd com:coffeeco:coffee:v1:orders  * timestamp 1637548458800 orderId ord124 storeId st1 customerId ca101 numItems 3 price 36.00
% xadd com:coffeeco:coffee:v1:orders  * timestamp 1637720977241 orderId ord125 storeId st2 customerId ca102 numItems 1 price 4.99
% xadd com:coffeeco:coffee:v1:orders  * timestamp 1637783601557 orderId ord126 storeId st1 customerId ca103 numItems 2 price 10.99
```

您应该会在驱动程序日志中看到一些活动（打开一个窗口并跟踪日志），或者前往驱动程序 UI 查看 Spark Structured Streaming UI 或 Spark SQL 微批次。当您准备好并确认应用程序正在运行（并处理数据）后，即可删除驱动程序和跳板 Pod。

#### 删除驱动程序和跳板 Pod

当您删除 Spark Structured Streaming 驱动程序时，与该驱动程序关联的所有资源也会被删除。这些资源会通过 `ownerReferences` 自动为您管理。当您完成应用程序运行，或者需要在事故期间关闭系统时，Kubernetes 将负责删除由 `spark-submit` 进程生成的任何资源，包括自动生成的 `configMaps`、驱动程序 Pod 服务以及执行器 Pod。

```
% kubectl delete pod/redis-streams-app-driver -n spark-apps
pod "redis-streams-app-driver" deleted
```

虽然驱动程序 Pod 及其所有资源现在都已移除，但您的跳板 Pod 仍然保留。这是因为您启动了跳板 Pod，而您触发的 `spark-submit` 进程生成了驱动程序及相关配置和密钥。当您准备好关闭跳板 Pod 时，只需同样将其删除即可。

#### 自动化部署与自动恢复

当您对应用程序的稳定性感到满意（一切按预期运行）时，可以应用简单的 `deployment-redis-streams.yaml` 文件来部署上一节中涵盖的所有内容。

```
% kubectl apply -f ch-15/spark-on-kubernetes/spark-redis-streams/deployment-redis-streams.yaml
service/redis-service unchanged
configmap/spark-redis-streams-conf unchanged
secret/hivesite-admin configured
secret/minio-access configured
deployment.apps/spark-redis-streams-app created
```

恭喜！您刚刚在 Kubernetes 上部署了一个幂等的 Spark Structured Streaming 应用程序。

### 第 2 部分：总结

本章的后半部分让您付诸实践，并让您掌控在 Kubernetes 上运行 Spark Structured Streaming 应用程序。您学习了如何创建可靠的有状态部署，以及如何使用服务别名（外部网络服务类型）在命名空间之间进行通信。本章是一个开端，教您如何充分利用完全容器化的 Apache Spark 应用程序。既然您已读完本书，还有许多方向可以探索。在结论之后，我还提供了一些额外的思考内容。敬请继续阅读！

## 结论

首先，感谢您达到这个重要的里程碑，并将本书坚持读完。我希望本书中传授的经验能帮助您以不同的视角思考您的工作、与您共事的工程师、您所效力（或拥有）的公司、您所影响的系统，以及如何优化您作为数据工程师的每日时间投入产出。本书基于我在 Twilio 公司任职时的真实经历、众多研讨会（内部和外部举办）、在 Spark 办公时间（同样在 Twilio）提供指导和调试应用程序的无数时光，以及过去六年与 Apache Spark 打交道并使其与不同数据系统和服务协同工作的无数其他经验教训。我认识到，通往流式处理卓越之路并非总是一帆风顺。但是，只要有时间、耐心，并能够使用本地数据平台或直接在基于 Spark 的单元测试中本地验证假设，您就能以更少的时间和麻烦解决最复杂的问题。

请记住，几乎所有数据问题，无论大小，都可以分解成更小、更易于处理的问题，然后再重新组合起来。花时间去理解手头的问题，制定一个行动计划，并朝着最优解决方案努力。如果问题需要一个更通用的解决方案以实现广泛复用，那就大胆去做。最后，请记住在其中寻找乐趣，并享受您所做的工作。



