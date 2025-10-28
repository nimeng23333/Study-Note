---
title: react如何防止子组件的重复渲染
---
PuerComponent、React.memo()和shouldComponentUpdate
PuerComponent是react的一个内置组件，实现了shouldComponentUpdate方法。根据组件的props和state来决定组件是否需要重新渲染。
React.memo()是一个高阶组件，通过浅比较props
shouldComponentUpdate：在class组件内，shouldComponentUpdate方法判断组件是否需要重新渲染。

浅比较就是只比较第一级，对于基本数据类型，只比较值;对于引用数据类型值，直接比较地址是否相同，不管里面内容变不变，只要地址一样，我们就认为没变。