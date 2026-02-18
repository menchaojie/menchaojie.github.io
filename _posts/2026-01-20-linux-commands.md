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

| 写法           | 含义     |
| ------------ | ------ |
| `HEAD~1`     | 上一个提交  |
| `HEAD~2`     | 上上个提交  |
| `HEAD~3`     | 往回 3 个 |
| `HEAD~0`<br> | 还是自己   |

## git 配置文件

 Git  有两个可以影响git push的配置文件， 在使用 `git push` 时，`.git/config` 和 `~/.ssh/config` 分别承担不同职责：

- `.git/config` 决定连接的远程地址（去哪里）
- `~/.ssh/config` 决定使用哪种身份认证（用哪把钥匙）

二者通过“主机名”产生关联， 执行流程如下：

当执行：
```bash
git push
````

实际发生的步骤如下：

1. Git 读取仓库配置 `.git/config` 中的远程地址，例如：
 ```ini
 [remote "origin"]
   url = git@alias.example.com:user/repository.git
 ```

Git 从这里解析出：
- 协议：SSH
- 用户：git
- 主机名：alias.example.com
 然后 Git 调用 SSH 建立连接。
 
2. **SSH 根据主机名匹配配置**
SSH 读取 `~/.ssh/config`，查找与主机名匹配的规则：

```sh
Host alias.example.com
	HostName real-server.example.com
	IdentityFile ~/.ssh/custom_private_key
```

匹配后会执行：
- 将真实连接地址改写为 `real-server.example.com`
- 强制使用指定私钥进行认证
3. **服务器验证公钥**
SSH 使用私钥签名认证请求。   服务器验证该私钥对应的公钥是否存在于账户中：
- 匹配 → 连接成功
- 不匹配 → `Permission denied (publickey)`

4. 关键理解
- Git 只负责“连接目标地址”
- SSH 负责“身份认证方式”
- 主机名是两者之间的连接点
如果远程地址中的主机名发生变化，对应的 SSH 配置也必须匹配，否则认证策略不会生效。

## 重定向

| 符号   | 方向  | 行为              |
| ---- | --- | --------------- |
| `<`  | 输入  | 文件 → stdin      |
| `<<` | 输入  | 文本 → stdin      |
| `>`  | 输出  | stdout → 文件（覆盖） |
| `>>` | 输出  | stdout → 文件（追加） |

## 文本

扩展后的 Linux 文本三剑客对比表格，包含详细的**命令格式**和更丰富的**实用示例**：

| 工具       | 主要用途       | 命令格式                         | 工作原理           | 典型场景与示例                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| :------- | :--------- | :--------------------------- | :------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **grep** | **查找**     | `grep [选项] "模式" [文件...]`     | 按行搜索，打印匹配行     | **1. 基础查找：** `grep "error" server.log`<br>**2. 忽略大小写：** `grep -i "warning" system.log`<br>**3. 显示行号：** `grep -n "Failed" auth.log`<br>**4. 反向匹配（不含某词）：** `grep -v "DEBUG" app.log`<br>**5. 递归查找目录：** `grep -r "main(" ./src/`<br>**6. 统计匹配行数：** `grep -c "404" access.log`<br>**7. 显示匹配上下文：** `grep -A 2 -B 2 "Exception" error.log`  (前后各2行)<br>**8. 只输出匹配部分：** `grep -o "[0-9]\{1,3\}\.[0-9]\{1,3\}\.[0-9]\{1,3\}\.[0-9]\{1,3\}" log.txt` (提取所有IP)                                                                                                                                                                                                                    |
| **sed**  | **替换/编辑**  | `sed [选项] '命令' [文件...]`      | 按行读取，进行编辑操作后输出 | **1. 基础替换：** `sed 's/foo/bar/' file.txt` (替换每行第一个foo)<br>**2. 全局替换：** `sed 's/foo/bar/g' file.txt` (替换所有foo)<br>**3. 直接修改文件：** `sed -i 's/192.168.1/10.0.0/g' config.ini`<br>**4. 删除特定行：** `sed '5d' file.txt` (删除第5行)；`sed '/^$/d' file.txt` (删除空行)<br>**5. 打印指定行：** `sed -n '20,30p' file.txt` (打印20-30行)<br>**6. 行首/行尾追加：** `sed 's/^/---> /' file.txt` (行首添加)；`sed 's/$/ <---/' file.txt` (行尾添加)<br>**7. 多重编辑：** `sed -e 's/foo/bar/g' -e 's/abc/xyz/g' file.txt`<br>**8. 只打印匹配行：** `sed -n '/ERROR/p' app.log` (类似grep)                                                                                                                                            |
| **awk**  | **格式化/分析** | `awk [选项] '模式 {动作}' [文件...]` | 按行读取，按列切片，进行计算 | **1. 打印指定列：** `awk '{print $1, $4}' file.txt`<br>**2. 指定分隔符：** `awk -F: '{print $1 " 的家目录是 " $6}' /etc/passwd`<br>**3. 过滤行并计算：** `awk '$3 > 500 {sum += $3} END {print "总和:" sum}' data.txt`<br>**4. 内置变量使用：** `awk '{print NR, NF, $0}' file.txt` (显示行号、字段数、整行)<br>**5. 统计访问IP次数：** `awk '{ips[$1]++} END{for(ip in ips) print ip, ips[ip]}' access.log`<br>**6. 格式化输出：** `awk '{printf "%-10s %8d\n", $1, $5}' file.txt`<br>**7. 条件判断：** `awk '$2 ~ /^2024-10-/ {print $0}' app.log` (匹配第二列以2024-10-开头)<br>**8. 求和、平均值：** `awk '{sum+=$3} END{print "平均值:", sum/NR}' grades.txt`<br>**9. 处理多文件：** `awk 'FNR==1{print "文件:" FILENAME} {print $0}' file1.txt file2.txt` |

