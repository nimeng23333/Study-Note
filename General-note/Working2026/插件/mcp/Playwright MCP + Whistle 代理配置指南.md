---
title: Playwright MCP + Whistle 代理配置指南
---

## 概述

  

通过 **Playwright MCP** 让 Cursor AI 能够直接操控 Chrome 浏览器访问、交互和测试 Web 应用；配合 **Whistle 代理**，可以让浏览器访问本地开发服务器（localhost）或需要代理转发的环境。

  

### 核心能力

  

| 能力 | 说明 |

| ---------- | ---------------------------------------------- |

| 页面导航 | AI 直接打开 URL、前进后退 |

| 元素交互 | 点击按钮、填写表单、上传文件 |

| 页面快照 | 获取页面 DOM 结构（accessibility tree） |

| JS 执行 | 在页面中执行任意 JavaScript，读取/修改应用状态 |

| 网络请求 | 查看页面发出的所有网络请求 |

| 控制台日志 | 读取浏览器 console 输出 |

| 截图 | 对页面进行截图 |

| 代理访问 | 通过 Whistle 代理访问 localhost 或内网环境 |

  

### 典型使用场景

  

- 在 Cursor 中让 AI 直接打开 pre/test 环境页面，验证功能是否正常

- 通过 `window.stores` 等全局变量读取/修改应用运行时状态

- 调用后端 API（通过页面内 fetch 或 store 方法，自动携带认证信息）

- 批量操作数据（如清理脏数据、批量删除节点等）

- UI 自动化测试

  

---

  

## 前置条件

  

1. **Node.js** 已安装（用于 npx 运行 Playwright MCP）

2. **Whistle** 已安装并配置好代理规则

  

```bash

# 安装 whistle（如果还没有）

npm install -g whistle

  

# 或使用 Whistle 客户端（macOS GUI 版本）

```

  

---

  

## 配置步骤

  

### 1. 配置 Playwright MCP

  

https://github.com/microsoft/playwright-mcp

  

展开cursor， 点击 add to cursor 按钮

  

编辑 `~/.cursor/mcp.json`，添加 Playwright 配置：

  

```json

{

"mcpServers": {

"Playwright": {

"command": "npx",

"args": ["@playwright/mcp@latest", "--proxy-server=http://127.0.0.1:8888", "--ignore-https-errors"],

"env": {}

}

}

}

```

  

**参数说明：**

  

| 参数 | 说明 |

| -------------------------------------- | -------------------------------------------------------------------------- |

| `--proxy-server=http://127.0.0.1:8888` | 让 Chromium 的所有请求走 Whistle 代理。端口号需与 Whistle 实际监听端口一致 |

| `--ignore-https-errors` | 忽略 HTTPS 证书错误（Whistle 做 HTTPS 中间人代理时需要） |

  

### 2. 确认 Whistle 端口

  

Whistle 默认端口为 `8899`，但 Whistle 客户端（GUI 版）默认端口为 `8888`。

  

确认方式：

  

```bash

# 查看 Whistle 监听端口

lsof -i -P | grep -i whistle | grep LISTEN

```

  

如果输出类似 `TCP *:8888 (LISTEN)`，则端口为 `8888`。

  

### 3. 启动 Whistle

  

```bash

# 命令行版本

w2 start

  

# 或直接打开 Whistle 客户端（macOS GUI 版本）

```

  

### 4. 在 Cursor 中启用

  

1. 打开 **Cursor Settings → MCP**

2. 确认 Playwright server 状态为绿色（已连接）

3. 如果修改了 `mcp.json`，点击 **重启** 按钮使配置生效

  

---

  

## 使用技巧

  

### 登录态注入

  

Playwright 打开的是全新浏览器，没有登录态。通过 `addCookies` 注入 token：

  

```javascript

// AI 会执行类似这样的代码

const context = page.context();

await context.addCookies([{ name: 'usertoken', value: '<your-token>', domain: '.liblib.tv', path: '/' }]);

await page.goto('https://www-pre.liblib.tv/canvas?projectId=xxx');

```

  

> 注意：cookie 的 `domain` 要与目标网站域名匹配。如果 API 在不同域名（如 `libtv-pre.liblib.art`），需要为该域名也设置 cookie。

  

### 读取应用状态

  

本项目在 `window.stores` 上暴露了 zustand store，AI 可以直接读取：

  

```javascript

// 获取画布所有节点

const state = window.stores.canvas.getState();

console.log(state.nodes);

```

  

### 调用 Store 方法

  

Store 上暴露了完整的操作方法，调用后会自动通过 SyncQueue 同步到后端：

  

```javascript

// 批量删除节点（会自动同步到后端）

window.stores.canvas.deleteNodes(['node-id-1', 'node-id-2']);

```

  

### 查看网络请求

  

AI 可以查看页面发出的所有网络请求，用于确认 API 地址和响应状态。

  

---

  

## 常见问题

  

### Q: 代理连接失败 (ERR_PROXY_CONNECTION_FAILED)

  

**原因：** Whistle 未启动，或端口配置不匹配。

  

**解决：**

  

1. 确认 Whistle 已启动：`lsof -i -P | grep -i whistle | grep LISTEN`

2. 确认 `mcp.json` 中的端口与 Whistle 实际端口一致

  

### Q: 修改 mcp.json 后不生效

  

**解决：** 在 Cursor Settings → MCP 中重启 Playwright server。

  

### Q: HTTPS 页面证书错误

  

**解决：** 确保 `--ignore-https-errors` 参数已添加。如果仍有问题，安装 Whistle 根证书：

  

```bash

w2 ca

# 然后在系统钥匙串中信任该证书

```

  

### Q: 页面需要登录但跳转到了登录页

  

**解决：** 通过 `addCookies` 注入 usertoken（见上方"登录态注入"）。

  

### Q: File chooser 弹窗阻塞操作

  

**原因：** 某些操作触发了文件选择器，Playwright 会等待处理。

  

**解决：** 关闭浏览器重新打开（`browser_close` → `browser_navigate`），或逐个用 `browser_file_upload` 传空数组消除。