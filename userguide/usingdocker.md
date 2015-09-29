# 与容器协同工作

在[上一节的 Docker 用户指南](dockerizing.md)中，我们启动了我们第一个容器。我们是用 `docker run` 命令启动的两个容器。

* 在前台以交互模式运行的容器。

* 在后台以进程形式运行的容器。

在这个过程中，我们学习到了几个 Docker 命令：

* `docker ps`   列出容器。
* `docker logs` 显示容器的标准输出
* `docker stop` 停止正在运行的容器

> **提示**：另一种学习 `docker` 命令的方式就是查看我们的[交互式教程页面](../linux/started.md)。

`docker` 客户端非常简单 。Docker 的每一项操作都是通过命令行来实现的，而每一条命令行都可以使用一系列的标识（flags）和参数。
```
# 使用方法：[sudo] docker [command] [flags] [arguments] ..
# 示例：
$ docker run -i -t ubuntu /bin/bash
```

让我们看看运行可以返回已安装的 Docker 客户端和后台程序版本信息的 `docker version` 命令具体都做了什么操作？
```
$ sudo docker version
```

这个命令不仅为您提供您正在使用的 Docker 客户端和后台程序的版本信息，还返回了 GO 语言的版本信息（Docker 的编程语言）。
```
Client:
  Version:      1.8.1
  API version:  1.20
  Go version:   go1.4.2
  Git commit:   d12ea79
  Built:        Thu Aug 13 02:35:49 UTC 2015
  OS/Arch:      linux/amd64

Server:
  Version:      1.8.1
  API version:  1.20
  Go version:   go1.4.2
  Git commit:   d12ea79
  Built:        Thu Aug 13 02:35:49 UTC 2015
  OS/Arch:      linux/amd64
```

## 获取 Docker 命令的帮助信息

您可以列出具体的 Docker 命令的帮助信息。帮助信息详细的描述了该命令的选项和使用方法。想要看到所有命令的列表，请使用下面的命令：
```
$ docker --help
```

想看到具体命令的使用方法，请在指定具体的命令之后再加上 `--help` 标示。
```
$ docker attach --help

Usage: docker attach [OPTIONS] CONTAINER

Attach to a running container

  --help=false        Print usage
  --no-stdin=false    Do not attach stdin
  --sig-proxy=true    Proxy all received signals to the process
```

> **注意：**
> 想要获取更多具体的命令的细节和实例，请访问本指南的[命令参照](../reference/commandline/cli.md)

## 在 Docker 中运行一个 web 应用程序

到现在为止，我们已经了解了很多关于 `docker` 客户端的知识，现在让我们将目光转移到重要的部分：运行多个容器。到目前为止我们运行的容器并没有做什么特别有用的事情，我们可以通过在 Docker 中运行一个 web 应用程序的示例来改变“现状”。

为了运行我们的 web 应用程序，我们还需要运行 Python Flask 应用程序。让我们从 `docker run` 命令开始。
```
$ docker run -d -P training/webapp python app.py
```

让我们看一下我们的命令都做了什么。我们指定了两个标识(flags)：`-d` 和 `-P`。我们已经了解了 `-d` 标识，它的作用是告诉 Docker 容器要在后台运行容器。`-P` 标识是我们新认识的，它会告诉 Docker 将容器内任意一个必需的网络端口映射到我们的主机上。让我们看看我们的 web 应用程序吧。

我们指定了 `training/web` 镜像。该镜像是我们创建的预先构建好的镜像，它包含了一个简单的 Python Flask web 应用程序。

最后，我们为我们的容器指定要运行的命令：`python app.py`。这样我们的 web 应用就启动了。

> **注意**：您可以在[命令参考](../reference/commandline/cli.md/#run)和 [Docker Run 参考](../reference/run.md)中查看到更多关于 `docker run` 命令的详细信息。

## 查看我们 web 应用程序的容器

现在让我们使用 `docker ps` 命令来查看我们正在运行的容器。
```
$ sudo docker ps -l
CONTAINER ID  IMAGE                   COMMAND       CREATED        STATUS        PORTS                    NAMES
bc533791f3f5  training/webapp:latest  python app.py 5 seconds ago  Up 2 seconds  0.0.0.0:49155->5000/tcp  nostalgic_morse
```

您可以看到我们为 `docker ps` 命令指定了新的标识 `-l`。它会告知 `docker ps` 命令返回最后一个被启动的容器的详细信息。

> **注意**：默认情况下，`docker ps` 命令只显示运行着的容器的信息。如果您还想看到已经停止的容器，请加上 `-a` 标识。

我们这里可以看到一些详细信息，不仅有与[我们 Docker 化的第一个容器](dockerizing.md)相同的信息还多了一个重要的 `PORTS` 列。
```
PORTS
0.0.0.0:49155->5000/tcp
```

如果我们为 `docker run` 命令添加 `-P` 标识，那么 Docker 会将我们镜像中开放的端口映射到我们的主机上。

> **提示**：当我们学习[如何构建镜像的时候](dockerimages.md)，我们将了解更多关于如何开放 Docker 镜像端口。

在本例中，Docker 将 5000 端口（默认的 Python Flask 端口）暴露在主机的 49155 端口上。

在 Docker 中网络端口绑定是非常容易配置的。在最后一个示例中，`-P` 标识是 `-p 5000` 的简写形式，它将会将容器内部的 5000 端口映射到本地 Docker 主机的临时端口范围（通常范围是 32768 至 61000)中的一个高位端口上。我们也可以使用 `-p` 标识来将 Docker 容器绑定到一个特定端口上。举例：
```
$ docker run -d -p 80:5000 training/webapp python app.py
```

这个做会将我们容器内的 5000 端口映射到我们本地主机的 5000 端口上。你现在可能会问：为什么我们不能使用与 Docker 容器中的端口相同的端口，而是要映射到高位端口的方式呢？1:1 的映射方式不允许 Docker 端口与本地主机的端口相一致。我们假设您想测试两个 Python 应用程序，两个容器内都绑定了 5000 端口，如果没有 Docker 的端口映射规则，您在 Docker 主机上一次只能访问其中一个应用程序。

所以，现在我们打开网页浏览器访问端口 49155，来观察该应用程序。

![应用访问](../images/webapp1.png)

我们的 Python 应用程序可以访问了！

> **注意**：
> 如果您在 OS X，windows 或 Linux 上使用了 boot2docker 虚拟机，你需要获取该虚拟机的 ip 来进行访问，而不是继续使用 localhost，您可以通过在 `boot2docker` 之外的 shell（例如，您的命令行下或终端应用程序下）运行下面的命令来获取 ip。
```
$ boot2docker ip
The VM's Host only interface IP address is: 192.168.59.103
```

在该例子中，您可以访问 http://192.168.59.103:49155 看到上面例子的结果。

### 网络端口快捷方式

使用 `docker ps` 命令来查看被映射的端口是一种比较笨拙的方式。为此，Docker 提供了一种快捷方式，我们可以使用：`docker port`。为了使用 `docker port`，我们还需指定容器的 ID 或名称还有与开放端口进行映射的那个端口。
```
$ sudo docker port nostalgic_morse 5000
0.0.0.0:49155
```

本例中，我们查看到了容器中的 5000 端口确实映射到了宿主机的 49155 端口。

### 查看Web 应用程序的日志

让我们看看我们的应用程序中都发生了什么，这里我们要使用学习到的另外一个命令 `docker logs`。
```
$ docker logs -f nostalgic_morse
* Running on http://0.0.0.0:5000/
10.0.2.2 - - [23/May/2014 20:16:31] "GET / HTTP/1.1" 200 -
10.0.2.2 - - [23/May/2014 20:16:31] "GET /favicon.ico HTTP/1.1" 404 -
```

这次我们添加了一个新的 `-f` 标识。`docker logs` 命令看起来很像 `tail -f` 命令，我们还能看到容器的标准输出。我们可以从显示屏上可以看到日志信息，它列出了运行在 5000 端口上的应用程序和你访问日志记录。

### 查看Web 应用程序容器的进程

我们除了可以查看容器日志，同时我们还可以使用 `docker top` 命令来检测运行在容器内部的进程：
```
$ sudo docker top nostalgic_morse
PID                 USER                COMMAND
854                 root                python app.py
```

这里我们可以看到我们的 `python app.py` 命令是容器中运行的唯一一个进程。

### 检查我们的 web 应用程序

最后，我们可以使用 `docker inspect ` 命令来查看 Docker 的底层信息。它会为具体指定的容器返回一个 JSON 文件，其中记录了非常有用的配置信息和状态信息。
```
$ docker inspect nostalgic_morse
```

来让我们看下JSON的输出。
```
[{
    "ID": "bc533791f3f500b280a9626688bc79e342e3ea0d528efe3a86a51ecb28ea20",
    "Created": "2014-05-26T05:52:40.808952951Z",
    "Path": "python",
    "Args": [
       "app.py"
    ],
    "Config": {
       "Hostname": "bc533791f3f5",
       "Domainname": "",
       "User": "",
. . .
```

我们还可以通过给出具体的元素提炼出我们想要的信息，例如返回容器的 IP 地址：
```
$ docker inspect -f '{{ .NetworkSettings.IPAddress }}' nostalgic_morse
172.17.0.5
```

### 停止我们的 web 应用程序所在的容器

现在，我们已经看到 `web` 应用程序开始工作了。现在让我们使用 `docker stop` 命令来停止 `nostalgic_morse` 的容器。
```
$ docker stop nostalgic_morse
nostalgic_morse
```

现在我们可以使用 `docker ps` 命令来检查容器是否已经停止了。
```
$ sudo docker ps -l
```

### 重启 web 我们应用程序所在的容器

哎呀！刚刚就在您停止了那个容器时，您得到了有另一个开发人员需要该容器的通知。这里您有两个选择：您可以创建一个新的容器或者重新启动刚刚被您关上的那个。让我们看看如何将之前的那个容器重新启动。
```
$ docker start nostalgic_morse
nostalgic_morse
```

现在赶紧再次运行 `docker ps -l` 来查看正在运行的容器是否已经恢复回来，或者通过访问 URL 来查看我们的应用程序是否有响应。

> **注意**：也可以使用 `docker restart` 命令来停止容器然后再启动容器。

### 移除 web 应用程序所在的容器

您的同事告诉您他们已经完成了在容器上的工作，不再需要该容器了。那么我们可以使用 `docker rm` 命令来删除它：
```
$ docker rm nostalgic_morse
Error: Impossible to remove a running container, please stop it first or use -f
2014/05/24 08:12:56 Error: failed to remove one or more containers
```

发生了什么？实际上，我们不能删除正在运行的容器。这避免您意外删除了一个运行着的以后可能还会继续使用的容器。让我们先关闭容器，然后再试试删除命令。
```
$ sudo docker stop nostalgic_morse
nostalgic_morse
$ sudo docker rm nostalgic_morse
nostalgic_morse
```

现在我们已经关闭并删除了该容器。

> **注意**：删除容器是最后一步！

### 下一步

直到现在，我们使用的镜像都是从 [Docker Hub]（https://hub.docker.com/) 上下载下来的。接下来，我们将会学习构建并分享属于自己的镜像。

阅读[使用镜像](dockerimages.md)
