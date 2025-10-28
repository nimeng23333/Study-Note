---
title: react中怎样的操作会导致内存泄漏？
---
1.未清理的计时器、事件监听器
2.未清理的引用
3.大量的渲染缓存
4.长时间运行的异步操作

react提供的生命周期方法和hooks来清理
检测和诊断工具：chrome devTools的memory面板和react profiler