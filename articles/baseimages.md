# 创建一个基本镜像

这么说，您想创建您自己的[基础镜像](../terms/image.md)？太好了！

具体的过程将会严重依赖于您想在哪个 Linux 发行版上打包。下面有一些例子供你参考，我们鼓励您提交推送请求来贡献您的新镜像。

## 使用 tar 命令来创建一个完整的镜像

通常，你要有一台运行了 Linux 发行版本的机器，您可能想打一个基础镜像的包，即便有一些工具像 Debian 的 [Deboostrap](https://wiki.debian.org/Debootstrap) 是不需要的，而您可以使用 Debootstrap 来够将 Ubuntu 镜像。

创建一个 Ubuntu 基础镜像是非常容易的：
```
  $ sudo debootstrap raring raring > /dev/null
  $ sudo tar -C raring -c . | sudo docker import - raring
  a29c15f1bf7a
  $ sudo docker run raring cat /etc/lsb-release
  DISTRIB_ID=Ubuntu
  DISTRIB_RELEASE=13.04
  DISTRIB_CODENAME=raring
  DISTRIB_DESCRIPTION="Ubuntu 13.04"
```

在 Docker 的 GitHub 上，有更多的创建基础镜像的脚本示例：

- [BusyBox](https://github.com/dotcloud/docker/blob/master/contrib/mkimage-busybox.sh)
- CentOS / Scientific Linux CERN (SLC) [on Debian/Ubuntu](https://github.com/dotcloud/docker/blob/master/contrib/mkimage-rinse.sh) or on [CentOS/RHEL/SLC/etc](https://github.com/dotcloud/docker/blob/master/contrib/mkimage-yum.sh).
- [Debian / Ubuntu](https://github.com/dotcloud/docker/blob/master/contrib/mkimage-debootstrap.sh)


## 使用 scratch 来创建一个简单的基础镜像

您可以使用 Docker 的保留条件，最小镜像和 scratch 作为构建容器的切入点。使用 `scratch`“镜像”来和构建过程进行通信，该构建过程指的是您相让 `Dockerfile` 中的下一条命令作为您镜像中的第一个文件系统层。

虽然 `scratch` 出现在 Docker Hub 的仓库中，您也不能下载它，运行它或者说用 `scratch` 为镜像添加标记。相反，您可以在 `Dockerfile` 文件中引用它。例如，使用 `scratch` 创建一个精炼版的容器。
```
FROM scratch
ADD hello /
CMD ["hello"]
```

该实例创建了教程中曾使用过的 hello-world 这个镜像。如果您想对其进行测试，您可以[克隆该镜像](https://github.com/docker-library/hello-world)。

## 更多资源
这里有许多可用的资源可以帮助您写 `Dockerfile`。
 
