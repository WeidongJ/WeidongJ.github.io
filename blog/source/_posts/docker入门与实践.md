---
title: docker入门与实践
date: 2020-05-11 17:14:11
tags:
    - docker
categories:
    - CI
    - CD
description:  docker学习笔记
---

# Docker从入门到实践

## 什么是Docker

Docker属于系统层面的虚拟化技术，能够对进程进行封装隔离。由于隔离的进程独立与宿主和其他的隔离进程，因此也被称做容器。
{% asset_img docker-on-linux.png  "Docker 架构" "Doceker 架构" %}

> runc 是一个命令行工具，用于根据`OCI容器运行时规范` 创建和运行容器。

> containerd 是一个守护程序，他管理容器的生命周期，提供了在一个节点上执行容器和管理镜像的最小功能集。

Docker在容器的基础上，进行了进一步的封装，从文件系统、网络互联到进程隔离等等，极大地简化了容器的创建和维护。使得Docker技术比虚拟化技术更为轻便、快捷。

Docker和传统虚拟化技术的不同之处，传统虚拟化技术是虚拟出一套硬件之后，在其上运行一个完整的操作系统，在该系统上再运行所需的应用进程；而容器内的应用进程直接运行于宿主的内核，容器内没有自己的内核，而且也没有进行硬盘虚拟。因此容器更为轻便。
{% asset_img virtualization.png "传统虚拟化" %}

{% asset_img docker.png "Docker" %}

## Docker的好处

*更高效的利用系统资源*

由于容器不需要进行硬件虚拟以及完整的操作系统等额外开销，Docker 对系统资源的利用率更高。无论是应用执行速度、内存损耗或者文件存储速度都比传统虚拟技术更高效。

*更快速的启动时间*
 
 由于直接运行于宿主内核，不需要启动完整的操作系统，因此可以做到秒级、甚至毫秒级的启动时间。大大节约了开发、测试、部署的时间。

 *一致的运行环境*

Docker的镜像提供了除内核外完整的运行时环境，确保了应用运行环境的一致性。


*持续交付和部署*

使用Docker可以通过定制应用镜像来实现持续集成、持续交付、部署。开发人员可以通过Dockerfile来进行镜像构建，并结合持续集成系统进行集成测试，而运维人员则可以直接在生产环境部署镜像，甚至可以通过持续部署系统进行自动部署。

*更轻松的应用迁移*

由于确保了运行环境的一致性，Docker迁移更加容易。Docker是跨平台的，用户可以很轻松的将应用跨平台迁移。

*更轻松的扩展和维护*

Docker使用的分层存储以及镜像的技术，使得应用重复的部分的复用更加容易，应用的维护更新更加简单。

*对比传统虚拟技术*

| 特性 | 容器 | 虚拟机 |
| ---- | --- | ---- |
| 启动 | 秒级 | 分钟级 |
|硬盘使用|一般为MB | 一般为GB |
|性能 | 接近原生 | 弱于 |
|系统支持量 | 单机支持上千个容器 | 一般十几个 |

## 基本概念

Docker包括三个基本概念
* 镜像（Image）
* 容器（Container）
* 仓库（Repository）

### 镜像

操作系统分为内核和用户空间。对于Linux而言，内核启动后，会挂载root文件系统为其提供用户空间支持。docker就相当于一个root文件系统。

Docker镜像是一个特殊的文件系统，除了提供容器运行时所需的程序、库、资源等文件外，还包括了一些为运行时准备的一些配置参数。镜像不包括任何动态数据，其内容在构建之后也不会改变。

#### 分层存储

镜像的采用了分层存储技术，构建时会一层层构建，前一层是后一层的基础，每一层构建之后就不会发生改变，后一层的改变只发生在自己这一层。分层存储的特征使得镜像的复用、定制变得更为容易。

## todo

## 使用镜像

从Docker Hub获取镜像：

    docker pull [选项] [Docker Registry 地址[:端口号]/]仓库名[:标签]

### 运行

以ubuntu镜像为例，运行bash

    docker run -it --rm ubuntu:18.04 bash

参数：
* -it: -i 交互式操作；-t 终端
* --rm: 容器推出之后随即将其删除

列出镜像

    PS D:\Weidong\django-blog> docker image ls [-afq] filter name [--format format]
    REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE  
    nginx               v3                  334bdfe45c56        5 days ago          127MB 
    nginx               v2                  ec40e4a4f34e        5 days ago          127MB 
    ubuntu              latest              1d622ef86b13        3 weeks ago         73.9MB
    nginx               latest              602e111c06b6        3 weeks ago         127MB 

参数：

* -a 列出所有镜像，包括中间层镜像。
* -q 返回image id
* filter name 返回指定name的镜像
* -f 高级filter 支持按时间筛选
* --format 指定返回内容格式


查看空间占用

    PS D:\Weidong\django-blog> docker system df
    TYPE                TOTAL               ACTIVE              SIZE                RECLAIMABLE
    Images              4                   1                   200.6MB             200.6MB (100%)
    Containers          1                   0                   2B                  2B (100%)
    Local Volumes       0                   0                   0B                  0B
    Build Cache         0                   0                   0B                  0B

虚悬镜像（dangling image）：镜像的名字和标签指定到了新版本镜像之后，会成为虚悬镜像。

    <none>               <none>              00285df0df87        5 days ago          342 MB

删除镜像

    docker image rm [name|tag|$(command)]

## Commit构建镜像

运行镜像

    PS D:\Github\WeidongJ.github.io> docker run --name webserver -d -p 80:80 nginx
    014dfd2cb4f1be7b03be0e6e8a8ab97de6deb92c9e7f6fd673ae673cfa4caa62

进入镜像修改内容：

    PS D:\Github\WeidongJ.github.io> docker exec -it webserver bash
    root@014dfd2cb4f1:/# echo '<h1>Hello, Docker!</h1>' > /usr/share/nginx/html/index.html
    root@014dfd2cb4f1:/# exit
    exit

nginx启动的webserver的首页已经发生变化
{% asset_img nginx-commited.png index %}

定制了变化之后，可以使用commit命令保存更改到镜像里：

    docker commit [选项] <容器ID或容器名> [<仓库名>[:<标签>]]

commit之后会生成新的镜像：

    PS D:\Github\WeidongJ.github.io> docker commit --author "weidong" --message "index change" webserver nginx:v3
    sha256:1f3d60c92743b93d008ce254a87dbae1d47cbc7cd06d00ef0f5e5ed9d9867397
    PS D:\Github\WeidongJ.github.io> docker image ls
    REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
    nginx               v3                  1f3d60c92743        24 seconds ago      127MB
    <none>              <none>              334bdfe45c56        5 days ago          127MB
    nginx               v2                  ec40e4a4f34e        5 days ago          127MB
    ubuntu              latest              1d622ef86b13        3 weeks ago         73.9MB
    nginx               latest              602e111c06b6        3 weeks ago         127MB


还可以使用docker history来查看镜像内的历史记录：

    PS D:\Github\WeidongJ.github.io> docker history nginx:v2
    IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
    ec40e4a4f34e        5 days ago          nginx -g daemon off;                            98B                 修改了index
    602e111c06b6        3 weeks ago         /bin/sh -c #(nop)  CMD ["nginx" "-g" "daemon…   0B
    <missing>           3 weeks ago         /bin/sh -c #(nop)  STOPSIGNAL SIGTERM           0B
    <missing>           3 weeks ago         /bin/sh -c #(nop)  EXPOSE 80                    0B
    <missing>           3 weeks ago         /bin/sh -c ln -sf /dev/stdout /var/log/nginx…   22B
    <missing>           3 weeks ago         /bin/sh -c set -x     && addgroup --system -…   57.6MB
    <missing>           3 weeks ago         /bin/sh -c #(nop)  ENV PKG_RELEASE=1~buster     0B
    <missing>           3 weeks ago         /bin/sh -c #(nop)  ENV NJS_VERSION=0.3.9        0B
    <missing>           3 weeks ago         /bin/sh -c #(nop)  ENV NGINX_VERSION=1.17.10    0B
    <missing>           3 weeks ago         /bin/sh -c #(nop)  LABEL maintainer=NGINX Do…   0B
    <missing>           3 weeks ago         /bin/sh -c #(nop)  CMD ["bash"]                 0B

### 慎用commit

commit修改的是运行中的container，同时会有大量的文件更改，一般达不到最小更改的意图，会使得镜像越来越臃肿。

### 使用DockerFile定制镜像

dockerFile是一个文本文件，如：

    FROM nginx
    RUN echo '<h1>Hello, Docker!</h1>' > /usr/share/nginx/html/index.html

命令详解：
* FROM 指定基础镜像（docker 还提供了一个空白镜像scratch）
* RUN 执行命令，其格式有2种：
    1. shell：直接运行
    2. exec：`RUN ["可执行文件", "参数1", "参数2"]`

