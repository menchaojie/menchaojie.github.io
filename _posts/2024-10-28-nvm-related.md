---
layout: post
title: nvm使用
categories:
  - nvm
description: nvm使用
keywords: nvm, nodejs, npm, pnpm
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---
nvm

## 官网
https://github.com/nvm-sh/nvm
## 容易成功的方式

If you have `git` installed (requires git v1.7.10+):

1. clone this repo in the root of your user profile
    - `cd ~/` from anywhere then `git clone https://github.com/nvm-sh/nvm.git .nvm`
2. `cd ~/.nvm` and check out the latest version with `git checkout v0.40.1`或者`git chechkout v0.39.3`等
3. activate `nvm` by sourcing it from your shell: `./nvm.sh`  或者 `install.sh`

完成之后执行`source ~/.bashrc`才能生效

### nvm安装命令

```shell
# 同时完成node和npm的安装
nvm install 14.7.0
```

## pnpm的安装

完成nvm的安装之后，很容易通过nvm install 安装 node 和 npm， 

安装完npm之后，很容易进行pnpm的安装

普通命令
```json
npm install -g pnpm
```

卸载命令
```python
npm uninstall -g pnpm
```

特定版本安装
```shell
npm install -g pnpm@x.x.x
```

## 总结

nvm->node and npm ->pnpm