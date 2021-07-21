---
title: Docker-compose 安装Gitlab和Gitrunner
top: false
cover: false
toc: true
mathjax: true
date: 2021-06-12 01:41:48
password:
summary:
tags: docker-compose
categories: Docker
---


### 安装
> docker-compose.yaml

```yaml
version: '3.5'
services:
  gitlab:
    image: gitlab/gitlab-ce:latest
    hostname: gitlab host
    restart: always
    environment:
      GITLAB_OMNIBUS_CONFIG: |
        gitlab_rails['gitlab_shell_ssh_port'] = 22
    ports:
      - "8000:80" # 网页端端口
      - "8822:22" # ssh端口
    volumes:
      - ./config/gitlab:/etc/gitlab
      - ./data/gitlab:/var/opt/gitlab
      - ./logs:/var/log/gitlab
    networks:
      - gitlab
  gitlab-runner:
    image: gitlab/gitlab-runner:alpine
    restart: always
    depends_on:
      - gitlab
    privileged: true # 使用root权限
    volumes:
      - ./config/gitlab-runner:/etc/gitlab-runner
      - /var/run/docker.sock:/var/run/docker.sock # windows上宿主机docker.sock路径://var/run/docker.sock      linux上宿主机docker.sock路径:/var/run/docker.sock 
    networks:
      - gitlab
 
networks:
  gitlab:

```

> 目录结构如下：

```sh
.
├── config
│   └── gitlab
├── data
│   └── gitlab
|   └── gitlab-runner
├── docker-compose.yaml
└── logs
```

**解决 ERROR:Docker Got permission denied while trying to connect to the Docker daemon socket at unix://**

1. 给权限
```sh
chmod 666 /var/run/docker.sock
```

2. 把当前用户加入docker组 
```sh
sudo usermod -aG docker $USER
```

3. 修改docker服务配置/usr/lib/systemd/system/docker.service
```python
将原有的注释
ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock
新加代码
ExecStart=/usr/bin/dockerd -H unix:///var/run/docker.sock -H tcp://0.0.0.0:2375
```

4. 重启docker服务
```sh
systemctl restart docker
```

### gitlab-runner 注册到gitlab
> 注册命令

```sh
$ docker exec -it <gitlab-runner_container_name> gitlab-runner register

Please enter the gitlab-ci coordinator URL (e.g. https://gitlab.com/):
http://gitlab.example.com/
Please enter the gitlab-ci token for this runner:
gitlab-token # gitlab项目里面的setting->CICD中 runner配置中
Please enter the gitlab-ci description for this runner:
[Double-dong.local]: gitlab-ci # 名字随便取
Please enter the gitlab-ci tags for this runner (comma separated):
python3.4 # 标签名， 后续在.gitlab-ci.yml中使用
Registering runner... succeeded                     runner=6-uZ1ndZ
Please enter the executor: docker+machine, kubernetes, ssh, virtualbox, docker-ssh, parallels, shell, docker-ssh+machine, custom, docker:
docker # 这里选docker， 可根据需求任选一个
Please enter the default Docker image (e.g. ruby:2.6):
python:3.6 # 镜像名
Runner registered successfully.
```

![gitlab_token](./gitlab-token.png)



