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
## install

这里以cri-dockerd安装为例，使用了两条install的命令

```bash
install -o root -g root -m 0755 cri-dockerd /usr/local/bin/cri-dockerd
install packaging/systemd/* /etc/systemd/system
#....
#install [选项]... 源文件 目标文件
#install [选项]... 源文件... 目标目录
#install -d [选项]... 目录...
#比如,-v, 是否显示详细操作信息
```

其他可以参考：[install 命令](https://www.runoob.com/linux/linux-comm-install.html)

## cut

对于文本，根据规则，按列选取内容
```bash
cut  [-bn] [file]
cut [-c] [file]
cut [-df] [file]
```

主要用于管道中，处理其他命名，比如grep之后的内容。`-d "xxx" -f 4` 这是常见参数组合。

## git reset

命令示意：

```bash
# 1. 撤销最后一次提交，但保留代码
git reset HEAD~1

# 2. 清理不该存在的文件
rm -f *.tgz
# 或者加到 .gitignore

# 3. 重新选择性 add
git add 01_install-docker-aliyun.sh
git add .gitignore

# 4. 再 commit
git commit -m "install docker via aliyun mirror"
```

不同参数时，git reset 对不同git区域的影响

| 命令                      | HEAD | 暂存区  | 工作区      |
| ----------------------- | ---- | ---- | -------- |
| `git reset --soft`      | ✅ 移动 | ❌ 不动 | ❌ 不动     |
| `git reset --mixed`（默认） | ✅    | ✅    | ❌        |
| `git reset --hard`      | ✅    | ✅    | ✅（⚠️删内容） |

回退的指针含义

| 写法       | 含义     |
| -------- | ------ |
| `HEAD~1` | 上一个提交  |
| `HEAD~2` | 上上个提交  |
| `HEAD~3` | 往回 3 个 |
| `HEAD~0` | 还是自己   |