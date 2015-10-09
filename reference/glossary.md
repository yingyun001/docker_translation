# 术语
Docker 项目所涉及的术语的列表。

## aufs
aufs（先进的多层统一的文件系统）是作为 Docker 后端存储的 Linux [文件系统](#文件系统)。它为 Linux 文件系统实现了[统一挂载](https://en.wikipedia.org/wiki/Union_mount)。

## 基本镜像
不依赖任何其它镜像的镜像叫作**基础镜像**。

## boot2docker
[boot2docker](bootdocker2.io) 是一个轻量级的专门用于运行 Docker 容器的 Linux 发行版。对于虚拟机来说，在 Windows 和 Mac OS X 上运行 Docker 是很常见的。

boot2docker 还涉及到了 Windows 和 Mac OS X 中的 boot2docker 管理工具，该管理工具是用于管理 boot2docker 虚拟机的。

## btrfs
btrfs（B 树文件系统）也是作为 Docker 后端存储的 Linux [文件系统](#文件系统)。这是一个[写入时复制](https://zh.wikipedia.org/wiki/%E5%AF%AB%E5%85%A5%E6%99%82%E8%A4%87%E8%A3%BD)文件系统。

## build
build 指的是使用 [dockerfile](#dockerfile) 构建镜像的过程。build 使用到了 Dockerfile 和该文件中的内容（“上下文”）。该上下文是构建镜像所在的目录中的文件的集合。

## cgroups
cgroups 是 Linux 的内核特性，它是一种用于限制进程集合的资源使用，并计算进程集合的资源使用率，同时可以使进程集合每个资源的使用相互隔离的机制。这里资源指的是 CPU，内存，磁盘 I/O，网络等。Docker 就是依赖 cgroups 来控制资源并使资源隔离化的。

*又名：控制组*

## Compose
Compose 是一个用于定义并运行 Docker 中的复杂应用程序的一种工具。利用 compose，您可以在一个单独的文件中定义多容器的应用程序，然后您就可以执行一条命令开启您的应用程序了。

*又名：docker-compose, fig*

## 容器
容器是镜像的运行时实例。

Docker 的容器中包含了
* Docker 镜像
* 执行环境
* 一组标准的指令

这个概念的灵感来源于船运集装箱，它定义了在全球范围内运送货物的标准。Docker 定义了运送软件的标准。

## 数据卷
数据卷是一个特别指定的目录，它可以绕过联合文件系统。数据卷可以持续存储数据，不受容器生命周期的影响。因此当您删除了容器的时候，Docker 绝不会自动删除卷，也不会有回收卷回收不再被容器引用的卷。

## Docker
Docker 术语可以参考
* Docker 项目总的来说就是一个为开发者和系统管理员提供的开发，运输和运行应用程序的平台。
* docker 后端程序运行在主机上，该主机是负责管理镜像和容器的。

## Docker Hub
[Docker Hub](https://hub.docker.com/) 是与 Docker 和其组件协同工作的中心资源。
它提供了下面的服务：
* Docker 镜像托管
* 用户身份验证
* 自动图像构建和工作流工具例如构建触发和网络挂钩
* 是 GitHub 和 Bitbucket 的集成

## Dockerfile
Dockerfile 是一个文本文件，它包含了所有您平常会用到的为构建一个 Docker 镜像而需要手动执行的命令。Docker 通过读取 Dockerfile 中的指令自动构建镜像。

## 文件系统
一个文件系统是操作系统为文件命名的方式和为有效存储都和检索为文件分配存放位置的方式。

例如：
* Linux：ext4，aufs，btrfs，zfs
* Windows：NTFS
* OS X：HFS+

## 镜像
Docker 镜像是[容器](#容器)的基础。镜像是一个有规则的由根文件系统更改的集合，并且还是容器运行时会使用到的相应的执行参数。一类镜像是在镜像的每一层上都包含了联合的多层文件系统。还有一类镜像是从未改变过的镜像。

## libcontainer
## link
## Machine
## overlay
## registry
## 仓库
## Swarm
## 标签
## 统一文件系统
## 虚拟机 
