---
title: 搭建个人博客（二）—— 应用主题和创建页面
tags:
  - Hexo
date: 2025-03-30 16:25:59
categories: 
  - 使用 Hexo 搭建个人博客
---


## 前言

> 本教程部分参考自 [Hexo博客搭建基础教程(二)](https://netlify.fomal.cc/posts/4aa2d85f)。

阅读本章前，请先阅读前面的文章：{% post_link build-personal-blog-1 %}。

## 安装主题

- 移动到 Hexo 项目的路径，使用 git 安装 [hexo-theme-butterfly](https://github.com/jerryc127/hexo-theme-butterfly) 主题：

	``` bash
	cd blog
	git clone -b 5.4.0 https://github.com/jerryc127/hexo-theme-butterfly.git themes/butterfly
	```

## 应用主题

1. 修改 hexo 配置文件 `_config.yml`，把 `theme` 字段修改为 `butterfly`。

2. 安装 pug 和 stylus 的渲染器：

	``` bash
	npm install hexo-renderer-pug hexo-renderer-stylus --save
	```

## 创建博客页面

### 标签页

1. 创建标签页基础文件，这将会在 `/source` 路径下生成一个含有 `index.md` 的 `tags` 文件夹：

	``` bash
	npx hexo new page tags
	```

2. 修改 `/source/tags/index.md` 文件，添加 `type: "tags"`：

	``` markdown
	---
	title: tags
	date: 2025-03-30 16:10:26
	type: "tags"
	---
	```

3. 在博客文章的 Front-matter 中添加类似如下的内容，即可为文章新增标签：

	``` markdown
	tags:
	  - tag1
	  - tag2
	```

### 分类页

1. 创建分类页基础文件，这将会在 `/source` 路径下生成一个含有 `index.md` 的 `categories` 文件夹：

	``` bash
	npx hexo new page categories
	```

2. 修改 `/source/categories/index.md` 文件，添加 `type: "categories"`：

	``` markdown
	---
	title: categories
	date: 2025-03-30 16:17:55
	type: "categories"
	---
	```

3. 在博客文章的 Front-matter 中添加类似如下的内容，即可为文章新增分类：

	``` markdown
	categories: 
	  - category1
	```

## 文章置顶

可以直接在文章的 Front-matter 区域里添加 `sticky: 1` 属性来把这篇文章置顶。数值越大，置顶的优先级越高。