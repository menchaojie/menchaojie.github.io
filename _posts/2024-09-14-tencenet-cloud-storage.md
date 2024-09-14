---
layout: post
title: 腾讯云对象存储COS使用
categories:
  - COS
description: COSCLI使用
keywords: COS
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---
### COSCLI是什么

COSCLI 是腾讯云对象存储（Cloud Object Storage，COS）提供的客户端命令行工具。通过 COSCLI 工具，您可以通过简单的命令行指令对您 COS 中的对象（Object）实现批量上传、下载、删除等操作。

之所以用到这个工具，是因为需要对COS中的3D模型数据进行统计对比，而使用网页版本的COSBrowser获得文件夹下所有的文件列表不是很方便。使用命令行的方式更方便。

### 使用方法

可以分为下载，安装，配置三个部分，具体参考官网： 

https://cloud.tencent.com/document/product/436/63144

命令格式如下：
```python
./coscli ls cos://examplebucket-1250000000
```

具体可以参考

https://cloud.tencent.com/document/product/436/71763

常用命令参考：

https://cloud.tencent.com/document/product/436/63679