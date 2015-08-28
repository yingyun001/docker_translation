# create

创建一个 machine。

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
> **注意：**
> 如果您在执行了创建命令之后，报错显示 boot2docker.iso 不能被成功下载，那么您需要手动将 boot2docker.iso 下载到 `~/.docker/machine/cache/` 目录中。

## 通过帮助文档中的 driver 过滤掉一些创建参数
您可能会注意到 `docker-machine create` 命令有很多选项，这是因为该命令提供了很多用于特殊情况的可用选项。
```
$ docker-machine create -h | wc -l
157
```


## 为已创建好的 Docker engine 指定配置选项
## 为已创建好的 machien 指定 Docker swarm 选项
