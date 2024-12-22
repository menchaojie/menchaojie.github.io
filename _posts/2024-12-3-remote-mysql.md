---
layout: post
title: MySQL远程连接问题
categories:
  - MySQL
description: DB
keywords: 数据库
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---
## 数据库远程连接

### MySQL

参考：
https://www.wgstart.com/help/docs67.html


但是这个设置是不一定能成功的，还需要一个设置

确保 MySQL 配置允许从任何主机或特定的主机（例如，% 或 192.168.1.%）进行连接。

1. **修改 MySQL 配置文件**（my.cnf 或 my.ini），找到以下行并取消注释或修改：
    
bind-address = [0.0.0.0](http://0.0.0.0)

bind-address = <MySQL服务器的IP地址>

这将允许 MySQL 接受来自所有 IP 地址的连接。

2. **重启 MySQL 服务**：

sudo systemctl restart mysql

注意：bind-address = 0.0.0.0 在文件夹/etc/mysql/中的哪个位置具体不定，需要找

持续

总结：mysql或者mariaDB数据库要实现跨主机调用，主要有连个方面设置
1.  数据库设置host:  localhost->%
2. 系统，/etc/mysql.cnf, bind-address 从127.0.0.1->0.0.0.0