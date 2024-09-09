---
layout: post
title: AI之工作流
categories:
  - FastGTP
description: FastGPT
keywords: FastGPT
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---

核心功能： 按照需求排列功能模块

1.要对功能模块熟悉, 2.要对模块连接熟悉

## 模块简介

### 工具调用

可以调用一个或多个应用，比如HTTP请求，AI对话，知识库搜索等

优点：逻辑清晰，可以调用多个应用，组合多种功能

缺点：对本地开源模型（llama等）支持不好

### 问题分类

根据用户的历史记录，和当前问题，判断问题类型：

优点：感觉和工具调用类似，逻辑清晰，可以组合多种应用

缺点：历史信息会干扰分类，需要很强的背景知识描述，来强化逻辑判断

## 知识库搜索

调用检索功能，从知识库中查找相关内容

优点：可以对接私有知识库

缺点：私有知识库需要提前部署，需要向量模型（如m3e）的支持

  

HTTP请求

通过HTTP请求，联网搜索

优点：可以通过谷歌搜索的API， 对整个互联的知识进行搜索

缺点：免费的谷歌搜索API使用有限制，100次/天

  

  

测试记录：

  

### 组合测试1： chatgpt+工具掉调用

![](https://u70f9ugeqz.feishu.cn/space/api/box/stream/download/asynccode/?code=YWMwM2I2NzY3ZjJlOWU2ZGRmNDQ2MGJmNjUxMGM4ZGRfTWNVT3Z0R2tmN3ZCRUtWeThqNmdtWFZCUE5JOTNhajFfVG9rZW46RUNZdmI0bzIzb0JKYjd4czQ3S2NUcG9EbnhnXzE3MjU4OTY3MDY6MTcyNTkwMDMwNl9WNA)

### 组合测试2： llama3.1+问题分类

![](https://u70f9ugeqz.feishu.cn/space/api/box/stream/download/asynccode/?code=NmZmYjVhYmFmODZiZmZhZWFmN2U0NWRmMDlmZmIxNDBfRVRqYUNwYXVMMGpJYVhzUUdIQUwxbEpOZXlzSk92UGJfVG9rZW46RldxdGJ0R1NTbzlwaGh4dG1Ec2N0b2hqbmxmXzE3MjU4OTY3MDY6MTcyNTkwMDMwNl9WNA)

![](https://u70f9ugeqz.feishu.cn/space/api/box/stream/download/asynccode/?code=OTU4YjRkN2MyY2MzMDE0NzA1ZjQxNWQ2MjRhMGM5NmZfQXkyd2dONE9FRmtZazd1SnR3d2ZGUzhPNkZBMVRPRUNfVG9rZW46VzNtaWI1clVWb3hGa0t4MnJwaWNEZVhJbmpoXzE3MjU4OTY3MDY6MTcyNTkwMDMwNl9WNA)![](https://u70f9ugeqz.feishu.cn/space/api/box/stream/download/asynccode/?code=MTI0MjNiMTA3MjBkMmJkY2NjYzhkMGEzOTg1OWNiNjdfV3o3TE80NG55V2VwUWFzYzhwNEhkVU42ZUNhSnhRUTdfVG9rZW46RnNDQWI0aEoxb2VKYmh4cTNwY2N0RURObmhkXzE3MjU4OTY3MDY6MTcyNTkwMDMwNl9WNA)

“带知识库和联网的llama3.1"

"仅llama3.1"

  

### 结论：

chatgpt有比较好的适用性，对问题的解析能力也比较强

本地模型处理私有问题时，需要调试才可胜任定制化任务。