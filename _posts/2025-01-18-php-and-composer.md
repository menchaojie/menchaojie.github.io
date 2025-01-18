---
layout: post
title: php入门
categories:
  - php
description: php入门学习
keywords: PHP
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---
#  php包管理composer

使用国内镜像可以让我们下载软件包速度更快,下面是使用阿里云镜像的配置方法。
全局配置
```phthon
composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/
```
取消设置
```phthon
composer config-g--unset repos.packagist
```