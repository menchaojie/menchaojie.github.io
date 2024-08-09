---
layout: post
title: Docker 镜像加速源
categories:
  - Docker
description: docker 镜像加速源
keywords: Docker
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---
## Docker images 下载时的加速源

```python
sudo nvim /etc/docker/daemon.json 
{
  "registry-mirrors": [
    "https://docker.registry.cyou",
    "https://docker-cf.registry.cyou",
    "https://dockercf.jsdelivr.fyi",
    "https://docker.jsdelivr.fyi",
    "https://dockertest.jsdelivr.fyi",
    "https://mirror.aliyuncs.com",
    "https://dockerproxy.com",
    "https://mirror.baidubce.com",
    "https://docker.m.daocloud.io",
    "https://docker.nju.edu.cn",
    "https://docker.mirrors.sjtug.sjtu.edu.cn",
    "https://docker.mirrors.ustc.edu.cn",
    "https://mirror.iscas.ac.cn",
    "https://docker.rainbond.cc",
  ]
}
```

并未测试，以备不时之需
[参考](https://blog.csdn.net/llc580231/article/details/139979603)

## 断点续传


```python
# path /etc/docker/daemon.json

# content
{
  "features": {"containerd-snapshotter": true}
}
```