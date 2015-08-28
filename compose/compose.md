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

```

## 发行通知
### 1.2.0 版本（2015 年 4 月 7 日）
## 获取帮助

