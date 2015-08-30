# Docker Compose 概述

Compose 是一个用于在 Docker 中定义并运行多个应用程序的工具。有了 Compose，您可以在一个单独的文件中定义一个多 container 的应用程序，然后以一条单独的命令启动您的应用程序。

Compose 非常适合用于开发环境，测试服务器和 CI 服务器。我们目前不建议您在产品中使用。

使用 Compose 仅需三步。
1. 使用 `Dockerfile` 定义您的应用程序的环境，这样的话它可以在任何地方被复制。
2. 在 `docker-compose.yml` 文件中定义组成了该应用程序的服务，这样一来这些服务可以在一个单独的环境中一起运行起来。
3. 最后，执行 `docker-compose up` 命令来启动 Compose，并运行您的应用程序。

`docker-compose.yml` 大概是这个样子的：

```
web:
  build: .
  ports:
   - "5000-5000"
  volumes:
   - .:/code
  links:
   - redis
redis:
  image: redis
```

Compose 有应用程序整个生命周期的命令：
- 启动，停止和重构服务
- 观察正在运行的服务的状态
- 将运行的服务时的输出内容输入到日志中
- 在一个服务中运行一次性的命令

## Compose 文档

- [安装 Compose](../compose/install.md)
- [从 Django 开始](../compose/django.md)
- [从 Rails 开始](../compose/rails.md)
- [从 Wordpress 开始](../compose/wordpress.md)
- [命令行参考](../compose/reference.md)
- [Yaml 文件参考](../compose/yml.md)
- [Compose 环境变量](../compose/env.md)
- [Compose 命令行补全](../compose/completion.md)

## 快速入门
让我们从预览一个简单运行在 Docker Compose 上的 Python web 应用程序。我们假设您了解一些 Python 的知识，不过即便您不熟悉 Python，这里的描述概念也比较容易理解。

### 安装并配置
首先，[安装 Docker 和 Compose](../compose/install.md)。
然后，您可以为您的应用程序创建一个目录。

```
$ mkdir composetest
$ cd composetest
```

在这个目录中，创建 `app.py` 文件，这是一个使用了 Flask 框架并在 Redis 数据库中增值的 web 应用程序。如果您没有安装 Redis 也不需要担心，当我们[定义服务](../compose/compose.md/#定义服务)时，docker 会解决这个问题。

```
from flask import Flask
from redis import Redis

app = Flask(__name__)
redis = Redis(host='redis', port=6379)

@app.route('/')
def hello():
    redis.incr('hits')
    return 'Hello World! I have been seen %s times.' % redis.get('hits')

if __name__ == "__main__":
    app.run(host="0.0.0.0", debug=True)
```

接下来，您需要在 `requirements.txt` 文件中定义 Python 的依赖。

```
flask
redis
```

### 创建一个 Docker image
现在，您要创建一个包含该应用程序的所有依赖的 Docker image。您需要指定如何使用一个名叫 `Dockerfile` 的文件构建 image：
```
FROM python:2.7
ADD . /code
WORKDIR /code
RUN pip install -r requirements.txt
CMD python app.py
```

这段配置的含义是：
- 以 Python 2.7 image 为基础构建出一个新的 image。
- 将当前目录 `.` 添加到 image 里的 `\code` 目录中。
- 将工作目录设置为 `/code`。
- 安装 Python 依赖。
- 为该 container 设置默认命令 `python app.py`

想了解更多关于写 Dockerfile 文件的方法，请您参见：[Docker 用户指南](../userguide/dockerimages.md) 和 [Dockerfile 参考](../reference/builder.md)

您可以通过运行 `docker build -t web .` 来测试这次的构建是否成功。

### 定义服务
下面让我们使用 `docker-compose.yml` 定义一组服务：

```
web:
  build: .
  ports:
   - "5000:5000"
  volumes:
   - .:/code
  links:
   - redis
redis:
  image: redis
```

该文件中定义了两个服务：

#### web
- 利用当前目录的 `Dockerfile` 来构建。
- 将 container 暴露的 5000 端口映射到本机器的 5000 端口。
- 通过链接将 web container 连接到 Redis 服务上。
- 将本机的当前目录挂载到 container 的 `/code` 目录下，这样您就可以在不重构 image 的情况下更改代码。

#### redis
- 使用公共的 [Redis](https://hub.docker.com/_/redis/) image，该 image 是从 Docker Hub 上下载下来的。

### 利用 Compose 构建并运行您的应用程序
现在，如果您执行 `docker-compose up`，Compose 会下载 Redis image，为您的代码构建出一个新的 image，并将其启动：

```
docker-compose up
Pulling image redis...
Building web...
Starting composetest_redis_1...
Starting composetest_web_1...
redis_1 | [8] 02 Jan 18:43:35.576 # Server started, Redis version 2.8.3
web_1   |  * Running on http://0.0.0.0:5000/
web_1   |  * Restarting with stat
```

如果您正在使用 [Docker Machine]()，通过执行 `docker-machine ip MACHINE_VM` 会查询出当前 Docker Machine 的 IP 地址，您可以在浏览器中打开 `http://MACHINE_VM_IP:5000`。

如果您在 Linux 操作系统上，而且没有在使用 Boot2Docker，web app 现在应该监听 Docker 主机的 5000 端口。如果 [http://0.0.0.0:5000](http://0.0.0.0:5000) 没有返回结果，您可以使用 localhost:5000。



您会在浏览器看到：
```
Hello World! I have been seen 1 times.
```

刷新页面，数值就会增加。

如果您想要在后台运行程序，您可以在 `docker-compose up` 的结尾处添加 `-d` 参数并使用 `docker-compose ps` 查看运行情况。

```
$ docker-compose up -d
Starting composetest_redis_1...
Starting composetest_web_1...
$ docker-compose ps
    Name                 Command            State       Ports
-------------------------------------------------------------------
composetest_redis_1   /usr/local/bin/run         Up
composetest_web_1     /bin/sh -c python app.py   Up      5000->5000/tcp
```

使用 `docker-compose run` 命令，可以一次性的运行您定义的服务。例如，查看 `web` 服务的环境变量：

```
$ docker-compose run web env
```

通过 `docker-compose --help` 命令查看其它可用的命令。您还可以为 bash shell 和 zsh shell 安装 [命令补全](../compose/completion.md)，命令补全也可以给出一些可用的命令。如果您用 `docker-compose up -d` 命令来启动 Compose 的话，您可能想在启动成功后停止您的服务，那么请您执行下面这条指令：

```
$ docker-compose stop
```

现在，您已经了解了 Compose 是如何运行的。

- 接下来您可以看 [Django](../compose/django.md)，[Rails](../compose/rails.md) 和 [Wordpress](../compose/wordpress.md) 的快速指南。
- 您可以进一步了解 [命令](../compose/reference.md)，[配置文件](../compose/yml.md) 和 [环境变量](../compose/env.md) 的详细信息。

## 发行通知
### 1.2.0 版本（2015 年 4 月 7 日）
想了解该版本更多信息，请参见：[1.2.0 里程碑项目页面](https://github.com/docker/compose/wiki/1.2.0-Milestone-Project-Page)。这个版本除了修复了一些 bug 和进一步的求精以外，还添加了如下内容：
- `extends` 关键字，它提供了通过共享一般的配置文件而能够扩展服务的功能。详情请见 [PR #1088](https://github.com/docker/compose/pull/1088)。
- 可以与 Swarm 更好的整合到一起。Swarm 现在可以在同一个主机上调用相互依赖，详情请见：[PR #972](https://github.com/docker/compose/pull/972)。

## 获取帮助
Docker Compose 还处于孕育和蓬勃发展的阶段。如果您需要帮助，想要向该项目贡献，或只是想和志同道合的人讨论这个项目，我们为您准备了很多交流的频道。

- 想要提交 bug，或文件特性需求，请您使用 [issue tracker on Github](https://github.com/docker/compose/issues)。
- 想和大家即时交流，请您加入到 IRC 的 `#docker-compose` 频道中来。
- 想要贡献代码或者更新文档：请提交一个 [pull request](https://github.com/docker/compose/pulls)。
- 想要更多信息和资源，请您参见：[项目的获取帮助页面](https://docs.docker.com/project/get-help/)。
