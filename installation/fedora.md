# Fedora

Docker 支持以下版本的 Fedora：

- Fedora 20
- Fedora 21
- Fedora 22

本小节会指导您使用 Docker 所管理的发行安装包和安装机制在 Fedora 上安装 Docker。使用这些包能确保您安装的 docker 是最新的。如果您想使用 Fedora 管理的安装包来安装 Docker 的话，请您参阅您的 Fedora 发布文档中的关于支持 Docker 的信息。

## 预备知识
不管您的 Fedora 是什么版本，Docker 都需要 64 位的操作系统来安装。同样，您的 Linux 内核版本不能低于 3.10。为了检查您当前系统的内核版本，请您打开终端并输入 `uname -r` 来查看内核版本。
```
$ uname -r 
3.19.5-100.fc20.x86_64
```
如果您的内核比较老，请你及时更新它。

最后，我们建议您更新一下您的系统。请您务必记住，您的系统不应该出现任何潜在的 kernel bugs，报告中的内核 bug 应该在最新内核包中被修复。

## 安装
安装 Docker Engine 有两种方式。您可以使用 `curl` 命令连接到 `get.docker.com` 上。该方法会运行一个通过 `yum` 包管理器安装的安装脚本。您还可以直接通过 `yum` 包管理器来安装。

### 利用脚本安装
1. 使用拥有 `sodu` 或者 `root` 特权的用户登录到您的 PC 上。
2. 请更新您的系统。
```
$ sudo yum -y update
```
3. 运行 Docker 的安装脚本。
```
$ curl -sSL https://get.docker.com/ | sh
This script adds the `docker.repo` repository and installs Docker.
```
4. 开启 Docker 的后台程序。
```
$ sudo service docker start
```
5. 通过在一个 container 中运行一个 image 来验证 `docker` 是否被安装。

   ```
   $ sudo docker run hello-world
   Unable to find image 'hello-world:latest' locally
   latest: Pulling from hello-world
   a8219747be10: Pull complete 
   91c95931e552: Already exists 
   hello-world:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
   Digest: sha256:aa03e5d0d5553b4c3473e89c8619cf79df368babd1.7.1cf5daeb82aab55838d
   Status: Downloaded newer image for hello-world:latest
   Hello from Docker.
   This message shows that your installation appears to be working correctly.

   To generate this message, Docker took the following steps:
    1. The Docker client contacted the Docker daemon.
    2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
           (Assuming it was not already locally available.)
    3. The Docker daemon created a new container from that image which runs the
           executable that produces the output you are currently reading.
    4. The Docker daemon streamed that output to the Docker client, which sent it
           to your terminal.

   To try something more ambitious, you can run an Ubuntu container with:
    $ docker run -it ubuntu bash

   For more examples and ideas, visit:
    http://docs.docker.com/userguide/
   ```

### 不利用脚本安装
1. 使用拥有 `sodu` 或者 `root` 特权的用户登录到您的 PC 上。
2. 请更新您的系统。
```
$ sudo yum -y update
```
3. 添加 yum 仓库。
   
   如果您的操作系统是 Fedora 20，请运行：
   ```
   $ cat >/etc/yum.repos.d/docker.repo <<-EOF
   [dockerrepo]
   name=Docker Repository
   baseurl=https://yum.dockerproject.org/repo/main/fedora/20
   enabled=1
   gpgcheck=1
   gpgkey=https://yum.dockerproject.org/gpg
   EOF
   ```

   如果您的操作系统是 Fedora 21，请运行：
   ```
   $ cat >/etc/yum.repos.d/docker.repo <<-EOF
   [dockerrepo]
   name=Docker Repository
   baseurl=https://yum.dockerproject.org/repo/main/fedora/21
   enabled=1
   gpgcheck=1
   gpgkey=https://yum.dockerproject.org/gpg
   EOF
   ```

   如果您的操作系统是 Fedora 22，请运行：
   ```
   $ cat >/etc/yum.repos.d/docker.repo <<-EOF
   [dockerrepo]
   name=Docker Repository
   baseurl=https://yum.dockerproject.org/repo/main/fedora/22
   enabled=1
   gpgcheck=1
   gpgkey=https://yum.dockerproject.org/gpg
   EOF
   ```
4. 安装 Docker 包。
```
$ sudo yum install docker-engine
```
5. 开启 Docker 的后台程序。
```
$ sudo service docker start
```
6. 通过在一个 container 中运行一个测试 image 来验证 `docker` 是否被安装。
```
$ sudo docker run hello-world
```

## 创建一个 docker 组
`docker` 后台程序绑定到了 Unix socket 上，而不是 TCP 端口上。默认情况下，Unix socket 是归 `root` 用户和其它可以使用 `sudo` 访问的用户拥有的。出于此原因，`docker` 后台程序需要 `root` 用户来运行。
为避免在您执行 `docker` 命令的时候使用 `sudo`，请您创建一个名叫 `docker` 的 Uxix 组，并将您的用户添加进去。当 `docker` 后台程序运行起来的时候，`docker` 组可以对 Unix socket 进行读写操作。
> **警告**
> `docker` 组与 `root` 的权限相当；欲知它是如何影响我们系统安全的的，请参见：[Docker Daemon Attack Surface](https://docs.docker.com/articles/security/#docker-daemon-attack-surface)

创建 `docker` 组并添加用户：
1. 使用拥有 `sodu` 特权的用户登录到您的 PC 上。
2. 创建 `docker` 组并添加您的用户。
```
sudo usermod -aG docker your_username
```
3. 注销并重新登录
这个步骤的作用是确保您正在以正确的用户权限运行在当前环境中。
4. 在不使用 `sudo` 的情况下运行 `docker`，验证 docker 是否可以正常运行。

   ```
   $ docker run hello-world
        Unable to find image 'hello-world:latest' locally
        latest: Pulling from hello-world
        a8219747be10: Pull complete 
        91c95931e552: Already exists 
        hello-world:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
        Digest: sha256:aa03e5d0d5553b4c3473e89c8619cf79df368babd18681cf5daeb82aab55838d
        Status: Downloaded newer image for hello-world:latest
        Hello from Docker.
        This message shows that your installation appears to be working correctly.

        To generate this message, Docker took the following steps:
         1. The Docker client contacted the Docker daemon.
         2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
                (Assuming it was not already locally available.)
         3. The Docker daemon created a new container from that image which runs the
                executable that produces the output you are currently reading.
         4. The Docker daemon streamed that output to the Docker client, which sent it
                to your terminal.

        To try something more ambitious, you can run an Ubuntu container with:
         $ docker run -it ubuntu bash

        For more examples and ideas, visit:
         http://docs.docker.com/userguide/
   ```

##	系统启动时开启 docker 后台程序
为确保在您启动系统的时候，Docker 就可以开启，请您执行如下操作：
   ```
   $ sudo chkconfig docker on
   ```
如果您需要添加一个 HTTP 代理，请您为 Docker 运行文件添加一个不同的目录或分区，或者做一些其它的定制，请您阅读并学习如何：[customize your Systemd Docker daemon options](https://docs.docker.com/articles/systemd/)。

## 使用手动定义的网络运行 Docker
如果您使用 `systemd` 版本不低于 219 的 `systemd-network` 来手动配置您的网络，那么那个您使用 Docker 启动的 container 是不能够访问您的网络的。从 systemd 220 版本开始，一个已知网络（`net.ipv4.conf.<interface>.forwarding`）的转发设置默认是*关闭*的。如此设置会阻止 IP 的转发。它还会和 container 中开启了 `net.ipv4.conf.all.forwarding` 设置的 Docker 有冲突。
为了避免上述问题，请您编辑 Docker host 中 `/usr/lib/systemd/network/` 目录下的 `<interface>.network` 文件：
```
[Network]
...
IPForward=kernel
# OR
IPForward=true
...
```
该配置允许 IP 从 container 中转发出去。
## 卸载
您可以使用 `yum` 卸载 Docker。
1. 列出您安装的所有的包。
```
$ yum list installed | grep docker
yum list installed | grep docker
docker-engine.x86_64                1.7.1-0.1.fc20            @/docker-engine-1.7.1-0.1.fc20.el7.x86_64
```
2. 删除包。
```
$ sudo yum -y remove docker-engine.x86_64
```
该命令还不能删除 images，containers，卷，或您在机器上创建的配置文件。
3. 为了删除所有的 image，containers，卷，请您执行以下命令：
```
$ rm -rf /var/lib/docker
```
4. 请您找到您为 Docker 创建的配置文件，并将之删除。
