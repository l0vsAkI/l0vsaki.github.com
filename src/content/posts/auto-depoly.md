---
title: 打包自动部署
published: 2026-01-15
category: "Frontend"
tags: ["部署"]
---

使用`node-ssh`将打包后的项目推送到服务器上部署

官网 [node-ssh](https://node-ssh.com/)

## 安装

```cmd

pnpm add -D node-ssh
```

## 配置

在项目根目录下添加`deploy.js`配置文件

```js
// deploy.js
import { NodeSSH } from "node-ssh";
const ssh = new NodeSSH();

ssh
  .connect({
    host: "x.x.x.x",
    username: "root",
    password: "xxxxxxxx",
  })
  .then(async () => {
    console.log(new Date().toISOString(), "start deploy");
    await ssh.putDirectory("./dist", "PATH_TO_DEPLOY_FOLDER", {
      recursive: true, // 是否递归上传
      concurrency: 10, // 并行上传数量
    });
    console.log(new Date().toISOString(), "deploy success");

    process.exit(0); // 执行完后退出
  });
```

## 运行

在项目根目录执行

```cmd
node ./deploy.js
```

## 打包+部署

在`package.json`中添加脚本执行

```json
// package.json
{
  "scripts": {
    "CD": "vite build && node ./deploy.js"
  }
}
```
