---
title: bind
---
# bind

连续多次bind会指向第一次bind的对象。

在 JavaScript 中，可以使用 bind() 方法来改变函数执行时的 this 上下文。如果连续多次对同一个函数使用 bind() 方法，那么每次调用 bind() 方法都会返回一个新的函数，但是这些新函数的 this 上下文都是相同的，指向第一个绑定的对象

```js
function foo() {
 console.log(this.name);
}
const obj1 = { name: 'object 1' };
const obj2 = { name: 'object 2' };
const bound1 = foo.bind(obj1).bind(obj2).bind(null);
bound1(); // 输出: "object 1"
```

需要注意的是，如果在使用 bind() 方法时，最终绑定的对象为 null 或 undefined，那么 this 上下文会指向全局对象 window（浏览器环境）或 global（Node.js 环境）

- **_call_**: 立即执行函数，参数以逗号分隔。
    
- **_apply_**: 立即执行函数，参数以数组形式传递。
    
- **_bind_**: 返回一个新的函数，_this_ 永久指向指定的对象，参数可以分多次传递。