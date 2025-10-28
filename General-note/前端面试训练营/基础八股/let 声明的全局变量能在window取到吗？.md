---
title: let 声明的全局变量能在window取到吗？
---
let声明的全局变量并不能用window.变量名称获取
```js
let test1 = 1;
var test2 = 2;
console.log(window.test1, window.test2) // undefined, 2
```