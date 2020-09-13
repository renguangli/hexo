---
layout: post
title: Docker 核心概念与原理
category: Docker
tags: [Docker]
---

# 什么是 Docker？

Docker 是一个开源的应用容器引擎，让开发者可以打包他们的应用以及依赖包到一个可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。容器是完全使用沙箱机制，相互之间不会有任何接口。

Docker 最初是 dotCloud 公司创始人 Solomon Hykes 在法国期间发起的一个公司内部项目，它是基于 dotCloud 公司多年云服务技术的一次革新，并于 2013 年 3 月以 Apache 2.0 授权协议开源，主要项目代码在 GitHub 上进行维护。Docker 项目后来还加入了 Linux 基金会，并成立推动开放容器联盟。

## Docker vs VM

下面的图片比较了 Docker 和传统虚拟化方式的不同之处。传统虚拟机技术是虚拟出一套硬件后，在其上运行一个完整操作系统，在该系统上再运行所需应用进程；而容器内的应用进程直接运行于宿主的内核，容器内没有自己的内核，而且也没有进行硬件虚拟。因此容器要比传统虚拟机更为轻便。

![虚拟机](/images/docker.png)
![dcoker](/images/virtualization.png)

## 为什么要用docker

作为一种新兴的虚拟化方式，Docker 跟传统的虚拟化方式相比具有众多的优势。

### 更高效的利用系统资源

由于容器不需要进行硬件虚拟以及运行完整操作系统等额外开销，Docker 对系统资源的利用率更高。无论是应用执行速度、内存损耗或者文件存储速度，都要比传统虚拟机技术更高效。因此，相比虚拟机技术，一个相同配置的主机，往往可以运行更多数量的应用。

### 更快速的启动时间

传统的虚拟机技术启动应用服务往往需要数分钟，而 Docker 容器应用，由于直接运行于宿主内核，无需启动完整的操作系统，因此可以做到秒级、甚至毫秒级的启动时间。大大的节约了开发、测试、部署的时间。

### 一致的运行环境

开发过程中一个常见的问题是环境一致性问题。由于开发环境、测试环境、生产环境不一致，导致有些 bug 并未在开发过程中被发现。而 Docker 的镜像提供了除内核外完整的运行时环境，确保了应用运行环境一致性，从而不会再出现 「这段代码在我机器上没问题啊」 这类问题。

### 持续交付和部署

对开发和运维（DevOps）人员来说，最希望的就是一次创建或配置，可以在任意地方正常运行。
使用 Docker 可以通过定制应用镜像来实现持续集成、持续交付、部署。开发人员可以通过 Dockerfile 来进行镜像构建，并结合 持续集成(Continuous Integration) 系统进行集成测试，而运维人员则可以直接在生产环境中快速部署该镜像，甚至结合 持续部署(Continuous Delivery/Deployment) 系统进行自动部署。
而且使用 Dockerfile 使镜像构建透明化，不仅仅开发团队可以理解应用运行环境，也方便运维团队理解应用运行所需条件，帮助更好的生产环境中部署该镜像。

### 更轻松的迁移

由于 Docker 确保了执行环境的一致性，使得应用的迁移更加容易。Docker 可以在很多平台上运行，无论是物理机、虚拟机、公有云、私有云，甚至是笔记本，其运行结果是一致的。因此用户可以很轻易的将在一个平台上运行的应用，迁移到另一个平台上，而不用担心运行环境的变化导致应用无法正常运行的情况。

### 更轻松的维护和扩展

Docker 使用的分层存储以及镜像的技术，使得应用重复部分的复用更为容易，也使得应用的维护更新更加简单，基于基础镜像进一步扩展镜像也变得非常简单。此外，Docker 团队同各个开源项目团队一起维护了一大批高质量的官方镜像，既可以直接在生产环境使用，又可以作为基础进一步定制，大大的降低了应用服务的镜像制作成本。

### 对比传统虚拟机总结

|特性|	容器|	虚拟机|
|:-:|:-:|:-:|
|启动|	秒级|	分钟级|
|硬盘使用|	一般为 MB|	一般为 GB|
|性能|	接近原生|	弱于
|系统支持量|	单机支持上千个容器|	一般几十个|

## Docker实现原理

Docker是Client/Server的架构，Docker客户端与Docker daemon进行交互，daemon负责构建、运行和发布Docker容器。客户端可以和服务端运行在同一个系统中，也可以连接远程的daemon。Docker的客户端的daemon通过RESTful API进行socket通信。Docker守护进程（daemon）在主机上运行，用户不能直接和守护进程打交道，但是可以通过Docker客户端与其进行交互；Client是Docker的初始用户界面，它接收用户的命令并反馈，并且与Docker的守护进行交互。

Docker 使用 Google 公司推出的 Go 语言 进行开发实现，基于 Linux 内核的 cgroup，namespace，以及 AUFS 类的 Union FS 等技术，对进程进行封装隔离，属于 操作系统层面的虚拟化技术。由于隔离的进程独立于宿主和其它的隔离的进程，因此也称其为容器。最初实现是基于 LXC，从 0.7 以后开始去除 LXC，转而使用自行开发的 libcontainer，从 1.11 开始，则进一步演进为使用 runC 和 containerd。

### Namespace

Docker用到的一些命名空间
- pid命名空间：用于隔离进程，容器都有自己独立的进程表和1号进程；
- net命名空间：用于管理网络，容器有自己独立的networkinfo；
- ipc命名空间：用于访问IPC资源（IPC:InterProcess Communication）；
- mnt命名空间：用于管理挂载点，每个容器都有自己唯一的目录挂载；
- uts命名空间：用于隔离内核和版本标识（UTS:UnixTimeProcess System），每个容器都有独立的hostname和domain。

### cgroup

cgroup(控制组)是 Linux 内核的一个特性，主要用来对共享资源进行隔离、限制、审计等。只有能控制分配到容器的资源，才能避免当多个容器同时运行时的对系统资源的竞争。控制组技术最早是由
 Google 的程序员 2006 年起提出，Linux 内核自 2.6.24 开始支持。控制组可以提供对容器的内存、CPU、磁盘 IO 等资源的限制和审计管理。

### UnionFS

UnionFS（联合文件系统）：Union文件系统（UnionFS）是一种分层、轻量级并且高性能的文件系统，它支持对 文件系统的修改作为一次提交来一层层的叠加，同时可以将不同目录挂载到同一个虚拟文件系统下(unite
 several directories into a single virtual filesystem)。Union 文件系统是 Docker 镜像的基础。镜像可以通过分层来进行继承，基于基础镜像（没有父镜像），可以制作各种具体的应用镜像。另外，不同 Docker 容器就可以共享一些基础的文件系统层，同时再加上自己独有的改动层，大大提高了存储的效率。Docker 中使用的 AUFS（AnotherUnionFS）就是一种 Union FS。 AUFS 支持为每一个成员目录（类似 Git 的分支）设定只读（readonly）、读写（readwrite）和写出（whiteout-able）权限,
 同时 AUFS 里有一个类似分层的概念, 对只读权限的分支可以逻辑上进行增量地修改(不影响只读部分的)。Docker 目前支持的 Union 文件系统种类包括 AUFS, btrfs, vfs 和 DeviceMapper。

## Docker核心概念

### image

Docker镜像类似于虚拟机镜像，是一个只读模板，并且包含了文件系统。一个镜像可以只包含一个操作系统环境（比如Centos镜像），也可以安装了用户程序及其运行环境（比如Tomcat镜像）。镜像其实就是一个文件，任何用户程序都可以成为镜像的一部分。镜像用来创建container，一个镜像可以运行多个container；镜像可以通过Dockerfile创建，也可以从Docker hub/registry上下载。

### container

容器是从镜像创建的运行实例，可以将其启动、开始。停止、删除，而这些容器都是相互隔离（独立进程），多个容器之间不会相互影响，保证容器中的程序运行在一个相对安全的环境中。
### registry

Docker镜像仓库简单来说就是存储Docker镜像的地方，就像git仓库一样,push、pull镜像。Docker官方提供了Docker Hub来存储我们的镜像文件，用户可以上传或者下载上面的镜像，也可以[搭建私有的Docker registry](https://renguangli.com/articles/docker-registry)。

## 参考资料

Docker技术入门与实战

Docker中文社区: <http://www.docker.org.cn/>

<https://blog.csdn.net/u012299594/article/details/52343910/>