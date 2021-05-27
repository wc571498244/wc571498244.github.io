---
title: Liunx安装docker
top: false
cover: false
toc: true
mathjax: true
date: 2021-05-26 20:41:48
password:
summary:
tags: docker
categories: Linux
---

## 使用脚本自动安装(快速)
Linux 系统上可以使用这套脚本安装，另外可以通过 --mirror 选项使用国内源进行安装：
**获取脚本**
[get-docker.sh](/medias/files/get-docker.sh)

**安装**
```sh
# 记得给可执行权限
chmod u+x get-docker.sh
sudo sh get-docker.sh --mirror Aliyun  # 使用阿里镜像
```
执行这个命令后，脚本就会自动的将一切准备工作做好，并安装好docker。

## 启动docker
```sh
sudo systemctl enable docker
sudo systemctl start docker
```

## 建立 docker 用户组
默认情况下只有 root 用户和 docker 组的用户才可以访问 Docker 引擎的 Unix socket。出于安全考虑，一般 Linux 系统上不会直接使用 root 用户。因此，更好地做法是将需要使用 docker 的用户加入 docker 用户组。

建立 docker 组：
```sh
sudo groupadd docker
```

将当前用户加入 docker 组：
```sh
sudo usermod -aG docker $USER
```
退出当前终端并重新登录即可。


## 使用国内镜像加速
在 `/etc/docker/daemon.json` 中写入如下内容（如果文件不存在请新建该文件）：
```json
{
  "registry-mirrors": [
    "https://hub-mirror.c.163.com",
    "https://mirror.baidubce.com"
  ]
}
```
*注意，一定要保证该文件符合 json 规范，否则 Docker 将不能启动。*

重新启动服务
```sh
sudo systemctl daemon-reload
sudo systemctl restart docker
```
