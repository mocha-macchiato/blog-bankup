---
title: 搭建个人博客（四）—— 高级修改配置
tags:
  - Hexo
date: 2025-03-30 20:02:04
categories: 
  - 使用 Hexo 搭建个人博客
---


## 前言

> 本教程部分参考自以下文章：
> - [博客魔改教程总结(一)](https://netlify.fomal.cc/posts/eec9786)
> - [博客魔改教程总结(四)](https://netlify.fomal.cc/posts/d739261b)
> - [Hexo博客添加自定义css和js文件](https://b.leonus.cn/2022/custom.html)

阅读本章前，请先阅读前面的文章：

- {% post_link build-personal-blog-1 %}
- {% post_link build-personal-blog-2 %}
- {% post_link build-personal-blog-3 %}

如无特殊说明，后文中提到的站点配置文件均为 `/_config.yml`，提到的主题配置文件均为 `/themes/butterfly/_config.yml`。

## 自定义字体

1. 下载字体文件，并将其存放到 `/source/font/` 路径下。

2. 创建 `/source/css/custom.css` 文件，新增如下内容：

	``` css
	@font-face {
	    font-family: 'YeZiGongChangDanDanHei';	
	    src: url(/font/Y-B008YeZiGongChangDanDanHei-2.ttf);
	    font-weight: normal;
	    font-style: normal;
	    font-display: block;
	}
	```

3. 在主题配置文件中引用 `custom.css` 文件，修改 `inject` 属性：

	``` yml
	# Inject
	# Insert the code to head (before '</head>' tag) and the bottom (before '</body>' tag)
	inject:
	  head:
	    # - <link rel="stylesheet" href="/xxx.css">
	    - <link rel="stylesheet" href="/css/custom.css">
	  bottom:
	    # - <script src="xxxx"></script>
	```

4. 修改主题配置文件的 `font` 和 `blog_title_font` 的 `font_family` 属性：

	``` yml
	# Global font settings
	# Don't modify the following settings unless you know how they work
	font:
	  global_font_size: '16px'
	  code_font_size: '14px'
	  font_family: 'YeZiGongChangDanDanHei'
	  code_font_family: 'Maple Mono'

	# Font settings for the site title and site subtitle
	blog_title_font:
	  font_link:
	  font_family: 'YeZiGongChangDanDanHei'
	```

## 博客宽屏适配

- 创建 `/source/css/custom.css` 文件，新增如下内容：

	``` css
	/* 全局宽度 */
	.layout {
	    max-width: 1400px;
	}
	
	/* 侧边卡片栏宽度 */
	.aside-content {
	    max-width: 318px;
	    min-width: 300px;
	}
	
	/* 平板尺寸自适应(不启用侧边栏宽度限制) */
	@media screen and (max-width: 900px) {
	    .aside-content {
	        max-width: none !important;
	        padding: 0 5px 0 5px;
	    }
	}
	```