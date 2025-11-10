---
title: 墙
published: 2025-02-24
category: "Frontend"
tags: ["开发环境"]
---

:::tip[快速启动]

```cmd

$env:HTTP_PROXY="http://127.0.0.1:7890"
$env:HTTPS_PROXY="http://127.0.0.1:7890"
```

:::

## 为什么要使用代理？

在日常开发和使用中，网络环境的稳定性对工具的体验至关重要。例如，Neovim 是一款强大的文本编辑器，但在某些网络环境下，可能会遇到以下问题：

- **插件安装失败**：Neovim 的插件管理工具（如 `lazy` 或 `packer`）需要从 GitHub 等国外资源下载插件，如果网络环境不佳，可能会导致插件安装失败。
- **更新缓慢**：Neovim 本身或其插件的更新速度可能会受到网络限制，影响开发效率。
- **访问受限**：某些资源可能因为地理位置限制而无法访问。

为了解决这些问题，使用代理是一个有效的解决方案。代理可以帮助我们绕过网络限制，提供更稳定的网络环境。

## 在 Windows Terminal 中配置代理

为了确保 Neovim 在 Windows Terminal 中能够顺畅地访问网络，我们需要配置 Windows Terminal 使用代理。以下是两种常见的代理软件的配置方法：Clash for Windows 和 SSR。

:::note
Clash和SSR都需要开启LAN代理.
:::

### 1. Clash for Windows 配置

Clash for Windows 是一款功能强大的代理工具，支持多种代理协议。以下是配置步骤：

#### 下载和安装 Clash for Windows

1. 前往 [Clash for Windows 官方 GitHub 仓库](https://github.com/FoyterClash/Clash-for-Windows) 下载最新版本。
2. 安装完成后，打开 Clash for Windows。

#### 配置代理

1. **导入配置文件**：
   - 在 Clash for Windows 中，点击左上角的 **Profile**，选择 **Import**，导入你的代理配置文件（通常是 `.yaml` 或 `.json` 格式）。
   - 点击 **Start** 启动代理。

2. **设置系统代理**：
   - 在 Clash for Windows 的主界面，点击右上角的 **System Proxy**，确保 **Enable System Proxy** 已勾选。
   - 确保 **HTTP Port** 和 **SOCKS Port** 已设置（默认为 7890 和 7891）。

3. **配置 Windows Terminal 使用代理**：
   - 打开 Windows Terminal，进入设置（点击右上角的齿轮图标，选择 **Settings**）。
   - 在设置文件中（通常是 JSON 格式），添加以下代理配置：
     ```json
     "profiles":
     {
         "defaults":
         {
             "env":
             {
                 "http_proxy": "http://127.0.0.1:7890",
                 "https_proxy": "http://127.0.0.1:7890",
                 "all_proxy": "socks5://127.0.0.1:7891"
             }
         }
     }
     ```
   - 保存设置并重启 Windows Terminal。

> 更推荐使用单次会话配置
>
> ```cmd
> $env:HTTP_PROXY="http://127.0.0.1:7890"
> $env:HTTPS_PROXY="http://127.0.0.1:7890"
> ```

### 2. SSR 配置

SSR（ShadowsocksR）是一款轻量级的代理工具，适合简单的代理需求。以下是配置步骤：

#### 下载和安装 SSR

1. 前往 [ShadowsocksR 客户端](https://github.com/shadowsocksrr) 下载适合 Windows 的客户端。
2. 安装完成后，打开 SSR 客户端。

#### 配置代理

1. **导入配置文件**：
   - 在 SSR 客户端中，点击 **Profiles**，选择 **Import**，导入你的 SSR 配置文件（通常是 `.json` 格式）。
   - 点击 **Start** 启动代理。

2. **设置系统代理**：
   - 在 SSR 客户端的设置中，找到 **System Proxy** 选项，启用 **Enable System Proxy**。
   - 确保 **HTTP Port** 和 **SOCKS Port** 已设置（默认为 1080）。

3. **配置 Windows Terminal 使用代理**：
   - 打开 Windows Terminal，进入设置（点击右上角的齿轮图标，选择 **Settings**）。
   - 在设置文件中（通常是 JSON 格式），添加以下代理配置：
     ```json
     "profiles":
     {
         "defaults":
         {
             "env":
             {
                 "http_proxy": "http://127.0.0.1:1080",
                 "https_proxy": "http://127.0.0.1:1080",
                 "all_proxy": "socks5://127.0.0.1:1080"
             }
         }
     }
     ```
   - 保存设置并重启 Windows Terminal。

> 更推荐使用单次会话配置
>
> ```cmd
> $env:HTTP_PROXY="http://127.0.0.1:1080"
> $env:HTTPS_PROXY="http://127.0.0.1:1080"
> ```

## 验证代理是否生效

完成上述配置后，可以通过以下命令验证代理是否生效：

```bash
curl -I https://github.com
```
