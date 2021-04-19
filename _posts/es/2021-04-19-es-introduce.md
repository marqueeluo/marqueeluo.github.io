---
layout:     post
title:      "Elastic入门"
subtitle:   ""
date:       2021-04-19 14:00:00
author:     "luohq"
header-img: "img/about-bg.jpg"
tags:
    - es
    - elastic
---
@[TOC](目录)
# 一、安装ElasticSearch
参考：
[Installing Elasticsearch](https://www.elastic.co/guide/en/elasticsearch/reference/master/install-elasticsearch.html)
[Configuring Elasticsearch](https://www.elastic.co/guide/en/elasticsearch/reference/7.11/settings.html)

#### 1、下载zip并解压
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210220113730936.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x1bzE1MjQyMjA4MzEw,size_16,color_FFFFFF,t_70)
#### 2、初始配置es
通过%ES_HOME%\config\elasticsearch.yml来配置ES，%ES_HOME%即解压后的目录，
如示例中的：D:/programs/dev/elastic/elasticsearch-7.11.1/config/elasticsearch.yml

```yaml
# ======================== Elasticsearch Configuration =========================
#
# NOTE: Elasticsearch comes with reasonable defaults for most settings.
#       Before you set out to tweak and tune the configuration, make sure you
#       understand what are you trying to accomplish and the consequences.
#
# The primary way of configuring a node is via this file. This template lists
# the most important settings you may want to configure for a production cluster.
#
# Please consult the documentation for further information on configuration options:
# https://www.elastic.co/guide/en/elasticsearch/reference/index.html
#
# ---------------------------------- Cluster -----------------------------------
#
# Use a descriptive name for your cluster:
#
cluster.name: my-es
#
# ------------------------------------ Node ------------------------------------
#
# Use a descriptive name for the node:
#
node.name: es-node-1
#
# Add custom attributes to the node:
#
#node.attr.rack: r1
#
# ----------------------------------- Paths ------------------------------------
#
# Path to directory where to store the data (separate multiple locations by comma):
#
#path.data: /path/to/data
#
# Path to log files:
#
#path.logs: /path/to/logs
#
# ----------------------------------- Memory -----------------------------------
#
# Lock the memory on startup:
#
#bootstrap.memory_lock: true
#
# Make sure that the heap size is set to about half the memory available
# on the system and that the owner of the process is allowed to use this
# limit.
#
# Elasticsearch performs poorly when the system is swapping the memory.
#
# ---------------------------------- Network -----------------------------------
#
# Set the bind address to a specific IP (IPv4 or IPv6):
#
#network.host: 192.168.0.1
#
# Set a custom port for HTTP:
#
#http.port: 9200
#
# For more information, consult the network module documentation.
#
# --------------------------------- Discovery ----------------------------------
#
# Pass an initial list of hosts to perform discovery when this node is started:
# The default list of hosts is ["127.0.0.1", "[::1]"]
#
#discovery.seed_hosts: ["host1", "host2"]
#
# Bootstrap the cluster using an initial set of master-eligible nodes:
#
#cluster.initial_master_nodes: ["node-1", "node-2"]
#
# For more information, consult the discovery and cluster formation module documentation.
#
# ---------------------------------- Gateway -----------------------------------
#
# Block initial recovery after a full cluster restart until N nodes are started:
#
#gateway.recover_after_nodes: 3
#
# For more information, consult the gateway module documentation.
#
# ---------------------------------- Various -----------------------------------
#
# Require explicit names when deleting indices:
#
#action.destructive_requires_name: true

```

#### 3、命令行启动
```bash
D:\programs\dev\elastic\elasticsearch-7.11.1\bin>elasticsearch.bat
[2021-02-20T13:07:27,306][INFO ][o.e.n.Node               ] [es-node-1] version[7.11.1], pid[11836], build[default/zip/ff17057114c2199c9c1bbecc727003a907c0db7a/2021-02-15T13:44:09.394032Z], OS[Windows 10/10.0/amd64], JVM[AdoptOpenJDK/OpenJDK 64-Bit Server VM/15.0.1/15.0.1+9]
[2021-02-20T13:07:27,311][INFO ][o.e.n.Node               ] [es-node-1] JVM home [D:\programs\dev\elastic\elasticsearch-7.11.1\jdk], using bundled JDK [true]
[2021-02-20T13:07:27,311][INFO ][o.e.n.Node               ] [es-node-1] JVM arguments [-Des.networkaddress.cache.ttl=60, -Des.networkaddress.cache.negative.ttl=10, -XX:+AlwaysPreTouch, -Xss1m, -Djava.awt.headless=true, -Dfile.encoding=UTF-8, -Djna.nosys=true, -XX:-OmitStackTraceInFastThrow, -XX:+ShowCodeDetailsInExceptionMessages, -Dio.netty.noUnsafe=true, -Dio.netty.noKeySetOptimization=true, -Dio.netty.recycler.maxCapacityPerThread=0, -Dio.netty.allocator.numDirectArenas=0, -Dlog4j.shutdownHookEnabled=false, -Dlog4j2.disable.jmx=true, -Djava.locale.providers=SPI,COMPAT, -XX:+UseG1GC, -Djava.io.tmpdir=C:\Users\luo\AppData\Local\Temp\elasticsearch, -XX:+HeapDumpOnOutOfMemoryError, -XX:HeapDumpPath=data, -XX:ErrorFile=logs/hs_err_pid%p.log, -Xlog:gc*,gc+age=trace,safepoint:file=logs/gc.log:utctime,pid,tags:filecount=32,filesize=64m, -Xms7871m, -Xmx7871m, -XX:MaxDirectMemorySize=4127195136, -XX:G1HeapRegionSize=4m, -XX:InitiatingHeapOccupancyPercent=30, -XX:G1ReservePercent=15, -Delasticsearch, -Des.path.home=D:\programs\dev\elastic\elasticsearch-7.11.1, -Des.path.conf=D:\programs\dev\elastic\elasticsearch-7.11.1\config, -Des.distribution.flavor=default, -Des.distribution.type=zip, -Des.bundled_jdk=true]
[2021-02-20T13:07:29,996][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [aggs-matrix-stats]
[2021-02-20T13:07:29,996][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [analysis-common]
[2021-02-20T13:07:29,996][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [constant-keyword]
[2021-02-20T13:07:29,997][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [flattened]
[2021-02-20T13:07:29,997][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [frozen-indices]
[2021-02-20T13:07:29,997][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [ingest-common]
[2021-02-20T13:07:29,997][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [ingest-geoip]
[2021-02-20T13:07:29,998][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [ingest-user-agent]
[2021-02-20T13:07:29,998][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [kibana]
[2021-02-20T13:07:29,998][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [lang-expression]
[2021-02-20T13:07:29,998][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [lang-mustache]
[2021-02-20T13:07:29,999][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [lang-painless]
[2021-02-20T13:07:29,999][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [mapper-extras]
[2021-02-20T13:07:29,999][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [mapper-version]
[2021-02-20T13:07:29,999][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [parent-join]
[2021-02-20T13:07:29,999][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [percolator]
[2021-02-20T13:07:30,000][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [rank-eval]
[2021-02-20T13:07:30,000][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [reindex]
[2021-02-20T13:07:30,000][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [repositories-metering-api]
[2021-02-20T13:07:30,000][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [repository-url]
[2021-02-20T13:07:30,001][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [search-business-rules]
[2021-02-20T13:07:30,001][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [searchable-snapshots]
[2021-02-20T13:07:30,001][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [spatial]
[2021-02-20T13:07:30,001][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [transform]
[2021-02-20T13:07:30,001][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [transport-netty4]
[2021-02-20T13:07:30,002][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [unsigned-long]
[2021-02-20T13:07:30,002][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [vectors]
[2021-02-20T13:07:30,002][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [wildcard]
[2021-02-20T13:07:30,003][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [x-pack-aggregate-metric]
[2021-02-20T13:07:30,004][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [x-pack-analytics]
[2021-02-20T13:07:30,004][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [x-pack-async]
[2021-02-20T13:07:30,004][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [x-pack-async-search]
[2021-02-20T13:07:30,005][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [x-pack-autoscaling]
[2021-02-20T13:07:30,005][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [x-pack-ccr]
[2021-02-20T13:07:30,006][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [x-pack-core]
[2021-02-20T13:07:30,007][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [x-pack-data-streams]
[2021-02-20T13:07:30,007][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [x-pack-deprecation]
[2021-02-20T13:07:30,008][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [x-pack-enrich]
[2021-02-20T13:07:30,008][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [x-pack-eql]
[2021-02-20T13:07:30,009][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [x-pack-fleet]
[2021-02-20T13:07:30,028][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [x-pack-graph]
[2021-02-20T13:07:30,028][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [x-pack-identity-provider]
[2021-02-20T13:07:30,029][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [x-pack-ilm]
[2021-02-20T13:07:30,030][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [x-pack-ingest]
[2021-02-20T13:07:30,030][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [x-pack-logstash]
[2021-02-20T13:07:30,031][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [x-pack-ml]
[2021-02-20T13:07:30,031][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [x-pack-monitoring]
[2021-02-20T13:07:30,032][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [x-pack-ql]
[2021-02-20T13:07:30,032][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [x-pack-rollup]
[2021-02-20T13:07:30,033][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [x-pack-runtime-fields]
[2021-02-20T13:07:30,033][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [x-pack-security]
[2021-02-20T13:07:30,034][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [x-pack-sql]
[2021-02-20T13:07:30,034][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [x-pack-stack]
[2021-02-20T13:07:30,035][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [x-pack-voting-only-node]
[2021-02-20T13:07:30,035][INFO ][o.e.p.PluginsService     ] [es-node-1] loaded module [x-pack-watcher]
[2021-02-20T13:07:30,036][INFO ][o.e.p.PluginsService     ] [es-node-1] no plugins loaded
[2021-02-20T13:07:30,447][INFO ][o.e.e.NodeEnvironment    ] [es-node-1] using [1] data paths, mounts [[DATA1 (D:)]], net usable_space [1.7tb], net total_space [1.8tb], types [NTFS]
[2021-02-20T13:07:30,447][INFO ][o.e.e.NodeEnvironment    ] [es-node-1] heap size [7.6gb], compressed ordinary object pointers [true]
[2021-02-20T13:07:30,712][INFO ][o.e.n.Node               ] [es-node-1] node name [es-node-1], node ID [2wgQ4iZWR4-T-I4tesC35Q], cluster name [my-es], roles [transform, master, remote_cluster_client, data, ml, data_content, data_hot, data_warm, data_cold, ingest]
[2021-02-20T13:07:34,121][INFO ][o.e.x.m.p.l.CppLogMessageHandler] [es-node-1] [controller/9548] [Main.cc@117] controller (64 bit): Version 7.11.1 (Build b7aec245e3d54f) Copyright (c) 2021 Elasticsearch BV
[2021-02-20T13:07:34,591][INFO ][o.e.x.s.a.s.FileRolesStore] [es-node-1] parsed [0] roles from file [D:\programs\dev\elastic\elasticsearch-7.11.1\config\roles.yml]
[2021-02-20T13:07:35,529][INFO ][o.e.t.NettyAllocator     ] [es-node-1] creating NettyAllocator with the following configs: [name=elasticsearch_configured, chunk_size=1mb, suggested_max_allocation_size=1mb, factors={es.unsafe.use_netty_default_chunk_and_page_size=false, g1gc_enabled=true, g1gc_region_size=4mb}]
[2021-02-20T13:07:35,585][INFO ][o.e.d.DiscoveryModule    ] [es-node-1] using discovery type [zen] and seed hosts providers [settings]
[2021-02-20T13:07:35,942][INFO ][o.e.g.DanglingIndicesState] [es-node-1] gateway.auto_import_dangling_indices is disabled, dangling indices will not be automatically detected or imported and must be managed manually
[2021-02-20T13:07:36,295][INFO ][o.e.n.Node               ] [es-node-1] initialized
[2021-02-20T13:07:36,295][INFO ][o.e.n.Node               ] [es-node-1] starting ...
[2021-02-20T13:07:36,368][INFO ][o.e.x.s.c.PersistentCache] [es-node-1] persistent cache index loaded
[2021-02-20T13:07:36,552][INFO ][o.e.t.TransportService   ] [es-node-1] publish_address {127.0.0.1:9300}, bound_addresses {127.0.0.1:9300}, {[::1]:9300}
[2021-02-20T13:07:36,964][WARN ][o.e.b.BootstrapChecks    ] [es-node-1] the default discovery settings are unsuitable for production use; at least one of [discovery.seed_hosts, discovery.seed_providers, cluster.initial_master_nodes] must be configured
[2021-02-20T13:07:36,967][INFO ][o.e.c.c.Coordinator      ] [es-node-1] cluster UUID [SnAkTjwqRCqTYhYi9iRtkA]
[2021-02-20T13:07:36,985][INFO ][o.e.c.c.ClusterBootstrapService] [es-node-1] no discovery configuration found, will perform best-effort cluster bootstrapping after [3s] unless existing master is discovered
[2021-02-20T13:07:37,163][INFO ][o.e.c.s.MasterService    ] [es-node-1] elected-as-master ([1] nodes joined)[{es-node-1}{2wgQ4iZWR4-T-I4tesC35Q}{qoSQo3X5SX6faLZkdr0FEQ}{127.0.0.1}{127.0.0.1:9300}{cdhilmrstw}{ml.machine_memory=16506888192, xpack.installed=true, transform.node=true, ml.max_open_jobs=20, ml.max_jvm_size=8254390272} elect leader, _BECOME_MASTER_TASK_, _FINISH_ELECTION_], term: 2, version: 40, delta: master node changed {previous [], current [{es-node-1}{2wgQ4iZWR4-T-I4tesC35Q}{qoSQo3X5SX6faLZkdr0FEQ}{127.0.0.1}{127.0.0.1:9300}{cdhilmrstw}{ml.machine_memory=16506888192, xpack.installed=true, transform.node=true, ml.max_open_jobs=20, ml.max_jvm_size=8254390272}]}
[2021-02-20T13:07:37,330][INFO ][o.e.c.s.ClusterApplierService] [es-node-1] master node changed {previous [], current [{es-node-1}{2wgQ4iZWR4-T-I4tesC35Q}{qoSQo3X5SX6faLZkdr0FEQ}{127.0.0.1}{127.0.0.1:9300}{cdhilmrstw}{ml.machine_memory=16506888192, xpack.installed=true, transform.node=true, ml.max_open_jobs=20, ml.max_jvm_size=8254390272}]}, term: 2, version: 40, reason: Publication{term=2, version=40}
[2021-02-20T13:07:37,393][INFO ][o.e.h.AbstractHttpServerTransport] [es-node-1] publish_address {127.0.0.1:9200}, bound_addresses {127.0.0.1:9200}, {[::1]:9200}
[2021-02-20T13:07:37,394][INFO ][o.e.n.Node               ] [es-node-1] started
[2021-02-20T13:07:37,595][INFO ][o.e.l.LicenseService     ] [es-node-1] license [f28bb8cb-c44d-4d5c-b23e-67885903336e] mode [basic] - valid
[2021-02-20T13:07:37,598][INFO ][o.e.x.s.s.SecurityStatusChangeListener] [es-node-1] Active license is now [BASIC]; Security is disabled
[2021-02-20T13:07:37,606][INFO ][o.e.g.GatewayService     ] [es-node-1] recovered [0] indices into cluster_state
```

启动后自动生成data目录，亦可通过path.data, path.log修改相应目录位置
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210220131041792.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x1bzE1MjQyMjA4MzEw,size_16,color_FFFFFF,t_70)

<font color="red"><b>注：</b></font>
关于Jdk版本兼容问题，参见：[https://blog.csdn.net/weixin_43706692/article/details/107494342](https://blog.csdn.net/weixin_43706692/article/details/107494342)
若已指定环境变量JAVA_HOME，则使用指的的Jdk，否则使用内嵌绑定的Jdk: %ES_HOME/jdk，
若已指定JAVA_HOME且版本过低，可通过修改%ES_HOME%/bin/elasticsearch-env.bat来强制使用内嵌Jdk
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210220195506588.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x1bzE1MjQyMjA4MzEw,size_16,color_FFFFFF,t_70)


#### 4、验证安装是否成功

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210220131236795.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x1bzE1MjQyMjA4MzEw,size_16,color_FFFFFF,t_70)
#### 5、安装es为系统服务
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210220131845334.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x1bzE1MjQyMjA4MzEw,size_16,color_FFFFFF,t_70)

```bash
elasticsearch-service.bat install
```
#### 6、安装es插件
https://www.cnblogs.com/hualess/p/11540477.html

# 二、ElastcSearch REST APIs使用
参考：[REST APIs](https://www.elastic.co/guide/en/elasticsearch/reference/7.11/rest-apis.html)
#### 1、CAT
```bash
# The compact and aligned text (CAT) APIs
curl -XGET http://localhost:9200/_cat
=^.^=
/_cat/allocation
/_cat/shards
/_cat/shards/{index}
/_cat/master
/_cat/nodes
/_cat/tasks
/_cat/indices
/_cat/indices/{index}
/_cat/segments
/_cat/segments/{index}
/_cat/count
/_cat/count/{index}
/_cat/recovery
/_cat/recovery/{index}
/_cat/health
/_cat/pending_tasks
/_cat/aliases
/_cat/aliases/{alias}
/_cat/thread_pool
/_cat/thread_pool/{thread_pools}
/_cat/plugins
/_cat/fielddata
/_cat/fielddata/{fields}
/_cat/nodeattrs
/_cat/repositories
/_cat/snapshots/{repository}
/_cat/templates
/_cat/ml/anomaly_detectors
/_cat/ml/anomaly_detectors/{job_id}
/_cat/ml/trained_models
/_cat/ml/trained_models/{model_id}
/_cat/ml/datafeeds
/_cat/ml/datafeeds/{datafeed_id}
/_cat/ml/data_frame/analytics
/_cat/ml/data_frame/analytics/{id}
/_cat/transforms
/_cat/transforms/{transform_id}

# cat api： 
# ?v 详细（包含列标题）
# ?help 列说明
# ?h=h1,h2 指定返回信息列
# ?format=json&pretty 指定结果返回格式- text (default) - json - smile - yaml - cbor
# s=column1:asc,column2:desc,column3 指定结果排序规则
# ?bytes=b&s=store.size:desc&v=true bytes指定byte单位b|kb|mb|gb|tb|pb

# Returns the health status of a cluster
curl -XGET http://localhost:9200/_cat/health?v
epoch      timestamp cluster status node.total node.data shards pri relo init unassign pending_tasks max_task_wait_time active_shards_percent
1613799622 05:40:22  my-es   green           1         1      0   0    0    0        0             0                  -                100.0%

curl -XGET http://localhost:9200/_cat/health?help
epoch                 | t,time                                   | seconds since 1970-01-01 00:00:00
timestamp             | ts,hms,hhmmss                            | time in HH:MM:SS
cluster               | cl                                       | cluster name
status                | st                                       | health status
node.total            | nt,nodeTotal                             | total number of nodes
node.data             | nd,nodeData                              | number of nodes that can store data
shards                | t,sh,shards.total,shardsTotal            | total number of shards
pri                   | p,shards.primary,shardsPrimary           | number of primary shards
relo                  | r,shards.relocating,shardsRelocating     | number of relocating nodes
init                  | i,shards.initializing,shardsInitializing | number of initializing nodes
unassign              | u,shards.unassigned,shardsUnassigned     | number of unassigned shards
pending_tasks         | pt,pendingTasks                          | number of pending tasks
max_task_wait_time    | mtwt,maxTaskWaitTime                     | wait time of longest task pending
active_shards_percent | asp,activeShardsPercent                  | active number of shards in percent

# 查询es索引状态
curl -XGET http://localhost:9200/_cat/indices?v
health status index    uuid                   pri rep docs.count docs.deleted store.size pri.store.size
yellow open   my_index 5uaA2h8CTtWTX8tGOpRmQg   1   1          0            0       208b           208b

curl -XGET http://localhost:9200/_cat/indices?help
health                           | h                              | current health status
status                           | s                              | open/close status
index                            | i,idx                          | index name
uuid                             | id,uuid                        | index uuid
pri                              | p,shards.primary,shardsPrimary | number of primary shards
rep                              | r,shards.replica,shardsReplica | number of replica shards
docs.count                       | dc,docsCount                   | available docs
docs.deleted                     | dd,docsDeleted                 | deleted docs
creation.date                    | cd                             | index creation date (millisecond value)
creation.date.string             | cds                            | index creation date (as string)
store.size                       | ss,storeSize                   | store size of primaries & replicas
pri.store.size                   |                                | store size of primaries
...
```
#### 2、索引操作
```bash
# 创建索引index 
# Default for number_of_shards is 1
# Default for number_of_replicas is 1 (ie one replica for each primary shard)
curl -XPUT http://localhost:9200/my_index
{"acknowledged":true,"shards_acknowledged":true,"index":"my_index"}
# 创建索引index - 指定设置、指定mapping(es 7.x版本后移除type，默认_doc)
curl -XPUT "http://localhost:9200/my_index" -d '
{
    "settings": {
        "number_of_shards": "3",
        "number_of_replicas": "1"
    },
    "mappings": {
        "properties": {
            "type": {
                "type": "keyword"
            },
            "name": {
                "type": "text"
            },
            "user_name": {
                "type": "keyword"
            },
            "email": {
                "type": "keyword"
            },
            "content": {
                "type": "text"
            },
            "tweeted_at": {
                "type": "date"
            }
        }
    }
}
'

{"acknowledged":true,"shards_acknowledged":true,"index":"my_index"}

# 修改mapping
# PUT /<target>/_mapping
# PUT /_mapping

# 获取mapping定义
curl -XGET http://localhost:9200/my_index/_mapping?pretty
{
  "my_index" : {
    "mappings" : {
      "properties" : {
        "content" : {
          "type" : "text"
        },
        "email" : {
          "type" : "keyword"
        },
        "name" : {
          "type" : "text"
        },
        "tweeted_at" : {
          "type" : "date"
        },
        "type" : {
          "type" : "keyword"
        },
        "user_name" : {
          "type" : "keyword"
        }
      }
    }
  }
}


# 删除索引 
curl -XDELETE http://localhost:9200/my_index
{"acknowledged":true}
```
#### 3、文档操作
```bash
# 新增文档document https://www.elastic.co/guide/en/elasticsearch/reference/7.11/docs-index_.html
# PUT /<target>/_doc/<_id>
# POST /<target>/_doc/
# PUT /<target>/_create/<_id>
# POST /<target>/_create/<_id>
curl -XPUT http://localhost:9200/my_index/_doc/user-kimchy -d '
{
  "type": "user", 
  "name": "Shay Banon",
  "user_name": "kimchy",
  "email": "shay@kimchy.com"
}
'

{
    "_index": "my_index",
    "_type": "_doc",
    "_id": "user-kimchy",
    "_version": 1,
    "result": "created",
    "_shards": {
        "total": 2,
        "successful": 1,
        "failed": 0
    },
    "_seq_no": 0,
    "_primary_term": 1
}

curl -XPUT http://localhost:9200/my_index/_doc/tweet-1 -d '
{
  "type": "tweet", 
  "user_name": "kimchy",
  "tweeted_at": "2017-10-24T09:00:00Z",
  "content": "Types are going away"
}
'

{
    "_index": "my_index",
    "_type": "_doc",
    "_id": "tweet-1",
    "_version": 1,
    "result": "created",
    "_shards": {
        "total": 2,
        "successful": 1,
        "failed": 0
    },
    "_seq_no": 0,
    "_primary_term": 1
}

# 根据ID查询文档doc
curl -XGET http://localhost:9200/my_index/_doc/tweet-1
{
    "_index": "my_index",
    "_type": "_doc",
    "_id": "tweet-1",
    "_version": 1,
    "_seq_no": 0,
    "_primary_term": 1,
    "found": true,
    "_source": {
        "type": "tweet",
        "user_name": "kimchy",
        "tweeted_at": "2017-10-24T09:00:00Z",
        "content": "Types are going away"
    }
}
```

# 三、安装Kibana
参考：
[Download Kibana](https://www.elastic.co/cn/downloads/kibana)
[在 Windows 上安装 Kibana](https://www.elastic.co/guide/cn/kibana/current/windows.html)
#### 1、下载zip并解压
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210220200005368.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x1bzE1MjQyMjA4MzEw,size_16,color_FFFFFF,t_70)

#### 2、配置Kibana
Kibana 默认情况下从 $KIBANA_HOME/config/kibana.yml 加载配置文件。该配置文件的格式在 [配置 Kibana](https://www.elastic.co/guide/cn/kibana/current/settings.html) 中做了说明。

```yaml
# Kibana is served by a back end server. This setting specifies the port to use.
server.port: 5601

# Specifies the address to which the Kibana server will bind. IP addresses and host names are both valid values.
# The default is 'localhost', which usually means remote machines will not be able to connect.
# To allow connections from remote users, set this parameter to a non-loopback address.
server.host: "localhost"

# Enables you to specify a path to mount Kibana at if you are running behind a proxy.
# Use the `server.rewriteBasePath` setting to tell Kibana if it should remove the basePath
# from requests it receives, and to prevent a deprecation warning at startup.
# This setting cannot end in a slash.
#server.basePath: ""

# Specifies whether Kibana should rewrite requests that are prefixed with
# `server.basePath` or require that they are rewritten by your reverse proxy.
# This setting was effectively always `false` before Kibana 6.3 and will
# default to `true` starting in Kibana 7.0.
#server.rewriteBasePath: false

# Specifies the public URL at which Kibana is available for end users. If
# `server.basePath` is configured this URL should end with the same basePath.
#server.publicBaseUrl: ""

# The maximum payload size in bytes for incoming server requests.
#server.maxPayloadBytes: 1048576

# The Kibana server's name.  This is used for display purposes.
server.name: "my-kibana"

# The URLs of the Elasticsearch instances to use for all your queries.
elasticsearch.hosts: ["http://localhost:9200"]

# Kibana uses an index in Elasticsearch to store saved searches, visualizations and
# dashboards. Kibana creates a new index if the index doesn't already exist.
kibana.index: ".kibana"

# The default application to load.
#kibana.defaultAppId: "home"

# If your Elasticsearch is protected with basic authentication, these settings provide
# the username and password that the Kibana server uses to perform maintenance on the Kibana
# index at startup. Your Kibana users still need to authenticate with Elasticsearch, which
# is proxied through the Kibana server.
#elasticsearch.username: "kibana_system"
#elasticsearch.password: "pass"

# Enables SSL and paths to the PEM-format SSL certificate and SSL key files, respectively.
# These settings enable SSL for outgoing requests from the Kibana server to the browser.
#server.ssl.enabled: false
#server.ssl.certificate: /path/to/your/server.crt
#server.ssl.key: /path/to/your/server.key

# Optional settings that provide the paths to the PEM-format SSL certificate and key files.
# These files are used to verify the identity of Kibana to Elasticsearch and are required when
# xpack.security.http.ssl.client_authentication in Elasticsearch is set to required.
#elasticsearch.ssl.certificate: /path/to/your/client.crt
#elasticsearch.ssl.key: /path/to/your/client.key

# Optional setting that enables you to specify a path to the PEM file for the certificate
# authority for your Elasticsearch instance.
#elasticsearch.ssl.certificateAuthorities: [ "/path/to/your/CA.pem" ]

# To disregard the validity of SSL certificates, change this setting's value to 'none'.
#elasticsearch.ssl.verificationMode: full

# Time in milliseconds to wait for Elasticsearch to respond to pings. Defaults to the value of
# the elasticsearch.requestTimeout setting.
#elasticsearch.pingTimeout: 1500

# Time in milliseconds to wait for responses from the back end or Elasticsearch. This value
# must be a positive integer.
#elasticsearch.requestTimeout: 30000

# List of Kibana client-side headers to send to Elasticsearch. To send *no* client-side
# headers, set this value to [] (an empty list).
#elasticsearch.requestHeadersWhitelist: [ authorization ]

# Header names and values that are sent to Elasticsearch. Any custom headers cannot be overwritten
# by client-side headers, regardless of the elasticsearch.requestHeadersWhitelist configuration.
#elasticsearch.customHeaders: {}

# Time in milliseconds for Elasticsearch to wait for responses from shards. Set to 0 to disable.
#elasticsearch.shardTimeout: 30000

# Logs queries sent to Elasticsearch. Requires logging.verbose set to true.
#elasticsearch.logQueries: false

# Specifies the path where Kibana creates the process ID file.
#pid.file: /run/kibana/kibana.pid

# Enables you to specify a file where Kibana stores log output.
#logging.dest: stdout

# Set the value of this setting to true to suppress all logging output.
#logging.silent: false

# Set the value of this setting to true to suppress all logging output other than error messages.
#logging.quiet: false

# Set the value of this setting to true to log all events, including system usage information
# and all requests.
#logging.verbose: false

# Set the interval in milliseconds to sample system and process performance
# metrics. Minimum is 100ms. Defaults to 5000.
#ops.interval: 5000

# Specifies locale to be used for all localizable strings, dates and number formats.
# Supported languages are the following: English - en , by default , Chinese - zh-CN .
#i18n.locale: "en"

```

#### 3、启动Kibana

```bash

D:\programs\dev\elastic>kibana.bat
  log   [10:50:36.691] [info][plugins-service] Plugin "visTypeXy" is disabled.
  log   [10:50:36.814] [warning][config][deprecation] "kibana.index" is deprecated. Multitenancy by changing "kibana.index" will not be supported starting in 8.0. See https://ela.st/kbn-remove-legacy-multitenancy for more details
  log   [10:50:36.815] [warning][config][deprecation] Config key [monitoring.cluster_alerts.email_notifications.email_address] will be required for email notifications to work in 8.0."
  log   [10:50:37.901] [info][plugins-system] Setting up [101] plugins: [taskManager,licensing,globalSearch,globalSearchProviders,code,usageCollection,xpackLegacy,telemetryCollectionManager,telemetry,telemetryCollectionXpack,kibanaUsageCollection,securityOss,newsfeed,mapsLegacy,kibanaLegacy,translations,share,expressions,charts,legacyExport,embeddable,uiActionsEnhanced,esUiShared,bfetch,data,home,observability,console,consoleExtensions,apmOss,searchprofiler,painlessLab,grokdebugger,management,indexPatternManagement,advancedSettings,fileUpload,savedObjects,visualizations,visTypeVislib,visTypeTagcloud,visTypeVega,visTypeTimelion,features,licenseManagement,dataEnhanced,watcher,canvas,visTypeTimeseries,visTypeTimeseriesEnhanced,visTypeTable,visTypeMarkdown,visTypeMetric,tileMap,regionMap,mapsOss,lensOss,inputControlVis,graph,timelion,dashboard,dashboardEnhanced,visualize,discover,discoverEnhanced,savedObjectsManagement,spaces,security,savedObjectsTagging,maps,lens,reporting,lists,encryptedSavedObjects,dashboardMode,beatsManagement,cloud,upgradeAssistant,snapshotRestore,fleet,indexManagement,remoteClusters,crossClusterReplication,rollup,indexLifecycleManagement,enterpriseSearch,ml,transform,ingestPipelines,eventLog,actions,alerts,triggersActionsUi,securitySolution,case,stackAlerts,infra,monitoring,logstash,apm,uptime]
  log   [10:50:37.903] [info][plugins][taskManager] TaskManager is identified by the Kibana UUID: afe4b5a3-56e4-41df-b2b5-11964800df12
  log   [10:50:38.626] [warning][config][plugins][security] Generating a random key for xpack.security.encryptionKey. To prevent sessions from being invalidated on restart, please set xpack.security.encryptionKey in the kibana.yml or use the bin/kibana-encryption-keys command.
  log   [10:50:38.627] [warning][config][plugins][security] Session cookies will be transmitted over insecure connections. This is not recommended.
  log   [10:50:38.677] [warning][config][plugins][reporting] Generating a random key for xpack.reporting.encryptionKey. To prevent sessions from being invalidated on restart, please set xpack.reporting.encryptionKey in the kibana.yml or use the bin/kibana-encryption-keys command.
  log   [10:50:38.679] [info][config][plugins][reporting] Chromium sandbox provides an additional layer of protection, and is supported for Win32 OS. Automatically enabling Chromium sandbox.
  log   [10:50:38.684] [warning][config][encryptedSavedObjects][plugins] Generating a random key for xpack.encryptedSavedObjects.encryptionKey. To decrypt encrypted saved objects attributes after restart, please set xpack.encryptedSavedObjects.encryptionKey in the kibana.yml or use the bin/kibana-encryption-keys command.
  log   [10:50:38.702] [warning][fleet][plugins] Fleet APIs are disabled because the Encrypted Saved Objects plugin uses an ephemeral encryption key. Please set xpack.encryptedSavedObjects.encryptionKey in the kibana.yml or use the bin/kibana-encryption-keys command.
  log   [10:50:38.749] [warning][actions][actions][plugins] APIs are disabled because the Encrypted Saved Objects plugin uses an ephemeral encryption key. Please set xpack.encryptedSavedObjects.encryptionKey in the kibana.yml or use the bin/kibana-encryption-keys command.
  log   [10:50:38.758] [warning][alerting][alerts][plugins][plugins] APIs are disabled because the Encrypted Saved Objects plugin uses an ephemeral encryption key. Please set xpack.encryptedSavedObjects.encryptionKey in the kibana.yml or use the bin/kibana-encryption-keys command.
  log   [10:50:38.835] [info][monitoring][monitoring][plugins] config sourced from: production cluster
  log   [10:50:39.002] [info][savedobjects-service] Waiting until all Elasticsearch nodes are compatible with Kibana before starting saved objects migrations...
  log   [10:50:39.086] [info][savedobjects-service] Starting saved objects migrations
  log   [10:50:39.316] [info][plugins-system] Starting [101] plugins: [taskManager,licensing,globalSearch,globalSearchProviders,code,usageCollection,xpackLegacy,telemetryCollectionManager,telemetry,telemetryCollectionXpack,kibanaUsageCollection,securityOss,newsfeed,mapsLegacy,kibanaLegacy,translations,share,expressions,charts,legacyExport,embeddable,uiActionsEnhanced,esUiShared,bfetch,data,home,observability,console,consoleExtensions,apmOss,searchprofiler,painlessLab,grokdebugger,management,indexPatternManagement,advancedSettings,fileUpload,savedObjects,visualizations,visTypeVislib,visTypeTagcloud,visTypeVega,visTypeTimelion,features,licenseManagement,dataEnhanced,watcher,canvas,visTypeTimeseries,visTypeTimeseriesEnhanced,visTypeTable,visTypeMarkdown,visTypeMetric,tileMap,regionMap,mapsOss,lensOss,inputControlVis,graph,timelion,dashboard,dashboardEnhanced,visualize,discover,discoverEnhanced,savedObjectsManagement,spaces,security,savedObjectsTagging,maps,lens,reporting,lists,encryptedSavedObjects,dashboardMode,beatsManagement,cloud,upgradeAssistant,snapshotRestore,fleet,indexManagement,remoteClusters,crossClusterReplication,rollup,indexLifecycleManagement,enterpriseSearch,ml,transform,ingestPipelines,eventLog,actions,alerts,triggersActionsUi,securitySolution,case,stackAlerts,infra,monitoring,logstash,apm,uptime]
  log   [10:50:39.867] [info][plugins][watcher] Your basic license does not support watcher. Please upgrade your license.
  log   [10:50:39.880] [info][crossClusterReplication][plugins] Your basic license does not support crossClusterReplication. Please upgrade your license.
  log   [10:50:39.880] [info][kibana-monitoring][monitoring][monitoring][plugins] Starting monitoring stats collection
  log   [10:50:40.704] [info][listening] Server running at http://localhost:5601
  log   [10:50:41.992] [info][server][Kibana][http] http server running at http://localhost:5601
  log   [10:55:53.827] [warning][fetcher][plugins][telemetry] Error sending telemetry usage data. (FetchError: request to https://telemetry.elastic.co/xpack/v2/send failed, reason: socket hang up)

```
#### 4、查询ES中的数据
（1）创建Index Pattern及指定Time Field
菜单目录：Management -> StackManagement -> Kibana -> Index Patterns -> Create Index Pattern
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210221115358610.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x1bzE1MjQyMjA4MzEw,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021022112220051.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x1bzE1MjQyMjA4MzEw,size_16,color_FFFFFF,t_70)

（2）在Discover界面选中创建的Index Pattern查询相关索引下的数据doc
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210221115802889.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x1bzE1MjQyMjA4MzEw,size_16,color_FFFFFF,t_70)
#### 5、Dev Tools
可在Dev Tools -> Console中执行对ES的操作请求
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210221120224577.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x1bzE1MjQyMjA4MzEw,size_16,color_FFFFFF,t_70)
