# 从 Docker Machine 和本地虚拟机开始

让我们来看看，使用 `docker-machine` 创建，使用并管理 [VirtualBox](https://www.virtualbox.org/) 中的 Docker 主机。

首先，请您确保 [VirtualBox 4.3.28](https://www.virtualbox.org/wiki/Downloads) 已经安装在您的系统上了。

如果您执行 `docker-machine ls` 命令，您会看到目前还没有创建任何 machine。
```
$ docker-machine ls
NAME   ACTIVE   DRIVER   STATE   URL
```

为了创建一个 machine，您需要执行 `docker-machine create` 命令，并将 `virtualbox` 传递给 `--driver` 参数。最后一个需要我们给出的参数是该 machine 的名字。在本例中，我们将他命名为 "dev"。

该命令会去下载一个轻量级的并带有安装好 Docker 后台程序的 Linux 发行版（[boot2docker](https://github.com/boot2docker/boot2docker)），并创建启动一个运行 Docker 的 VirtualBox 虚拟机。

```
$ docker-machine create --driver virtualbox dev
Creating CA: /home/username/.docker/machine/certs/ca.pem
Creating client certificate: /home/username/.docker/machine/certs/cert.pem
Image cache does not exist, creating it at /home/username/.docker/machine/cache...
No default boot2docker iso found locally, downloading the latest release...
Downloading https://github.com/boot2docker/boot2docker/releases/download/v1.6.2/boot2docker.iso to /home/username/.docker/machine/cache/boot2docker.iso...
Creating VirtualBox VM...
Creating SSH key...
Starting VirtualBox VM...
Starting VM...
To see how to connect Docker to this machine, run: docker-machine env dev
```

您可以通过执行 `docker-machine ls` 命令查看您刚刚创建的 machine。

```
$ docker-machine ls
NAME   ACTIVE   DRIVER       STATE     URL                         SWARM
dev             virtualbox   Running   tcp://192.168.99.100:2376
```

接下来，我们按照 `docker-machine create` 命令的输出提示来执行 `docker-machine env` 命令，执行这个命令是为了通知 Docker 与该 machine 进行通信。例如：

```
$ eval "$(docker-machine env dev)"
$ docker ps
```
> **注意：**
> 如果您正在使用 `fish`，或者 Windows shell，例如 Powershell/`cmd.exe`，那么上面的执行命令不会生效。请您参考 [`env` 命令的文档](../machine/overview.md/#env)，学习如何为您的 shell 设置环境变量。

这将会设置环境变量，且 Docker 客户端会读取这个制定了 TLS 设置的环境变量。注意在您每次开启一个新标签或重启 machine 的时候，您都需要执行这个命令。

现在让我们来看看运行 `docker-machine env dev` 命令时，都有哪些东西将要被设置。

```
$ docker-machine env dev
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://172.16.62.130:2376"
export DOCKER_CERT_PATH="/Users/<your username>/.docker/machine/machines/dev"
export DOCKER_MACHINE_NAME="dev"
# Run this command to configure your shell:
# eval "$(docker-machine env dev)"
```

您现在可以在该 Docker 主机上运行 Docker 命令了。

```
$ docker run busybox echo hello world
Unable to find image 'busybox' locally
Pulling repository busybox
e72ac664f4f0: Download complete
511136ea3c5a: Download complete
df7546f9f060: Download complete
e433a6c5b276: Download complete
hello world
```

在 Docker 主机的 IP 地址上，任何公开的端口都是可用的，您可以通过执行 `docker-machine ip` 命令，获取 Docker 主机的 IP 地址。

```
$ docker-machine ip dev
192.168.99.100
```

例如，您可以执行如下命令，尝试运行一个 webserver（例如：nginx） container。

```
$ docker run -d -p 8000:80 nginx
```

Docker image 下载完成后，您可以通过 Docker host IP 地址的 8000 端口上访问该服务。例如：

```
$ curl $(docker-machine ip dev):8000
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

您可以通过 `docker-machine create` 创建运行 Docker 的虚拟机，数量不限。您所创建的所有 machine 将会在您执行 `docker-machine ls` 命令时呈现出来。

如果您只是暂时用某一台 Docker 主机，您可以通过执行 `docker-machine stop` 命令关闭它，如果想再次开启，请您执行 `docker-machine start` 命令。不过您要将 machine 的名称作为参数写在命令的结尾处：

```
$ docker-machine stop dev
$ docker-machine start dev
```
