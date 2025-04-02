---
title: 在 uboot 中使用 nand 命令管理内核文件
date: 2025-04-02 21:36:54
tags:
  - Uboot
  - Linux
categories:
  - 学习 Linux 操作系统
---

## 前言

uboot 支持使用 `nand` 命令管理 NAND Flash 存储空间。通常，NAND Flash 的容量为 16MB~512MB，适合存放 uboot、Linux 内核、设备树文件甚至根文件系统。

我使用的单板采用了 MX30LF2G18AC 的 NAND Flash 芯片，容量有 2GB。

## Linux 启动方案

1. CPU 从 NAND Flash 启动 uboot。
2. uboot 从 NAND Flash 中读取内核、设备树文件到内存中。
3. uboot 启动内核，内核从 SD 卡启动根文件系统。

## 烧录内核和设备树文件

根据如上的启动方案，需要将 uboot、Linux 内核和设备树文件烧写到 NAND Flash 中。可以参照如下流程进行操作：

1. 将 uboot.bin 文件烧录到 NAND Flash 中，这一步参考使用的板卡的官方提供的文件和工具。
2. 使用串口线连接 PC 与目标设备，打开 uboot。
3. 执行 `nand info` 查看 NAND Flash 的基本信息，若存在多个 NAND Flash，可以使用 `nand device` 命令切换。
4. 在烧写之前需要提前规划好 NAND Flash 的分区，可以参考如下规划：

    ```bash
    0x000000000000-0x000004000000: "uboot"	# 64MB
    0x000004000000-0x000006000000: "kernel"	# 32MB
    0x000006000000-0x000007000000: "dtb"	# 16MB
    0x000007000000-0x000080000000: "reserved"
    ```

    实际上，0x0000-0x20000 这段空间存放的不属于 uboot 的内容，而是 flash 的识别信息。**切记不要擦除这部分空间内的数据！**
5. 参考 {% post_link loading-linux-kernel-via-tftp %} 搭建 TFTP 服务器，让 uboot 可以从 TFTP 服务器上读取 Linux 内核和设备树文件。
6. 将 Linux 内核文件烧录到指定位置：

    ```bash
    setenv kernel_loadaddr 0x03000000
    tftp ${kernel_loadaddr} /kernel.Image
    nand erase 0x4000000 0x2000000		# 从 0x4000000 开始擦除 32MB 空间
    nand write ${kernel_loadaddr} 0x4000000 0x2000000	# 将 kernel.Image 写入到 NAND 中
    ```

7. 将设备树文件烧录到指定位置：

    ```bash
    setenv fdt_loadaddr 0x02100000
    tftp ${fdt_loadaddr} /dft.dtb
    nand erase 0x6000000 0x1000000		# 从 0x6000000 开始擦除 16MB 空间
    nand write ${fdt_loadaddr} 0x6000000 0x1000000		# 将 dft.dtb 写入到 NAND 中
    ```

## 启动系统

1. 从 NAND Flash 中将 Linux 内核文件读取到内存中：

    ```bash
    setenv kernel_loadaddr 0x03000000
    nand read ${kernel_loadaddr} 0x4000000 0x2000000
    ```

2. 从 NAND Flash 中将设备树文件读取到内存中：

    ```bash
    setenv fdt_loadaddr 0x02100000
    nand read ${fdt_loadaddr} 0x6000000 0x1000000
    ```

3. 配置根文件系统，从 SD 卡启动：

    ```bash
    setenv bootargs "console=ttyS0,115200 root=/dev/mmcblk0p1 rw rootwait"
    ```

4. 引导 Linux 启动。