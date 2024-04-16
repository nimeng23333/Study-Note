---
title: JSON.parse()
---

与stringify相反，`JSON.parse()` 方法将数据转换为 JavaScript 对象。

JSON 不允许包含函数，但可以将函数作为字符串存储，之后再将字符串转换为函数。不建议在 JSON 中使用函数。
eg:
```js
var text = '{ "name":"Runoob", "alexa":"function () {return 10000;}", "site":"www.runoob.com"}';
var obj = JSON.parse(text);
obj.alexa = eval("(" + obj.alexa + ")");
 
document.getElementById("demo").innerHTML = obj.name + " Alexa 排名：" + obj.alexa();
```