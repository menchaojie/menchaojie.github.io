---
layout: post
title: Headscale 实现异地组网记录
categories:
  - 网络
description: 学一些网络相关
keywords:  
  - network
  - headscale
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---


# 异地组网搭建与配置：Headscale + Caddy + DERP + ACLs + Headscale-UI

本文将介绍如何使用 **Headscale** 搭建异地组网（VPN）环境，详细讲解所涉及的各个模块，包括 **Headscale**、**Caddy**、**DERP**、**ACLs** 和 **Headscale-UI**，以及在搭建过程中遇到的常见问题和解决方法。

## 1. 异地组网原理简介

异地组网（VPN）是通过公共网络（如互联网）将多个地理位置分散的设备（如电脑、服务器等）通过虚拟专用网络连接在一起，从而使得它们之间可以像在同一局域网中一样进行通信。

 **Tailscale** 基于 **WireGuard** 协议，提供了一种更简便的方式来搭建跨区域的虚拟网络。Tailscale 本身提供了一些额外的功能，而 **Headscale** 是一个开源的实现，允许托管 **Tailscale** 的服务器，掌控更多的配置和隐私设置。

## 2. 各个模块

### 2.1 Headscale

**Headscale** 是一个开源的 **Tailscale** 服务器，可以让我们自己托管 **Tailscale** 的服务，而不依赖于官方的服务。它提供了类似 **Tailscale** 的功能，支持节点管理、ACL（访问控制列表）等。

#### 安装和配置

参考文档：[headscale](https://headscale.net/stable/)


1. **Docker Compose 配置文件**

   下面是 `docker-compose.yml` 中的 **Headscale** 部分配置：

   ```yaml
   version: "3.7"
   services:
     headscale:
       image: ghcr.io/headscale/headscale:latest
       container_name: headscale
       restart: unless-stopped
       command: serve
       volumes:
         - ./config/config.yaml:/etc/headscale/config.yaml:ro
         - ./config/acl.hujson:/etc/headscale/acl.hujson:ro
         - ./data:/var/lib/headscale
       network_mode: host
   ```

2. **Headscale 配置（`config.yaml`）**

   ```yaml
   server_url: https://headscale.example.com:port # port is optional
   listen_addr: 0.0.0.0:8080
   private_key_file: /etc/headscale/private.key
   derp:
     server:
       enabled: true
       ipv4: 198.51.100.1
       ipv6: 2001:db8::1
   ```

### 2.2 Caddy

**Caddy** 是一个高效的反向代理服务器，通常用于负载均衡和提供 SSL 支持。本文使用 **Caddy** 来为 **Headscale** 和 **Headscale-UI** 提供 HTTPS 服务。

#### Caddy 配置

1. **Caddyfile 配置：**

   ```text
   headscale.example.com {
       encode gzip
       reverse_proxy /web* 127.0.0.1:8082
       reverse_proxy * 127.0.0.1:8081
   }
   ```

2. **Caddy 服务在 Docker Compose 中的配置：**

   ```yaml
   caddy:
     image: caddy:latest
     container_name: headscale-caddy
     restart: unless-stopped
     volumes:
       - ./caddy/Caddyfile:/etc/caddy/Caddyfile
       - ./data/caddy:/data
       - ./config/caddy:/config
     depends_on:
       - headscale
     network_mode: host
   ```
   但是caddy设置https TLS时候，有时候要根据不同dns提供商安装不同插件，对于使用容器化部署的方式，需要重新编译源文件以及构建新的惊醒，
   以腾讯云为例，需要先试用caddy builder 编译新的caddy程序， 再打包进新的镜像中。

   对应命令分别为：

   ```bash

   docker run --rm -it \
      -e GOPROXY=https://goproxy.cn,direct \
      -v $PWD:/build \
      -w /build \
      caddy:builder \
      xcaddy build \
        --with github.com/caddy-dns/tencentcloud
   ```

   ```bash
   docker build -t caddy-tencentdns ./caddy
   ```

 其中`./caddy`的目录结构大为：
 ```bash
   caddy/
      ├── caddy
      ├── Caddyfile
      └── Dockerfile
 ```


### 2.3 DERP

**DERP** 是一种用于穿透 NAT（网络地址转换）和防火墙的中继协议，用于 **Tailscale** 在无法直接建立对等连接时进行中继。可以通过启用 **Headscale** 内建的 **DERP** 服务器来简化连接配置。

#### DERP 配置

参考：[DERP](https://headscale.net/stable/ref/derp/)

在 `config.yaml` 中启用 **DERP** 服务：

```yaml
derp:
  server:
    enabled: true

    # Pick a unique ID > 900 to avoid conflicts with Tailscale
    region_id: 900
    region_code: "xxxxx"
    region_name: "xxxyyyyxxxx"

    # STUN (UDP)
    stun_listen_addr: "0.0.0.0:3478"

    # DERP relay (UDP)
    derp_listen_addr: "0.0.0.0:443"

    # Public IP of this server (VERY IMPORTANT)
    ipv4: xx.xx.xx.xx
    # ipv6: <leave commented unless you really have one>

    # REQUIRED — this is what you are missing
    private_key_path: /var/lib/headscale/derp.key

  # Disable public Tailscale DERP map
  urls: []
  paths: []
  auto_update_enabled: false
```

DERP 服务会随着 **Headscale** 一起启动，不需要单独启动。通过下面的命令确认 **Headscale** 是否已经启用 DERP：

```bash
docker exec headscale headscale serve
```

### 2.4 ACLs（访问控制列表）

参考： [acls 配置](https://headscale.net/0.27.1/ref/acls/)

要有两个地方的配合，

1. headscale的配置文件config.yaml中

```bash
policy:
  mode: file
  path: /etc/headscale/acl.hujson
```

2. docker-compose.yaml文件中

```bash
volumes:
      - ./config/acl.hujson:/etc/headscale/acl.hujson:ro
```

**ACLs** 用于管理网络中的访问权限。通过配置 **Headscale** 中的 **ACLs** 文件，可以控制哪些节点可以访问哪些服务。

#### ACL 配置（`acl.hujson`）

```json
{
  "groups": {
    "group:moon": ["xxx@local"],
  },
  "tagOwners": {
    "tag:client": ["user:xxx@local"],
    "tag:server": ["user:xxx@local"]
  },

  "acls": [
    {
      "action": "accept",
      "src": ["tag:client"],
      "dst": ["tag:server:*"]
    },
    {
      "action": "accept",
      "src": ["tag:server"],
      "dst": ["tag:server:*"]
    }
  ]
}

```

### 2.5 Headscale-UI

**Headscale-UI** 提供了一个用于管理 **Headscale** 节点和配置的图形用户界面。它通过 **Caddy** 进行反向代理，便于用户通过浏览器管理 **Headscale** 网络。

#### Headscale-UI 配置

1. **Docker Compose 配置：**

   ```yaml
   headscale-ui:
     image: ghcr.io/gurucomputing/headscale-ui:latest
     container_name: headscale-ui
     restart: unless-stopped
     environment:
       - HEADSCALE_URL=https://headscale.example.com:port
       - HEADSCALE_API_KEY=your-headscale-api-key
     ports:
       - "xxxx:8080"
   ```

2. **访问 Headscale-UI：**


   通过浏览器访问 `https://headscale.example.com:port/web` 即可进入 **Headscale-UI**。

参考： [headscale-ui](https://github.com/gurucomputing/headscale-ui)

这里需要注意的是, 将 `ports: - "xxxx:8080"` 映射出的端口挂在headscale实例网址的 /web下，

即，如果headscale的网址 `https://headscale.example.com:port` 那要将 `ports: - "xxxx:8080"` 通过caddy转发为： `https://headscale.example.com:port/web` ,  对应的caddy设置为：

```bash
 https://headscale.example.com:port {

	reverse_proxy /web* http://headscale-ui:8080
	reverse_proxy * http://headscale:8080

  #other settings
}

```


---

## 3. 易错部分

在搭建过程中，我们可能会遇到一些常见的错误，以下是一些易错部分及解决方法：

### 3.1 DERP 配置错误

* **错误症状**：无法成功连接节点，或者 DERP 服务器没有生效。
* **解决方法**：检查 **Headscale** 配置文件中的 `derp.server.enabled` 是否被正确启用，且 `ipv4` 地址是否正确配置。确保容器内存储的路径可访问并且能够创建私钥。

### 3.2 Caddy 配置错误

* **错误症状**：无法通过 HTTPS 访问 **Headscale-UI** 或 **Headscale**。
* **解决方法**：确保 **Caddyfile** 配置正确，尤其是反向代理路径和端口映射。检查 `reverse_proxy` 设置，确保指向正确的内部容器地址和端口。

### 3.3 ACL 配置不当

* **错误症状**：访问控制不生效，节点间无法通信。
* **解决方法**：检查 **ACLs** 文件中的配置，确保 `src` 和 `dst` 地址设置正确。验证标签是否正确配置和分配。

### 3.4 网络冲突

* **错误症状**：端口冲突导致无法启动服务。
* **解决方法**：检查是否有其他服务占用了 8080、8443 等端口，避免和系统中其他应用冲突。
