# 欢迎来到 Docker 的用户手册页面

在[介绍](../README.md)中，您已经对什么是 Docker，它是如何工作的有了或多或少的了解。在本指南中，我们要向您讲解使用 Docker 的基础知识，并将 Docker 整合到您的环境中去。

我们会教您如何使用 Docker：
- Docker 化您的应用程序。
- 运行 Docker。
- 构建 Docker image。
- 与其他人分享您的 Docker image。
- 更多内容

我们将本指南划分成了几个重要的章节，这样可以让您更深刻的了解 Docker 的生命周期：

## 从 Docker Hub 开始
*我应该如何使用 Docker Hub 呢？*

Docker Hub 是 Docker 的中央枢纽。它掌管了公共的 Docker image，并提供可以协助您构建并管理 Docker 环境的服务。想了解更多？
请您跳转到 [使用 Docker Hub](../docker-hub/docker-hub.md)

## Docker 化应用程序：“Hello world”
*我应该如何运行 container 中的应用程序呢？*
Docker 为运行您的应用程序，从而提供了一个基于 container 的虚拟化平台。想要学习如何 Dockerize 应用程序并运行它们？

请见：[Docker 化应用程序](../userguide/dockerizing.md)

## 使用 container
*我应该如何管理我的 container 呢？*

一旦您熟悉了在 Docker container 中运行您的应用程序，您可能就想知道如何管理这些 container 了。想知道如何查看，监测并管理 container 吗？

请见：[使用 container](../userguide/usingdocker.md)

## 使用 Docker image
*我应该如何访问，分享并构建我自己的 image 呢？*
一旦您学会了如何使用 Docker，那么接下来您就应该去了解如何利用 Docker 构建您自己的应用程序 image 了。

请见：[使用 Docker image](../userguide/dockerimages.md)

## 将 container 联系在一起
到目前为止，我们已经知道了如何构建在 Docker container 中构建自己的应用程序。现在我们需要学习如何通过将多个 Docker container 连结到一起从而构建出整个应用程序栈出来。

请见：[将 container 联系到一起](../userguide/dockervolumes.md)

## 管理 container 中的数据
现在我们知道了如何将 Docker container 连结到一起，下面我们要学习如何在我们的 container 中管理数据，卷和挂载的文件系统。

请见：[管理 Container 中的数据](../userguide/dockervolumes.md)

## 使用 Docker Hub
现在我们已经了解了如何使用 Docker，下面我们将要了解如何将 Docker 与 Docker Hub 上的服务（包括受信任的编译版本和私有仓库）结合起来。

请见：[使用 Docker Hub](../userguide/dockerrepos.md)

## Docker Compose
Docker Compose 允许您在一个单独的文件中定义应用程序的组建（包括应用程序的 container 配置，链接和卷）。然后通过一个单独的命令开启所有的服务并将您的应用程序运行起来。

请见：[Docker Compose 用户指南](../compose/compose.md)

## Docker Machine
Docker Machine 能帮您启动 Docker Engine，并使之快速运行起来。Docker Machine 能在您的机器上，云供应商提供的云主机中或您的数据中心里为 Docker Engine 设置主机，为了能让 Docker 主机和您的机器进行安全的交互，Docker Machine 还会帮您配置 Docker client。 

请见：[Docker Machine 用户指南](../compose/compose.md)

## Docker Swarm
Docker Swarm 将很多 Docker Engine 汇聚到一起，将其定义为一个单独的虚拟 Docker Engine。它提供了标准的 Docker API，所以任何与 Docker 协同执行的工具现在都可以显式的扩展到多个 Docker 主机上。

请见：[Docker Swarm 用户指南](../userguide/swarm.md)

## 获取帮助
