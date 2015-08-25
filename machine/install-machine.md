安装 Docker Machine
===

Docker Machine 支持 Windows ,OS X ,和 Linux，并且是以独立的二进制文件的形式安装的。用于各平台架构的二进制文件可从 [Github Release](https://github.com/docker/machine/releases/) 获得。

## OS X 和 Windows
通过 [Mac OS X 安装](mac.md)说明或 [Windows 安装](windows.md)说明，并使用 Docker Toolbox 来安装 Docker。

## Linux
为了在 Linux 下安装 Docker，请按如下说明操作：
1. 安装 [1.7.1 或更高的版本的 Docker](supportedInstallation.md)。
2. 将二进制文件下载到您的指令目录中，例如：`/usr/local/bin`。
```
$ curl -L https://github.com/docker/machine/releases/download/v0.4.0/docker-machine_linux-amd64 > /usr/local/bin/docker-machine
```
3. 为该二进制文件添加可执行权限。
```
$ chmod +x /usr/local/bin/docker-machine
```
4. 通过查看 Docker Machine 的版本验证 docker-machine 是否安装成功。
```
docker-machine -v
docker-machine version 0.4.0 (9d0dc7a)
```

## 还需要做什么
- [Docker Machine 概述](../machine/overview.md)
- [Docker Machine 参考驱动](../machine/drivers.md)
- [Docker Machine 参考子命令](../machine/reference.md)
