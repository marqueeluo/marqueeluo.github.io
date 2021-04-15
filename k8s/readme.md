@[TOC](目录)

参考：
[K8S - Horizontal Pod Autoscaler](https://v1-18.docs.kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/#support-for-metrics-apis)
[K8S - horizontal-pod-autoscale-walkthrough](https://v1-18.docs.kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale-walkthrough)
[简书 - 探索Kubernetes HPA](https://zhuanlan.zhihu.com/p/89453704)
[Github - K8S metrics Implementations](https://github.com/kubernetes/metrics/blob/master/IMPLEMENTATIONS.md#custom-metrics-api)
[Github - metrics-server](https://github.com/kubernetes-sigs/metrics-server)


## 一、Horizontal Pod Autoscaler

The Horizontal Pod Autoscaler <font color='blue'>automatically scales the number of pods</font> in a <font color='green'>replication controller, deployment, replica set or stateful set</font> based on observed <font color='red'>CPU utilization (or, with custom metrics support, on some other application-provided metrics)</font>. Note that Horizontal Pod Autoscaling does not apply to objects that can't be scaled, for example, DaemonSets.

The Horizontal Pod Autoscaler is implemented as a Kubernetes API resource and a controller. The resource determines the behavior of the controller. <font color='red'>The controller periodically adjusts the number of replicas</font> in a replication controller or deployment to match the observed average CPU utilization to the target specified by user.

The Horizontal Pod Autoscaler is implemented as a control loop, with <font color='red'>a period controlled by the controller manager's --horizontal-pod-autoscaler-sync-period flag (with a default value of <b>15 seconds</b>)</font>.

## 二、Algorithm Details

From the most basic perspective, the Horizontal Pod Autoscaler controller operates on the ratio between desired metric value and current metric value:
#### 1、计算公式

> desiredReplicas = ceil[currentReplicas * ( currentMetricValue / desiredMetricValue )]
> 期望副本数=ceil[当前副本数*(当前指标值/期望指标值)]
> 期望副本数/ 当前副本数 = 当前指标值/期望指标值
> 其中期望副本数则为autoscale后的待设置的副本数
> 当前副本数则为被监控target pod的当前实例个数
> 当前指标值则为HPA指定metric的当前值
> 期望指标值则为HPA中指定的averageValue, averageUtilizationValue

When a **targetAverageValue** or **targetAverageUtilization** is specified, the **currentMetricValue** is computed by taking **the average of the given metric across all Pods in the HorizontalPodAutoscaler's scale target**. Before checking the tolerance and deciding on the final values, we take pod readiness and missing metrics into consideration, however.

> **targetAverageValue**: 所有目标pod的metric的平均值 
> **targetAverageUtilization**: 所有目标pod的metric的使用率（百分比）的平均值，例如limit.cpu=1000m，实际使用500m，则utilization=50%，例如deployment.replica=3, limit.cpu=1000m，则pod1实际使用cpu=500m, pod2=300m, pod=600m，则averageUtilization=(500/1000+300/1000+600/1000)/3 = (500 + 300 + 600)/(3*1000)）

#### 2、默认行为
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200921152429569.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x1bzE1MjQyMjA4MzEw,size_16,color_FFFFFF,t_70#pic_left)



**缩容策率：**
稳定窗口 **5分钟**（或–horizontal-pod-autoscaler-downscale-stabilization）
允许同时停止 **当前运行副本数*100%** 的pod数量，即可以一次性将缩放目标缩小到最小允许的副本

**扩容策略：**
无稳定窗口（立即触发扩容）
策略1：允许 **每隔15秒** 增加 **当前运行副本数*100%** 的pod数量
策略2：允许 **每隔15秒** 增加 **4** 个pod数量
且取两个策略中的最大改变值the highest amount of change


## 三、示例

```yaml
apiVersion: autoscaling/v2beta2
kind: HorizontalPodAutoscaler
metadata:
  name: php-apache
  namespace: default
spec:
  # HPA的伸缩对象描述，HPA会动态修改该对象的pod数量
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: php-apache
  # HPA的最小pod数量和最大pod数量
  minReplicas: 1
  maxReplicas: 10
  # 监控的指标数组，支持多种类型的指标共存
  metrics:
  # 【Resource】类型的指标，该类型指标依赖metric-server（目前仅支持cpu和memory）
  - type: Resource
    resource:
      # 支持cpu | memory
      name: cpu
      # Resource类型的target只支持Utilization和AverageValue类型的目标值
      target:
        # Utilization类型的目标值(type:Utilization, averageUtilization: 具体百分值)
        # AverageValue类型的目标值(type:AverageValue, averageValue: 具体值)
        type: Utilization
        averageUtilization: 50
  # Pods类型的指标
  - type: Pods
    pods:
      metric:
        name: packets-per-second
      # AverageValue类型的目标值，Pods指标类型下只支持AverageValue类型的目标值
      target:
        type: AverageValue
        averageValue: 1k
  # Object类型的指标
  - type: Object
    object:
      metric:
        # 指标名称
        name: requests-per-second
      # 监控指标的对象描述，指标数据来源于该对象
      describedObject:
        apiVersion: networking.k8s.io/v1beta1
        kind: Ingress
        name: main-route
      # Value类型的目标值，Object类型的指标只支持Value和AverageValue类型的目标值
      target:
        type: Value
        value: 10k
  # External类型的指标
  - type: External
    external:
      metric:
        name: queue_messages_ready
        # 该字段与第三方的指标标签相关联
        selector:
          matchLabels:
            env: "stage"
            app: "myapp"
      # External指标类型下只支持Value和AverageValue类型的目标值
      target:
        type: AverageValue
        averageValue: 30
```
autoscaling/v1版本将metrics字段放在了annotation中进行处理。
若同时存在多个metrics块，则HPA依次计算每个metrics块对应的期望副本数，然后选择maxValue为最后的期望副本数。
**metrics.{type}.target.type共有3种类型**

 1. **Utilization**：平均使用率 
 2. **AverageValue**：平均值 
 3. **Value**：裸值

**metrics.type字段有四种类型**
1. **Resource**：指的是当前伸缩对象下的pod的cpu和memory指标，只支持Utilization和AverageValue类型的目标值。
2. **Object**：指定k8s内部对象的指标，数据需要第三方adapter提供，只支持Value和AverageValue类型的目标值。
3. **Pods**：伸缩对象（statefulSet、replicaController、replicaSet）底下的Pods的指标，数据需要第三方的adapter提供，并且只允许AverageValue类型的目标值。
4. **External**：k8s外部的指标，数据同样需要第三方的adapter提供，只支持Value和AverageValue类型的目标值。


若需简单使用，可结合metrics-server、resource类型，通过对pod的**cpu**或**memory**进行监控来进行自动伸缩pod副本数，在程序负载低时释放服务器资源，在程序负载突然升高时自动扩充pod副本数，以此应对流量访问高峰。
在使用前需确保：
1. 首先已安装metrics-server，是否支持kubectl top node|pod -n {yourNamespace}
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200922174841161.png#pic_left)
2. 其次检查是否支持metrics.api
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200922174940474.png#pic_left)



例如根据cpu自动伸缩

```yaml
apiVersion: autoscaling/v2beta2
kind: HorizontalPodAutoscaler
metadata:
  name: luo-vp-hpa
  namespace: tsp
spec:
  # HPA的伸缩对象描述，HPA会动态修改该对象的pod数量
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: mx-vehicle-parts-management
  # HPA的最小pod数量和最大pod数量
  minReplicas: 1
  maxReplicas: 10
  # 监控的指标数组，支持多种类型的指标共存
  metrics:
  # 【Resource】类型的指标，该类型指标依赖metric-server（目前仅支持cpu和memory）
  - type: Resource
    resource:
      # 支持cpu
      name: cpu
      # Resource类型的target只支持Utilization和AverageValue类型的目标值
      target:
        # Utilization类型的目标值(type:Utilization, averageUtilization: 具体百分值)
        type: Utilization
        averageUtilization: 50
```

针对memory自动伸缩

```yaml
apiVersion: autoscaling/v2beta2
kind: HorizontalPodAutoscaler
metadata:
  name: luo-vp-hpa
  namespace: tsp
spec:
  # HPA的伸缩对象描述，HPA会动态修改该对象的pod数量
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: mx-vehicle-parts-management
  # HPA的最小pod数量和最大pod数量
  minReplicas: 1
  maxReplicas: 10
  # 监控的指标数组，支持多种类型的指标共存
  metrics:
  # 【Resource】类型的指标，该类型指标依赖metric-server（目前仅支持cpu和memory）
  - type: Resource
    resource:
      # 支持memory
      name: memory
      # Resource类型的target只支持Utilization和AverageValue类型的目标值
      target:
        # AverageValue类型的目标值(type:AverageValue, averageValue: 具体值)
        type: AverageValue
        averageValue: 1Gi
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200923085736955.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x1bzE1MjQyMjA4MzEw,size_16,color_FFFFFF,t_70#pic_left)
