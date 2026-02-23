---
title: react1s - 点击网页元素跳转对应代码
---

[插件商店地址](https://chromewebstore.google.com/detail/react1s/gpcoahaomdfmekggblkckofkgjggnjlp)

但next19以后无法用外部插件进行跳转，需要在项目内部装插件

# Code-inspector

## 背景


> React 19 移除了 DOM 元素上的 `__reactFiber` 内部引用，这是 React 团队的有意设计，目的是防止外部代码直接访问 React 内部结构。
> 
> 这导致所有基于查找 DOM 元素 Fiber 属性的 click-to-component 工具（包括 react1s）都无法工作。
> 
> 官方推荐使用 [Code Inspector](https://github.com/zh-lx/code-inspector) 作为替代方案。

  

不好用，经常失效，10次能成功启动一次

  

## 安装和配置

  

```Plain
pnpm add code-inspector-plugin -D
```

  

`apps/lovart/next.config.mjs` 中添加（Next.js 16 + Turbopack）：

```JavaScript
import { codeInspectorPlugin } from 'code-inspector-plugin';

const nextConfig = {
  // ... 其他配置

  turbopack: {
    rules: {
      // Code Inspector 规则（仅在开发环境启用，用于代码定位）
      ...codeInspectorPlugin({
            bundler: 'turbopack',
            hotKeys: ['altKey', 'shiftKey'],
            editor: 'cursor',
          }),
    },
  },
};
```

## 使用

1. 按住 `Alt/Option` 键
    
2. 点击页面上的任意元素
    
3. 自动在编辑器中打开对应的源代码文件
    

  

## 自定义配置

```TypeScript
codeInspectorPlugin({
  bundler: 'turbopack', // Next.js 15.3+ 推荐
  hotKeys: ['altKey'], // Alt
  editor: 'cursor', // 或 'vscode', 'webstorm'
})
```

### 更改热键

  

如果想使用不同的热键组合：

```JavaScript
codeInspectorPlugin({
  bundler: 'turbopack',
  hotKeys: ['shiftKey', 'altKey'], // Shift + Alt + Click
})
```

支持的热键： - `'altKey'` - Alt/Option - `'ctrlKey'` - Ctrl - `'shiftKey'` - Shift - `'metaKey'` - Command/Win

  

### 指定编辑器

Code Inspector 会自动检测编辑器，但也可以手动指定

  

## 参考链接

- [Code Inspector GitHub](https://github.com/zh-lx/code-inspector)
    
- [Code Inspector 文档](https://inspector.fe-dev.cn/)
    
- [npm 包](https://www.npmjs.com/package/code-inspector-plugin)
    
- [React 19 Upgrade Guide](https://react.dev/blog/2024/04/25/react-19-upgrade-guide)