---
title: Object方法
---
# Object的preventExtensions、seal、freeze

|方法|能否添加属性|能否删除属性|能否修改属性值|能否修改属性描述符|
|---|---|---|---|---|
|`preventExtensions`|❌|✅|✅|✅|
|`seal`|❌|❌|✅|❌|
|`freeze`|❌|❌|❌|❌|

# Object.defineProperty()

**`Object.defineProperty()`** 静态方法会直接在一个对象上定义一个新属性，或修改其现有属性，并返回此对象。
```js
const object1 = {};

Object.defineProperty(object1, "property1", {
  value: 42,
  writable: false,
});

object1.property1 = 77;
// Throws an error in strict mode

console.log(object1.property1);
// Expected output: 42
```