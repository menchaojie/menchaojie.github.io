---
layout: post
title: ratarmount 实现挂载压缩文件
categories:
  - files
description: 学一些系统相关
keywords:
  - files
  - mount
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---
# 用 ratarmount 把 tar 当成文件系统（Linux  and macOS ）

在处理大型 `.tar / .tar.gz / .tar.xz` 归档时，经常只想“进去看看”，而不是完整解压。  
`ratarmount` 正是为这个需求而生的工具：**通过 FUSE 把 tar 归档挂载成一个只读文件系统**。

本文总结：

1. ratarmount 在 Linux / macOS 上的安装方式  
2. ratarmount 的工作原理  
3. 实际使用中最容易踩的坑

---

## 1. ratarmount 的安装方式
### 1.1 Linux（Ubuntu / Debian）
`ratarmount` 不在 Ubuntu 官方仓库中，**推荐使用 pip 安装**。
#### 安装依赖
```bash
sudo apt update
sudo apt install -y python3-pip fuse3
````

确认 FUSE 可用：
```bash
ls /dev/fuse
```
#### 安装 ratarmount
```bash
pip3 install ratarmount
```

验证：
```bash
ratarmount --version
```

如果命令不存在，可尝试：
```bash
ratarmount.py --version
```

#### 使用示例
```bash
mkdir ~/mnt_tar
ratarmount archive.tar.gz ~/mnt_tar
```

卸载：
```bash
fusermount -u ~/mnt_tar
#or
umount ~/mnt_tar
```

---

### 1.2 macOS

macOS 需要先安装 **macFUSE**。

#### 安装 macFUSE

```bash
brew install --cask macfuse
```

安装完成后，**必须**到：
` 系统设置 → 通用 → 登录项与扩展 →扩展→按类别→文件系统扩展`

否则 FUSE 无法工作。

#### 安装 ratarmount

安装pipx
```bash
brew install python
#pip3 install ratarmount
brew install pipx
pipx ensurepath
#confirm
pipx --version
```

安装zstd

```bash
brew install zstd
#confirm
ls $(brew --prefix zstd)/include/zstd.h

#Apple Silicon（M1/M2/M3）required
export CPPFLAGS="-I$(brew --prefix zstd)/include"
export LDFLAGS="-L$(brew --prefix zstd)/lib"
```


安装以及验证：
```bash
#insall
pipx install ratarmount \
  --pip-args="-i https://pypi.tuna.tsinghua.edu.cn/simple"
#confirm
ratarmount --version
```

#### 使用示例
```bash
mkdir ~/mnt_tar
ratarmount archive.tar.gz ~/mnt_tar
```
第一次挂载是可能需要在‘恢复模式’下开启系统扩展, 参考[开启系统扩展](https://www.52mac.com/soft/13613-1-1.html)

卸载：
```bash
umount ~/mnt_tar
```

查看挂载的情况：
```bash
mount | grep fuse
#FuseMount on /Users/moon/mnt (mac**fuse**, nodev, nosuid, synchronous, #mounted by moon)
```


---

## 2. ratarmount 的工作原理

### 2.1 tar 的本质

- tar 是**顺序归档格式**，不是随机访问文件系统
- “目录结构”只是路径字符串的逻辑效果
- `.tar.gz / .tar.xz` 本质上是**压缩后的流**

这也是传统工具（如 archivemount）容易出问题的根源。

---

### 2.2 ratarmount 的核心思路

ratarmount 的策略非常直接：

1. **完整扫描 tar 文件**
2. 建立一个 **索引（SQLite）**
    - 路径 → tar 内偏移
    - 目录 → 子项列表
3. 通过 **FUSE** 把索引后的结构暴露为文件系统
4. 文件访问时按需读取对应偏移的数据

也就是说：

> ratarmount 不是“边读边猜目录”，  
> 而是 **先理解整个归档，再提供文件系统视图**。

索引文件通常位于：
```text
~/.cache/ratarmount/
```

第一次挂载会慢一些，后续挂载同一个 tar 会非常快。

---

### 2.3 与 archivemount 的关键区别

| 对比项       | archivemount | ratarmount |
| --------- | ------------ | ---------- |
| 是否建索引     | 否            | 是          |
| tar.gz 支持 | 不稳定          | 稳定         |
| macOS tar | 易出问题         | 正常         |
| 中文文件名     | 可能异常         | 正常         |
| 目录完整性     | 可能“空目录”      | 完整         |

一句话总结：
> **archivemount 依赖运气，ratarmount 依赖索引。**

---

## 3. 使用 ratarmount 的易踩坑点

### 3.1 挂载点不能在 NTFS 上（Linux）

如果 tar 文件位于 NTFS 没问题，  
但 **挂载点必须在 ext4 / xfs 等原生 Linux 文件系统上**。

❌ 错误示例：
```bash
/media/ntfs_disk/mnt   # NTFS
```

✅ 正确示例：
```bash
~/mnt_tar               # ext4
```

原因：  
NTFS 在 Linux 上本身是 FUSE（ntfs-3g），  
**FUSE 默认禁止叠加 FUSE**。

---

### 3.2 挂载点必须对当前用户可写

常见错误信息：
```text
fusermount: user has no write access to mountpoint
```

解决：
```bash
chown $USER:$USER mnt
```

或直接在 `$HOME` 下创建挂载目录。

---

### 3.3 第一次挂载慢是正常的

第一次挂载会：
- 扫描整个 tar
- 生成索引数据库

这是 ratarmount “靠谱”的代价，不是性能问题。

---

### 3.4 不建议在 Finder / 文件管理器中频繁浏览（macOS）

- Spotlight 可能递归扫描挂载点
- 会触发大量无意义的访问

建议主要在 **终端中使用**。

---

## 4. 什么时候该用 ratarmount

适合：

- 大型 tar 包
- macOS 打包的归档（xattr / pax / 中文文件名）
- 只读分析、查看、grep、脚本处理
- 不想完整解压

不适合：

- 需要写入 / 修改归档内容
- 极端性能敏感场景
 
---

## 5. 总结

`ratarmount` 的价值在于：

>**把 tar 从“压缩包”升级成“可索引的数据结构”**

当你需要的是“像目录一样可靠地查看 tar”，  
而不是“赌工具有没有看全”，  
`ratarmount` 是当前最稳妥的选择。

