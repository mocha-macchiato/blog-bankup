---
title: 搭建个人博客（一）—— 搭建基础框架和部署
date: 2025-03-28 21:41:50
tags:
  - Hexo
categories: 
  - 使用 Hexo 搭建个人博客
---


## 前言

> 本教程部分参考自 [Hexo博客搭建基础教程(一)](https://netlify.fomal.cc/posts/e593433d)。

本教程记录的博客环境搭建于 WSL（Windows Subsystem for Linux，适用于 Linux 的 Windows 子系统）上，基于 Nodejs 和 Hexo。博客最终部署到 Github 的免费托管页面。

## 安装 Nodejs

1. 打开 Nodejs 历史版本下载页面（[https://nodejs.org/en/about/previous-releases](https://nodejs.org/en/about/previous-releases)），下载 v22.14.0 版本的 Nodejs，选择 x64 的 Linux 系统软件包。

2. 将 Nodejs 解压后移动到 `/usr/local/nodejs` 路径下：

	``` bash
	tar -xvf node-v22.14.0-linux-x64.tar.gz
	sudo mv node-v22.14.0-linux-x64 /usr/local/nodejs
	```

3. 将 Nodejs 的二进制可执行文件软链接到 `/usr/local/bin` 路径下：

	``` bash
	sudo ln -s /usr/local/nodejs/bin/node /usr/local/bin/node
	sudo ln -s /usr/local/nodejs/bin/npm /usr/local/bin/npm
	sudo ln -s /usr/local/nodejs/bin/npx /usr/local/bin/npx
	```

## 安装 Hexo 工具

- 使用 npm 安装 Hexo 工具： 

	``` bash
	sudo npm install hexo-cli
	```

## 创建 Hexo 项目

1. 创建一个路径，作为 Hexo 项目的路径，在这个路径初始化 Hexo 项目：

	``` bash
	mkdir blog && cd blog
	npx hexo init
	npm install
	```

2. 将博客静态页面部署到本地：

	``` bash
	npx hexo service
	```

	此时打开浏览器，在 http://localhost:4000/ 地址可以看到博客页面。

## 将博客部署到 Github 托管页面

1. 在 Github 创建仓库，仓库命名为 `<用户名>.github.io`。

2. 安装 `hexo-deployer-git`：

	``` bash
	npm install hexo-deployer-git --save
	```

3. 修改 Hexo 配置文件。Hexo 项目路径下有一个 `_config.yml` 文件，修改最后一行的配置，将 repository 修改为你自己的 github 仓库的地址：

	``` yml
	deploy:
	  type: git
	  repository: git@github.com:mocha-macchiato/mocha-macchiato.github.io.git
	  branch: main
	```

4. 执行命令将 Hexo 静态页面部署到 Github：

	``` bash
	npx hexo clean && npx hexo g && npx hexo d
	```