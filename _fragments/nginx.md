---
layout: fragment
title: nginx
tags: [nginx]
description: nginx使用时事项
keywords: nginx
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---

## nginx配置时使用的一些正则表达式

~：表示对 URI 使用 区分大小写的正则匹配。

~*：表示对 URI 使用 不区分大小写的正则匹配。

这种正则匹配方式通常用于 URL 模式的匹配，比如文件扩展名、路径中的特定部分等

如

```python
location ~* \.jpg$ {
    expires 30d;
}
```
