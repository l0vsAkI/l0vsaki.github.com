---
title: 我的Neovim
published: 2023-04-17
category: "Frontend"
tags: ["开发环境", "开发工具"]
---

## 概述

围绕 Neovim 来配置开发环境, 通过少数的环境安装能获得最大的 vim 模式开发体验.

IDE 配置同步在 github 仓库中, 安装之后开箱即用. 新增语言支持仅需在 Mason(LSP 包管理器)
与 treesitter(语法解析器)中下载相应的语言支持即可.

## 环境依赖

1. [**Neovim**](https://neovim.io/)

使用命令行工具安装以方便更新

```cmd
winget install Neovim.Neovim
```

2. [fzf](https://github.com/junegunn/fzf) - 模糊查找工具, lazyvim 14.X后为必装以替代老旧的telescope

```
winget install fzf
```

3. [lazygit](https://github.com/jesseduffield/lazygit) - 简洁的终端 Git UI (可选), 建议安装

```cmd
winget install -e --id=JesseDuffield.lazygit
```

4. [ripgrep](https://github.com/BurntSushi/ripgrep) - telescope 插件的 live grep 依赖 (可选), 建议安装

```cmd
winget install BurntSushi.ripgrep.MSVC
```

5. [fd](https://github.com/sharkdp/fd) - 简单快速的find替代品, 用于文件查询(可选), 建议安装

```cmd
winget install sharkdp.fd
```

6. [**MinGW**](https://www.mingw-w64.org/) - 用于 treeistter 编译解析器

> MinGW 的全称是：Minimalist GNU on Windows 。它实际上是将经典的开源 C 语言 编译器 GCC 移植到了 Windows 平台下，并且包含了 Win32API ，因此可以将源代码编译为可在 Windows 中运行的可执行程序。

7. node环境, 用于 下载语法解析器, LSP服务等. node版本>=14

8. [**im-select**](https://github.com/daipeihust/im-select) - vim 中英文输入切换工具 (可选)

> 在`/nvim/environment/`文件夹中有已下载好的安装程式

## 开始配置

安装环境依赖中的**Neovim**, **MinGW**, **nvm**

> MinGW 的安装配置需要手动设置

| Label          | Value               |
| -------------- | ------------------- |
| Version        | 6.2.0(选择最新版本) |
| Architecture   | x86_64              |
| Threads        | win32               |
| Exception      | seh                 |
| Build revision | 1                   |

- 安装完成后将`{pathTo}/mingw64/bin/`路径添加到系统环境变量中

:::note
可直接将环境依赖中的 MinGW 压缩包解压, 将 bin 目录添加到系统 path 中
:::

### Step 1

在*C:\Users\{USER}\AppData\Local*路径下新建*nvim*文件夹

### Step 2

将[neovim-config](https://github.com/l0vsAkI/neovim-config)仓库 clone 到 nvim 文件夹

### Step 3

1. [使用魔法](#那一道墙)
2. 在终端中配置命令行代理
3. 进入 Neovim

```bash
nvim
```

### Step 4

等待包管理器与插件安装完毕

> 如包管理器安装失败, 退出 Neovim, 检查代理并重新进入

> 插件安装失败可退出并重进 Neovim, 或在 Lazy 包管理器面板使用`I`重新安装插件

> 插件安装立刻失败, 请根据 Lazy 中的报错信息, 删除`{pathTo}/nvim-data/lazy/`中安装失败的插件文件夹和临时下载文件

### Step 5

插件安装完毕后:
**现可直接在homepage按`x`进入<Extras>浏览自己所需要的开发语言服务安装使用, 不再需要自行安装LSP服务**
{% aside type="caution" %}
不再是必要操作

> 1. 在 Neovim 中通过`:Mason`命令安装 LSP 服务.
> 2. 于`{pathTo}/nvim/lua/plugins/cmp.lua`中配置自动补全. _PS:可选操作_ > {% /aside %}

### Step Extra

- 配置 im-select
  在 C 盘根目录新建`im-select`文件夹, 然后将 nvim 仓库中`environment`文件夹下的 im-select.exe 程式拷贝至`im-select`文件夹中

## 为什么不使用 VS code 或 WebStorm ?

VS code 与 WebStorm 的开发体验并不流畅, 包括但不限于 buffer 切换, 文件树操作,
代码折叠的反直觉操作等.

### 关于 VS code

- 插件会拖慢编辑器的启动与响应速度
- 插件之间会相互阻塞导致 buffer 无响应
- 糟糕的 vim 操作
- 层出不穷的 bug

### 关于 WebStorm

- 启动耗时过长
- 操作有粘滞感

## 写在最后

本来只是 22 年底接近春节放假的时候, 没太多的工作需要做, 便抱着好奇的探究心开始接触 Vim. 最初对 Vim 的了解还是通过键圈, 闻名遐迩的 HKKB 键盘, 号称转为程序员设计的键盘, 配合 Vim 可以极大的提高编码效率. 然后开始了磕磕绊绊的 Vim 学习.

对于 Vim 的使用总体分为了三个阶段:

### 1. 使用 VS code 和 WebStorm 的 Vim 插件

WebStorm 的 Vim 插件使用体验非常不错, 不仅操作流畅, 还整合了些 Vim 常用的插件.

而 VS code 的 Vim 体验就非常不尽人意. VS code 的 Vim 插件主要为两个:

1. [Vim](https://marketplace.visualstudio.com/items?itemName=vscodevim.vim) - 使用 VS code 自身行为模拟 Vim 操作
2. [VSCode Neovim](https://marketplace.visualstudio.com/items?itemName=asvetliakov.vscode-neovim) - 将文本内容复制到一个 buffer 中, 通过插件与 VS code 通信, buffer 编辑由 Neovim 承担

以上两个插件, 前者因为是模拟操作, 存在着大量的 bug, 且如若配置了自定义快捷键, 禁用插件后会报错以致无法正常使用.
后者有着高昂的通信成本, 会与其他插件冲突并阻塞 VS code 进程.

VS code 体验不好, WebStorm 太吃电脑配置, 因此开始了使用终端配置 Neovim.

### 2. 完全照抄别人的配置

知乎上有位大佬分享了自己的 [Neovim 配置](https://zhuanlan.zhihu.com/p/382092667), 当时没注意只有 macos 的配置流程, 原地踏步了很久却也没有进展.

后又在 B 站刷到一从零还是配置 Neovim 的视频, 跟着做终于成功配置好了视频中的 Neovim, 基本满足开发相关的配置.

### 3. 自行配置搭建最适合自己的 Neovim

抄的配置, 必定不是最适合自己的配置. 在别人的配置上折腾确实有很大的限制. 了解到了[Lazy](https://github.com/folke/lazy.nvim)这个最新的包管理器之后, 便开始迁移之前的插件与配置, 并且在这一过程中:

- 重新组织了项目结构
- 将依赖的环境放入到了 Neovim 的文件目录下
- 了解到了终端与系统不同的代理模式
- 更加灵活的配置插件的安装与加载

 
