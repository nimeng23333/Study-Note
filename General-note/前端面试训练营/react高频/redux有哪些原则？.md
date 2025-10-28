---
title: redux有哪些原则？
---
1.单一数据源：所有state都存在一个js对象里，就是store。让全局状态管理更简单可维护
2.状态只读：state不能直接被修改，只能派action。可以确保状态的可控。
3.使用纯函数来执行修改：reducer是纯函数，接受旧state、action返回一个新的state，因此可以被预测、测试、重用

其他：
1.操作副作用要用中间件：比如异步操作、记录日志等
2.UI和state分离：UI只负责渲染state，而state的操作逻辑应该放在action里。