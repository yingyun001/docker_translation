# Docker Machine

> **注意**:
> Machine 目前还处于测试阶段，所以可能会有些许变化。我们现在不建议您在产品中使用它。

Machine 可以让您在自己的机器上，在供应商提供的云主机上乃至在您的数据中心中创建 Docker 主机。该 Docker 主机会被自动创建，并在上面安装 Docker，然后通过配置 `Docker` 客户端使其能够与 Docker 主机进行通信。Docker 主机和配置好的 Docker 客户端组合在一起就是我们所说的 "Machine"。

一旦您创建了一个或者更多的 Docker Host，Docker Machine 有很多命令来管理主机。您可以使用以下命令：
* 开启，检查，停止并重启主机
* 升级 Docker 的客户端和后台程序。
* 为了和主机进行通信而配置 Docker 客户端。

## 理解 Docker Machine 的基本概念
Docker Machine 允许您在自己系统中的虚拟机或在云供应商中的虚拟机中部署 Docker。Docker Machine 会在虚拟机上创建一个主机，您可以根据您的需求在主机上利用 Docker Engine 客户端构建 image，并创建 container。

为了创建一台虚拟机，您需要给 Docker Machine 提供您想使用的那个驱动器的名称。驱动器决定了虚拟环境。例如，在本地 Linux，Mac，或 Windows 系统上的驱动器是典型的 Oracle Virtual Box。对于云供应商而言，Docker Machine 提供了例如 AWS, Microsoft Azure, Digital Ocean 乃至更多供应商的 driver。Docker Machine 参考中包含了完整的[被支持的驱动器列表](../machine/drivers.md)

因为 Docker 是运行在 Linux 上的，Docker Machine 提供的每台虚拟机都依赖于一个基本的操作系统。为了方便起见，对于不同的 driver，都会有默认的基本操作系统。对于 Oracle Virtual Box 驱动器而言，这个基本的操作系统就是 `boot2docker.iso`。对于那些用于和云供应商连接的驱动器来说，基本的操作系统就是 12.04 或更高版本的 Ubuntu。当您创建了一个 Docker Machine 的时候，您就可以更改默认的基本操作系统。Docker Machine 参考包含了完整的[被支持的操作系统列表](../machine/drivers/os-base.md)。

对于您创建的每个 Docker Machine，Docker 主机地址就是 Linux 虚拟机的 IP 地址。该地址是由 `docker-machine create` 这个子命令创建的。您还可以使用 `docker-machine ls` 命令列出您创建的所有 Docker Machine。`docker-machine ip <machine-name>` 命令返回一个指定的主机的 IP 地址。

在 Docker machine 上运行 Docker 命令之前，请您通过配置将本地命令行环境连接到 Docker Machine 中。`docker-machine env <machine-name>` 子命令出输出下面您应该执行的配置命令。当您在 Docker host 上运行一个 container 的时候，container 的端口会映射到虚拟机的端口上。

想要知道更多`docker-machine` 的子命令，请见：[Docker Machine 子命令参考](../machine/subcommands.md)

## 获取帮助
Docker Machine 仍然处于起步阶段并还在蓬勃发展。如果您需要寻求帮助，或者想要和志趣相投的人一起为该项目做贡献或一起探讨问题，我们会提供您一些开放的用于交流的平台。

- 提交 Bug 或者文件的功能要求：请使用 [Github 上的问题追踪器](https://github.com/docker/machine/issues)。
- 即时和大家讨论项目：请加入到 IRC 的 `#docker-machine` 频道。
- 想要贡献代码或更新文档：请见 [向 Github 提交一个 pull request](https://github.com/docker/machine/pulls)。

想要了解更多信息和资源，请访问[帮助页面](https://docs.docker.com/project/get-help/)。

## 下面做什么
- [使用 VirtualBox 在本地](../machine/get-started.md)安装一个 Docker Machine。
- [在您的云供应商提供的机器中](../machine/get-started-cloud)安装多个 Docker Machine。
- [Docker Machine 驱动器参考](../machine/driver.md)。
- [Docker Machine 子命令参考](../machine/subcommands.md)。
