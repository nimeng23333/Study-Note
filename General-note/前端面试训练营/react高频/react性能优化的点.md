---
title: react性能优化的点
---
1.React.memo
2.shouldComponentUpdate、PuerComponent
3.使用虚拟列表：只渲染当前可见的列表项目而不是渲染整个列表，减少不必要的DOM操作
4.使用分页加载：用于处理大量数据，将数据分成多个页面，并在需要的时候逐页加载。
5.避免不必要的重复渲染
6.React.lazy和Suspense：是新的技术，用于延迟加载组件和数据。将某些组件和数据分割成小块，并在需要的时候逐个加载，减少初始加载时间和渲染时间。

React.lazy：懒加载组件
```jsx
import React, { lazy } from 'react';

const LazyComponent = lazy(() => import('./LazyComponent'));
```

- `React.lazy()` 接收一个函数，该函数返回一个动态 `import()` 的 Promise。
- 它会在组件首次渲染时触发加载，而不是在应用启动时就加载所有组件。

Suspense：处理加载状态
```jsx
import React, { Suspense } from 'react';

function App() {
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <LazyComponent />
    </Suspense>
  );
}
```

- `Suspense` 是一个包装组件，用于在懒加载组件尚未准备好时显示备用 UI（fallback）。
- fallback 可以是任何 React 元素，比如加载动画、文字提示等。

Suspense 的核心作用
- 暂停渲染：等待异步资源加载完成。
- 显示占位符：提供用户友好的过渡体验。
- 与并发模式结合：在 React 18 中支持更细粒度的控制，比如 `useTransition`。