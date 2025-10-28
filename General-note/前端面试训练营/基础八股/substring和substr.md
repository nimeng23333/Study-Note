---
title: substring和substr
---

substring() 和 substr() 都是 JavaScript 字符串的方法，用于截取字符串的一部分。它们的区别在于参数的不同。

substring()接受两个参数，第一个参数是起始位置，第二个参数是结束位置，截取内容包含起始位置但不包含结束位置。如果省略第二个参数就截取到字符串末端
substr()接受两个参数，第一个参数是起始位置，第二个参数是截取字符串的长度。如果省略第二个参数就截取到字符串末端。
```js
let str = "Hello world!";
str.substring(0,3); // Hel
str.substring(6); //world
str.substr(6,3); //wor
str.substr(6); //world
```

字符串还有slice()方法，但没有splice方法。splice是数组的方法。
slice与substring的区别在于substring不能接受负数参数，slice可以