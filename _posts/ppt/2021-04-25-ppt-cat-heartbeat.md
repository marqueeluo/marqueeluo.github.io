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
#### ❤️CAT心跳指标
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

#### ⭐cat.status
![img](/img/in-post/cat-heartbeat/cat-status.png)

---

#### ⭐jvm.memory
![img](/img/in-post/cat-heartbeat/cat-jvm-1.png)

--

#### ⭐jvm.memory
![img](/img/in-post/cat-heartbeat/cat-jvm-2.png)

---

#### ⭐jvm.gc
![img](/img/in-post/cat-heartbeat/cat-jvmgc.png)

---

#### ⭐jvm.thread
![img](/img/in-post/cat-heartbeat/cat-thread.png)

---

#### ⭐system.static
![img](/img/in-post/cat-heartbeat/cat-static.png)

---

### ⭐jvm.classingloading
![img](/img/in-post/cat-heartbeat/cat-class.png)

---

#### ⭐system.process
![img](/img/in-post/cat-heartbeat/cat-systemprocess.png)

---

#### ⭐http.status
![img](/img/in-post/cat-heartbeat/cat-http.png)
</textarea>