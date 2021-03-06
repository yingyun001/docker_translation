# 写 Dockerfile 的最佳实践

## 概述

Docker 能够通过读取 `Dockerfile` 文件中的指令从而自动地编译镜像。Dockerfile 是一个文本文件，它包含了构建一个已知镜像所需要的所有指令，且这些指令都是有序的。`Dockerfile` 使用特定的格式并且使用特定的一组指令。您可以从 [Dockerfile 参考](reference/builder.md) 中了解它的基础知识。如果您是第一次写 Dockerfile 文件，您应该先从那里开始。

本文档涵盖了 Docker 公司和 Docker 社区推荐的 Dockerfile 最佳实践和方法，为的是让我们能够创建易用且高效的 `Dockerfile`。我们强烈建议您遵循这些建议（事实上，如果您创建的是正式的镜像，您必须遵循这些实践）。

您可以在 [buildpack-deps](https://github.com/docker-library/buildpack-deps/blob/master/jessie/Dockerfile) `Dockerfile` 中看到很多遵循了这些实践和建议的且还在运行中的示例。

> **注意**
> 想要获取到更详细的本文提到过的 Dockerfile 指令的解释，请您前往 [Dockerfile 参考](reference/builder.md)页面。

## 总指南和建议

### 容器是一个仅在短期内有用的东西

根据您在 `Dockerfile` 中定义的镜像而生成的容器应该是稍纵即逝的。“稍纵即逝”在这里的意思是说容器可以被停止、摧毁，还可以替换为一个全新创建的且配有最小配置的容器。

### 使用 .dockerignore 文件

在大多数的情况下，最好是每一个 Dockerfile 都放置在一个空目录里，然后，再向对应的目录里添加 Dockerfile 编译时所需的文件。为了增加编译的性能，您可通过再对应目录下放置 .dockerignore 文件，以排除编译时扫描的文件和目录。该文件内容的格式支持类似于 .gitignore 中的写法，像要获得创建该文件的更多信息，请看[.dockerignore 文件]()。

### 避免安装不必要的包

为了减少复杂度、依赖、文件大小和编译时间，您应该避免安装额外的或者不必要的软件包，因为这些软件包可能只是“有了更好”而已。例如再一个数据库的镜像中没有必要安装一个文本编辑器。

### 每一个容器中只运行一个进程

几乎在所有的场景中，您应该在一个容器中仅运行一个进程。将多个应用解耦为多个容器将会使应用的横向扩展能力和容器的复用变得简单的多。如果一个应用依赖另一个应用，可以使用[容器链接]()。

### 最小化层数

您需要找到一个平衡点，能在 Dockerfile 的可读性（并因此有长期的可维护性）与尽可能的减少 Dockerfile 编译镜像所产生的层数之间达到平衡。对准备要使用的层数具有战略眼光，并谨慎。

### 对多行参数进行排序

在可能的时候，通过对多行参数进行按首字母顺序排序可以简化今后的改动，这样就可以有效避免重复软件包的声明，让软件包列表更新起来简单的多。这样也会使 pull request 变得更加容易阅读和 review。另外，在反斜杠（\）前加一个空格也有帮助。

这里有一个来自 buildpack-deps 镜像的例子：

```
RUN apt-get update && apt-get install -y \
  bzr \
  cvs \
  git \
  mercurial \
  subversion
```

### 编译缓存

在编译镜像的过程中，Docker 将会从上到下逐步执行您写的 Dockerfile 中的每一行内容。在每一行内容被检视时，Docker 将会在缓存中查找已经存在的并且该行内容能够使用的镜像，如果有的话 Docker 就不会创建新的（重复的）镜像了。如果您不想使用缓存，您可以通过在 docker-build 命令中使用 --no-cache 参数来声明。

然而，如果您准备让 Docker 使用它的缓存的话，您应该要知道 Docker 什么时候会，什么时候不会查找和匹配缓存中的镜像，并且这是非常重要的。Docker 查找匹配缓存将像的基本规则列举如下：

* 一切从缓存中的基本镜像（base image）开始，从 Dockerfile 中读到的下一条指令（当前指令）将会与该基本镜像在缓存中的所有子镜像进行对比，对比时，查看在所有的子镜像中，是否存在与当前指令及其上文完全一致的指令序列所编译出来的子镜像，如果不存在，缓存将对当前指令无效。

* 在大多数的情况下，将 Dockerfile 中的指令与缓存中的子镜像进行简单的指令对比就已经够了，但是，具体的某些指令需要一点更多的检查和解释。

* 对于 ADD 和 COPY 指令，镜像中的文件内容还会被检查，并且对每个文件都会计算校验值。但是文件的最后修改时间和最后访问时间不会是计算校验值的依据。在查找缓存时，新文件的校验值将会与缓存中的文件的校验值进行比较。如果文件有任何改变，比如文件的内容和 metadata，那么缓存将失效。

* 对于 ADD 和 COPY 指令的缓存检查的补充：缓存的检查机制不会与容器里的文件进行校验值检查。例如，当处理一个 RUN apt-get -y update 命令时，文件在容器中的更新不会作为 ADD 和 COPY 指令的缓存检查是否命中的依据。在这种情况下，只会以 “RUN apt-get -y update” 这个字符串为缓存是否匹配的依据。

## Dockerfile 指令

在下面的例子中，您将会看到用各种可用的指令最好地写出 Dockerfile 的建议。

### FROM

[FROM 指令的 Dockerfile 参考]()

可能的话，使用当前的官方仓库作为您的镜像的基础。并且我们建议使用 [Debian 镜像]()，因为该镜像控制很严格，并且保持了很小的容量（目前为 100 mb 以下），并且它还是完整的发行版。

### RUN

[RUN 指令的 Dockerfile 参考]()

一如既往地，要让您写的 Dockerfile 更加的可读，易懂，并且容易维护，您就需要把长度很大的 Run 命令写在多行中，并以反斜杠连接。

最可能时用 Run 命令的场景为 apt-get 应用，当使用 apt-get 的时候，有几条需要注意的地方：

* 不要在单独的一行写 RUN apt-get update。这样当相关的文件被更新后可能会导致缓存问题，并且会让接下来的 apt-get install 命令出错，且无错误提示。

* 避免执行 RUN apt-get upgrade 或者 dist-upgrade，因为很多 base 镜像中“必须的”软件包会由于权限问题而升级失败。如果 base 镜像中的某个软件包过时了，您应该联系该镜像的维护者。如果您知道某一个特定的软件包如 foo，需要更新，使用 apt-get install -y foo 命令，然后该软件包就会自动更新完成。

* 务必以以下格式写指令：

  ```
  RUN apt-get update && apt-get install -y \
      package-bar \
      package-baz \
      package-foo
  ```

  使用这种方式编写指令不但使命令易读且易维护，而且当包含了 apt-get update 时，缓存很自然地抛弃，并且最新的软件包版本将会被安装，不需要任何进一步的编码或人工参与。

* 对于进一步的缓存抛弃方法为：版本定义软件包的方法（比如 package-foo=1.3.*）。这将会强制搜索对应的软件包版本，不管缓存里有什么。通过这种方式编写 apt-get 代码将会很大程度地简化维护，并且当有意想不到的变动时降低出错的可能。

#### 例子：

下面是一个格式良好的的 RUN 指令写法，演示了上面列出的建议，s3cmd，指定了一个版本 1.1.0*。如果镜像之前使用的是较老的版本，该指定版本的命令会导致 apt-get update 的缓存失效，且确保最新的对应版本被安装（在这个例子中需要 s3cmd 软件的新的功能）。

```
RUN apt-get update && apt-get install -y \
    aufs-tools \
    automake \
    btrfs-tools \
    build-essential \
    curl \
    dpkg-sig \
    git \
    iptables \
    libapparmor-dev \
    libcap-dev \
    libsqlite3-dev \
    lxc=1.0* \
    mercurial \
    parallel \
    reprepro \
    ruby1.9.1 \
    ruby1.9.1-dev \
    s3cmd=1.1.0*
```

用这种方式编写指令也可以避免特定软件包的重复安装，因为这种方式比如下方式编写的指令更加易读：

```
RUN apt-get install -y package-foo && apt-get install -y package-bar
```

### CMD
### EXPOSE
### ENV
### ADD 或 COPY
### ENTRYPOINT
### VOLUME
### USER
### WORKDIP
### ONBUILD
## 官方仓库示例
## 额外的资源
