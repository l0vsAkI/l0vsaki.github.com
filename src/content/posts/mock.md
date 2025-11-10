---
title: 请求mock
published: 2025-08-15
category: "Frontend"
tags: ["mock"]
---

使用更现代且类型完备的msw来进行请求mock

## 安装

```cmd
pnpm add msw -D

```

## 配置拦截处理器

```ts
// src/mocks/handlers.ts
import { http, HttpResponse } from "msw";

export const handlers = [
  http.get("https://api.example.com/user", () => {
    return HttpResponse.json({
      id: "abc-123",
      firstName: "John",
      lastName: "Maverick",
    });
  }),
];
```

## 配置请求处理器

```ts
// src/mocks/browser.ts
import { setupWorker } from "msw/browser";

import handlers from "./handlers";

export const worker = setupWorker(...handlers);

export const startMockWorker = () => {
  worker.start({
    onUnhandledRequest: "bypass", // 放过未在拦截列表中的请求
  });
};
```

## 使用命令创建worker.js脚本

由于需要浏览器直接使用脚本, 建议将脚本创建在public目录下

```cmd
pnpx msw init public/ --save
```

## 在项目入口调用

例如在vue中启用

```ts
// src/main.ts
import { startMockWorker } from "../mock/browser";
import.meta.env.DEV && startMockWorker();
```
