---
title: 使用 TFTP 加载 Linux 内核
date: 2025-04-02 20:29:02
tags:
  - Linux
  - Kernel
  - Uboot
categories:
  - 学习 Linux 操作系统
---

## 搭建 TFTP 服务器

**TFTP** (Trivial File Transfer Protocol) 简单文件传输协议，内存占用量很小，通常用于在网络上传输小文件。默认使用 69 端口号。

1. 安装 tftp 服务端程序：

    ```bash
    sudo apt-get install tftpd-hpa
    ```

2. 编辑 `tftpd-hpa` 的配置文件 `/etc/default/tftpd-hpa`，假设使用 `/srv/tftp` 作为 TFTP 的共享路径：

    ```conf
    # /etc/default/tftpd-hpa

    TFTP_USERNAME="tftp"
    TFTP_DIRECTORY="/srv/tftp"
    TFTP_ADDRESS=":69"
    TFTP_OPTIONS="--secure"
    ```

3. 创建 TFTP 目录，并指定所有人都可读取：

    ```bash
    mkdir -p /srv/tftp
    chmod a+r /srv/tftp
    ```
4. 重启 `tftpd-hpa`：

    ```bash
    sudo service tftpd-hpa restart
    ```

## 使用 uboot 下载 TFTP 服务器上的内核文件并加载内核

1. 将 TFTP 服务器和需要使用 uboot 启动内核的 Linux 主机放置在同一局域网中，并获取 TFTP 服务器的 IP 地址。假设 TFTP 服务器的 IP 地址为 `192.168.1.10`。
2. 将内核文件和设备树文件放置在 TFTP 服务器的 `/srv/tftp` 路径下，假设内核文件名为 `kernel.Image`，假设设备树文件文件名为 `dft.dtb`。
3. 启动 Linux 主机 uboot，将 uboot 的 IP 地址配置为与 TFTP 服务器同一网段的 IP，假设配置的 IP 地址为 `192.168.1.11`：

    ```bash
    setenv ipaddr "192.168.1.11"
    ```

4. 配置 uboot 的网关，假设网关地址为 `192.168.1.1`：

    ```bash
    setenv gatewayip "192.168.1.1"
    ```

5. 配置 uboot 的子网掩码和 TFTP 服务器 IP 地址：

    ```bash
    setenv netmask "255.255.255.0"
    setenv serverip "192.168.1.10"
    ```

6. （可选）保存环境变量，避免下次启动后需要重新配置：

    ```bash
    saveenv
    ```

7. 将设备树文件和内核文件加载到内存：

    ```bash
    setenv fdt_loadaddr 0x02100000
    setenv kernel_loadaddr 0x03000000
    tftp ${fdt_loadaddr} /dft.dtb
    tftp ${kernel_loadaddr} /kernel.Image
    ```

8. 配置好根文件系统，即可引导 Linux 启动了。