---
title: Docker 安装与简述
date: 2019-01-11 19:34:59
tags: docker
categories: 安装
---
> 该文档基于新的服务器Centos 7

<!-- more -->
#### Docker 基本概念
> 镜像（Image）

```
Docker 镜像是一个特殊的文件系统，除了提供容器运行时所需的程序、库、资源、配置等文件外，还包含了一些为运行时准备的一些配置参数（如匿名卷、环境变量、用户等）。镜像不包含任何动态数据，其内容在构建之后也不会被改变。
```
> 容器（Container）

```
镜像（Image）和容器（Container）的关系，就像是面向对象程序设计中的类和实例 一样，镜像是静态的定义，容器是镜像运行时的实体。容器可以被创建、启动、停止、删除、暂停等。
```
> 仓库（Repository）

```
镜像构建完成后，可以很容易的在当前宿主机上运行，但是，如果需要在其它服务器上使用这个镜像，我们就需要一个集中的存储、分发镜像的服务，Docker Registry 就是这样的服务。
```

#### 更新yum源
> yum install epel-release -y
yum update -y

#### Docker 安装
> 卸载旧版本

```
sudo yum remove docker \
    docker-client \
    docker-client-latest \
    docker-common \
    docker-latest \
    docker-latest-logrotate \
    docker-logrotate \
    docker-selinux \
    docker-engine-selinux \
    docnker-engine
```
> 安装依赖包

```
sudo yum install -y yum-utils \
    device-mapper-persistent-data \
    lvm2
```
> 设置 Docker CE yum 源

```
sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
```
> 安装 Docker CE

```
sudo yum install docker-ce
```
> 设置开启自启

```
sudo systemctl enable docker
```

> 启动 Docker

```
sudo systemctl start docker
```

> 安装nginx

```
> nginx 配置
docker volume create nginx-conf
> nginx 目录
docker  volume create nginx-html
> 查看新建的卷
docker volume ls
> 创建并启动nginx
docker run -it -p 80:80 -p 443:443 -v nginx-conf:/etc/nginx/ -v nginx-html:/usr/share/nginx/html/ --name my-nginx -d nginx
> 最小化安装缺少ifconfig、route等命令
yum install net-tools
> 查看nginx-html
docker inspect nginx-html
> 查看nging配置文件
docker inspect nginx-conf
> 查看所有运行的docker
docker ps
> 删除docker
docker rm -f id
>  建立 docker 用户组（默认情况下，docker 命令会使用 Unix socket 与 Docker 引擎通讯。而只有 root 用户和 docker 组的用户才可以访问 Docker 引擎的 Unix socket。出于安全考虑，一般 Linux 系统上不会直接使用 root 用户。因此，更好地做法是将需要使用 docker 的用户加入 docker 用户组。）
sudo groupadd docker
> 把当前用户假如 docker 用户组
 sudo usermod -aG docker $USER
> 测试 Docker 安装是否成功
 docker run hello-world
```

#### 镜像加速
> 请在 /etc/docker/daemon.json 中写入如下内容（如果文件不存在请新建该文件）

```
{
    "registry-mirrors": [
        "https://registry.docker-cn.com"
    ]
}
```

> 之后重新启动服务
```
> 重载所有修改过的配置文件
 sudo systemctl daemon-reload
> 重启 Docker 服务
 sudo systemctl restart docker
```
