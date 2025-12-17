---
layout: post
title: frp使用中proxy名称问题
categories:
  - 网络
description: 学一些网络相关
keywords:  
  - network
  - frp
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---

# frp 0.65.0 使用 Toml 配置代理名不生效的问题排查记录

## 背景

在使用 **frp 0.65.0**（frpc / frps）进行内网穿透时，遇到了一个非常迷惑的问题：

* frpc 可以正常启动
* 能成功连接 frps
* 代理端口可以正常使用
* **但无论怎么配置，server 端和 client 日志里显示的代理名始终是 `[[proxies]]`**

而不是我在配置中写的 name。

这篇文章完整记录问题现象、排查过程，以及最终的**根因和正确写法**，记录以供参考

---

## 问题现象

frps 日志中显示：

```
new proxy [[proxies]] type [tcp] success
```

frpc 本地日志显示：

```
proxy added: [[proxies]]
[[proxies]] start proxy success
```

但我在 `frpc.toml` 中明明写了：

```toml
[[proxies]]
name = "my-ssh"
```

端口能用，名字却丢了。

---

## 初始配置（无效，但不报错）

这是一开始使用的配置方式，看起来完全合理：

```toml
[common]
server_addr = "<SERVER_IP>"
server_port = 7000
authentication_method = "token"
token = "<TOKEN>"

[[proxies]]
name = "my-ssh"
type = "tcp"
local_ip = "127.0.0.1"
local_port = 22
remote_port = 2222
```

特点是：

* 不报错
* 能连上
* 代理可用
* **代理名始终显示为 `[[proxies]]`**

这一步非常具有迷惑性。

---

## 排查方向

在排查过程中，逐一排除了以下可能性：

* frps 配置问题（已确认 server 端无关）
* token / 认证问题（连接完全正常）
* frpc 是否读取了错误的配置文件路径
* toml 语法错误（frpc 未报任何解析错误）

关键证据来自 frpc 本地日志：

```
proxy added: [[proxies]]
```

这说明：

> **在 frpc 本地解析配置阶段，代理对象就已经没有 name 了**

问题不在 server，而在 frpc 对 toml 的解析路径。

---

## 真正的原因

在 **frp 0.65.0** 中，实际上存在 **两套 Toml 配置解析路径**：

1. **兼容旧写法的 Toml（类 ini 映射）**
2. **新的 schema 驱动 Toml（v2 配置模型）**

关键点在于：

* 使用 `[common]` + `server_addr` 这种写法
* 会触发 **兼容解析分支**
* 在这个分支中，`[[proxies]]` 的 `name` 字段 **不会被正确绑定**
* frpc 最终只能退化使用段名 `[[proxies]]` 作为代理名

所以：

> **不是 Toml 不支持 name，而是你用了“旧人格”的 Toml**

---

## 正确且可行的 Toml 写法（frp 0.65.0）

下面这份配置是 **在 frp 0.65.0 下实测可用的**，代理名可以正常显示：

```toml
serverAddr = "<SERVER_IP>"
serverPort = 7000

auth.method = "token"
auth.token = "<TOKEN>"

[[proxies]]
name = "my-ssh"
type = "tcp"
localIp = "127.0.0.1"
localPort = 22
remotePort = 2222
```

对应的 frpc 日志会变成：

```
proxy added: [my-ssh]
[my-ssh] start proxy success
```

frps 端也会正确显示：

```
new proxy [my-ssh] type [tcp] success
```

---

## 核心差异总结

关键不是 toml vs ini，而是：

* 是否使用 **新 schema 风格的字段名**

对比一下就很清楚：

| 旧写法（问题来源）               | 新写法（正确）       |
| ----------------------- | ------------- |
| `[common]`              | 无 common 段    |
| `server_addr`           | `serverAddr`  |
| `server_port`           | `serverPort`  |
| `authentication_method` | `auth.method` |
| `token`                 | `auth.token`  |

---

## 经验总结

1. frp 0.65.0 **并非完全向后兼容所有 Toml 写法**
2. Toml 不等于只有一种解析方式
3. 配置“能跑”不代表字段“生效”
4. 代理名异常时，一定要看 **frpc 本地日志**，不要只盯 server

如果你在 frp 0.65.x 使用 Toml，**建议直接采用 schema 风格写法**，避免隐性降级。

**注意**: 这次测试中服务端的frps.toml中使用的确是旧写法，可正常使用。
意思是：服务端用了旧写法不影响通讯，客户端用了新写法，才能正确解析proxy的name字段。
这个向下兼容是部分性质的
---

## 附注
这次问题排查有一个较深的感受。
刚开始，我觉得问题不大，直接让GPT写一段配置，结果出现名字无法显示的小问题，后来继续用GPT进行修改，结构就是越修改越错误，本想节省时间，反而浪费了大量的时间。故而，尽信GPT,不如无GPT。我们使用工具，不能依赖工具。


