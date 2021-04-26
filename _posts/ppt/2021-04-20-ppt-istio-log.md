---
layout:     ppt
title:      "PPT Istio Log"
subtitle:   ""
date:       2021-04-20 08:07:00
author:     "luohq"
header-img: "img/about-bg.jpg"
tags:
    - ppt
    - istio
---
<textarea data-template>
# 1.istio-pilot日志级别
编辑istio-system.deployment.istio-pilot，
<br/>修改args中--log_output_level=default:指定日志级别
---
# 2.istio-policy日志级别设置同istio-pilot
---
# 3.istio-proxy（envoy）日志级别设置
进入istio-proxy容器中，通过如下命令进行设置：
```
curl -X POST localhost:15000/logging?level=trace
```

---

# 4.istio-proxy access日志设置
官方参考：[https://istio.io/docs/tasks/telemetry/logs/access-log/](https://istio.io/docs/tasks/telemetry/logs/access-log/)

修改istio-system.configMap.istio中的accessLogFile: "/dev/stdout"

修改后即可在istio-proxy看到如下accessLog（业务app pod容器无需重启）： 

默认accessLogFormat：

--

通过如下命令查看accessLogFormat配置：
```
istioctl proxy-config listeners <your pod> -n <your namespace> -o json
```

--

[%START_TIME%] \"%REQ(:METHOD)% %REQ(X-ENVOY-ORIGINAL-PATH?:PATH)% %PROTOCOL%\" %RESPONSE_CODE% %RESPONSE_FLAGS% \"%DYNAMIC_METADATA(istio.mixer:status)%\" %BYTES_RECEIVED% %BYTES_SENT% %DURATION% %RESP(X-ENVOY-UPSTREAM-SERVICE-TIME)% \"%REQ(X-FORWARDED-FOR)%\" \"%REQ(USER-AGENT)%\" \"%REQ(X-REQUEST-ID)%\" \"%REQ(:AUTHORITY)%\" \"%UPSTREAM_HOST%\" %UPSTREAM_CLUSTER% %UPSTREAM_LOCAL_ADDRESS% %DOWNSTREAM_LOCAL_ADDRESS% %DOWNSTREAM_REMOTE_ADDRESS% %REQUESTED_SERVER_NAME%\n

--

实际日志与默认格式对比：
```
[2019-09-24T05:57:56.836Z] 
"POST /256/vendorRetire/list HTTP/1.1" 
200 - "-" 
113 6029 110 110 
"-" "Apache-HttpClient/4.5.7 (Java/1.8.0_192)" "d17359bf-7b4c-4c44-8dab-003c59fa810b" "s267.tsp" 
"127.0.0.1:8080" inbound|80|http|s267.tsp.svc.cluster.local - 
172.25.193.104:8080 172.25.123.74:33340 - 

====================================================================================

[%START_TIME%] 
\"%REQ(:METHOD)% %REQ(X-ENVOY-ORIGINAL-PATH?:PATH)% %PROTOCOL%\" 
%RESPONSE_CODE% %RESPONSE_FLAGS% \"%DYNAMIC_METADATA(istio.mixer:status)%\" 
%BYTES_RECEIVED% %BYTES_SENT% %DURATION% %RESP(X-ENVOY-UPSTREAM-SERVICE-TIME)% 
\"%REQ(X-FORWARDED-FOR)%\" \"%REQ(USER-AGENT)%\" \"%REQ(X-REQUEST-ID)%\" \"%REQ(:AUTHORITY)%\" 
\"%UPSTREAM_HOST%\" %UPSTREAM_CLUSTER% %UPSTREAM_LOCAL_ADDRESS% 
%DOWNSTREAM_LOCAL_ADDRESS% %DOWNSTREAM_REMOTE_ADDRESS% %REQUESTED_SERVER_NAME%\n
```

--

Date 
MPP: method path protocol
RFS: respCode respFlag metadataStaus
RSDR: recvBytes sendBytes duration upStreamServiceTime
FARA: x-forward-for user-agent x-request-id authority
UCL: upstream-host upstream-cluster upstream-local-address
DRS: downstream-localhocal-address downstream-remote-address requested-server-name(sni)
3-3-4-4-3-3

--

其中RESPONSE_FLAGS定义如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210413163605230.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x1bzE1MjQyMjA4MzEw,size_16,color_FFFFFF,t_70)

--

envoy log参考：

Envoy官方参考 - access_log#config-access-log-default-format

https://www.envoyproxy.io/docs/envoy/latest/configuration/observability/access_log/usage#configuration

stackoverflow - what-is-istio-proxy-access-log-mean 
--
注：Isito 1.7以后最新默认访问日志格式如下：

[%START_TIME%]
\"%REQ(:METHOD)% %REQ(X-ENVOY-ORIGINAL-PATH?:PATH)% %PROTOCOL%\"
%RESPONSE_CODE% %RESPONSE_FLAGS% %RESPONSE_CODE_DETAILS% %CONNECTION_TERMINATION_DETAILS% \"%UPSTREAM_TRANSPORT_FAILURE_REASON%\"
%BYTES_RECEIVED% %BYTES_SENT% %DURATION% %RESP(X-ENVOY-UPSTREAM-SERVICE-TIME)%
\"%REQ(X-FORWARDED-FOR)%\" \"%REQ(USER-AGENT)%\" \"%REQ(X-REQUEST-ID)%\" \"%REQ(:AUTHORITY)%\"
\"%UPSTREAM_HOST%\" %UPSTREAM_CLUSTER% %UPSTREAM_LOCAL_ADDRESS% 
%DOWNSTREAM_LOCAL_ADDRESS% %DOWNSTREAM_REMOTE_ADDRESS% %REQUESTED_SERVER_NAME% %ROUTE_NAME%\n



参考：ISTIO/DOCS/TASKS/OBSERVABILITY/LOGS/Getting Envoy's Access Logs
![xxx](https://img-blog.csdnimg.cn/20210401085241332.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x1bzE1MjQyMjA4MzEw,size_16,color_FFFFFF,t_70)

---

5.Springboot Tomcat访问日志
开启如下配置：

server.tomcat.accesslog.enabled = true
server.tomcat.accesslog.directory = /data/logs/tomcat

即可在应用容器中/data/logs/tomcat看到如下访问日志：


查看最近的access_log即可看到tomcat的访问日志：
![https://img-blog.csdnimg.cn/2019092610340318.png](https://img-blog.csdnimg.cn/2019092610340318.png)

</textarea>
