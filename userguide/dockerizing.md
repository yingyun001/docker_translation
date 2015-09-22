# Docker 化应用程序之 “hello World‘

'所以，Docker 这个东西究竟是什么呢？'

Docker 允许您在容器内运行应用程序。要在容器内运行一个应用程序需要执行一条命令：`docker run`。

> **注意：**
> 基于您 Docker 系统的配置，在您执行本页面的所有命令之前都要加上 `sudo`。为了避免不必要的麻烦，您系统的管理员可以创建一个叫 `docker` 的 Unix 组并将用户添加进去。

## Hello world

现在让我们来试试。

```
$ docker run ubuntu:14.04 /bin/echo 'Hello world'
Hello world
```

刚刚您启动了您的第一个容器！

那么刚才发生了什么? 我们逐步来分析 `docker run` 命令做了哪些事情。

首先，我们指定了 `docker` 二进制可执行文件和我们想要执行的命令，即 `run`。`docker run` 命令会'运行容器'。

接下来，我们指定了一个镜像: `ubuntu 14.04`。这是我们所运行的容器的来源。Docker 称其为镜像。在本例中，我们使用的是 `Ubuntu 14.04` 操作系统镜像。

当您指定了一个镜像之后，Docker 首先会从你的 Docker 本地主机上查看该镜像是否已经存在。如果没有的话，Docker 就会从镜像仓库 [Docker Hub](https://hub.docker.com/) 下载该公共镜像。

接下来，我们告诉 Docker 要在新容器种执行什么命令：
```
/bin/echo 'Hello world'
```

当我们的容器启动了之后， Docker 会创建新的 Ubuntu 14.04 环境，并在容器内执行 `/bin/echo` 命令。而后我们就会在命令行看到如下结果：
```
Hello world
```

那么这之后，我们的容器会发生什么呢？只要该命令还处于激活状态，Docker 容器就会一直运行。这里 "hello world" 一旦被输出，容器就会停止。

## 一个交互式的容器

让我们再次尝试 `docker run` 命令，这次我们在容器中指定一条新的命令来运行。
```
$ sudo docker run -t -i ubuntu:14.04 /bin/bash
root@af8bae53bdd3:/#
```

我们继续指定了 `docker run` 命令，并启动了 `ubuntu:14.04` 镜像。与此同时我们还添加了两个参数：`-t` 和 `-i` 。参数 `-t` 表示在新容器内指定一个虚拟终端机（伪终端）或终端，参数 `-i` 表示允许我们通过抓取容器内的标准输入 (`STDIN`) 来进行交互。

我们还为容器指定了一个新的命令来运行：`/bin/bash`。这将在容器内启动 `bash shell`。

所以当容器（container）启动之后，我们会获取到一个命令提示符：
```
root@af8bae53bdd3:/#

```
我们尝试在容器内运行一些命令：
```
root@af8bae53bdd3:/# pwd
/
root@af8bae53bdd3:/# ls
bin boot dev etc home lib lib64 media mnt opt proc root run sbin srv sys tmp usr var
```

可以看到我们运行 `pwd` 来显示当前目录，这时候可以看到我们是在根目录下。我们还列出了根目录的文件列表，通过目录文件列表我们可以看出这是一个典型的 Linux 文件系统。

您可以在该容器内随便折腾，你可以使用 `exit` 命令或者使用 CTRL-D 来退出容器。
```
root@af8bae53bdd3:/# exit
```

与我们之前的容器一样，一旦你的 Bash shell 进程关闭了，你的容器就停止了。


##  Hello world 的进程化

现在当一个容器运行完一个命令后就会退出，但是这样并不是很好。让我们创建一个运行起来像后台程序那样的容器，就像我们运行在 Docker 中的大多数应用程序一样。

这里我们仍使用 `docker run` 命令：
```
$ sudo docker run -d ubuntu:14.04 /bin/sh -c "while true; do echo hello world; sleep 1; done"
1e5535038e285177d5214659a068137486f96ee5c2e85a4ac52dc83f2ebe4147
```

等等，“hello world” 的输出呢？让我们看看我们在这里运行了什么。这个命令看起来应该很熟悉。我们运行 `docker run` ，但是这次我们指定了参数 `-d`。参数 `-d` 告诉 docker 要以后台进程模式运行容器并将运行结果输出到后台。

我们指定了相同的镜像: `ubuntu:14.04`。

最后，我们指定要运行的命令：
```
/bin/sh -c "while true; do echo hello world; sleep 1; done"
```

这是一个不起眼的 hello world 进程：一个脚本会一直输出 "hello world"。

但是为什么我们一个 "hello world" 都看不到呢? 取而代之的是一个很长的字符串：
```
1e5535038e285177d5214659a068137486f96ee5c2e85a4ac52dc83f2ebe4147
```

这个很长的字符串叫做*容器ID（container ID）*。它对于每一个容器来说都是唯一的，所以我们可以使用它。

> **注意**：容器 ID 是有点长并且不放变出力，稍后我们会看到一个短点的 ID 和更方便处理的命名方式。

我们可以使用该容器 ID，看看 "hello world" 进程究竟发生了什么。

首先，我们要确保我们的容器正在运行中。我们可以使用 `docker ps` 命令来查看。`docker ps` 命令可以查询 docker 进程中所有容器的信息。
```
$ sudo docker ps
CONTAINER ID  IMAGE         COMMAND               CREATED        STATUS       PORTS NAMES
1e5535038e28  ubuntu:14.04  /bin/sh -c 'while tr  2 minutes ago  Up 1 minute        insane_babbage
```

这里我们看到了以进程模式运行的容器。`docker ps` 命令会返回一些有用的信息，第一个就是一个较短的容器 ID：`1e5535038e28`。

我们还可以查看到构建容器时使用的镜像，即 `ubuntu:14.04`，还有正在运行的命令，以及容器的状态和动态分配到的名称 `insane_babbage`。

>**注意**：Docker 会为任何启动的容器生成名称。稍后我们会看到如何为容器自定义名称。

好了，现在我们知道它正在运行。但是它确实在按照我们要求的那样工作吗？为了证实这一点，我们要使用 `docker logs` 命令来查看容器中是否输出了我们想要的结果。让我们借助 Docker 分配的容器名称来查看。
```
$ docker logs insane_babbage
hello world
hello world
hello world
. . .
```

`docker logs` 命令会查看容器中的内容并其以标准输出形式反乎：这个例子里输出的是 `hello world`。

太棒了！我们的后台正续正在工作了，我们刚刚创建了自己的第一个 docker 化的应用程序。

现在我们已经可以创建我们自己的容器了，我们可以对其进行整理并停止这个后台运行着的容器。我们使用 `docker stop` 命令来停止容器。
```
$ sudo docker stop insane_babbage
insane_babbage
```

`docker stop` 命令会通知 Docker 温柔的关掉正在运行的容器。如果容器已经成功地被关闭，它将返回刚刚停止的容器名称。

让我们通过 `docker ps` 命令来检查它是否还在运行。
```
$ sudo docker ps
CONTAINER ID  IMAGE         COMMAND               CREATED        STATUS       PORTS NAMES
```

太好了.我们的容器已经停止了。

#下一步

现在我们已经知道了原来 Docker 入门是如此简单，让我们学习如何做一些更高级的工作。

去阅读[使用容器](usingdocker.md)。



