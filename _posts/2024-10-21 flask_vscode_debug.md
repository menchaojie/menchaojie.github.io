---
layout: post
title: vscode中调试flask
categories:
  - Program
  - Debug
description: 在vscode中调试flask程序测试
keywords: Program
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---
## 设置launch.json

**在 VSCode 中创建调试配置**

  

你需要在 VSCode 中设置 launch.json 文件来配置调试任务：

  

1. 打开 VSCode，点击左侧活动栏中的**调试图标**，然后点击 **“创建 launch.json 文件”**。

2. 选择 **Python** 作为环境。

3. VSCode 会为你生成一个基本的调试配置。你可以按需修改配置。

  

示例的 launch.json 文件配置：

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Flask Debug",
            "type": "python",
            "request": "launch",
            "program": "${workspaceFolder}/app.py",  // 你的 Flask 应用入口文件
            "console": "integratedTerminal",
            "env": {
                "FLASK_APP": "app.py",  // Flask 应用的文件名
                "FLASK_ENV": "development"  // 开启 Flask 的开发模式
            },
            "args": ["run", "--no-debugger", "--no-reload"],  // 关闭 Flask 自带的调试器和自动重载
            "jinja": true  // 如果使用了 Jinja 模板引擎，可以设置为 true
        }
    ]
}

```

• program：指定 Flask 应用的入口文件（通常是 app.py）。

• FLASK_APP：指定 Flask 的主应用文件名（和上面的 program 一致）。

• FLASK_ENV：设置为 "development"，启用 Flask 的开发模式。

• args：这里我们关闭 Flask 自带的调试器和自动重载（因为 VSCode 自己会处理这些功能）。

  

**3. 打断点调试**

  

配置完成后，你可以在 Flask 应用的任意代码处设置断点：

  

1. 打开任意 Flask 源文件。

2. 在代码行号旁边点击鼠标左键设置断点。

3. 在 VSCode 中点击 **“开始调试”** 按钮（绿色播放按钮），启动 Flask 应用并进入调试模式。

  

**4. 使用自动重载**

  

在调试 Flask 时，如果你希望代码变更后自动重载，VSCode 自带的调试器可以处理这个问题。即使在 launch.json 中关闭了 Flask 的自动重载，VSCode 的调试功能依然会监听文件变化，并在你保存代码后自动重启 Flask 应用。

  

**结合 VSCode 和 Flask 的调试功能**

  

• **自动重载**：VSCode 会自动检测代码的更改并重新启动 Flask 应用。

• **断点调试**：VSCode 允许你在代码中打断点，执行逐行调试，查看变量状态，甚至在运行时修改代码。

• **调试信息**：Flask 自带的调试模式依然提供详细的错误信息和 traceback，帮助你快速定位问题。


<font color="#ff0000">注：</font>
理论阶段，未经测试，仅供参考 