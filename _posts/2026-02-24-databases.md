---
layout: post
title: 一些常用的数据库命令
categories:
  - database 
description: 学一些数据库相关
keywords:
  - mysql
  - postgresql
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---
记录一些常用数据库命令，备用查找

## 远程连接问题

| 数据库        | 配置位置                     | 配置项 / 文件                       | 作用                                           | 示例配置 / 命令 |
|---------------|------------------------------|-------------------------------------|-----------------------------------------------|----------------|
| PostgreSQL    | 监听地址                     | `postgresql.conf` 中 `listen_addresses` | 决定 PostgreSQL 监听哪些 IP 地址             | `listen_addresses = '*'` |
| PostgreSQL    | 访问控制                     | `pg_hba.conf`                        | 决定哪些用户从哪些 IP 可以连接哪些数据库     | `host all all 117.186.6.108/32 md5` |
| MySQL / MariaDB | 监听地址                   | `my.cnf` 或 `mysqld.cnf` 中 `bind-address` | 决定 MySQL 监听哪些 IP 地址                  | `bind-address = 0.0.0.0` |
| MySQL / MariaDB | 用户权限控制               | MySQL 用户表 (`CREATE USER` / `GRANT`) | 决定哪些用户从哪些 IP 可以访问哪些数据库     | ```sql CREATE USER 'myuser'@'117.186.6.108' IDENTIFIED BY 'mypassword'; GRANT ALL PRIVILEGES ON mydb.* TO 'myuser'@'117.186.6.108'; FLUSH PRIVILEGES;``` |

