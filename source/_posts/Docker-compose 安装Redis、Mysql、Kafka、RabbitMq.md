---
title: Docker-compose 安装Redis、Mysql、Kafka、RabbitMq
top: false
cover: false
toc: true
mathjax: true
date: 2021-05-26 16:41:48
password:
summary:
tags: docker-compose
categories: Docker
---

### Redis

docker-compose.yaml

```yaml
version: '3'
services:
  redis:
    restart: always
    image: redis
    container_name: redis
    privileged: true # 容器内使用root权限
    volumes:
      - ./datadir:/data
      - ./conf/redis.conf:/usr/local/etc/redis/redis.conf
      - ./logs:/logs
    command:
#      两个写入操作 只是为了解决启动后警告 可以去掉
      /bin/bash -c "echo 511 > /proc/sys/net/core/somaxconn
      && echo never > /sys/kernel/mm/transparent_hugepage/enabled
      && redis-server /usr/local/etc/redis/redis.conf"
    ports:
      - 6379:6379

```

redis.conf

```sh
daemonize no
pidfile /var/run/redis.pid
port 6379
bind 0.0.0.0
timeout 0
loglevel verbose
logfile /logs/redis.log
databases 16
```

目录结构如下：

```
.
├── conf
│   └── redis.conf
├── datadir
├── docker-compose.yaml
└── logs
```

### Mysql

docker-compose.yaml

```yaml
version: '3'
services:
  mysql:
    restart: always
    image: mysql:5.7.16
    container_name: mysql
    volumes:
      - ./datadir:/var/lib/mysql
      - ./conf/my.cnf:/etc/my.cnf
    environment:
      MYSQL_ROOT_PASSWORD: "root"
    ports:
      - 3306:3306

```

my.cnf

```sh
[mysqld]
user=mysql
default-storage-engine=INNODB
character-set-client-handshake=FALSE
character-set-server=utf8mb4
collation-server=utf8mb4_unicode_ci
init_connect='SET NAMES utf8mb4'
[client]
default-character-set=utf8mb4
[mysql]
default-character-set=utf8mb4

```

目录结构如下：

```
.
├── conf
│   └── my.cnf
├── datadir
└── docker-compose.yaml
```

### Kafka

docker-compose.yaml

```yaml
version: '3'
services:
  zookeeper:
    restart: always
    container_name: zookeeper
    image: wurstmeister/zookeeper
    volumes:
      - ./zookeeper/data:/data
    ports:
      - "2181:2181"
       
  kafka:
    restart: always
    container_name: kafka
    image: wurstmeister/kafka
    ports:
      - "9092:9092"
    environment:
      KAFKA_ADVERTISED_HOST_NAME: localhost
      KAFKA_MESSAGE_MAX_BYTES: 2000000
      KAFKA_CREATE_TOPICS: "test:1:1"
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
    volumes:
      - ./kafka:/kafka
 
  kafka-manager:
    container_name: kafka-manager
    image: sheepkiller/kafka-manager
    ports:
      - 9020:9000
    environment:
      ZK_HOSTS: zookeeper:2181
```

 目录结构如下： 

```
├── docker-compose.yaml
├── kafka
└── zookeeper
    └── data
```

### RabbitMq

docker-compose.yaml

```yaml
version: "3.2"
services:
  rabbitmq:
    image: rabbitmq:3-management-alpine
    container_name: 'rabbitmq'
    ports:
        - 5672:5672
        - 15672:15672
    volumes:
        - ./data/:/var/lib/rabbitmq/
        - ./log/:/var/log/rabbitmq
    networks:
        - rabbitmq_go_net
    environment:
      - RABBITMQ_DEFAULT_VHOST=my_vhost
      - RABBITMQ_DEFAULT_USER=admin
      - RABBITMQ_DEFAULT_PASS=admin
networks:
  rabbitmq_go_net:
    driver: bridge

```



目录结构如下：

```sh
├── data
├── docker-compose.yaml
└── log
```

