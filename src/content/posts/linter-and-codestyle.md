---
title: Linter 与 Code Style
published: 2023-02-27
category: "Frontend"
tags: ["代码规范"]
---

## 概述

编码风格是个很简单但是又很严肃的事情. 在代码开发的过程中应将主要的精力放在程序的编写上,
而不是将大量的时间耗费在代码格式上.

但是不注重代码风格, 会极大的影响团队的协作开发与工程的维护, 增加了后期的交流与代码阅读的成本.

由此便是引入 ESLint 和 Prettier

## Linter - 代码审查工具

当前前端方面最主流的 Linter 是 ESLint

> ESLint 是一个开源项目，可以帮助你发现并修复 JavaScript 代码中的问题。 不论你的 JavaScript 是在浏览器还是在服务器，是否使用框架，ESLint 都可以帮助你的代码变得更好。

- ESLint 是一个插件形式的代码审查工具, 有着众多框架适配的可调整配置.
- ESLint 在各个主流编辑器和部分小众编辑器中都有着良好的支持.
- JavaScript 是一种运行时语言编程语言, ESLint 可在编写代码时就提供代码审查, 提高开发效率.

## Formatter - 代码格式化工具

在项目依赖中配置使用统一的格式化工具, 可以保证相同的代码风格,
Prettier 便是其中的佼佼者,

> Prettier 是什么？
> 一个“有态度”的代码格式化工具,
> 支持大量编程语言,
> 已集成到大多数编辑器中,
> 几乎不需要设置参数.

## 安装

### [ESLint](https://zh-hans.eslint.org/)

ESLint 的配置十分的简单, 在项目根目录中执行命令:

```cmd
npm init @eslint/config
```

然后根据项目架构, 跟随 CLI 创建基础配置

```cmd
? How would you like to use ESLint? ...
  To check syntax only
  To check syntax and find problems
> To check syntax, find problems, and enforce code style
```

```cmd
? What type of modules does your project use? ...
> JavaScript modules (import/export)
  CommonJS (require/exports)
  None of these
```

```cmd
? Which framework does your project use? ...
> React
  Vue.js
  None of these
```

```cmd
? Does your project use TypeScript? » No / Yes
```

```cmd
? Where does your code run? ...  (Press <space> to select, <a> to toggle all, <i> to invert selection)
√ Browser
√ Node
```

```cmd
? How would you like to define a style for your project? ...
> Use a popular style guide
  Answer questions about your style
```

```cmd
? Which style guide do you want to follow? ...
> Standard: https://github.com/standard/eslint-config-standard-with-typescript
  XO: https://github.com/xojs/eslint-config-xo-typescript
```

```cmd
? What format do you want your config file to be in? ...
> JavaScript
  YAML
  JSON
```

```cmd
? Would you like to install them now? » No / Yes
```

```cmd
? Which package manager do you want to use? ...
> npm
  yarn
  pnpm
```

### [Prettier](https://www.prettier.cn/)

使用包管理工具安装 Prettier

```cmd [npm]
npm install --save-dev --save-exact prettier
```

```cmd [yarn]
yarn add --dev --exact prettier
```

在项目根目录创建 Prettier 配置文件

```cmd
echo {}> .prettierrc.json
```

### 编辑器

- WebStorm 已内置了 Eslint 插件, 开箱即用.
- VS Code 于插件商店中安装插件即可.
- Neovim 键入命令 `:Mason` 并安装 eslint-lsp 服务, 会自动加载项目中的 eslint 配置文件.

## 配置文件

单独的 ESLint 或 Prettier 各自都能很好的工作, 但是两个相结合会有部分冲突, 主要在于 Prettier
格式化后的代码如不匹配 ESLint 的规则, ESLint 依然会报错.

在此开始便是根据个人/公司的项目要求进行 eslint 和 prettier 的配置

```js [.eslintrc.js]
module.exports = {
  env: {
    browser: true,
    es2021: true,
  },
  extends: [
    "plugin:react/recommended",
    "prettier", // eslint-config-prettier - 关闭eslint与prettier冲突的配置
  ],
  overrides: [],
  parserOptions: {
    ecmaVersion: "latest",
    sourceType: "module",
    project: "./tsconfig.json", // TypeScript文件需要指定项目的tsconfig配置文件
  },
  plugins: ["react"],
  rules: {},
};
```

```json [.prettierrc.json]
{
  "semi": false,
  "singleQuote": true,
  "trailingComma": "none"
}
```

## eslint-config-prettier 与 eslint-prettier-plugin

eslint-config-prettier 为 eslint 的配置文件. 用途为关闭 eslint 与 prettier 冲突的配置,实际体现为将 eslint 规则中会与 prettier 格式化后报错的规则全部关闭, 然后使用 prettier 格式化代码.

eslint-prettier-plugin 是 eslint 的一个插件, 不会影响 eslint 本身的规则, 而是在已有规则的基础上额外添加规则. 插件会将 prettier 与 eslint 冲突的部分以严重错误规则的形式加入到 eslint 的配置文件中. 此插件不干扰 perttier 的任何行为. 最后以 eslint 的 --fix 指令, 通过 eslint 的修复, 而非 prettier 来实现代码的格式化工作.

To be continued ...
