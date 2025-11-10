---
title: Node版本管理
published: 2024-05-14
category: "Frontend"
tags: ["node", "fnm", "开发环境"]
---

公司的项目数量从一开始的一两个, 3 年的时间翻了十倍有余, 这 3 年间 node 版本也从最初用的 14 发展到了 LTS20. 技术一直在迭代, 前端开发的基底
是 node, 随着项目质量的上升,也就必然遇到了项目的 node 版本切换的问题.

很长一段时间, 一直使用[nvm-windows](https://github.com/coreybutler/nvm-windows)来管理各个项目的 node 版本, 不过因为是手动切换 node 版本, 终
是不很方便. 而[volta](https://volta.sh/)在各种社区中了解过后因为一些问题一直没有下手.

## fnm

Fast Node Manager

> Fast and simple Node.js version manager, built in Rust

- 跨平台支持(macOS, windows, Linux)
- 单文件管理, 轻松安装, 快速启动
- 速度极快
- 可通过`.node-version`与`.nvmrc`管理

### 安装

windows 系统直接通过 winget 安装

```cmd
winget install Schniz.fnm
```

### 配置

windows 中直接将 fnm 配置写入到终端配置文件中

1. 在终端运行命令:

```cmd
notepad $profile
```

:::tip
如遇到报错找不到$profile地址, 可手动在配置文件夹中创建配置文件, 或者安装oh-my-posh执行初始化命令生成

1. 手动在`%USERPROFILE%\Documents\WindowsPowerShell`中新建文件`Microsoft.PowerShell_profile.ps1`

2. 通过oh-my-posh初始化

```cmd
// 安装oh-my-posh
winget install JanDeDobbeleer.OhMyPosh -s winget

// 创建oh-my-posh配置文件
oh-my-posh init pwsh | Invoke-Expression
```

:::

2. 将 fnm 配置加入到终端配置文件中:

```cmd
fnm env --use-on-cd | Out-String | Invoke-Expression
fnm env --corepack-enabled | Out-String | Invoke-Expression
fnm env --node-dist-mirror https://mirrors.tuna.tsinghua.edu.cn/nodejs-release/ | Out-String | Invoke-Expression
```

> 对应的配置分别为:
>
> 1.  执行 cd 命令时, 检查并切换目录下的 node 版本
> 2.  启用 corepack, 可安装 yarn 与 pnpm
> 3.  替换包管理器下载地址为国内镜像(当前为清华镜像)

### 使用

每个项目均只需要在项目根目录下添加`.node-version`文件, 并在文件中直接记录 node 版本号即可.

```cmd title='为项目指定fnm使用的node版本'
echo 20 > .node-version
```

```cmd title='写入当前node版本'
node --version > .node-version
```

:::tip[配置默认node版本]

```cmd title='配置默认node版本'
fnm default 20
```

:::
