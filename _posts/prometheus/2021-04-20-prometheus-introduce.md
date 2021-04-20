---
layout:     post
title:      "Istio prometheus"
subtitle:   ""
date:       2021-04-20 13:40:00
author:     "luohq"
header-img: "img/about-bg.jpg"
tags:
    - istio
    - prometheus
---
# Prometheus
参考：

[https://istio.io/latest/docs/ops/integrations/prometheus/](https://istio.io/latest/docs/ops/integrations/prometheus/)
[https://istio.io/latest/docs/tasks/observability/metrics/tcp-metrics/](https://istio.io/latest/docs/tasks/observability/metrics/tcp-metrics/)
[https://prometheus.io/docs/prometheus/latest/getting_started/](https://prometheus.io/docs/prometheus/latest/getting_started/)
[Prometheus-中文文档](https://prometheus.fuckcloudnative.io/)

1、配置文件：


```yaml
# prom服务端全局配置
global:
  # 抓取(scrape)间隔15s 
  scrape_interval:     15s
  # 执行规则（evaluate rules）频率15s
  evaluation_interval: 15s

# prom服务端规则
# recording rules | alerting rules）
rule_files:
  # - "first.rules"
  # - "second.rules"

# 抓取配置 - 定义prom监控的资源
scrape_configs:
  # 监控prom服务端本身（通过http://localhost:9090/metrics）
  - job_name: prometheus
    static_configs:
      # Prometheus expects metrics to be available on targets on a path of /metrics
      - targets: ['localhost:9090']
```

2、启动命令

```bash
./prometheus --config.file=prometheus.yml
```

3、访问方式
[http://localhost:9090](http://localhost:9090)
[http://localhost:9090/metrics](http://localhost:9090/metrics)
[http://localhost:9090/graph](http://localhost:9090/graph)
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021032716171627.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x1bzE1MjQyMjA4MzEw,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021032716184674.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x1bzE1MjQyMjA4MzEw,size_16,color_FFFFFF,t_70)


# 集成node exporter（监控主机host） 
[https://prometheus.io/docs/guides/node-exporter/](https://prometheus.io/docs/guides/node-exporter/)
[https://github.com/prometheus-community/windows_exporter](https://github.com/prometheus-community/windows_exporter)
以下示例使用windows系统演示
1、安装windows_exporter
进入[:link:windows_exportorGitHub主页](https://github.com/prometheus-community/windows_exporte)，查看最新的release
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210327170208859.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x1bzE1MjQyMjA4MzEw,size_16,color_FFFFFF,t_70)
其中exe是可以直接运行的（临时启动或停止），
而msi是直接安装成为系统服务的，
仅用作演示需要所以选用exe形式一键点击启动。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210327170454229.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x1bzE1MjQyMjA4MzEw,size_16,color_FFFFFF,t_70)
启动成功后即可通过[http://localhost:9182/metrics](http://localhost:9182/metrics)访问exporter暴露的metrics

2、修改prom配置以监控windows_exporter

```yaml
...
# 仅在scrapy_configs中添加新的job即可
scrape_configs:
  - job_name: 'prometheus'
    static_configs:
    - targets: ['localhost:9090']
  # 配置windows主机监控
  # 启动job_name被转换为job标签，target转为为instance标签
  - job_name: win_lenovo_node
    static_configs:
    # 此处对应被监控的主机exporter
    - targets: ['localhost:9182']
```
通过prom -> status -> Targets即可查看到该windows主机已被监控
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210327171140718.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x1bzE1MjQyMjA4MzEw,size_16,color_FFFFFF,t_70)


# prometheus概念
1、数据模型
时间序列数据库，每个采样（sample）由以下组成：
1. a float64 value
2. a millisecond-precision timestamp

指标、标签

```bash
格式：
<metric name>{<label name>=<label value>, ...}
示例：
api_http_requests_total{method="POST", handler="/messages"}
```
2、metric类型
Counter
Gauge
Histogram
Summary

3、JOBS AND INSTANCES
- job: The configured job name that the target belongs to.
- instance: The <host>:<port> part of the target's URL that was scraped.
For each instance scrape, Prometheus stores a sample in the following time series:

- `up{job="<job-name>", instance="<instance-id>"}`: 1 if the instance is healthy, i.e. reachable, or 0 if the scrape failed.
- scrape_duration_seconds{job="<job-name>", instance="<instance-id>"}: duration of the scrape.
- scrape_samples_post_metric_relabeling{job="<job-name>", instance="<instance-id>"}: the number of samples remaining after metric relabeling was applied.
- scrape_samples_scraped{job="<job-name>", instance="<instance-id>"}: the number of samples the target exposed.
- scrape_series_added{job="<job-name>", instance="<instance-id>"}: the approximate number of new series in this scrape. New in v2.10

# Prometheus配置
参考：[prometheus/configuration/#relabel_config](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#relabel_config)

1、relabel_config（重写label配置）

```yaml
# The source labels select values from existing labels. Their content is concatenated
# using the configured separator and matched against the configured regular expression
# for the replace, keep, and drop actions.
[ source_labels: '[' <labelname> [, ...] ']' ]

# Separator placed between concatenated source label values.
[ separator: <string> | default = ; ]

# Label to which the resulting value is written in a replace action.
# It is mandatory for replace actions. Regex capture groups are available.
[ target_label: <labelname> ]

# Regular expression against which the extracted value is matched.
[ regex: <regex> | default = (.*) ]

# Modulus to take of the hash of the source label values.
[ modulus: <int> ]

# Replacement value against which a regex replace is performed if the
# regular expression matches. Regex capture groups are available.
[ replacement: <string> | default = $1 ]

# Action to perform based on regex matching.
[ action: <relabel_action> | default = replace ]
```


 **action:** `replace` | `keep` | `drop` | `labelmap` | `hashmod` | `labeldrop` | `labelkeep`
 
 <relabel_action> determines the relabeling action to take:

`replace`: Match regex against the concatenated source_labels. Then, set target_label to replacement, with match group references (${1}, ${2}, ...) in replacement substituted by their value. If regex does not match, no replacement takes place.

1. 设置指标待重写的labels集合为<font color='blue'>source_labels</font>
2. 根据<font color='blue'>regex</font>匹配指标<font color='blue'>source_labels</font>对应的值
3. 满足匹配则将<font color='blue'>replacement</font>作为新的值写入到指标的<font color='blue'>target_label</font>
4. 不满足匹配则不发生改变


`keep`: Drop targets for which regex does not match the concatenated source_labels.

1. 根据<font color='blue'>regex</font>匹配指标<font color='blue'>source_labels</font>对应的值
2. 满足匹配则保留指标的记录（target） 
3. 不满足匹配则丢弃当前指标记录（target） 

`drop`: Drop targets for which regex matches the concatenated source_labels.

`hashmod`: Set target_label to the modulus of a hash of the concatenated source_labels.
`labelmap`: Match regex against all label names. Then copy the values of the matching labels to label names given by replacement with match group references (${1}, ${2}, ...) in replacement substituted by their value.

1. 根据<font color='blue'>regex</font>匹配指标的所有`labels名称`
2. 满足匹配则`拷贝原label值`到<font color='blue'>replacement</font>指定的新label中
3. 不满足匹配则不发生改变


`labeldrop`: Match regex against all label names. Any label that matches will be removed from the set of labels.
`labelkeep`: Match regex against all label names. Any label that does not match will be removed from the set of labels.

2、kubernetes_sd_config
参考：
[prometheus/configuration/#kubernetes_sd_config](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#kubernetes_sd_config)
[纯K8s配置：github - prometheus/examples/prometheus-kubernetes.yml](https://github.com/prometheus/prometheus/blob/release-2.25/documentation/examples/prometheus-kubernetes.yml)

Kubernetes SD configurations allow retrieving scrape targets from `Kubernetes' REST API` and always staying `synchronized with the cluster state`.

**Role types**: `node` | `service` | `pod` | `endpoints` | `ingress`

`node`: The node role discovers `one target per cluster node` with the address defaulting to `the Kubelet's HTTP port`
`service`: The service role discovers a target for `each service port for each service`.
`pod`:The pod role discovers `all pods and exposes their containers as targets`.
`endpoints`:The endpoints role discovers targets from `listed endpoints of a service`.
`ingress`:The ingress role discovers a target for `each path of each ingress`. 

以下为Istio 1.7集成Prometheus默认配置示例：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210329151931955.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x1bzE1MjQyMjA4MzEw,size_16,color_FFFFFF,t_70)


```yaml
global:
  scrape_interval: 15s
  scrape_timeout: 10s
  evaluation_interval: 1m
rule_files:
- /etc/config/recording_rules.yml
- /etc/config/alerting_rules.yml
- /etc/config/rules
- /etc/config/alerts
scrape_configs:
- job_name: prometheus
  honor_timestamps: true
  scrape_interval: 15s
  scrape_timeout: 10s
  metrics_path: /metrics
  scheme: http
  static_configs:
  - targets:
    - localhost:9090
- job_name: kubernetes-apiservers
  honor_timestamps: true
  scrape_interval: 15s
  scrape_timeout: 10s
  metrics_path: /metrics
  scheme: https
  kubernetes_sd_configs:
  - role: endpoints
  bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
  tls_config:
    ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
    insecure_skip_verify: true
  relabel_configs:
  - source_labels: [__meta_kubernetes_namespace, __meta_kubernetes_service_name, __meta_kubernetes_endpoint_port_name]
    separator: ;
    regex: default;kubernetes;https
    replacement: $1
    action: keep
- job_name: kubernetes-nodes
  honor_timestamps: true
  scrape_interval: 15s
  scrape_timeout: 10s
  metrics_path: /metrics
  scheme: https
  kubernetes_sd_configs:
  - role: node
  bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
  tls_config:
    ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
    insecure_skip_verify: true
  relabel_configs:
  - separator: ;
    regex: __meta_kubernetes_node_label_(.+)
    replacement: $1
    action: labelmap
  - separator: ;
    regex: (.*)
    target_label: __address__
    replacement: kubernetes.default.svc:443
    action: replace
  - source_labels: [__meta_kubernetes_node_name]
    separator: ;
    regex: (.+)
    target_label: __metrics_path__
    replacement: /api/v1/nodes/$1/proxy/metrics
    action: replace
- job_name: kubernetes-nodes-cadvisor
  honor_timestamps: true
  scrape_interval: 15s
  scrape_timeout: 10s
  metrics_path: /metrics
  scheme: https
  kubernetes_sd_configs:
  - role: node
  bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
  tls_config:
    ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
    insecure_skip_verify: true
  relabel_configs:
  - separator: ;
    regex: __meta_kubernetes_node_label_(.+)
    replacement: $1
    action: labelmap
  - separator: ;
    regex: (.*)
    target_label: __address__
    replacement: kubernetes.default.svc:443
    action: replace
  - source_labels: [__meta_kubernetes_node_name]
    separator: ;
    regex: (.+)
    target_label: __metrics_path__
    replacement: /api/v1/nodes/$1/proxy/metrics/cadvisor
    action: replace
- job_name: kubernetes-service-endpoints
  honor_timestamps: true
  scrape_interval: 15s
  scrape_timeout: 10s
  metrics_path: /metrics
  scheme: http
  kubernetes_sd_configs:
  - role: endpoints
  relabel_configs:
  - source_labels: [__meta_kubernetes_service_annotation_prometheus_io_scrape]
    separator: ;
    regex: "true"
    replacement: $1
    action: keep
  - source_labels: [__meta_kubernetes_service_annotation_prometheus_io_scheme]
    separator: ;
    regex: (https?)
    target_label: __scheme__
    replacement: $1
    action: replace
  - source_labels: [__meta_kubernetes_service_annotation_prometheus_io_path]
    separator: ;
    regex: (.+)
    target_label: __metrics_path__
    replacement: $1
    action: replace
  - source_labels: [__address__, __meta_kubernetes_service_annotation_prometheus_io_port]
    separator: ;
    regex: ([^:]+)(?::\d+)?;(\d+)
    target_label: __address__
    replacement: $1:$2
    action: replace
  - separator: ;
    regex: __meta_kubernetes_service_label_(.+)
    replacement: $1
    action: labelmap
  - source_labels: [__meta_kubernetes_namespace]
    separator: ;
    regex: (.*)
    target_label: kubernetes_namespace
    replacement: $1
    action: replace
  - source_labels: [__meta_kubernetes_service_name]
    separator: ;
    regex: (.*)
    target_label: kubernetes_name
    replacement: $1
    action: replace
  - source_labels: [__meta_kubernetes_pod_node_name]
    separator: ;
    regex: (.*)
    target_label: kubernetes_node
    replacement: $1
    action: replace
- job_name: kubernetes-service-endpoints-slow
  honor_timestamps: true
  scrape_interval: 5m
  scrape_timeout: 30s
  metrics_path: /metrics
  scheme: http
  kubernetes_sd_configs:
  - role: endpoints
  relabel_configs:
  - source_labels: [__meta_kubernetes_service_annotation_prometheus_io_scrape_slow]
    separator: ;
    regex: "true"
    replacement: $1
    action: keep
  - source_labels: [__meta_kubernetes_service_annotation_prometheus_io_scheme]
    separator: ;
    regex: (https?)
    target_label: __scheme__
    replacement: $1
    action: replace
  - source_labels: [__meta_kubernetes_service_annotation_prometheus_io_path]
    separator: ;
    regex: (.+)
    target_label: __metrics_path__
    replacement: $1
    action: replace
  - source_labels: [__address__, __meta_kubernetes_service_annotation_prometheus_io_port]
    separator: ;
    regex: ([^:]+)(?::\d+)?;(\d+)
    target_label: __address__
    replacement: $1:$2
    action: replace
  - separator: ;
    regex: __meta_kubernetes_service_label_(.+)
    replacement: $1
    action: labelmap
  - source_labels: [__meta_kubernetes_namespace]
    separator: ;
    regex: (.*)
    target_label: kubernetes_namespace
    replacement: $1
    action: replace
  - source_labels: [__meta_kubernetes_service_name]
    separator: ;
    regex: (.*)
    target_label: kubernetes_name
    replacement: $1
    action: replace
  - source_labels: [__meta_kubernetes_pod_node_name]
    separator: ;
    regex: (.*)
    target_label: kubernetes_node
    replacement: $1
    action: replace
- job_name: prometheus-pushgateway
  honor_labels: true
  honor_timestamps: true
  scrape_interval: 15s
  scrape_timeout: 10s
  metrics_path: /metrics
  scheme: http
  kubernetes_sd_configs:
  - role: service
  relabel_configs:
  - source_labels: [__meta_kubernetes_service_annotation_prometheus_io_probe]
    separator: ;
    regex: pushgateway
    replacement: $1
    action: keep
- job_name: kubernetes-services
  honor_timestamps: true
  params:
    module:
    - http_2xx
  scrape_interval: 15s
  scrape_timeout: 10s
  metrics_path: /probe
  scheme: http
  kubernetes_sd_configs:
  - role: service
  relabel_configs:
  - source_labels: [__meta_kubernetes_service_annotation_prometheus_io_probe]
    separator: ;
    regex: "true"
    replacement: $1
    action: keep
  - source_labels: [__address__]
    separator: ;
    regex: (.*)
    target_label: __param_target
    replacement: $1
    action: replace
  - separator: ;
    regex: (.*)
    target_label: __address__
    replacement: blackbox
    action: replace
  - source_labels: [__param_target]
    separator: ;
    regex: (.*)
    target_label: instance
    replacement: $1
    action: replace
  - separator: ;
    regex: __meta_kubernetes_service_label_(.+)
    replacement: $1
    action: labelmap
  - source_labels: [__meta_kubernetes_namespace]
    separator: ;
    regex: (.*)
    target_label: kubernetes_namespace
    replacement: $1
    action: replace
  - source_labels: [__meta_kubernetes_service_name]
    separator: ;
    regex: (.*)
    target_label: kubernetes_name
    replacement: $1
    action: replace
- job_name: kubernetes-pods
  honor_timestamps: true
  scrape_interval: 15s
  scrape_timeout: 10s
  metrics_path: /metrics
  scheme: http
  kubernetes_sd_configs:
  - role: pod
  relabel_configs:
  - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
    separator: ;
    regex: "true"
    replacement: $1
    action: keep
  - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
    separator: ;
    regex: (.+)
    target_label: __metrics_path__
    replacement: $1
    action: replace
  - source_labels: [__address__, __meta_kubernetes_pod_annotation_prometheus_io_port]
    separator: ;
    regex: ([^:]+)(?::\d+)?;(\d+)
    target_label: __address__
    replacement: $1:$2
    action: replace
  - separator: ;
    regex: __meta_kubernetes_pod_label_(.+)
    replacement: $1
    action: labelmap
  - source_labels: [__meta_kubernetes_namespace]
    separator: ;
    regex: (.*)
    target_label: kubernetes_namespace
    replacement: $1
    action: replace
  - source_labels: [__meta_kubernetes_pod_name]
    separator: ;
    regex: (.*)
    target_label: kubernetes_pod_name
    replacement: $1
    action: replace
- job_name: kubernetes-pods-slow
  honor_timestamps: true
  scrape_interval: 5m
  scrape_timeout: 30s
  metrics_path: /metrics
  scheme: http
  kubernetes_sd_configs:
  - role: pod
  relabel_configs:
  - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape_slow]
    separator: ;
    regex: "true"
    replacement: $1
    action: keep
  - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
    separator: ;
    regex: (.+)
    target_label: __metrics_path__
    replacement: $1
    action: replace
  - source_labels: [__address__, __meta_kubernetes_pod_annotation_prometheus_io_port]
    separator: ;
    regex: ([^:]+)(?::\d+)?;(\d+)
    target_label: __address__
    replacement: $1:$2
    action: replace
  - separator: ;
    regex: __meta_kubernetes_pod_label_(.+)
    replacement: $1
    action: labelmap
  - source_labels: [__meta_kubernetes_namespace]
    separator: ;
    regex: (.*)
    target_label: kubernetes_namespace
    replacement: $1
    action: replace
  - source_labels: [__meta_kubernetes_pod_name]
    separator: ;
    regex: (.*)
    target_label: kubernetes_pod_name
    replacement: $1
    action: replace
```

# Prometheus规则（ Rules）

- **recording rules**
>precompute frequently needed or computationally expensive expressions and save their result as a new set of time series


示例
```yaml
groups:
  # group名称
  - name: example
  # How often rules in the group are evaluated.
  [ interval: <duration> | default = global.evaluation_interval ]
  	# group下的规则列表
    rules:
      # 规则名称（即对应新的指标名（metric name））
    - record: job:http_inprogress_requests:sum
      # 规则PromQL expression
      expr: sum by (job) (http_inprogress_requests)
      # Labels to add or overwrite before storing the result.
	  labels:
	     [ <labelname>: <labelvalue> ]
```


- **alerting rules**
>Alerting rules allow you to define alert conditions based on Prometheus expression language expressions and to send notifications about firing alerts to an external service. Whenever the alert expression results in one or more vector elements at a given point in time, the alert counts as active for these elements' label sets.


示例


```yaml
groups:
# group名称
- name: example
  # 规则列表
  rules:
  # 报警名称
  - alert: HighRequestLatency
  	# 报警PromQL expression
    expr: job:request_latency_seconds:mean5m{job="myjob"} > 0.5
    # 持续时间
    for: 10m
    # 重写alert labels
    labels:
      severity: page
    # 重写alert annotations
    annotations:
      summary: High request latency
   # Alert for any instance that is unreachable for >5 minutes.
  - alert: InstanceDown
    expr: up == 0
    for: 5m
    labels:
      severity: page
    annotations:
      summary: "Instance {{ $labels.instance }} down"
      description: "{{ $labels.instance }} of job {{ $labels.job }} has been down for more than 5 minutes."

  # Alert for any instance that has a median request latency >1s.
  - alert: 
groups:
# group名称
- name: example
  # 规则列表
  rules:
  # 报警名称
  - alert: HighRequestLatency
  	# 报警PromQL expression
    expr: job:request_latency_seconds:mean5m{job="myjob"} > 0.5
    # 持续时间
    for: 10m
    # 重写alert labels
    labels:
      severity: page
    # 重写alert annotations
    annotations:
      summary: High request latency
      
  # Alert for any instance that is unreachable for >5 minutes.
  - alert: InstanceDown
    expr: up == 0
    for: 5m
    labels:
      severity: page
    annotations:
      summary: "Instance {{ $labels.instance }} down"
      description: "{{ $labels.instance }} of job {{ $labels.job }} has been down for more than 5 minutes."

  # Alert for any instance that has a median request latency >1s.
  - alert: APIHighRequestLatency
    expr: api_http_request_latencies_second{quantile="0.5"} > 1
    for: 10m
    annotations:
      summary: "High request latency on {{ $labels.instance }}"
      description: "{{ $labels.instance }} has a median request latency above 1s (current value: {{ $value }}s)"
    expr: api_http_request_latencies_second{quantile="0.5"} > 1
    for: 10m
    annotations:
      summary: "High request latency on {{ $labels.instance }}"
      description: "{{ $labels.instance }} has a median request latency above 1s (current value: {{ $value }}s)"
```


# Prometheus Querying

#### four types（4种类型）

In Prometheus's expression language, an expression or sub-expression can evaluate to one of four types:

- **Instant vector** - a set of time series containing a single sample for each time series, all sharing the same timestamp
- **Range vector** - a set of time series containing a range of data points over time for each time series
- **Scalar** - a simple numeric floating point value
- **String** - a simple string value; currently `unused`


#### label matching operators（label匹配 - 操作符）

It is also possible to `negatively match a label value`, or to `match label values against regular expressions`. The following label matching operators exist:

- **=**: Select labels that are exactly equal to the provided string.
- **!=**: Select labels that are not equal to the provided string.
- **=~**: Select labels that regex-match the provided string.
- **!~**: Select labels that do not regex-match the provided string.

#### time durations（持续时间）

Time durations are specified as a number, followed immediately by one of the following units:

- **ms** - milliseconds
- **s** - seconds
- **m** - minutes
- **h** - hours
- **d** - days - assuming a day has always 24h
- **w** - weeks - assuming a week has always 7d
- **y** - years - assuming a year has always 365d

```bash
http_requests_total{job="prometheus"}[5m]
```

The **offset modifier** allows changing the time offset for individual instant and range vectors in a query.

```bash
http_requests_total offset 5m
```
The **@ modifier** allows changing the evaluation time for individual instant and range vectors in a query. The time supplied to the @ modifier is **a unix timestamp** and described with a float literal.

For example, the following expression returns the value of http_requests_total at 2021-01-04T07:40:00+00:00:

```bash
http_requests_total @ 1609746000
```

#### Arithmetic binary operators（二元算数 - 操作符）

- \+ (addition)
- \- (subtraction)
- \* (multiplication)
- / (division)
- % (modulo)
- ^ (power/exponentiation)

#### Comparison binary operators（二元比较 - 操作符）

- == (equal)
- != (not-equal)
- \> (greater-than)
- \< (less-than)
- \>= (greater-or-equal)
- <= (less-or-equal)

#### Logical/set binary operators（二元逻辑比较 - 操作符）

- and (intersection)
- or (union)
- unless (complement)

#### One-to-one vector matches（1对1 - 向量匹配）


```bash
<vector expr> <bin-op> ignoring(<label list>) <vector expr>
<vector expr> <bin-op> on(<label list>) <vector expr>
```

#### Many-to-one and one-to-many vector matches（多对1、1对多 - 向量匹配）


```bash
<vector expr> <bin-op> ignoring(<label list>) group_left(<label list>) <vector expr>
<vector expr> <bin-op> ignoring(<label list>) group_right(<label list>) <vector expr>
<vector expr> <bin-op> on(<label list>) group_left(<label list>) <vector expr>
<vector expr> <bin-op> on(<label list>) group_right(<label list>) <vector expr>
```

#### Aggregation operators（聚合运算 - 操作符）


- **sum** (calculate sum over dimensions)
- **min** (select minimum over dimensions)
- **max** (select maximum over dimensions)
- **avg** (calculate the average over dimensions)
- **group** (all values in the resulting vector are 1)
- **stddev** (calculate population standard deviation over dimensions)
- **stdvar** (calculate population standard variance over dimensions)
- **count** (count number of elements in the vector)
- **count_values** (count number of elements with the same value)
- **bottomk** (smallest k elements by sample value)
- **topk** (largest k elements by sample value)
- **quantile** (calculate φ-quantile (0 ≤ φ ≤ 1) over dimensions)

```bash
<aggr-op> [without|by (<label list>)] ([parameter,] <vector expression>)
<aggr-op>([parameter,] <vector expression>) [without|by (<label list>)]
```

参考：

[标准差stddev和方差stdvar的区别-标准差和方差公式](http://www.ab126.com/goju/9539.html)

#### Binary operator precedence（二元操作符优先级）

1. ^
2. *, /, %
3. +, -
4. ==, !=, <=, <, >=, >
5. and, unless
6. or

#### FUNCTIONS（函数）

参考：[【通俗易懂】CSDN - Prometheus的函数和计算公式](https://blog.csdn.net/wc1695040842/article/details/107013799)
- abs()
- absent()
- absent_over_time()
- ceil()
- changes()
- clamp_max()
- clamp_min()
- day_of_month()
- day_of_week()
- days_in_month()
- delta()
- deriv()
- exp()
- floor()
- histogram_quantile()
- holt_winters()
- hour()
- idelta()
- <font color='green'>increase()</font>

>increase() 函数表示某段时间内数据的增量
>rate() 函数则表示某段时间内数据的平均值
>两个函数如何选取使用？
>两个函数都是专门搭配counter数据类型使用函数
>当我们获取数据比较精细的时候 类似于1m取样推荐使用rate()
>当我们获取数据比较粗糙的时候 类似于5m，10m甚至更长时间取样推荐使用increase()
>例如：获取eth0网卡1m内流量的增量
>increase(node_network_receive_bytes_total{device="eth0"}[1m])

- irate()
- label_join()
- label_replace()
- ln()
- log2()
- log10()
- minute()
- month()
- predict_linear()
- <font color='green'>rate()</font>

>rate() 函数是专门搭配counter数据类型使用函数，功能是取counter在这个时间段中平均每秒的增量。
>例如：获取eth0网卡1m内每秒流量的平均值
>rate(node_network_receive_bytes_total{device="eth0"}[1m])


- resets()
- round()
- scalar()
- sort()
- sort_desc()
- sqrt()
- time()
- timestamp()
- vector()
- year()
- <aggregation>_over_time()




| 参数 | 描述 | 
| :-----: | :----: |
 |--config.file="alertmanager.yml"|指定Alertmanager配置文件路径| 
 |--storage.path="data/"|Alertmanager的数据存放目录|
  |--data.retention=120h |历史数据保留时间，默认为120h| 
  |--alerts.gc-interval=30m|警报gc之间的间隔|
   |--web.external-url=WEB.EXTERNAL-URL|外部可访问的Alertmanager的URL(例如Alertmanager是通过nginx反向代理)| 
   |--web.route-prefix=WEB.ROUTE-PREFIX|wen访问内部路由路径，默认是 --web.external-url|
   |--web.listen-address=":9093"|监听端口，可以随意修改| 
   |--web.get-concurrency=0|并发处理的最大GET请求数，默认为0|
   |--web.timeout=0|web请求超时时间| 
   |--cluster.listen-address="0.0.0.0:9094"|集群的监听端口地址。设置为空字符串禁用HA模式| 
   |--cluster.advertise-address=CLUSTER.ADVERTISE-ADDRESS|配置集群通知地址| 
   |--cluster.gossip-interval=200ms|发送条消息之间的间隔，可以以增加带宽为代价更快地跨集群传播。| 
   |--cluster.peer-timeout=15s|在同级之间等待发送通知的时间| 
   |...|...|
   |--log.level=info|自定义消息格式 [debug, info, warn, error]|
   |--log.format=logfmt|日志消息的输出格式: [logfmt, json]|
   |--version|显示版本号|