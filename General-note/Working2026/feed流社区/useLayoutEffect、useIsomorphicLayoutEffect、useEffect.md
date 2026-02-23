---
title: useLayoutEffect、useIsomorphicLayoutEffect、useEffect
---
https://juejin.cn/post/7582809606067208218


#### 1）只要不读/写布局，就用 `useEffect`

典型场景：

- 请求数据、上报埋点
    
- 订阅/取消订阅（WebSocket、EventEmitter）
    
- `document.title`、`localStorage` 同步
    
- 给 window/document 绑事件
    

这些东西不需要卡在 paint 之前完成，`useEffect` 更合适。

#### 2）要读布局（layout read）并且会影响第一帧渲染，就用 `useLayoutEffect`

典型场景：

- `getBoundingClientRect()` / `offsetWidth` / `scrollHeight` 这种
    
- 计算初始滚动位置、同步滚动
    
- 需要避免视觉抖动的“测量 -> setState”
    
- focus / selection（输入框聚焦、光标定位）对首帧体验敏感
    

一句话：**“不想让用户看到中间态”**。

#### 3）别在 `useLayoutEffect` 里干重活

因为它会阻塞 paint：

- 你在里面做重计算，页面就掉帧
    
- 你在里面频繁 setState，可能放大卡顿
    

---

### 容易踩的坑

- **Strict Mode 下 effect 会在开发环境额外执行一次**：`useEffect` 和 `useLayoutEffect` 都一样，不能拿这个现象判断线上行为。
    

---

### 简单总结一下

- `useEffect`：大多数副作用的默认选择。
    
- `useLayoutEffect`：只在“必须卡在 paint 前解决”的那一小撮场景里用。
    

真要说区别，其实就是一句：**愿不愿意为了“第一帧正确”去挡住 paint**。

  

### useIsomorphicLayoutEffect

在NextJS服务端渲染（SSR）时：

- `useEffect` 本来就不会执行（只在浏览器跑）
    
- `useLayoutEffect` 也不会执行，但 React 会提示：它在服务端没意义，可能导致你写出“依赖布局但 SSR 不存在布局”的代码
    

如果写的是“浏览器才有意义的 layout effect”，又不想看到 warning，常见做法是包一层：

```TypeScript
import { useEffect, useLayoutEffect } from "react";
export const useIsomorphicLayoutEffect =  typeof window !== "undefined" ? useLayoutEffect : useEffect;
```

然后把需要 layout 的地方用 `useIsomorphicLayoutEffect`。

在Ahook库里提供了`useIsomorphicLayoutEffect`方法