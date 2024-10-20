---
title: Docker
date: 2024-10-16 17:03:31
tags: tools
categories: 个人笔记
---
## 介绍

1. 核心思想：打包装箱，相互隔离
2. 是基于Go语言开发的开源项目
3. DevOps（开发，运维）
4. 内核级别的虚拟化

## 基本组成

客户端 + 服务器 + 仓库

Client + Server  通过sokect访问服务器

镜像：一个模板，可以通过这个模板来创建容器服务，这个镜像可以创建多个容器
容器：通过镜像实例化一个或多个容器，可以理解为一个简易的Linux操作系统
仓库：存放镜像的地方

默认存储位置：/var/lib/docker

## Docker 为什么比虚拟机快

因为Docker的抽象层更少
利用的宿主机的内核，启动的时候不需要再启动一个新的内核

## 安装和测试

```bash
sudo pacman -Su docker
```

### 测试环境

```
sudo docker run hello-world
```

### 查看镜像

```
docker images
```

## Docker基本命令

![docker官方文档](https://docs.docker.com/reference/)

```bash
[root@mhduiy docker]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
hello-world   latest    d2c94e258dcb   11 months ago   13.3kB

REPOSITORY 镜像的仓库源
TAG 镜像的标签
IMAGE ID 镜像的ID
CREATED 镜像的创建时间
SIZE 镜像的大小

docker images -a  列出所有的镜像
docker images -q 只显示id
```

- docker info 显示docker的系统信息，显示镜像和容器数量
- 

### 搜索镜像

```bash
docker search mysql

docker search mysql --filter=STARS=5000 搜索5000以上的
```

### 下载镜像

```bash
docker pull mysql  下载最新版本
docker pull mysql:5.7 下载5.7镜像

```

### 删除镜像

```bash
docker rmi -f <镜像名/镜像ID>
docker rmi -f $(docker images -aq)  删除所有的镜像
```

### 容器命令

```bash
docker run [可选参数] image
--name="Name" 指定容器名字
-d 后台方式运行
-it 使用交互方式运行
-P 指定容器的端口 -p 8080:8080
-p 指定随机的端口


docker run -it deepin /bin/bash

查看当前运行的容器
docker ps

查看历史运行的容器(所有容器)
docker ps -a

退出容器
exit
退出容器，不停止运行
CTRL + P + Q

删除容器
docker rm

启动容器
docker start 容器id
docker stop 容器id
docker kill 容器id  强制重启
docker restart 容器id
```

### 配置代理

https://www.lfhacks.com/tech/pull-docker-images-behind-proxy/

#### 创建服务配置文件

```bash
sudo mkdir -p /etc/systemd/system/docker.service.d
```
若没有这个文件夹需要手动创建

#### 写入代理环境变量配置

```bash
[Service]
Environment="HTTP_PROXY=http://proxy.example.com:80"
Environment="HTTPS_PROXY=https://proxy.example.com:443"
```

#### 重启docker服务

```bash
sudo systemctl daemon-reload
sudo systemctl restart docker
```

#### 验证是否设置成功

```bash
sudo systemctl show --property=Environment docker
docker info
```

### 常用其他命令

#### 进入容器

```bash
docker exec -it 容器id bashshell
```

#### 查看日志

```bash
docker logs -f -t --tail 容器id
```

#### 查看容器内部运行的进程

```bash
docker top 容器id
```

#### 查看镜像元数据

```bash
docker inspect 容器id
```

#### 进入正在运行的容器并以命令行交互

```bash
docker attach 容器id
```

#### 从容器内拷贝文件到主机上

```bash
docker cp 容器id:容器内路径 目的主机路径
```

### 容器命令小结

```bash
run 创建并启动容器
attach 进入容器
exec 进入容器并执行命令
start 启动容器
stop 停止容器
restart 重启容器
kill 强制停止容器
rm 删除容器
pause 暂停容器
unpause 取消暂停容器
```