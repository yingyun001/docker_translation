# CentOS

以下版本的CentOS 支持 Docker ：

- [*CentOS 7 (64-bit)*](#installing-docker-centos-7)
- [*CentOS 6.5 (64-bit)*](#installing-docker-centos-6.5) or later

该指南可能会适用于其它的  EL6/EL7 的 Linux 发行版，譬如 Scientific Linux 。但是我们没有做过任何测试。

请注意，由于 Docker 的局限性，Docker 只能运行在64位的系统中。

## 预备知识

目前的 CentOS 项目，仅发行版本中的内核支持 Docker。如果你打算在非发行版本的内核上运行 Docker ，内核的改动可能会导致出错。

Docker 运行在 [CentOS-6.5](www.centos.org) 或更高的版本的 CentOS 上，需要内核版本是 2.6.32-431 或者更高版本 ，因为这是允许它运行的指定内核补丁版本。

## 安装

Docker 软件包已经包含在默认的 CentOS-Extras 软件源里，安装命令如下：

	$ sudo yum install docker

开始运行 [Docker daemon](#starting-the-docker-daemon)。

### 用 yum 安装

CentOS-7 中介绍了 firewalld，firewall的底层是使用iptables进行数据过滤，建立在iptables之上，这可能会与 Docker 产生冲突。

当 `firewalld` 启动或者重启的时候，将会从 iptables 中移除 `DOCKER` 的规则，从而影响了 Docker 的正常工作。

当你使用的是 Systemd 的时候， `firewalld` 会在 Docker 之前启动，但是如果你在 Docker 启动之后再启动 或者重启 `firewalld` ，你就需要重启 Docker 进程了。

### 用脚本安装

## 创建一个 Docker 组
## 启动即开启 Docker 后台程序
## 卸载
