---
layout: post
title: 一些常用的Linux命令
categories:
  - linux
description: 学一些系统相关
keywords:
  - linux
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---
记录一些常用Linux命令，备用查找

## hostname 主机名
[参考](https://www.runoob.com/linux/linux-comm-hostname.html)

主要有两点：

```bash
#1. 使用命令修改主机名，实例如下：
sudo hostnamectl set-hostname new-hostname
#2. 更新 `/etc/hosts` 文件，确保有一行将主机名映射到 127.0.0.1,或者127.0.1.1
127.0.0.1   localhost new-hostname
#应用更改
sudo systemctl restart systemd-hostnamed
```

他们之间的关系如下：

| 组件         | 角色  | 作用        |
| ---------- | --- | --------- |
| hostname   | 身份  | 机器自己叫什么   |
| /etc/hosts | 映射  | 名字对应哪个 IP |
