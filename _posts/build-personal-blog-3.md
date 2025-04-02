---
title: 搭建个人博客（三）—— 修改配置
tags:
  - Hexo
date: 2025-03-30 17:42:19
categories: 
  - 使用 Hexo 搭建个人博客
---


## 前言

> 本教程部分参考自 [Hexo博客搭建基础教程(三)](https://netlify.fomal.cc/posts/3451f874)。

阅读本章前，请先阅读前面的文章：

- {% post_link build-personal-blog-1 %}
- {% post_link build-personal-blog-2 %}

如无特殊说明，后文中提到的站点配置文件均为 `/_config.yml`，提到的主题配置文件均为 `/themes/butterfly/_config.yml`。

## 导航栏设置

**导航菜单**

修改主题配置文件，在导航菜单中增加 `Home`、`Archives`、`Tags`、`Categories` 标签：

``` yml
menu:
  Home: / || fas fa-home
  Archives: /archives/ || fas fa-archive
  Tags: /tags/ || fas fa-tags
  Categories: /categories/ || fas fa-folder-open
```

## 代码块配置

### 代码高亮主题

Butterfly 支持 5 种代码高亮样式：`darker`, `pale night`, `light`, `ocean`, `false`，以及是否打开 MAC 风格：

修改主题配置文件的 `code_blocks` 中的 `theme` 和 `macStyle` 属性：

``` yml
code_blocks:
  # Code block theme: darker / pale night / light / ocean / false
  theme: pale night
  macStyle: true
```

### 代码自动换行

修改主题配置文件的 `code_blocks` 中的 `word_wrap`：

``` yml
code_blocks:
  word_wrap: true
```

### 复制代码

修改主题配置文件的 `code_blocks` 中的 `copy` 属性，`true` 表示允许复制：

``` yml
code_blocks:
  copy: true
```

### 代码框折叠

修改主题配置文件的 `code_blocks` 中的 `shrink` 属性：

``` yml
code_blocks:
  # true: shrink the code blocks | false: expand the code blocks | none: expand code blocks and hide the button
  shrink: none
```

## 图片配置

### 网站图标

1. 将网站图标文件放到 `/source/img/` 路径下，假设图标文件命名为 `favicon.svg`。

2. 修改主题配置文件的 `favicon` 属性为 `/img/favicon.svg`。

``` yml
favicon: /img/favicon.svg
```

### 个人资料头像

修改主题配置文件 `avatar` 的 `img` 属性：

``` yml
avatar:
  img: /img/avatar.jpg
  effect: false
```

`effect` 属性为 `true` 时，头像会一直转动。

### 顶部图

如果不需要显示顶部图，修改主题配置文件的 `disable_top_img` 为 `true`：

``` yml
# Disable all banner images
disable_top_img: true
```

### 网站背景

修改主题配置文件的 `Background` 属性：

``` yml
# Website Background
# Can set it to color or image url
background: url(http://xxxxxx.com/xxx.jpg)
```

## 文章页面

### 相关文章

修改主题配置文件中 `related_post` 下的属性，可以修改推荐相关文章的数量和依据：

``` yml
# Related Articles
related_post:
  enable: false
  # Number of posts displayed
  limit: 6
  # Choose: created / updated
  date_type: created
```

### 下一篇

修改主题配置文件中 `post_pagination` 属性，可以修改推荐下一篇文章的依据：

``` yml
# Choose: 1 / 2 / false
# 1: The 'next post' will link to old post
# 2: The 'next post' will link to new post
# false: disable pagination
post_pagination: false
```

## 页脚设置

修改主题配置文件中 `footer` 下的属性，可以修改一些内容的显示开关：

``` yml
footer:
  nav:
  owner:
    enable: true
    since: 2025
  # Copyright of theme and framework
  copyright:
    enable: true
    version: true
  custom_text:
```

## 边栏设置

### 修改作者卡片链接

修改主题配置文件中 `aside`->`card_author`->`button`->`link`，修改为自己 GitHub 的主页链接：

``` yml
  card_author:
    enable: true
    description:
    button:
      enable: true
      icon: fab fa-github
      text: Follow Me
      link: https://github.com/mocha-macchiato
```

### 修改公告

修改主题配置文件中 `aside`->`card_announcement`->`content`：

``` yml
  card_announcement:
    enable: true
    content: This is my Blog
```

## 分享系统

修改主题配置文件中 `share` 下的属性，可以配置分享文章的方式：

``` yml
# --------------------------------------
# Share System
# --------------------------------------

share:
  # Choose: sharejs / addtoany
  # Leave it empty if you don't need share
  use: sharejs

  # Share.js
  # https://github.com/overtrue/share.js
  sharejs:
    sites: facebook,twitter,wechat,weibo,qq

  # AddToAny
  # https://www.addtoany.com/
  addtoany:
    item: facebook,twitter,wechat,sina_weibo,facebook_messenger,email,copy_link
```

## 全局设置

### 卜蒜子计数器

修改主题配置文件中 `busuanzi` 下的属性，可以打开和关闭网站、页面访问计数：

``` yml
# Busuanzi count for PV / UV in site
busuanzi:
  site_uv: true
  site_pv: true
  page_pv: true
```

## 美化、特效设置

**字体**

修改主题配置文件的 `font` 和 `blog_title_font` 属性：

``` yml
# Global font settings
# Don't modify the following settings unless you know how they work
font:
  global_font_size: '15px'
  code_font_size: '14px'
  font_family: 'Noto Serif SC'
  code_font_family: 'Maple Mono'

# Font settings for the site title and site subtitle
blog_title_font:
  font_link:
  font_family: 'Noto Serif SC'
```