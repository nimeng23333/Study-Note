---
title: UmiJS & MFSU 简介（工程实践向）
---
https://umijs.org/docs/guides/prepare

  

本文介绍前端工程中常用的 **UmiJS** 框架，以及其核心加速能力 **MFSU（Module Federation Speed Up）**，并结合 monorepo / workspace 场景说明常见问题与最佳实践。

  

---

  

## 一、UmiJS 是什么？

  

### 1. 定义

**UmiJS 是一个基于 React 的前端应用框架和工程体系**，主要面向中大型 Web 应用（尤其是中后台系统）。

  

它并不仅是一个“框架”，而是一套：

- 工程规范
    
- 构建工具封装
    
- 运行时能力
    
- 性能优化方案
    
      
    

---

  

### 2. UmiJS 解决了什么问题？

  

如果不使用 Umi，开发者通常需要自行处理：

  

- Webpack / Vite 配置
    
- 路由系统
    
- 构建 & 启动脚本
    
- 开发服务器
    
- 环境变量
    
- monorepo 依赖解析
    
- 性能优化方案
    
      
    

**UmiJS 的目标是：**

> 让开发者“关注业务，而不是工程细节”。

  

---

  

### 3. UmiJS 的典型能力

  

- 约定式路由
    
- 插件体系
    
- 内置 Webpack 配置
    
- dev / build 一体化
    
- monorepo / workspace 友好
    
- 深度集成性能优化方案（如 MFSU）
    
      
    

---

  

## 二、MFSU 是什么？（UmiJS 里的）

  

⚠️ 注意：

**这里的 MFSU ≠ 性能指标里的 MFSU（Mean First Screen Update）**

  

本文中的 MFSU 指的是：

  

> **Module Federation Speed Up**

  

---

  

### 1. MFSU 的一句话解释

  

**MFSU 是 Umi 基于 Webpack Module Federation 实现的「依赖预编译 + 缓存复用」机制，用于显著提升开发环境启动速度和热更新速度。**

  

---

  

### 2. 为什么需要 MFSU？

  

#### 不使用 MFSU 的情况

每次启动 dev server：

  

启动

→ 扫描 node_modules

→ 打包全部依赖

→ 启动时间长

  

  

#### 使用 MFSU 之后

第一次启动：

→ 预编译依赖

→ 缓存到磁盘

  

后续启动：

→ 直接复用缓存

→ 秒级启动

  

  

---

  

### 3. MFSU 的核心思想

  

- 利用 **Webpack Module Federation**
    
- 把稳定的第三方依赖拆成「远程模块」
    
- 在本地缓存编译结果
    
- 多次启动复用
    
      
    

---

  

## 三、MFSU 在 monorepo / workspace 中的行为

  

### 1. MFSU 会做什么？

  

在启动时，MFSU 会：

  

1. 扫描 workspace 下的所有包
    
2. 判断哪些是「可参与预编译的模块」
    
3. 读取每个包的 `package.json`
    
4. 找到入口文件（main / module / exports）
    
5. 决定是否纳入 Module Federation
    
      
    

---

  

### 2. 常见问题

  

#### ❌ 不完整的 package 目录

例如：

  

packages/

admin-form/

node_modules/

❌ 没有 package.json

  

  

这种目录会导致：

  

- MFSU 认为它是一个 workspace 包
    
- 但找不到入口文件
    
- 最终抛出 AssertionError 或构建失败
    
      
    

---

  

### 3. 正确的 workspace 包结构

  

packages/

admin-form/

package.json

src/

index.ts

  

  

至少要有：

- `package.json`
    
- `name` 字段
    
- 可解析的入口文件
    
      
    

---

  

## 四、MFSU 相关缓存说明

  

MFSU 使用了**强缓存机制**，常见缓存目录包括：

  

src/.umi

src/.umi-production

node_modules/.cache

  

  

当遇到异常构建问题时，**清缓存是第一优先级操作**。

  

```Bash
rm -rf src/.umi
rm -rf src/.umi-production
rm -rf node_modules/.cache
```

## 五、如何关闭 MFSU（兜底方案）

在调试或紧急情况下，可以临时关闭 MFSU：

```TypeScript
// .umirc.ts or config.ts
export default {
  mfsu: false,
};
```

关闭后：

启动速度会变慢但可以规避 MFSU 的扫描 / 缓存问题

## 总结

UmiJS 是前端工程框架，负责“项目如何运行”

MFSU（Umi） 是工程加速方案，负责“项目运行得多快”

在 monorepo 中，不完整的包结构是 MFSU 的高频雷点