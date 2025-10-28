---
title: react setState是同步还是异步
---
看情况，视当前执行环境而定。
在合成事件和生命周期函数里是异步的，也就是说调用setState不会立刻更新组件状态，而是会将更新放入一个更新队列，等待后续的批量更新。batching。它可以优化性能并避免不必要的重复渲染。

在其他代码执行环境，可能是同步的，也就是说调用setState会立刻对组件状态进行更新。但是这样可能出现一些意想不到的问题。
建议避免依赖setState的同步特性，使用回调函数或者setEffect更新副作用。

[React18 setState: 消失的面试题 - 知乎](https://zhuanlan.zhihu.com/p/460668459)

在v18以后要体验同步，需要用ReactDOM.render方法（legacymode 过时模式）或者`flushSync`函数
**ReactDOM.render** 方法用于将 React 组件渲染到 DOM 中。在 React 18 中，_ReactDOM.render_ 方法被 _ReactDOM.createRoot_ 和 _root.render_ 取代

`flushSync`函数会在执行完传给他的`fn`函数后马上去清空一次更新队列, 也就是调用`flushSyncCallbacks`方法, 就是我们之前在异步中调用`setState`的行为.
只有在整个函数执行完以后才会对队列进行刷新. 所以如果两个`setState`都写在一个`flushSync`里面是没有效果的