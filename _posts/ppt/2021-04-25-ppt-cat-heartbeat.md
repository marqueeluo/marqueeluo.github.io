---
layout:     ppt
title:      "【PPT】Cat心跳指标说明"
subtitle:   ""
date:       2021-04-25 11:42:00
author:     "luohq"
header-img: "img/about-bg.jpg"
tags:
    - ppt
---
<textarea data-template>
```xml
<!-- https://mvnrepository.com/artifact/com.dianping.cat/cat-client -->
<dependency>
    <groupId>com.dianping.cat</groupId>
    <artifactId>cat-client</artifactId>
    <version>3.0.0</version>
</dependency>
```

**注：**
以下的指标都是以1分钟为统计单位，且每分钟后都重新计数

--

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210325110334799.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x1bzE1MjQyMjA4MzEw,size_16,color_FFFFFF,t_70)

---

#### cat.status
| 指标 | 说明      |
|:-------- | :-------|
cat.status.send.sample.ratio | cat客户端采样比例（默认100%）
cat.status.send.queue.size | cat客户端普通消息队列size
cat.status.send.atomic.queue.size | cat客户端atomic队列size（<font color='red'>TODO 待研究源码</font>）
cat.status.message.bytes | cat客户端上报的消息message字节byte数量
cat.status.message.produced | cat客户端上报的消息message数量
cat.status.message.overflowed | cat客户端丢弃的（1小时以前的）消息message数量

---

#### jvm.memory - 1
| 指标 | 说明      |
|:-------- | :-------|
jvm.memory.used | jvm实际使用的内存大小（单位：字节）<br/>`total - free` <br/> `cur capacity - free`<br/>` cur utilization`
jvm.memory.used.percent | jvm内存使用率（当前使用 / 最大允许使用 * 100）
jvm.memory.nonheap.used | jvm实际使用的非堆内存大小（单位：字节）
jvm.memory.nonheap.used.percent | jvm非堆内存使用率（当前使用 / 最大允许使用 * 100）
jvm.memory.oldgen.used | jvm老生代实际使用的内存大小（单位：字节）
jvm.memory.oldgen.used.percent |  jvm老生代使用率（当前使用 / 最大允许使用 * 100）
jvm.memory.oldgen.used.percent.after.fullgc |  执行过fullgc后的jvm老生代使用率（当前使用 / 最大允许使用 * 100），若当前时间段未执行fullgc，则该值为0
jvm.memory.eden.used | jvm新生代实际使用的内存大小（单位：字节）
jvm.memory.eden.used.percent |  jvm新生代使用率（当前使用 / 最大允许使用 * 100）
jvm.memory.survivor.used | jvm survivor区实际使用的内存大小（单位：字节）

--

#### jvm.memory - 2
| 指标 | 说明      |
|:-------- | :-------|
jvm.memory.survivor.used.percent |  jvm survivor区使用率（当前使用 / 最大允许使用 * 100）
jvm.memory.perm.used |  jvm永久代实际使用的内存大小（单位：字节）<br/>`适用于<JDK8`
jvm.memory.perm.used.percent | jvm永久代使用率（当前使用 / 最大允许使用 * 100）<br/>`适用于<JDK8`
jvm.memory.metaspace.used |  jvm元空间实际使用的内存大小（单位：字节）<br/>`适用于JDK8`
jvm.memory.metaspace.used.percent |  jvm元空间使用率（当前使用 / 最大允许使用 * 100）<br/>`适用于JDK8`
jvm.memory.codecache.used | jvm代码缓冲区实际使用的内存大小（单位：字节）<br/>`适用于JDK8`
jvm.memory.codecache.used.percent | jvm代码缓冲区使用率（当前使用 / 最大允许使用 * 100）<br/>`适用于JDK8`
jvm.nio.directbuffer.used | java.nio:type=BufferPool,name=direct对象使用内存（单位：字节）
jvm.nio.mapped.used | java.nio:type=BufferPool,name=mapped对象使用内存（单位：字节）

---

#### jvm.gc
| 指标 | 说明      |
|:-------- | :-------|
jvm.gc.count | jvm gc总次数（包括younggc和fullgc）
jvm.gc.time | jvm gc持续时长总和 （包括younggc和fullgc）
jvm.fullgc.count | jvm fullgc次数
jvm.fullgc.time | jvm fullgc持续时长总和
jvm.younggc.count | jvm younggc次数
jvm.younggc.time | jvm younggc持续时长总和
jvm.younggc.meantime | jvm younggc平均时长

---

#### jvm.thread - 1 
| 指标 | 说明      |
|:-------- | :-------|
jvm.thread.count | 所有活动的线程数（包括daemon和非daemon）
jvm.thread.daemon.count | 当前存活的daemon线程数
jvm.thread.totalstarted.count | 自jvm启动后累计创建的线程数
jvm.thread.new.count | 当前new状态的线程数量
jvm.thread.runnable.count | 当前runnable状态的线程数量
jvm.thread.blocked.count | 当前blocked状态的线程数量
jvm.thread.waiting.count | 当前waiting状态的线程数量

--

#### jvm.thread - 2 
| 指标 | 说明      |
|:-------- | :-------|
jvm.thread.time_waiting.count | 当前time_waiting状态的线程数量
jvm.thread.terminated.count | 当前terminated状态的线程数量
jvm.thread.deadlock.count | 当前发生死锁的线程数量
jvm.thread.http.count | tomcat, jetty工作线程数量（ http-, catalina-exec-, @qtp）
jvm.thread.cat.count | CAT客户端工作线程数量（Cat-, cat-）
jvm.thread.pigeon.count | pigeon工作线程数量（Pigeon-, DPSF-, Client-ResponseProcessor）

---

#### system.static
| 指标 | 说明      |
|:-------- | :-------|
system.java.classpath | Java classPath路径
system.java.verision | Java版本（对应系统属性java.version）
system.user.name | Java用户名（对应系统属性user.name）
java.cat.version | cat版本

---

### jvm.classingloading
|:-------- | :-------|
jvm.classloading.loaded.count | 当前Jvm加载的类数量
jvm.classloading.totalloaded.count | Jvm启动后累计加载的类数量
jvm.classloading.unloaded.count | Jvm启动后累计卸载的类数量

---

#### system.process
| 指标 | 说明      |
|:-------- | :-------|
system.load.average | 操作系统级（即所属宿主机）上一分钟的平均系统负载<br/>（系统负载即同时运行的进程数，通常系统负载不超过CPU数量即为正常）<br/>`getSystemLoadAverage`
cpu.system.load.percent   | 操作系统级（即所属宿主机）cpu使用率（包括全部CPU，取值范围0-100）<br/>`getSystemCpuLoad()`
cpu.jvm.load.percent  | jvm进程（包括jvm内部线程和应用线程）的cpu使用率（与全部CPU进行比较）<br/>`getProcessCpuLoad()`
system.process.used.phyical.memory |   操作系统级（即所属宿主机）当前使用内存大小（单位：字节）<br/>`getTotalPhysicalMemorySize() - getFreePhysicalMemorySize()`
system.process.used.swap.size | 操作系统级（即所属宿主机）当前使用交换空间大小（单位：字节）<br/>`getTotalSwapSpaceSize() - getFreeSwapSpaceSize()`

---

#### http.status
| 指标 | 说明      |
|:-------- | :-------|
http.count | http请求数量
http.meantime  | http请求平均耗时（单位：毫秒）
http.status400.count  | 响应状态为status=4xx的http请求数量
http.status500.count  | 响应状态为status=5xx的http请求数量
</textarea>