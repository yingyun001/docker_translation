安装 Docker Compose 
===

您可以在 OS X 和 64 位 Linux 上运行 Compose。目前 Windows 操作系统还不支持运行 Docker Compose。想要安装 Docker Compose，您需要先安装 Docker。

基于您系统的配置，您可能会需要使用 `sudo` 的使用权限来安装 Compose。如果您系统的配置要求您使用 `sudo`，在您安装 Compse 的时候，可能会收到 `Permission denied` 的错误。如果确实是这样的话，请您在 install 的前面加上 `sudo`。

想要安装 Compose，请做如下操作：
1. 安装 1.7.1 或以上版本的 Docker Engine。
 * [在 Mac OS X installation](mac.md)（既安装了 Docker Engine 又安装了 Docker Compose）
 * [在 Ubunbu 上安装](ubuntulinux.md)
 * [在其它系统上的安装](supportedInstallation.md)
2. Mac OS X 用户已经完成了安装。使用其它操作系统的用户还需要继续执行下一步的命令。
3. 
4. 在您的终端中输入 `curl` 命令。
   命令格式如下：
   ```
   curl -L https://github.com/docker/compose/releases/download/VERSION_NUM/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
   ```
   如果您对用 `curl` 安装有任何疑问，您可以使用 `pip` 来安装：```pip install -U docker-compose```
5. 为该二进制文件添加可执行权限。
   ```
   $ chmod +x /usr/local/bin/docker-compose
   ```
6. 另外，您还需要为 `bash` 和 `zsh`shell 安装[命令补全](../compose/completion.md)
7. 测试安装是否成功。
```
$ docker-compose --version
docker-compose version: 1.4.0
```

## 升级

如果你升级的是 1.2 或者早期版本的 Docker Compose，那么当您升级完成后，您需要删除或者迁移你现有的 container。这是因为 1.3 版本的 Docker Compose 使用 Docker 标签来跟踪 container，所以 1.2 以及更早版本的 Docker Compose 需要在被标记后重启创建。

如果 Compose 检测到了未被标记的但被创建出来的 container，它将拒绝运行，这样你就不会有两组容器。如果你想要保留已经存在的容器（举例：这里有容器的数据卷上保留这非常重要的数据），你可以使用下边的命令来迁移：

	docker-compose migrate-to-labels

或者，如果这些容器是不必要的，你可以删除它们 - Composer 会重新创建一个新的。

	docker rm -f myapp_web_1 myapp_db_1 ...

## 卸载
## 还需要做什么
