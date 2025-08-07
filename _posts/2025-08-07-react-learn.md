---
layout: post
title: 学一点react 
categories:
  - react
  - node
description: 学一些前端的知识
keywords: react 
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---
# node 安装依赖

有时候使用npm安装很慢，这时候使用yarn替代npm是一个不错的选择

```bash
npm install -g yarn
yarn config set registry https://registry.npmmirror.com
yarn install
```

## npm、yarn、pnpm 安装依赖的优缺点及启动方式

### 1. npm

**优点：**
- 官方自带，安装 Node.js 时自动包含，无需额外安装。
- 社区庞大，文档丰富，兼容性好。
- 支持 package-lock.json，保证依赖一致性。

**缺点：**
- 安装速度较慢，尤其是依赖较多时。
- node_modules 体积较大，重复依赖多。
- 早期版本有安全和性能问题（新版本已优化）。

**安装依赖命令：**
npm install

**启动项目命令：**
npm run start 或 npm start

---

### 2. yarn

**优点：**
- 安装速度快，依赖管理高效。
- 支持离线安装（缓存依赖）。
- 更好的依赖一致性（yarn.lock）。
- 输出信息更友好。

**缺点：**
- 需要单独安装。
- 某些新特性更新略慢于npm。

**安装依赖命令：**
yarn install

**启动项目命令：**
yarn start

---

### 3. pnpm

**优点：**
- 安装速度极快，硬盘占用小（依赖去重，符号链接）。
- 支持 monorepo 项目管理。
- 依赖隔离性好，防止包污染。

**缺点：**
- 需要单独安装。
- 某些老项目兼容性需注意。

**安装依赖命令：**
pnpm install

**启动项目命令：**
pnpm start

---

> 总结：  
> - npm 适合初学者和追求稳定的项目  
> - yarn 适合追求速度和体验的开发者  
> - pnpm 适合大型项目和对磁盘空间敏感的场景






# 时光如逝

上次写东西已经将近三个月的时间了

没有沉淀，没有静思的日子过的很不踏实

看似忙碌，实则内心空虚。每天被琐事填满，却很少有真正属于自己的时间。

偶尔停下来，才发现生活需要更多的热情和目标。

希望接下来的日子，能慢下来，认真感受生活的美好，找到属于自己的节奏。


