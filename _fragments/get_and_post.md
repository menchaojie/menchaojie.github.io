---
layout: fragment
title: GET 和 POST
tags: 
description: differents between them
keywords: methods between front-end and back-end
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---
### 将数据提交服务器运算并返回的两种方式

#### GET 方法

后端flask框架
```python
from flask import Flask, request, jsonify
app = Flask(__name__)
@app.route('/process', methods=['GET'])
def process_data():
	# recieve data
    try:
        a = float(request.args.get('data_a'))
        b = float(request.args.get('data_b'))
    except (TypeError, ValueError):
        return jsonify({'error': 'Invalid or missing data'}), 400
	# function used to process task
    x, y = function(a, b)
	# prepare returned data
    response = {
        'X': x,
        'Y': y,
    }
    return jsonify(response)
if __name__ == '__main__':
    app.run(host="0.0.0.0", port=xxx, debug=True)
```

测试方法，浏览器：
```txt
http://remotehost:xxx/process?data_a=3.33&data_b=4.33
```

#### POST 方法

```python
from flask import Flask, request, jsonify
app = Flask(__name__)
@app.route('/process', methods=['POST'])
def process_data():
    try:
        a = float(request.args.get('data_a'))
        b = float(request.args.get('data_b'))
    except (TypeError, ValueError):
        return jsonify({'error': 'Invalid or missing data'}), 400
	# core function
    x, y = function(a,b)
	# return data
    response = {
        'X': x,
        'Y': y, 
    }
    return jsonify(response)
if __name__ == '__main__':
    app.run(host="0.0.0.0", port=xxx, debug=True)
```

测试方法, curl

```txt
curl -X POST http://remotehost:xxx/process -H "Content-Type: application/json" -d '{"data_a": 30.931212203974912, "data_b": 121.5003097272177}'
```

### 路由

flask后端代码有路由的概念，其他语言后端是否也有呢？