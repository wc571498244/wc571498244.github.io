---
title: pod假死
top: false
cover: false
toc: true
mathjax: true
date: 2021-10-08 17:24:00
password:
summary:
tags: Kubernetes
categories: Kubernetes
---

## K8S pod假死处理方案
近期在调研新技术方案, 开发和灰度过程中遇到pod假死问题; 总结出两个方案, 可供参考.
详细内容: https://jimmysong.io/kubernetes-handbook/guide/configure-liveness-readiness-probes.html

### 案例1
pod在运行一段时间, 出现pod假死; 假死之后流量还是会正常打入到该pod, 无法处理这部分请求, 体现在API接口上就是大量504超时.

#### 处理方法
添加pod健康检查, 探测方式：exec方式, http探测, tcp探测; 可自行根据服务添加合理方式.
示例http探测方式:
```yaml
livenessProbe:
    httpGet:
        path: /healthy
        port: 80
    initialDelaySeconds: 5  # 容器启动 5s 之后开始执行 Liveness 探测
    periodSeconds: 5  # 每 5s 探测一次
```

### 案例2
kafka消费异常, 某个pod消费能力不足(原因有许多, 比如代码需优化, 消息内容大, DB压力大等...), 导致rebalance, 从而同一个消费者组内的其他pod也rebalance, 数次之后, 出现pod假死, 表现为消息不消费, 观察pod无日志输出.

#### 处理方法
添加生命探测, 与上面差不多.
```yaml
livenessProbe:
	exec:
  	command:
    - /bin/bash
    - probe.sh
    failureThreshold: 3
    periodSeconds: 6
    successThreshold: 1
    timeoutSeconds: 2
```

probe.sh
```sh
#!/bin/bash

outputfile=./health_check

echo "OK" > ${outputfile}

result=$(cat ${outputfile})

if [[ "${result}" == "OK" ]]
then
    # sleep 10s
    sleep 1s
    rm -rf ${outputfile}
    exit 0
else
    exit 1
fi
```