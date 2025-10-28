---
title: fiber在hooks上有哪些体现？
---
react 是把 vdom 转成 fiber，然后把 hook 链表存放到了 fiber.memorizedState 属性上，通过 next 串联

[前端 - React Hooks 的实现必须依赖 Fiber 么？ - 个人文章 - SegmentFault 思否](https://segmentfault.com/a/1190000041733027)

在 v16 之前的 React 里，是直接递归遍历 vdom，通过 dom api 增删改 dom 的方式来渲染的。但当 vdom 过大，频繁调用 dom api 会比较耗时，而且递归的过程不能被终止，就产生的性能问题，其实归根到底一句话就是递归比对的过程长期占用主线程产生了性能问题。
后来就引入了 fiber 架构，它以链表的形式以循环来模拟递归组件树，而放弃了递归调用，因为循环可以随时被中断，可以灵活的暂停、继续和丢弃执行的任务（requestIdleCallback API来实现）