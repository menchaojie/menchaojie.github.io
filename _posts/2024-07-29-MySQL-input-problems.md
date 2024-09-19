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

####  通过配置文件解决：

```txt
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

#### 通过SQL命令解决

有时候，通过配置文件结解决会出现很多问题， 比如：
1.  版本通匹配，
2. 配置文件的路径不统一，
3. 需要重新启动。
总之各种问题, 费时费力

通过sql命令解决是更为稳妥的方式：

#### 命令行中使用命令

```c
# 登录mysql数据库
mysql -u root -p

# 显示数据库
show databases；

# 选择数据库
USE xxxdatabase;

# 显示sql模式
SELECT @@sql_mode;

# 删除某种模式

SET sql_mode = REPLACE(@@sql_mode, 'NO_ENGINE_SUBSTITUTION', '');
SET sql_mode = REPLACE(@@sql_mode, 'NO_ZERO_DATE', '');

```

####  Navicat中操作更方便

点击相应表，然后建立新的查询

```c
# 删除某种模式

SET sql_mode = REPLACE(@@sql_mode, 'NO_ENGINE_SUBSTITUTION', '');
SET sql_mode = REPLACE(@@sql_mode, 'NO_ZERO_DATE', '');
```

即可

#### 数据库导入注意事项

1.  导出‘结构+数据’相当于导出所有内容
2.  导出结构部分含有view视图
3. 再导入的时候经常出错，有可能是视图建立先后顺序的问题
