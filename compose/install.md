# 安装 Docker Compose

您可以在 OS X 和 64 位的 Linux 操作系统上运行 Compose。目前还不支持 Windows 操作系统。为了安装 Compose，您需要先安装 Docker。

鉴于您系统的配置，您可能需要 `sudo` 使用权来安装 Compose。如果您的系统确实需要 `sudo`，那么在您安装 Compose 的时候，您会接收到 “Permission denied” 这个错误，请您在安装 Compose 命令的前面加上 `sudo`。

想要安装 Compose，请进行下面的操作：
1. 安装 1.7.1 或更高版本的 Docker Engine：
   * [在 Mac OS X 上安装](../installation/mac.md)（这里即安装了 Docker Engine 又安装了 Docker Compose）
   * [在 Ubuntu 上安装](../installation/ubuntu.md)
   * [在其它操作系统上安装](../installation/installation.md)

2. Mac OS X 用户现在已经完成了 Compose 的安装。其他用户继续进行下一步的操作。
3. 跳转到 [Compose 仓库的版本页面](https://github.com/docker/compose/releases)。
4. 在您的终端中输入 `curl` 命令。
   该命令的格式如下：
   ```
   curl -L https://github.com/docker/compose/releases/download/1.4.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
   ```
5. 对该二进制文件应用可执行权限：
   ```
   chmod +x /usr/local/bin/docker-compose
   ```
6. 另外，您需要为 `bash` shell 和 `zsh` shell 安装[命令补全](../compose/completion.md)。
7. 测试安装。
   ```
   docker-compose --version
   docker-compose version: 1.4.0
   ```

## 升级
如果您正在更新 Compose 的 1.2 乃至更早的版本，您需要在升级 Compose 时删除或者迁移已有的 container。这时因为，自 1.3 版本起，Compose 使用了 Docker 标签来追踪 container，所以这些 container 需要被重新创建为带有标记的 container。

如果 Compose 监测到了未带标记的 container，Compose 将不会运行。如果您还想继续使用 container（例如，container 里有您想要存留的数据卷），您可以通过执行下面的命令执行它：

```
docker-compose migrate-to-labels
```

或者，如果您觉得这些 container 可以删除，您可以删除它们；Compose 会创建一个新的。

```
$ docker rm -f -v myapp_web_1 myapp_db_1 myapp_db_1 ...
```

## 卸载
想要卸载使用 `curl` 安装的的  Docker Compose：

```
$ rm /usr/local/bin/docker-compose
```

想要卸载使用 `pip` 安装的 Docker Compose：

```
pip uninstall docker-compose
```

> **注意：**
> 如果您按照上述操作执行时接收到 “Permission denied” 错误，那么您可能不具有删除 `docker-compose` 的权限。为了强制删除它，请在命令的前面加上 `sudo` 并再次运行它。

## 下面做什么？
- [用户手册](compose.md)
- [从 Django 开始](../compose/django.md)
- [从 Rails 开始](../compose/rails.md)
- [从 Wordpress 开始](../compose/wordpress.md)
- [命令行参考](../compose/reference.md)
- [Yaml 文件参考](../compose/yml.md)
- [Compose 环境变量](../compose/env.md)
- [Compose 命令行补全](../compose/completion.md)
