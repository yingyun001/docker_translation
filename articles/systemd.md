# 使用 systemd 控制并配置 Docker
许多 Linux 发行版都使用了 systemd 来开启 Docker 后台程序。本章节给出了许多自定义 Docker 设置的示例。

## 开启 Docker 后台
一旦 Docker 被安装，您就需要开启 Docker 后台程序了。

```
$ sudo systemctl start docker
# 或者在更老的版本中，您可能需要使用
$ sudo service docker start
```

如果您想要在系统启动时就开启 Docker，您应该：
```
sudo systemctl enable docker
# 或者在更老的版本中，您可能需要使用
$ sudo chkconfig docker on
```
## 自定义 Docker 后台选项
这里有一些为您的 Docker 后台程序配置后台程序标识和环境变量的方式。

我们推荐的方式是使用 systemd 的 drop-in 文件，就是那些在 `/etc/systemd/system/docker.service.d` 目录中的本地文件，还可以是 `/etc/systemd/system/docker.service` 目录中的文件，这个文件还用于重写 `/lib/systemd/system/docker.service` 文件中的默认值。

但是，如果您之前出于向后兼容的考虑，使用了一个具有 `EnvironmentFile` 文件(通常指的是 `/etc/sysconfig/docker` 文件）的包，那么您应该在 `/etc/systemd/system/docker.service.d` 目录下创建具有以下内容的文件。
```
[Service]
EnvironmentFile=-/etc/sysconfig/docker
EnvironmentFile=-/etc/sysconfig/docker-storage
EnvironmentFile=-/etc/sysconfig/docker-network
ExecStart=
ExecStart=/usr/bin/docker -d -H fd:// $OPTIONS \
          $DOCKER_STORAGE_OPTIONS \
          $DOCKER_NETWORK_OPTIONS \
          $BLOCK_REGISTRY \
          $INSECURE_REGISTRY
```

为了检查 `docker.service` 是否使用了一个 `EnvironmentFile`：
```
$ sudo systemctl show docker | grep EnvironmentFile
EnvironmentFile = -/etc/sysconfig/docker（ignore_errors=yes）
```
或者找出服务文件所在的位置：
```
$ sudo systemctl status docker | grep Loaded
Loaded: loaded (/usr/lib/systemd/system/docker.service; enabled)
$ sudo grep EnvironmentFile /usr/lib/systemd/system/docker.service
EnvironmentFile=-/etc/sysconfig/docker
```

您可以像 [HTTP Proxy example](#HTTP 代理)中描述的那样来覆盖文件，从而可以自定义 Docker 的后台应用程序选项。存放在 `/usr/lib/systemd/system` 目录或者 `/lib/systemd/system` 目录中的文件包含了默认选项且不能被编辑。

### 运行时目录和驱动存储
您可能想通过将 Docker 镜像，容器和卷移动到一个独立的分区中，从而控制它们的磁盘空间的使用率。

在这个示例中，我们假设您的 `docker.service` 文件是下面这样的：
```
[Unit]
Description=Docker Application Container Engine
Documentation=https://docs.docker.com
After=network.target docker.socket
Requires=docker.socket

[Service]
Type=notify
ExecStart=/usr/bin/docker daemon -H fd://
LimitNOFILE=1048576
LimitNPROC=1048576

[Install]
Also=docker.socket
```

我们可以通过前面提及过的 drop-in 文件来添加额外的标识，drop-in 文件是需要放置在 `/etc/systemd/system/docker.service.d` 目录下且包含以下内容的文件：
```
[service]
ExecStart=
ExecStart=/usr/bin/docker daemon -H fd://  --graph /mnt/docker-data --storage-driver btrfs
```

您还可以在该文件中设置其它的环境变量，例如即将要说到的 `HTTP_PROXY` 环境变量。

### HTTP 代理
该示例覆盖了默认的 `docker.service` 文件。

如果您需要通过 HTTP 代理访问外网，例如公司中的网络设置，您需要在 Docker 的 systemd 服务文件中添加该设置。

首先，为 docker 服务创建一个 systemd 的 drop-in 文件的目录：
```
mkdir /etc/systemd/system/docker.service.d
``` 

然后在该目录中创建一个叫 `http-proxy.conf` 的文件，在该文件中添加 `HTTP_PROXY` 环境变量：
```
[Service]
Environment="HTTP_PROXY=http://proxy.example.com:80/"
```

如果您有 Docker 内部注册（不通过代理也可以访问），那么您可以通过 `NO_PROXY` 环境变量来说明这一点：
```
Environment="HTTP_PROXY=http://proxy.example.com:80/" "NO_PROXY=localhost,127.0.0.0/8,docker-registry.somecorporation.com"
```

激活您的更改：
```
$ sudo systemctl daemon-reload
```

核实配置是否已经成功加载进来：
```
$ sudo systemctl show docker --property Environment
Environment=HTTP_PROXY=http://proxy.example.com:80/
```

重启 Docker：
```
$ sudo systemctl restart docker
```

### 手动创建 systemd 单元文件
当您在不使用包的情况下来安装儿进制文件，您可能想将 Docker 和 systemd 整合起来。为了实现它们的整合，请您在 `/etc/systemd/system` 目录中添加两个单元文件（service 和 socket），您可以在 [github 仓库](https://github.com/docker/docker/tree/master/contrib/init/systemd)中找到。
