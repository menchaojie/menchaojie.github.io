---
layout: post
title: MySQL导入问题及解决方法
categories:
  - MySQL
description: MySQL导入问题
keywords:
  - MySQL
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---
## 数据格式问题导致导入sql文件失败

问题描述如下：

```txt
MySQL导入数据库时，[SQL] Query water_pv3 start [ERR] 1292 - Incorrect date value: '0000-00-00' for column 'DATE_I' at row 1 [ERR] 是什么原因
```

解决方法， 在配置文件：
```bash
### 在 Linux 上：

1. **全局配置文件**（通常适用于所有 MySQL 实例）：
    
    - `/etc/my.cnf`
    - `/etc/mysql/my.cnf`
2. **本地配置文件**（适用于特定 MySQL 实例）：
    
    - `/usr/local/mysql/etc/my.cnf`
    - `/usr/local/etc/my.cnf`
    - `~/my.cnf`

### 在 Windows 上：

- `C:\ProgramData\MySQL\MySQL Server X.Y\my.ini`（其中 `X.Y` 是你的 MySQL 版本号）
- `C:\Program Files\MySQL\MySQL Server X.Y\my.ini` 或 `my.cnf`
- `C:\Windows\my.ini` 或 `my.cnf`

### 在 macOS 上：

- `/usr/local/mysql/my.cnf`
- `/etc/my.cnf`
```

中写入下面的语句即可：

```ini
[mysqld]
sql_mode = "NO_ENGINE_SUBSTITUTION"
```

注意：

1. 配置文件在不同的系统中有不同的位置，得好好找
2. `[mysqld]`中字母d可别忘记，不仍然是没用的