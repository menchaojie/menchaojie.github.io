---
layout: post
title: windows上matlab引擎的api
categories:
  - matlab
description: 学一些matlab的使用
keywords: matlab 
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---

重点 **三件核心事情**：

1. **Windows 端口开放让其他主机访问**
2. **MATLAB 后台启动并保持运行**
3. **把 MATLAB 结果返回给 API 调用方**

我给你整理一个 **完整、清晰的操作步骤**。

---

## **1️⃣ Windows 端口对外开放**

默认 Flask 只允许本机访问，要让局域网其他电脑访问，需要做两件事：

### **1.1 Flask 绑定所有网络接口**

```python
app.run(host='0.0.0.0', port=5000)
```

* `0.0.0.0` 表示监听所有接口
* `port=5000` 可以自定义端口

### **1.2 Windows 防火墙放行端口**

1. 打开 **控制面板 → 系统和安全 → Windows Defender 防火墙 → 高级设置**
2. **入站规则 → 新建规则**

   * 规则类型：端口
   * 协议：TCP
   * 端口号：5000（你 Flask 的端口）
   * 允许连接
   * 配置文件：域/专用/公用（根据你网络情况）
   * 名称：Flask API

### **1.3 获取本机局域网 IP**

在命令行：

```cmd
ipconfig
```

找到 IPv4 地址，例如：`192.168.31.105`
然后局域网其他电脑访问：

```bash
http://192.168.31.105:5000/run_script
```

---

## **2️⃣ MATLAB 后台启动并保持运行**

### **2.1 启动 MATLAB Engine**

在 Python 脚本里：

```python
import matlab.engine

# 无 GUI 加快启动
eng = matlab.engine.start_matlab("-nodesktop -nosplash")
```

### **2.2 可选：切换工作目录**

```python
eng.cd(r'D:\Codes\matlab\fourParameterClustering', nargout=0)
```

### **2.3 可选：添加子目录到 MATLAB 路径**

```python
eng.addpath(r'D:\Codes\matlab\fourParameterClustering\codegen', nargout=0)
```

这样 MATLAB 启动一次，就可以保持后台运行，Python 多次调用都直接连过去，不用每次重启 MATLAB。

---

## **3️⃣ 传参 & 返回 MATLAB 结果**

### **3.1 脚本类型 `.m`**

假设 `FourParameterClustering_test.m` 生成了 `result` 变量：

```python
# 传递参数到 MATLAB 工作区
eng.workspace['inputFile'] = 'fengxian_shanghaizhiyu_thomas.xlsx'

# 运行脚本
eng.run('FourParameterClustering_test.m', nargout=0)

# 从 MATLAB 获取结果
result = eng.workspace['result']  # 返回 Python
print(result)
```

### **3.2 函数类型 `.m`**

如果是函数，比如 `mysum.m`：

```matlab
function y = mysum(a, b)
    y = a + b;
end
```

Python 调用：

```python
result = eng.mysum(3, 5)
print(result)  # 输出 8
```

### **3.3 JSON API 封装**

结合 Flask，就能把 MATLAB 返回结果通过 HTTP 返回：

```python
from flask import Flask, request, jsonify
import matlab.engine

eng = matlab.engine.start_matlab("-nodesktop -nosplash")
eng.cd(r'D:\Codes\matlab\fourParameterClustering', nargout=0)

app = Flask(__name__)

@app.route('/run_script', methods=['POST'])
def run_script():
    data = request.json
    eng.workspace['inputFile'] = data.get('inputFile', 'fengxian_shanghaizhiyu_thomas.xlsx')
    eng.run('FourParameterClustering_test.m', nargout=0)
    result = eng.workspace['result']
    return jsonify({'result': result})

app.run(host='0.0.0.0', port=5000)
```

这样：

* **局域网主机访问 API**
* **MATLAB 后台一次启动**
* **Python 从 MATLAB 获取结果返回 JSON**


