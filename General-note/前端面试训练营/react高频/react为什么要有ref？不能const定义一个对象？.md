---
title: react为什么要有ref？不能const定义一个对象？
---
uesRef是一个hook函数，类似于class组件的this.xxx，可以在函数组件中存储保留一个变量或者引用DOM节点，而不会触发重新渲染。

1.在函数组件中只能用useState和useReducer等hook函数而不能用const定义变量。函数组件中没有实例的概念，无法在组件中使用this关键字或者实例属性。而useRef可以跳过这个限制，在函数组件中保存状态而且不会触发重新渲染。

2.在函数组件中如果用const定义一个对象，每次重新渲染都会重新创建，而如果想在多次渲染中保持这个变量的值，将变量保存在组件状态中，就可以用useRef。

3.useRef可以引用一个DOM节点，这比document.getElementByID这种DOM API方便高效。

总之useRef适用于保存一个可变的量并在渲染期间进行更新而不触发组件重新更新。