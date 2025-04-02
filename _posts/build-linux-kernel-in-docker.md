---
date: 2025-04-02 20:47:44
title: 使用 Docker 编译 Linux 内核
tags:
  - Linux
  - Docker
  - Kernel
categories:
  - 学习 Linux 操作系统
---

## 前言

编译一个大型项目，例如 Linux 内核、OpenWrt 等，往往需要大量的环境依赖和配置。搭建编译环境不仅复杂繁琐，而且如果要在同一个操作系统编译多个项目，可能会有环境冲突，需要来回切换环境变量或者编译器版本。使用 Docker 镜像搭建的编译环境可以打包复杂的环境依赖和配置，便于管理和使用。

## 安装 Docker

> 官方安装指南：[https://docs.docker.com/engine/install/debian/#installation-methods](https://docs.docker.com/engine/install/debian/#installation-methods)

### 使用 WSL

如果使用 WSL，直接在官网下载安装 [Docker Desktop for Windows](https://docs.docker.com/desktop/setup/install/windows-install/)

### 使用脚本安装

> 官方安装指南： [https://docs.docker.com/engine/install/debian/#install-using-the-convenience-script](https://docs.docker.com/engine/install/debian/#install-using-the-convenience-script)

``` bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh ./get-docker.sh --dry-run
```

### 使用包管理器安装（以 Debian 为例）

> 官方安装指南：[https://docs.docker.com/engine/install/debian/#install-using-the-repository](https://docs.docker.com/engine/install/debian/#install-using-the-repository)

1. 添加 Docker 的 apt 镜像源：

    ``` bash
    # Add official GPG key of Docker:
    sudo apt-get update
    sudo apt-get install ca-certificates curl
    sudo install -m 0755 -d /etc/apt/keyrings
    sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
    sudo chmod a+r /etc/apt/keyrings/docker.asc

    # Add the repository to Apt sources:
    echo \
    "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian \
    $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
    sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    sudo apt-get update
    ```
2. 安装最新版本的 Docker：

    ``` bash
    sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
    ```

### 离线安装（以 Debian 为例）

> 官方安装指南：[https://docs.docker.com/engine/install/debian/#install-from-a-package](https://docs.docker.com/engine/install/debian/#install-from-a-package)

1. 在 [Docker 发布路径](https://download.docker.com/linux/debian/dists/) 寻找与系统匹配的 Docker 版本：`<Debian version>`/`pool`/`stable`/`<Arch>`
2. 下载安装 Docker 需要的 5 个安装包文件：

    * `containerd.io_<version>_<arch>.deb`
    * `docker-ce_<version>_<arch>.deb`
    * `docker-ce-cli_<version>_<arch>.deb`
    * `docker-buildx-plugin_<version>_<arch>.deb`
    * `docker-compose-plugin_<version>_<arch>.deb`
3. 安装：

    ``` bash
    sudo dpkg -i ./containerd.io_<version>_<arch>.deb \
    ./docker-ce_<version>_<arch>.deb \
    ./docker-ce-cli_<version>_<arch>.deb \
    ./docker-buildx-plugin_<version>_<arch>.deb \
    ./docker-compose-plugin_<version>_<arch>.deb
    ```

## Docker 常用命令

> 参考 Docker cheatsheet: [https://cheatsheets.zip/docker](https://cheatsheets.zip/docker)

* 启动 Docker 守护进程：
  * 使用 service 进行服务管理的 Linux 系统：`sudo service docker start`
  * 使用 systemd 作为初始化系统的 Linux 系统：`sudo systemctl start docker`
* 查看所有镜像：`docker images`
* 查看所有容器：`docker ps -a`
* 查看所有运行中的容器：`docker ps -s`
* 启动/停止/重启容器：`docker start/stop/restart <CONTAINER ID>`

## 使用 Docker 容器作为编译环境

- 编写 `Dockerfile` 可以定制镜像，使用镜像可以创建容器，容器可以视作是镜像的实例。
- 可以在前台执行 Docker 容器并启动容器中的 `bash` 进行操作；也可以在后台执行 Docker 容器，将容器的 22 端口映射出来，使用 `ssh` 远程链接到 Docker 容器的系统中。
- 编译项目时，通常将项目挂载到 Docker 容器中。
- 首次使用容器可以使用 `docker run` 命令创建容器，如果后续还想要使用之前创建的容器，执行 `docker start <CONTAINER ID>` 命令，再次使用 `docker run` 命令将会创建新的容器。

> [sinovoip/bpi-build-linux-4.4](https://hub.docker.com/r/sinovoip/bpi-build-linux-4.4/) 是用于编译 bpi 4.4/4.9 版本的 Linux 内核源码的 Docker 镜像。

下文以 `sinovoip/bpi-build-linux-4.4` 为例，介绍两种连接 Docker 环境的方式。

### 在前台执行 Docker 并启动 bash 进行编译

``` bash
docker pull sinovoip/bpi-build-linux-4.4:ubuntu16.04
docker run --privileged -t -i sinovoip/bpi-build-linux-4.4:ubuntu16.04 /bin/bash
# -t: 申请一个虚拟终端
# -i: 保持 STDIN 开放
# /bin/bash: 在 Docker 容器内执行命令
```

### 在后台启动 Docker 并用 ssh 连接

``` bash
docker pull sinovoip/bpi-build-linux-4.4:ubuntu16.04
docker run --privileged -d -p 2222:22 -v /media:/media sinovoip/bpi-build-linux-4.4:ubuntu16.04
# --privileged: 使 Docker 容器内拥有扩展权限
# -d: 后台执行容器
# -p: 将容器的端口映射到 Host 端口 <Host 端口>:<容器端口>
# -v: 将 Host 路径挂载到容器路径 <Host 路径>:<容器路径>
# sinovoip/bpi-build-linux-4.4:ubuntu16.04: <REPOSITORY>:<TAG>

ssh root@127.0.0.1 -p 2222
# 使用 ssh 连接 Docker 环境，端口号为 2222，默认密码为 root
```