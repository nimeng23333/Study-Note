---
title: next.config.js配置
---
> 文件：`next.config.js` 适用范围：Next.js 应用（自定义 CDN 静态资源前缀、请求转发、SPA 多入口、资源构建规则、部署优化、兼容 Node-only 依赖）

---

1. ## 文档目标与背景
    

`next.config.js` 是 Next.js 项目的核心配置入口，负责在**构建阶段**与**运行阶段**对以下内容进行统一控制：

- 静态资源加载路径（CDN 化）
    
- 路由跳转（Redirect）与路由重写（Rewrite）
    
- API 请求的同域转发（反向代理形态）
    
- Webpack 构建规则（图片、SVG、CSS 字体图标、Node-only 依赖处理）
    
- 构建产物输出形态（standalone）
    
- 构建容错策略（TypeScript / ESLint）
    

本文档提供对该配置的逐项解释、运行机制说明、风险点与运维排查清单

---

2. ## 配置文件整体结构概览
    

配置文件包含四类逻辑：

1. **环境判定与业务配置加载**
    
2. **Next.js 核心配置项（assetPrefix、output、images、pageExtensions 等）**
    
3. **路由规则（redirects、rewrites）**
    
4. **webpack 自定义（loader、plugin、fallback、externals 等）**
    

---

3. ## 环境判定与业务配置来源
    

### 3.1 环境判定

```JavaScript
const isLocal = process.env.NODE_ENV === 'development';

let nodeEnv = 'prod';
if (process.env.NODE_ENV === 'development') {
  nodeEnv = 'dev';
}
```

- `process.env.NODE_ENV === 'development'`：本地开发（通常对应 `next dev`）
    
- 非 `development`：构建与生产运行（通常对应 `next build` / `next start`）
    

> 说明：`isLocal` 与 `nodeEnv` 表达同一维度的环境判断，分别用于不同配置片段的条件分支。

### 3.2 业务配置来源（monorepo 配置包）

`const { config } = require('@liblibaix/config');`

该配置包作为统一环境配置入口，通常提供如下字段（以本配置中使用到的为准）：

- `config.staticCDNHost`：静态资源 CDN 环境路径标识（例如 test/prod/gray 等）
    
- `config.apiHost`：开发环境对外 API 域名（公网可访问）
    
- `config.internalApiHost`：生产环境内部 API 域名（内网/集群可访问）
    

---

4. ## CDN 静态资源策略（assetPrefix）
    

### 4.1 配置定义

```JavaScript
const cdnUrl = isLocal ? '/' : `https://liblibai-web-static.liblib.cloud/${config.staticCDNHost}/static/`;

const nextConfig = {assetPrefix: cdnUrl,
  ...
};
```

### 4.2 设计目的

`assetPrefix` 用于控制 Next.js 生成的页面中，各类静态资源引用 URL 的统一前缀，实现：

- 生产环境静态资源从 CDN 域名加载（提升访问速度与缓存命中）
    
- 开发环境静态资源从本地服务加载（提升开发效率与一致性）
    

### 4.3 生效范围（典型项）

`assetPrefix` 通常影响以下资源的生成 URL（具体取决于 Next 版本与构建形态）：

- `/_next/static/chunks/*`（客户端 JS chunks）
    
- `/_next/static/css/*`（CSS 文件）
    
- `/_next/static/runtime/*`（runtime）
    
- 以及 webpack 运行时动态加载资源所依赖的基础路径（间接影响）
    

### 4.4 URL 形态示例（概念级）

- 默认（未设置 assetPrefix）：
    
    - `https://<site-domain>/_next/static/chunks/main-xxx.js`
        
- 设置为 CDN 前缀后：
    
    - `https://liblibai-web-static.liblib.cloud/<staticCDNHost>/static/_next/static/chunks/main-xxx.js`
        

### 4.5 风险提示（与自定义图片 publicPath 的耦合）

该配置同时自定义了图片输出与 `publicPath`（见第 8.5 节），当 `assetPrefix` 末尾包含 `/static/` 且图片输出目录同样包含 `static/` 片段时，可能出现 URL 中重复 `static/static` 的现象。该问题属于**CDN 目录结构与构建输出目录的契合度问题**，需要在部署约定中明确目录映射（详见第 11 节排查清单）。

---

5. ## ESLint / TypeScript 构建策略
    

```JavaScript
eslint: { ignoreDuringBuilds: true },
typescript: { ignoreBuildErrors: true },
```

### 5.1 行为定义

- 构建阶段忽略 ESLint 规则报错
    
- 构建阶段忽略 TypeScript 类型报错
    

### 5.2 设计目的

- 在历史遗留/重构期降低构建阻塞概率
    
- 在发布流程中优先保证产物可生成
    

### 5.3 风险与治理建议

- 类型错误与规范问题可能延迟至运行时暴露，增加线上风险
    
- 建议在 CI 层补齐质量门禁：
    
    - 独立执行 `tsc --noEmit`
        
    - 独立执行 `eslint`（或 lint-staged + CI lint）
        

---

6. ## Next Images 配置（禁用静态图片导入）
    

`images: { disableStaticImages: true }`

### 6.1 行为定义

禁用 Next 内建的“静态图片 import”能力（例如 `import img from './a.png'` 的默认行为）。

### 6.2 设计目的

- 统一交由 webpack loader 管理图片输出路径、hash、CDN 前缀策略
    
- 避免 Next 内建图片处理管线与自定义 loader 产生冲突或路径不一致
    

---

7. ## 页面入口约束（pageExtensions）
    

`pageExtensions: ['page.tsx']`

### 7.1 行为定义

仅将以 `.page.tsx` 结尾的文件识别为页面入口文件。

### 7.2 设计目的

- 强约束页面文件命名规范，降低页面误注册风险
    
- 适用于大型仓库（组件与页面文件数量多）场景
    
- 有利于目录治理与路由可维护性
    

---

8. ## 构建输出形态（output: standalone）
    

`output: 'standalone'`

### 8.1 行为定义

启用 Next.js standalone 输出模式，生成更易部署的服务端运行包。

### 8.2 设计目的与收益

- 输出包含最小化依赖集合的服务端包（由 trace 得到）
    
- 更适合容器化与镜像瘦身
    
- 部署时不必携带完整的 `node_modules`（视部署方式而定）
    

---

9. ## 路由规则：Redirects（外部跳转）
    

### 9.1 配置定义

```JavaScript
redirects: async () => [
  {source: '/activity/2',destination: 'https://www.liblib.art/activities/....',permanent: true,
  },
];
```

### 9.2 行为与语义

- `permanent: true` → 返回 301 永久重定向
    
- 浏览器地址栏变更为 `destination`
    
- SEO 与浏览器缓存会长期记忆此跳转
    

### 9.3 使用场景

- 老路径迁移新路径
    
- 活动页统一入口
    
- 外部站点跳转与 SEO 规范化
    

---

10. ## 路由规则：Rewrites（内部转发与 SPA 多入口）
    

### 10.1 API 代理转发

#### 10.1.1 配置定义

```JavaScript
{source: '/api/www/:path*',destination: `${nodeEnv === 'dev' ? config.apiHost : config.internalApiHost}/api/www/:path*`,
},
{source: '/gateway/:path*',destination: `${nodeEnv === 'dev' ? config.apiHost : config.internalApiHost}/gateway/:path*`,
},
```

#### 10.1.2 设计目的

- 将浏览器对同域 `/api/www/*`、`/gateway/*` 的请求转发至后端真实域名
    
- 统一前端请求写法，避免环境差异渗透到业务代码
    
- 规避 CORS，减少前端直连后端域名的依赖
    

#### 10.1.3 请求链路（概念图）

```JavaScript
Client Browser
   │  GET/POST https://<site>/api/www/xxx▼
Next Server (rewrite handler)│  Forward to:│   - dev:  config.apiHost/api/www/xxx│   - prod: config.internalApiHost/api/www/xxx▼
Backend Service
   ▼
Next Server (response passthrough)▼
Client Browser
```

#### 10.1.4 关键注意点

- 转发发生在 Next 服务端，服务端将承担代理流量
    
- 认证策略（Cookie/Token/header）需要与后端契合：
    
    - 例如后端依赖 Cookie 时需要关注同域 cookie 的策略与转发透传
        
    - 例如后端依赖 Authorization header 时需要确保前端按同域写法发送并被转发
        

---

### 10.2 SPA 多入口路径重写到 `/`

#### 10.2.1 配置定义

```JavaScript
{ source: '/inspiration', destination: '/' },
{ source: '/workflows', destination: '/' },
{ source: '/image-model', destination: '/' },
{ source: '/video-effect', destination: '/' },
{ source: '/image-template', destination: '/' },
```

#### 10.2.2 行为说明

- 请求路径保持不变（地址栏仍为 `/workflows` 等）
    
- 实际返回 `/` 路由对应的页面内容（HTML/数据）
    
- 前端应用根据 `pathname` 决定展示不同模块（Tab、子页面、状态）
    

#### 10.2.3 设计目的

- 实现“多入口 URL + 单页面壳”的访问方式
    
- 支持分享链接、可读性路径、SEO/可运营路径管理
    
- 统一页面壳与渲染逻辑，减少重复页面构建
    

---

### 10.3 rewrites 阶段：beforeFiles

`return { beforeFiles: [ ... ] }`

#### 10.3.1 含义

将 rewrite 规则放在 `beforeFiles` 阶段，意味着在 Next 进行页面文件路由匹配之前优先执行。

#### 10.3.2 目的

- API 代理与 SPA 重写路径必须具备高优先级
    
- 避免同路径页面文件或静态资源路径与 rewrite 发生冲突
    

---

11. ## Webpack 自定义配置（核心工程化部分）
    

### 11.1 开发 sourcemap

```JavaScript
if (dev) {
  config.devtool = 'cheap-module-source-map';
}
```

#### 目的

- 在开发环境提升调试体验（可定位源码）
    
- 相对平衡构建速度与 sourcemap 质量
    

---

### 11.2 浏览器端 Node 模块 fallback

```JavaScript
if (!isServer) {
  config.resolve.fallback = {fs: false,path: false,module: false,canvas: false,
  };
}
```

#### 11.2.1 背景

部分第三方包包含 Node 内置模块依赖（如 `fs`/`path`），客户端 bundle 环境不存在这些模块，可能导致构建失败或运行时报错。

#### 11.2.2 行为

- 在客户端构建中，将这些模块标记为不可用（不 polyfill）
    
- 避免 webpack 尝试注入 polyfill 或报错
    

---

### 11.3 CSS 字体图标乱码修复（插件）

`config.plugins.push(new CssUtf8Plugin(), new CssContentUnicodePlugin());`

#### 11.3.1 典型问题

字体图标常通过 CSS 伪元素 `content` 输出 unicode 字符（如 `\e600`）。在压缩/转码/处理链路中，若转义被破坏或编码不一致，可能导致：

- `content` 输出字符异常
    
- iconfont 显示为方块或丢失
    

#### 11.3.2 修复策略

- 通过自定义插件保证 CSS 输出编码与 `content` unicode 转义稳定
    
- 具体逻辑取决于 `./webpack.plugin` 内实现
    

---

### 11.4 SVG 双通道处理：React 组件模式 vs 资源 URL 模式

#### 11.4.1 React 组件模式（SVGR）

```JavaScript
{test: /\.svg$/i,issuer: /\.[jt]sx?$/,resourceQuery: /react/,use: ['@svgr/webpack'],
}
```

- 匹配 `import xxx from './a.svg?react'`
    
- 输出为 React 组件（支持 `className`、props、动态样式）
    

#### 11.4.2 资源模式（url-loader/file-loader）

`{test: /\.(jpg|gif|png|svg)$/i,resourceQuery: { not: /react/ },use: [ { loader: 'url-loader', options: { ... } } ],` `}`

- 匹配普通图片与普通 svg（不带 `?react`）
    
- 小图内联，超限输出文件
    

#### 11.4.3 设计收益

- 同一 SVG 可按用途选择：组件化渲染或静态资源引用
    
- 明确约定，减少歧义与冲突
    

---

### 11.5 图片输出目录、hash 命名与 publicPath

```JavaScript
const imageOutputPath = 'static/images/';

options: {emitFile: true,esModule: false,fallback: 'file-loader',limit: 4096,name: '[name].[hash].[ext]',outputPath: imageOutputPath,publicPath: config.output.publicPath + imageOutputPath,
}
```

#### 11.5.1 关键参数含义

- `limit: 4096`
    
    - 小于 4KB → base64 内联
        
    - 大于等于 4KB → 输出文件
        
- `name: '[name].[hash].[ext]'`
    
    - 通过内容 hash 进行强缓存失效控制
        
- `outputPath: 'static/images/'`
    
    - 物理输出目录（构建产物中图片放置位置）
        
- `publicPath: config.output.publicPath + 'static/images/'`
    
    - 资源访问 URL 前缀（决定浏览器请求图片的路径）
        

#### 11.5.2 与 CDN 的关系

- `config.output.publicPath` 通常由 Next/webpack 根据运行模式与 `assetPrefix` 推导
    
- 该 publicPath 决定图片最终是走本域还是走 CDN（以及路径层级）
    

---

### 11.6 canvas 依赖处理（externals + fallback）

`config.externals = [...(config.externals || []), { canvas: 'canvas' }];`

与客户端：

`canvas: false`

#### 11.6.1 背景

`canvas` 常指 Node 侧 native 模块（例如 node-canvas），用于服务端图像处理。该模块通常：

- 无法在浏览器端打包运行
    
- 需要 native 编译环境，容易导致构建失败或安装困难
    

#### 11.6.2 行为与目的

- `externals`：将 `canvas` 作为外部依赖，不被 webpack 打入 bundle
    
- `fallback.canvas = false`：客户端构建中不尝试解析/注入 canvas
    

目标是避免 `canvas` 依赖影响前端构建与浏览器运行时。

---

12. ## 运行行为总结（按关注点）
    

### 12.1 静态资源加载

- 开发环境：静态资源从本地服务加载
    
- 生产环境：Next 静态资源按 `assetPrefix` 指向 CDN 域名
    

### 12.2 API 请求（同域代理）

- 前端始终请求同域 `/api/www/*` 与 `/gateway/*`
    
- Next 服务端按环境转发到外网或内网后端域名
    

### 12.3 路由（多入口 SPA）

- 指定路径（`/workflows` 等）由 rewrite 映射至 `/`
    
- 地址栏保持原路径，页面由前端逻辑决定具体展示
    

### 12.4 构建产物与部署

- `standalone` 输出提高部署便捷性与产物可控性
    
- TypeScript/ESLint 构建错误忽略降低构建阻塞，但需 CI 补齐质量门禁
    

---

13. ## 运维与排查清单（强建议纳入发布流程）
    

### 13.1 静态资源 404（最常见）

现象：

- 页面 HTML 返回正常
    
- 控制台报 `_next/static/*` 或图片资源 404
    

排查项：

5. HTML 中资源 URL 是否包含期望的 `assetPrefix`
    
6. CDN 上是否存在对应目录层级（特别是是否包含 `/static/`）
    
7. CDN 部署脚本是否将 `.next/static` 与自定义 `static/images` 一并上传
    
8. 是否存在路径重复（如 `.../static/static/...`）导致目录不匹配
    

### 13.2 API 代理不生效

现象：

- `/api/www/*` 返回 404 或返回站点页面而非后端响应
    

排查项：

9. rewrites 是否处于 `beforeFiles`
    
10. Next 服务端是否实际启动（rewrite 发生在服务端）
    
11. `config.apiHost` / `config.internalApiHost` 是否正确
    
12. 后端路径是否与 `/api/www/`、`/gateway/` 保持一致
    

### 13.3 SVG 使用异常

现象：

- 作为组件使用时报错
    
- 或作为 url 使用时返回 `[object Module]` 等
    

排查项：

13. 组件模式是否使用 `?react`
    
14. `esModule: false` 已配置（避免默认导出差异导致的路径问题）
    
15. 资源 query 是否被工具链改写（例如某些构建插件）
    

### 13.4 字体图标不显示

现象：

- iconfont 显示空白或方块 排查项：
    

16. `CssUtf8Plugin`、`CssContentUnicodePlugin` 是否在构建中生效
    
17. 构建后 CSS 中 `content` 是否仍为预期 unicode 转义格式
    

---

14. ## 推荐的团队规范
    

### 14.1 API 请求路径规范

- 统一使用同域路径：
    
    - `/api/www/...`
        
    - `/gateway/...`
        
- 禁止在业务代码中硬编码后端域名（避免环境扩散）
    

### 14.2 SVG 使用规范

- React 组件模式：`import Icon from './a.svg?react'`
    
- 资源 URL 模式：`import url from './a.svg'`
    

---

15. ## 附录：配置项对照表（快速索引）
    

|                              |       |                     |
| ---------------------------- | ----- | ------------------- |
| 配置项                          | 作用域   | 主要目的                |
| assetPrefix                  | 运行/构建 | 静态资源 CDN 化          |
| redirects                    | 运行时   | 浏览器跳转（地址栏变化）        |
| rewrites.beforeFiles         | 运行时   | API 转发 + SPA 多入口    |
| pageExtensions               | 构建/路由 | 页面入口命名约束            |
| images.disableStaticImages   | 构建    | 禁用 Next 静态图片导入体系    |
| output: 'standalone'         | 构建/部署 | 生成易部署的独立运行产物        |
| eslint.ignoreDuringBuilds    | 构建    | 构建忽略 ESLint         |
| typescript.ignoreBuildErrors | 构建    | 构建忽略 TS 错误          |
| webpack.devtool              | 构建    | 开发调试 sourcemap      |
| resolve.fallback             | 构建    | 客户端剔除 Node 模块       |
| @svgr/webpack                | 构建    | svg?react 组件化       |
| url-loader/file-loader       | 构建    | 图片输出与 hash 缓存策略     |
| externals.canvas             | 构建    | 排除 Node-only canvas |