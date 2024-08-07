---
layout: post
title: 试用Llamma3.1
categories:
  - AI
description: llama3.1
keywords: llama
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---
## 安装管理工具Ollama

Ollama使得本地部署llama变得轻松愉悦，但也有一些问题，首先是国内安装困难，软件包下载缓慢，中断等问题层出不穷
#### 解决github文件下载安装问题
在文件`/etc/hosts`中加入以下语句
``` python
# github 注意下面的IP地址和域名之间有一个空格
140.82.114.3 github.com
199.232.69.194 github.global.ssl.fastly.net
185.199.108.153 assets-cdn.github.com
185.199.109.153 assets-cdn.github.com
185.199.110.153 assets-cdn.github.com
185.199.111.153 assets-cdn.github.com

```
[参考博客](https://blog.csdn.net/okyanxingkui/article/details/136657627)

#### 下载并安装Ollama
1.  官网安装指令：https://ollama.com/download
 linux 安装指令为：curl -fsSL https://ollama.com/install.sh | sh
2.  查看可用模型：https://ollama.com/library
例如llama3.18B的安装指令为：ollama run llama3.1
安装完成后直接在终端进入问答的交互模式。
3. ollama的配置文件：/etc/systemd/system/ollama.service
默认情况下， ollama只允许本地访问，如果想`直接`被外网访问，可以在配置文件的[Service]
中添加 Environment="OLLAMA_HOST=0.0.0.0:11434", 然后重启
4. ollama的重启命令
```python
systemctl daemon-reload
systemctl restart ollama
```

[参考](https://zhuanlan.zhihu.com/p/686952702)

## 安装open webui

在命令行进行对话总感觉不太习惯，此时可以使用开源项目open webui将llama3.1的接口连接到web页面。

1. open webui项目地址：https://github.com/open-webui/open-webui
2.  使用docker安装比较方便，安装命令如下：

```python
docker run -d -p 3000:8080 --add-host=host.docker.internal:host-gateway -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:main
```
####  端口设置：
上述命令是官方命令，但是不一定能正常使用，原因是端口设置的问题。
要想open webui能正常调用llama3.1提供的服务，要保证以下几点：
1. ollama 启动并运行llama3.1(或其他)大模型，提供调用端口（ollama 默认11434）
2.  docker 启动open webui镜像的容器，并使得其web服务连接主机的11434端口
3. open webui 暴漏出端口，供其他用户调用
4. 将Docker网络设置成主机连接模式比较方便，实测可行代码为：
```python
sudo docker run -d \
  --network=host \
  -v /home/ubuntu/Documents/open-webui/data:/app/backend/data \
  -e HF_ENDPOINT=https://hf-mirror.com \
  -e OLLAMA_BASE_URL=http://127.0.0.1:11434 \
  -e DEFAULT_USER_ROLE=user \
  -e DEFAULT_MODELS=llama \
  -e ENABLE_IMAGE_GENERATION=True \
  --name open-webui \
  --restart always \
  ghcr.io/open-webui/open-webui:main
```

## 内网穿透
如果服务部署在内网的电脑上，则外网无法访问，可可以使用如下方式进行端口转发：
1. 如果没有可用的服务器，使用ngrok进行端口转发
2. 如果有服务器，可以使用ssh 进行端口转发，命令如下：
```python
ssh -R remote_port:127.0.0.1:local_ui_server_port(8080) name@remote_host 
```

[参考1](https://zhuanlan.zhihu.com/p/686952702)
[参考2](https://blog.csdn.net/alalaal/article/details/140529382)
