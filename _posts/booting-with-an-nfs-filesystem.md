---
title: 从 NFS 启动根文件系统
date: 2025-04-02 21:26:53
tags:
  - Uboot
  - Linux
  - Rootfs
categories:
  - 学习 Linux 操作系统
---

## 搭建 NFS 服务器

**NFS** (Network File System) 网络文件系统，是一种分布式文件系统，力求客户端主机可以访问服务器端文件，并且其过程与访问本地存储时一样。

**RPC** (Remote Procedure Call) 远端程序调用，允许像调用本地服务一样调用远程服务。

1. 安装 NFS 服务端程序及其依赖，因为 NFS 依赖 RPC 服务，因此需要安装 `rpcbind`：

    ```bash
    sudo apt-get install rpcbind nfs-utils
    ```

2. 编辑 NFS 服务端配置文件 `/etc/exports`，假设使用 `/srv/nfs` 作为共享路径：

    ```conf
    /srv/nfs *(rw,sync,no_root_squash,no_subtree_check)
    RPCSVCGSSDOPTS="--nfs-version 2,3,4 --debug --syslog"
    ```

3. 启动 NFS 服务：

    ```bash
    sudo service rpcbind start
    sudo service nfs-utils start
    ```

## 使用 uboot 从 NFS 启动 Linux 根文件系统

1. 将 NFS 服务器和需要使用 uboot 启动根文件系统的 Linux 主机放置在同一局域网中，并获取 NFS 服务器的 IP 地址。假设 NFS 服务器的 IP 地址为 `192.168.1.10`。
2. 将根文件系统目录放置在 NFS 服务器的 `/srv/nfs` 路径下，假设根文件系统目录名为 `rootfs/`。
3. 启动 Linux 主机 uboot，配置 `bootargs`：

    ```bash
    setenv bootargs "console=ttyS0,115200 root=/dev/nfs rw rootwait nfsroot=192.168.1.10:/srv/nfs/rootfs,proto=tcp,vers=4 ip=dhcp"
    ```

    需要内核支持 DHCP 服务才能使用上述配置，内核不支持 DHCP 请按照如下方式配置 `bootargs`，假设配置 Linux IP 地址为 `192.168.1.11`，网关为 `192.168.1.1`：

    ```bash
    setenv bootargs "console=ttyS0,115200 root=/dev/nfs rw rootwait nfsroot=192.168.1.10:/srv/nfs/rootfs,proto=tcp,vers=4 ip=192.168.1.11:192.168.1.10:192.168.1.1:255.255.255.0::eth0:off"
    ```

4. （可选）保存环境变量，避免下次启动后需要重新配置：

    ```bash
    saveenv
    ```

5. 配置完成后，启动内核即可。