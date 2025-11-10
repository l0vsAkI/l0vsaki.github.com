---
title: 跨域
published: 2024-01-16
category: "Frontend"
tags: ["跨域"]
---

造成跨域问题的原因为浏览器的*同源安全策略*

> 同源: 协议, 域名, 端口 三者相同

跨域的请求浏览器能正常发出, 也能收到返回的结果, 只是**浏览器拦截了结果**

## 解决方法

1. 禁用浏览器跨域检测
2. 修改域名后缀

### 禁用浏览器跨域策略

此方法用于本地开发, 直接禁用浏览器的跨域检查
常用于*地图*, *天气*这类需要发送请求的三方库

操作方法:

1. 在系统资源管理器中新建文件夹, 如`C:\MyChromeDevUserData`
2. 创建一个浏览器应用程式的启动快捷方式
3. 在快捷方式的**目标**属性末尾, 添加启动项`--user-data-dir=C:\MyChromeDevUserData`

注意: **启动项与原本内容中由空格隔开**

完整的目标属性值如下:
`"C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe" --disable-web-security --user-data-dir=C:\MyChromeDevUserData`

### 修改域名后缀

主域下的子域名也会收到同源策略的限制, 可以通过修改`document.domain`使浏览器认为是同源的

eg: A 的地址为https://orders.example.com, B 的地址为https://example.com,
将 A 的`document.domain`修改为https://example.com使浏览器认为是同源的

To be continued...
