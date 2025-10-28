---
title: typeof null
---
```js
console.log(typeof null) //object
console.log(typeof none) //undefined
console.log(typeof undefined) //undefined
```

typeof null是object这是一个历史遗留问题。在最早的时候js用32位的值来表示变量
其中前3位代表类型，000是对象，010是浮点数，100是字符串，110是布尔值。其他的表示指针。而null被认为是全0指针，指向一个空对象，因此是object
因此在判断是否为null的时候用严格相等符号。