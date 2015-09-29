# 使用命令行

为了列出所有可能的命令行，请运行 `docker` 或者运行 `docker help`。
```
$ docker
Usage: docker [OPTIONS] COMMAND [arg...]
       docker daemon [ --help | ... ]
       docker [ --help | -v | --version ]

A self-sufficient runtime for containers.
```

基于您 Docker 系统配置的不同，您可能需要在每个 `docker` 命令的前面加上 `sudo`。为了避免每次运行执行 `docker` 命令的时候都使用 `sudo`，您的系统管理员需要创建一个名叫 `docker` 的用户组并将用户添加到该组中。

想要获取更多关于安装 Docker 或者 `sudo` 配置的信息，请您参考与您的操作系统相对应的 [安装](../../installation/README.md) 教程。

## 环境变量
为了便与您的参考，`docker` 命令行支持下面的这些环境变量：
* `DOCKER_CONFIG` 您客户端配置文件的所在位置。
* `DOCKER_CERT_PATH` 您认证密钥的位置。
* `DOCKER_DRIVER` 要使用的图形驱动。
* `DOCKER_HOST` 要连接的后台程序的会话。
* `DOCKER_NOWARN_KERNEL_VERSION` 阻止警告您的 Linux 内核不适合运行 Docker。
* `DOCKER_TLS_VERIFY`
* `DOCKER_CONTENT_TRUST`
* `DOCKER_TMPDIR` Docker 临时目录的位置。

由于 Docker 是使用 Go 语言开发的，您还可以使用任意 Go 运行时用到的环境变量。您可能会发现这些有用的东西：
* `HTTP_PROXY`
* `HTTPS_PROXY`
* `NO_PROXY`

这些 Go 的环境变量是对大小写不敏感的环境变量。请看 [Go 说明书](https://golang.org/pkg/net/http/)中关于这些变量的详细信息。

## 配置文件
默认情况下，Docker 命令行在 `HOME` 目录下的 `.docker` 目录中存放了它的配置文件。但是您可以

## 帮助
## 选项类型
### 布尔类型
### 
### 字符串类型和数值类型
