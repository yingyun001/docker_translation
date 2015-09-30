# 开启镜像之旅

通过[Docker 介绍](introduction/understanding-docker.md)小节，我们已经知道了 Docker 镜像是容器的基础。在[前面的小节](dockerizing.md)中，我们使用的是已经存在的 Docker 镜像，例如： `ubuntu` 镜像和 `training/webapp` 镜像。

我们还知道 Docker 会将下载好的镜像存储到 Docker 主机上。如果一个镜像还不存在于 Docker 主机中，那么它就会从 Docker 的注册中心上下载下来：Docker 中默认的注册中心就是 [Docker Hub 公共镜像源](https://hub.docker.com/)。

在这一小节中，我们将会解释更多关于 Docker 镜像的东西：

* 管理并使用 Docker 主机上的镜像；
* 创建基本镜像；
* 将 Docker 镜像上传到 [Docker Hub Registry](https://registry.hub.docker.com/)。

## 列出 Docker 主机上的镜像

让我们列出本地主机上的镜像。您可以使用 `docker images` 命令：
```
$ docker images
REPOSITORY       TAG      IMAGE ID      CREATED      VIRTUAL SIZE
training/webapp  latest   fc77f57ad303  3 weeks ago  280.5 MB
ubuntu           13.10    5e019ab7bf6d  4 weeks ago  180 MB
ubuntu           saucy    5e019ab7bf6d  4 weeks ago  180 MB
ubuntu           12.04    74fe38d11401  4 weeks ago  209.6 MB
ubuntu           precise  74fe38d11401  4 weeks ago  209.6 MB
ubuntu           12.10    a7cf8ae4e998  4 weeks ago  171.3 MB
ubuntu           quantal  a7cf8ae4e998  4 weeks ago  171.3 MB
ubuntu           14.04    99ec81b80c55  4 weeks ago  266 MB
ubuntu           latest   99ec81b80c55  4 weeks ago  266 MB
ubuntu           trusty   99ec81b80c55  4 weeks ago  266 MB
ubuntu           13.04    316b678ddf48  4 weeks ago  169.4 MB
ubuntu           raring   316b678ddf48  4 weeks ago  169.4 MB
ubuntu           10.04    3db9c44f4520  4 weeks ago  183 MB
ubuntu           lucid    3db9c44f4520  4 weeks ago  183 MB
```

我们可以看到之前使用过的镜像。当我们使用镜像来启动一个新容器的时候，每个镜像都是从 [Docker Hub](https://hub.docker.com/) 下载下来的。

我们可以从列表中看出与我们镜像有关的三份重要的信息。

* 它们来自什么镜像源，例如 `ubuntu`。
* 每个镜像的标签(tags)，例如 `14.04`
* 每个镜像的镜像 ID。

> **注意**
> 之前的 `docker images` 命令支持 `--tree` 和 `--dot` 参数，通过这些参数可以从不同的视角展现镜像数据。自 1.7 版本开始，Docker 内核就删除了这个功能。如果您喜欢这个功能，您可以从[第三方 dockviz 工具](https://github.com/justone/dockviz)中找到它。

一个镜像仓库中可能存储着一个镜像源的多个版本。就拿 `ubuntu` 这个镜像来说，我们可以看到多个版本：10.04, 12.04, 12.10, 13.04, 13.10 和 14.04。镜像的每个版本都会被唯一的标签所标记，您可以像这样引用一个打了标签的镜像：
```
ubuntu:14.04
```

所以如果我们要运行一个容器，我们应该像这样引用一个打了标签的镜像：
```
$ docker run -t -i ubuntu:14.04 /bin/bash
```

如果我们想运行 `Ubuntu 12.04` 镜像，我们可以这样做:
```
$ docker run -t -i ubuntu:12.04 /bin/bash
```

如果您没有为镜像指定任何一个版本，例如您只写了 `ubuntu`，Docker 会默认使用 `Ubuntu:latest` 镜像。

> **提示**：
> 我们建议您使用打了标签的镜像，例如 `ubuntu:12.04` 。这样您就知道您正在使用的是哪一个版本的镜像。

## 获取一个新的镜像

我们如何获取一个新的镜像呢？如果我们要使用的那个镜像没有在本地主机上，Docker 将会自动下载那个镜像。但是这可能导致会花费很长时间来启动这个容器。如果我们想提前下载好这个镜像，我们可以使用 `docker pull` 命令来下载它。我们来下载 `centos` 镜像。
```
$ docker pull centos
Pulling repository centos
b7de3133ff98: Pulling dependent layers
5cc9e91966f7: Pulling fs layer
511136ea3c5a: Download complete
ef52fb1fe610: Download complete
. . .

Status: Downloaded newer image for centos
```

我们可以看到镜像的每一层都被下载下来了，现在我们就可以使用这个镜像来运行容器了，而不需要再下载该镜像了。
```
$ docker run -t -i centos /bin/bash
bash-4.1#
```

## 查找镜像

Docker 的特性之一就是人们可以出于各种各样的目的来创建 Docker 镜像。而且许多镜像已经被上传到了 [Docker Hub](https://hub.docker.com/) 上。我们可以在 [Docker Hub](https://hub.docker.com/) 的网站上来搜索这些镜像。

![dockerHub](../images/search.png)

我们还可以在命令行中使用 `docker search` 命令来搜索镜像。比如，我们团队需要一个安装了 Ruby 和 Sinatra 的镜像来支持我们的 web 应用程序开发。我们可以通过 `docker search` 命令找到所有包含  `sinatra` 关键字的镜像，从而找到适合我们的镜像。
```
$ docker search sinatra
NAME                                   DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
training/sinatra                       Sinatra training image                          0                    [OK]
marceldegraaf/sinatra                  Sinatra test app                                0
mattwarren/docker-sinatra-demo                                                         0                    [OK]
luisbebop/docker-sinatra-hello-world                                                   0                    [OK]
bmorearty/handson-sinatra              handson-ruby + Sinatra for Hands on with D...   0
subwiz/sinatra                                                                         0
bmorearty/sinatra                                                                      0
. . . 
```

我们可以看到返回的带有 `sinatra` 关键字的镜像。列表中有镜像名称、描述、Stars（衡量镜像的知名度 ——  如果用户喜欢这个镜像他就会 “star” 它。)和是否是官方出品的标识以及自动构建的状态。[官方镜像仓库](docker-hub/official_repos.md)是 Docker 支持的一个注入很多心血的 Docker 镜像库。[自动化构建](dockerrepos.md/#automated-builds)的镜像仓库是允许您验证镜像的来源和内容的。

我们看到了可被使用的镜像，并决定使用 `training/sinatra` 镜像。到目前为止，我们已经看到了两类镜像仓库，比如 `ubuntu` 镜像，它被称为基础镜像或者根镜像。这些基础镜像是由 Docker 官方提供，构建，验证并支持的。这些镜像都可以通过它们单个词的名字来标识。

我们还看到了用户镜像，例如我们选择的 `training/sinatra` 镜像。用户镜像属于 Docker 社区的所有成员并被成员们构建和维护。您可以标识用户镜像，因为这些镜像总是以用户名为前缀，好比 `training` 就是由 `training` 用户创建的镜像。

## 下载镜像

我们已经找到了我们想要的镜像，即 `training/sinatra`，现在我们可以使用 `docker pull` 命令来下载这个镜像。
```
$ docker pull training/sinatra
```

现在这个团队就可以通过运行自己的容器来使用这个镜像了。
```
$ docker run -t -i training/sinatra /bin/bash
root@a8cb6ce02d85:/#
```

## 创建我们自己的镜像

团队发现 `training/sinatra` 镜像对于我们来说是非常有用的，但它还不能完全满足他们的需求，我们需要针对这个镜像做出改变。这里有两种方法：更新镜像或者创建一个新的镜像。

1. 我们可以从已经创建的容器中更新镜像，并且提交这个镜像。
2. 我们可以使用 `Dockerfile` 来指定创建一个镜像的指令。

### 更新并且提交一个镜像

为了更新镜像，我们首先需要用我们想更新的镜像创建一个容器出来。
```
$ docker run -t -i training/sinatra /bin/bash
root@0b2616b0e5a8:/#
```

> **注意**：
> 注意容器ID `0b2616b0e5a8` 已经被创建出来了，我们马上就需要它。

在运行的容器内，让我们使用 gem 来安装 `json`。
```
root@0b2616b0e5a8:/# gem install json
```

操作之后，让我们输入 `exit` 命令来退出该容器。

现在我们有了一个被我们更改的容器。我们可以使用 `docker commit` 将该容器的副本提交到一个镜像上。
```
$ docker commit -m "Added json gem" -a "Kate Smith" 0b2616b0e5a8 ouruser/sinatra:v2
4f177bd27a9ff0f6dc2a830403925b5360bfe0b93d476f7fc3231110e7f71b1c
```

这里我们使用到了 `docker commit` 命令，并且我们还指定了两个标识（flags）：`-m` 和 `-a`。`-m` 允许我们指定一个提交的信息，很您在版本控制系统上所做的提交很像。`-a` 标识允许我们为所作的变更指定一个作者。

我们还为创建出来的新镜像指定了其所属容器，即 `0b2616b0e5a8`(我们之前记录过）并且还为该镜像指定了目标名称：
```
ouruser/sinatra:v2
```

我们来讲解一下这个目标名称的意义。它包含了一个新的用户名称 `ouruser`，我们正在将镜像写入用户目录中。我们还指定了镜像的名称，这里我们继续使用原来的镜像名称 `sinatra`。最后我们还为该镜像指定了一个标签：`v2`。

我们可以使用 `docker images` 命令来查看我们的新镜像,`ouruser/sinatra`。
```
$ docker images
REPOSITORY          TAG     IMAGE ID       CREATED       VIRTUAL SIZE
training/sinatra    latest  5bc342fa0b91   10 hours ago  446.7 MB
ouruser/sinatra     v2      3c59e02ddd1a   10 hours ago  446.7 MB
ouruser/sinatra     latest  5db5f8471261   10 hours ago  446.7 MB
```

想要使用我们的新镜像来创建一个容器，您需要这样做：
```
$ docker run -t -i ouruser/sinatra:v2 /bin/bash
root@78e82f680994:/#
```

### 从 `Dockerfile` 中构建镜像

使用 `docker commit` 命令来扩展镜像是非常容易的，但是它有点麻烦，如果想在团队中分享镜像的开发流程也是不容易的。为了解决这一问题，我们可以使用 `docker build` 命令从头开始够将一个新的镜像出来。

为此，我们需要创建一个 `Dockerfile` 文件，其中包含一组指令来告诉 Docker 如何构建我们的镜像。

现在创建一个目录，并且创建一个 `Dockerfile`

	$ mkdir sinatra
	$ cd sinatra
	$ touch Dockerfile

如果您是在 Windows 上使用的 Boot2Docker，您可以通过使用 `cd` 命令来访问您的本地主机目录 `/c/Users/your_user_name`

每一个指令都会在镜像上创建一个新的层，来看一个简单的例子，我们的开发团建来构建一个自己的 `Sinatra `镜像：

	# This is a comment
	FROM ubuntu:14.04
	MAINTAINER Kate Smith <ksmith@example.com>
	RUN apt-get update && apt-get install -y ruby ruby-dev
	RUN gem install sinatra

让我们看一下我们的 `Dockerfile` 做了什么。每一个指令的前缀都必须是大写的。

	INSTRUCTION statement

>提示：我们可以使用 **#** 来注释

第一个指令 `FROM` 是告诉 Docker 使用的哪个镜像源，在这个案例中，我们使用的是 Ubuntu 14.04 基础镜像。

下一步，我们使用 `MAINTAINER ` 指令来指定谁在维护这个新镜像。

最后，我们指定了两个 `RUN` 指令。 `RUN` 指令在镜像内执行一条命令，例如：安装一个包。这里我们更新了 APT 的缓存，并且安装 Ruby 和 RubyGems ，然后使用 gem 安装 Sinatra 。


> **注意**：
> 我们还提供了更多的 Dockerfile 指令参数。

现在，我们使用 `Dockerfile` 文件，通过 `docker build` 命令来构建一个镜像。

	$ docker build -t ouruser/sinatra:v2 .
	Sending build context to Docker daemon 2.048 kB
	Sending build context to Docker daemon 
	Step 0 : FROM ubuntu:14.04
	 ---> e54ca5efa2e9
	Step 1 : MAINTAINER Kate Smith <ksmith@example.com>
	 ---> Using cache
	 ---> 851baf55332b
	Step 2 : RUN apt-get update && apt-get install -y ruby ruby-dev
	 ---> Running in 3a2558904e9b
	Selecting previously unselected package libasan0:amd64.
	(Reading database ... 11518 files and directories currently installed.)
	Preparing to unpack .../libasan0_4.8.2-19ubuntu1_amd64.deb ...
	Unpacking libasan0:amd64 (4.8.2-19ubuntu1) ...
	Selecting previously unselected package libatomic1:amd64.
	Preparing to unpack .../libatomic1_4.8.2-19ubuntu1_amd64.deb ...
	Unpacking libatomic1:amd64 (4.8.2-19ubuntu1) ...
	Selecting previously unselected package libgmp10:amd64.
	Preparing to unpack .../libgmp10_2%3a5.1.3+dfsg-1ubuntu1_amd64.deb ...
	Unpacking libgmp10:amd64 (2:5.1.3+dfsg-1ubuntu1) ...
	Selecting previously unselected package libisl10:amd64.
	Preparing to unpack .../libisl10_0.12.2-1_amd64.deb ...
	Unpacking libisl10:amd64 (0.12.2-1) ...
	Selecting previously unselected package libcloog-isl4:amd64.
	Preparing to unpack .../libcloog-isl4_0.18.2-1_amd64.deb ...
	Unpacking libcloog-isl4:amd64 (0.18.2-1) ...
	Selecting previously unselected package libgomp1:amd64.
	Preparing to unpack .../libgomp1_4.8.2-19ubuntu1_amd64.deb ...
	Unpacking libgomp1:amd64 (4.8.2-19ubuntu1) ...
	Selecting previously unselected package libitm1:amd64.
	Preparing to unpack .../libitm1_4.8.2-19ubuntu1_amd64.deb ...
	Unpacking libitm1:amd64 (4.8.2-19ubuntu1) ...
	Selecting previously unselected package libmpfr4:amd64.
	Preparing to unpack .../libmpfr4_3.1.2-1_amd64.deb ...
	Unpacking libmpfr4:amd64 (3.1.2-1) ...
	Selecting previously unselected package libquadmath0:amd64.
	Preparing to unpack .../libquadmath0_4.8.2-19ubuntu1_amd64.deb ...
	Unpacking libquadmath0:amd64 (4.8.2-19ubuntu1) ...
	Selecting previously unselected package libtsan0:amd64.
	Preparing to unpack .../libtsan0_4.8.2-19ubuntu1_amd64.deb ...
	Unpacking libtsan0:amd64 (4.8.2-19ubuntu1) ...
	Selecting previously unselected package libyaml-0-2:amd64.
	Preparing to unpack .../libyaml-0-2_0.1.4-3ubuntu3_amd64.deb ...
	Unpacking libyaml-0-2:amd64 (0.1.4-3ubuntu3) ...
	Selecting previously unselected package libmpc3:amd64.
	Preparing to unpack .../libmpc3_1.0.1-1ubuntu1_amd64.deb ...
	Unpacking libmpc3:amd64 (1.0.1-1ubuntu1) ...
	Selecting previously unselected package openssl.
	Preparing to unpack .../openssl_1.0.1f-1ubuntu2.4_amd64.deb ...
	Unpacking openssl (1.0.1f-1ubuntu2.4) ...
	Selecting previously unselected package ca-certificates.
	Preparing to unpack .../ca-certificates_20130906ubuntu2_all.deb ...
	Unpacking ca-certificates (20130906ubuntu2) ...
	Selecting previously unselected package manpages.
	Preparing to unpack .../manpages_3.54-1ubuntu1_all.deb ...
	Unpacking manpages (3.54-1ubuntu1) ...
	Selecting previously unselected package binutils.
	Preparing to unpack .../binutils_2.24-5ubuntu3_amd64.deb ...
	Unpacking binutils (2.24-5ubuntu3) ...
	Selecting previously unselected package cpp-4.8.
	Preparing to unpack .../cpp-4.8_4.8.2-19ubuntu1_amd64.deb ...
	Unpacking cpp-4.8 (4.8.2-19ubuntu1) ...
	Selecting previously unselected package cpp.
	Preparing to unpack .../cpp_4%3a4.8.2-1ubuntu6_amd64.deb ...
	Unpacking cpp (4:4.8.2-1ubuntu6) ...
	Selecting previously unselected package libgcc-4.8-dev:amd64.
	Preparing to unpack .../libgcc-4.8-dev_4.8.2-19ubuntu1_amd64.deb ...
	Unpacking libgcc-4.8-dev:amd64 (4.8.2-19ubuntu1) ...
	Selecting previously unselected package gcc-4.8.
	Preparing to unpack .../gcc-4.8_4.8.2-19ubuntu1_amd64.deb ...
	Unpacking gcc-4.8 (4.8.2-19ubuntu1) ...
	Selecting previously unselected package gcc.
	Preparing to unpack .../gcc_4%3a4.8.2-1ubuntu6_amd64.deb ...
	Unpacking gcc (4:4.8.2-1ubuntu6) ...
	Selecting previously unselected package libc-dev-bin.
	Preparing to unpack .../libc-dev-bin_2.19-0ubuntu6_amd64.deb ...
	Unpacking libc-dev-bin (2.19-0ubuntu6) ...
	Selecting previously unselected package linux-libc-dev:amd64.
	Preparing to unpack .../linux-libc-dev_3.13.0-30.55_amd64.deb ...
	Unpacking linux-libc-dev:amd64 (3.13.0-30.55) ...
	Selecting previously unselected package libc6-dev:amd64.
	Preparing to unpack .../libc6-dev_2.19-0ubuntu6_amd64.deb ...
	Unpacking libc6-dev:amd64 (2.19-0ubuntu6) ...
	Selecting previously unselected package ruby.
	Preparing to unpack .../ruby_1%3a1.9.3.4_all.deb ...
	Unpacking ruby (1:1.9.3.4) ...
	Selecting previously unselected package ruby1.9.1.
	Preparing to unpack .../ruby1.9.1_1.9.3.484-2ubuntu1_amd64.deb ...
	Unpacking ruby1.9.1 (1.9.3.484-2ubuntu1) ...
	Selecting previously unselected package libruby1.9.1.
	Preparing to unpack .../libruby1.9.1_1.9.3.484-2ubuntu1_amd64.deb ...
	Unpacking libruby1.9.1 (1.9.3.484-2ubuntu1) ...
	Selecting previously unselected package manpages-dev.
	Preparing to unpack .../manpages-dev_3.54-1ubuntu1_all.deb ...
	Unpacking manpages-dev (3.54-1ubuntu1) ...
	Selecting previously unselected package ruby1.9.1-dev.
	Preparing to unpack .../ruby1.9.1-dev_1.9.3.484-2ubuntu1_amd64.deb ...
	Unpacking ruby1.9.1-dev (1.9.3.484-2ubuntu1) ...
	Selecting previously unselected package ruby-dev.
	Preparing to unpack .../ruby-dev_1%3a1.9.3.4_all.deb ...
	Unpacking ruby-dev (1:1.9.3.4) ...
	Setting up libasan0:amd64 (4.8.2-19ubuntu1) ...
	Setting up libatomic1:amd64 (4.8.2-19ubuntu1) ...
	Setting up libgmp10:amd64 (2:5.1.3+dfsg-1ubuntu1) ...
	Setting up libisl10:amd64 (0.12.2-1) ...
	Setting up libcloog-isl4:amd64 (0.18.2-1) ...
	Setting up libgomp1:amd64 (4.8.2-19ubuntu1) ...
	Setting up libitm1:amd64 (4.8.2-19ubuntu1) ...
	Setting up libmpfr4:amd64 (3.1.2-1) ...
	Setting up libquadmath0:amd64 (4.8.2-19ubuntu1) ...
	Setting up libtsan0:amd64 (4.8.2-19ubuntu1) ...
	Setting up libyaml-0-2:amd64 (0.1.4-3ubuntu3) ...
	Setting up libmpc3:amd64 (1.0.1-1ubuntu1) ...
	Setting up openssl (1.0.1f-1ubuntu2.4) ...
	Setting up ca-certificates (20130906ubuntu2) ...
	debconf: unable to initialize frontend: Dialog
	debconf: (TERM is not set, so the dialog frontend is not usable.)
	debconf: falling back to frontend: Readline
	debconf: unable to initialize frontend: Readline
	debconf: (This frontend requires a controlling tty.)
	debconf: falling back to frontend: Teletype
	Setting up manpages (3.54-1ubuntu1) ...
	Setting up binutils (2.24-5ubuntu3) ...
	Setting up cpp-4.8 (4.8.2-19ubuntu1) ...
	Setting up cpp (4:4.8.2-1ubuntu6) ...
	Setting up libgcc-4.8-dev:amd64 (4.8.2-19ubuntu1) ...
	Setting up gcc-4.8 (4.8.2-19ubuntu1) ...
	Setting up gcc (4:4.8.2-1ubuntu6) ...
	Setting up libc-dev-bin (2.19-0ubuntu6) ...
	Setting up linux-libc-dev:amd64 (3.13.0-30.55) ...
	Setting up libc6-dev:amd64 (2.19-0ubuntu6) ...
	Setting up manpages-dev (3.54-1ubuntu1) ...
	Setting up libruby1.9.1 (1.9.3.484-2ubuntu1) ...
	Setting up ruby1.9.1-dev (1.9.3.484-2ubuntu1) ...
	Setting up ruby-dev (1:1.9.3.4) ...
	Setting up ruby (1:1.9.3.4) ...
	Setting up ruby1.9.1 (1.9.3.484-2ubuntu1) ...
	Processing triggers for libc-bin (2.19-0ubuntu6) ...
	Processing triggers for ca-certificates (20130906ubuntu2) ...
	Updating certificates in /etc/ssl/certs... 164 added, 0 removed; done.
	Running hooks in /etc/ca-certificates/update.d....done.
	 ---> c55c31703134
	Removing intermediate container 3a2558904e9b
	Step 3 : RUN gem install sinatra
	 ---> Running in 6b81cb6313e5
	unable to convert "\xC3" to UTF-8 in conversion from ASCII-8BIT to UTF-8 to US-ASCII for README.rdoc, skipping
	unable to convert "\xC3" to UTF-8 in conversion from ASCII-8BIT to UTF-8 to US-ASCII for README.rdoc, skipping
	Successfully installed rack-1.5.2
	Successfully installed tilt-1.4.1
	Successfully installed rack-protection-1.5.3
	Successfully installed sinatra-1.4.5
	4 gems installed
	Installing ri documentation for rack-1.5.2...
	Installing ri documentation for tilt-1.4.1...
	Installing ri documentation for rack-protection-1.5.3...
	Installing ri documentation for sinatra-1.4.5...
	Installing RDoc documentation for rack-1.5.2...
	Installing RDoc documentation for tilt-1.4.1...
	Installing RDoc documentation for rack-protection-1.5.3...
	Installing RDoc documentation for sinatra-1.4.5...
	 ---> 97feabe5d2ed
	Removing intermediate container 6b81cb6313e5
	Successfully built 97feabe5d2ed


我们使用 `docker build` 命令并指定 `-t` 标识(flag)来标示属于 `ouruser` ，镜像名称为 `sinatra `,标签是 `v2`。

如果 `Dockerfile` 在我们当前目录下，我们可以使用 `.` 来指定 `Dockerfile`

>提示：您也可以指定 `Dockerfile` 路径


现在我们可以看到构建过程。Docker 做的第一件事是通过您的上下文进行构建，基本上是目录的内容构建。这样做是因为 Docker 进程构建镜像是实时构建的，并且是需要本地的上下文来做这些工作的。（这里上下文是指Context）

下一步，`Dockerfile` 中的每一条命令都一步一步的被执行。我们会看到每一步都会创建一个新的容器，在容器内部运行指令并且提交更改 - 就像我们之前使用的 `docker commit` 一样。当所有的指令执行完成之后，我们会得到`97feabe5d2ed ` 镜像（也帮助标记为 `ouruser/sinatra:v2`）, 然后所有中间容器会被清除。

> **注意**：
> 与存储程序驱动无关，镜像不能超过127层。这是一种全局设置，为了是从整体上来优化镜像的大小。

我们可以使用新的镜像来创建容器：

	$ docker run -t -i ouruser/sinatra:v2 /bin/bash
	root@8196968dac35:/#

>注意：这里只是简单的介绍一下如何创建镜像。我们跳过了很多您可以使用的其它指令。您会从后边的章节看到更多的指令或者您可以参考 `Dockerfile` 的详细说明和每一条指令的例子。为了帮助您编写清晰、易读、易维护的 `Dockerfile` ，我们也编写了 `Dockerfile` [最佳实践指南](../articles/dockerfile_best-practices.md)

###更多

学习更多，请查看 [ Dockerfile 教程](https://docs.docker.com/userguide/level1/)

## 在镜像上设置标签

您可以在提交更改和构建之后为镜像来添加标签(tag)。我们可以使用 `docker tag` 命令。为我们的 `ouruser/sinatra` 镜像添加一个新的标签。

	$ docker tag 5db5f8471261 ouruser/sinatra:devel

`docker tag` 需要使用镜像ID，这里是 `5db5f8471261` ,用户名称、镜像源名(repository name)和新的标签名(tag)。

	$ docker images ouruser/sinatra
	REPOSITORY          TAG     IMAGE ID      CREATED        VIRTUAL SIZE
	ouruser/sinatra     latest  5db5f8471261  11 hours ago   446.7 MB
	ouruser/sinatra     devel   5db5f8471261  11 hours ago   446.7 MB
	ouruser/sinatra     v2      5db5f8471261  11 hours ago   446.7 MB

## Image Digests

v2 或后续版本格式的镜像会有内容定位标示符叫做 `digest`。只要用于生成镜像的镜像源不更改， digests 值就是可以预料的。使用 ` --digests` 标识来列出镜像 digests 的值

	$ docker images --digests | head
	REPOSITORY                         TAG                 DIGEST                                                                     IMAGE ID            CREATED             VIRTUAL SIZE
	ouruser/sinatra                    latest              sha256:cbbf2f9a99b47fc460d422812b6a5adff7dfee951d8fa2e4a98caa0382cfbdbf    5db5f8471261        11 hours ago        446.7 MB

当我们从 v2 版本的镜像仓库来推送或者拉取镜像的时候，`pull` 和 `push` 命令包含了镜像 digests 。 您可以使用 `digests` 值来拉取镜像。

	$ docker pull ouruser/sinatra@cbbf2f9a99b47fc460d422812b6a5adff7dfee951d8fa2e4a98caa0382cfbdbf

您可以参考 digest 的 `create`、`run` 和 `rmi` 命令，以及 `Dockerfile` 中的 `FROM` 镜像。

## 向 Docker Hub 上传一个镜像

一旦您构建或创建了一个新的镜像，您可以使用 `docker push` 命令将镜像推送到 Docker Hub 。这样您就可以分享您的镜像了，镜像可以是公开的，或者您可以把镜像添加到您的私有仓库中。

	$ docker push ouruser/sinatra
	The push refers to a repository [ouruser/sinatra] (len: 1)
	Sending image list
	Pushing repository ouruser/sinatra (3 tags)
	. . .

## 从主机中移除一个镜像

您可以删除您主机上的镜像，类似于删处容器的方法，这里我们使用 `docker rmi` 命令。

让我们删除这个不需要使用的容器：`training/sinatra`。

	$ docker rmi training/sinatra
	Untagged: training/sinatra:latest
	Deleted: 5bc342fa0b91cabf65246837015197eecfa24b2213ed6a51a8974ae250fedd8d
	Deleted: ed0fffdcdae5eb2c3a55549857a8be7fc8bc4241fb19ad714364cbfd7a56b22f
	Deleted: 5c58979d73ae448df5af1d8142436d81116187a7633082650549c52c3a2418f0

> **提示**：
> 为了能够从主机上删除镜像，请确保没有基于此镜像的容器。

## 下一步

现在，我们已经看到如何在容器中构建单独的应用程序。接下来，我们要学习如何把多个 Docker 容器连接在一起构建一个完整的应用程序。

请阅读[连接容器](dockerlinks.md)
