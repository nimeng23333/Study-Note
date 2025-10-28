---
title: js中如何判断数据类型
---
1.typeof
2.instanceof
3.Object.prototype.toString.call(value)

typeof
判断基础数据类型。日期、数组、对象和null都返回object

instanceof
判断一个实例是否是某个构造函数的实例。

Object.prototype.toString.call(value)
返回一个值的内部类型，它适用于所有值，包括 null 和 undefined
返回值是一个形如 `[object Type] `的字符串，其中 Type 表示值的内部类型
```js
Object.prototype.toString.call(undefined); // "[object Undefined]"
Object.prototype.toString.call(null); // "[object Null]"
Object.prototype.toString.call(true); // "[object Boolean]"
Object.prototype.toString.call(123); // "[object Number]"
Object.prototype.toString.call("abc"); // "[object String]"
Object.prototype.toString.call(Symbol("foo")); // "[object Symbol]"
Object.prototype.toString.call({}); // "[object Object]"
Object.prototype.toString.call(function () {}); // "[object Function]"
Object.prototype.toString.call([]); // "[object Array]"
Object.prototype.toString.call(new Date()); // "[object Date]"
Object.prototype.toString.call(/abc/); // "[object RegExp]"
```

需要注意的是，使用 Object.prototype.toString 方法判断基本类型值时，返回的是其包装对象的类型，而不是基本类型本身的类型。
在 JavaScript 中，基本类型（Primitive Types）包括 `string`、`number`、`boolean`、`null`、`undefined` 和 `symbol`。这些基本类型本身不是对象，因此它们没有方法或属性。然而，JavaScript 提供了对应的包装对象（Wrapper Objects），如 `String`、`Number`、`Boolean` 等，这些对象具有方法和属性。

```js
const strPrimitive = "hello"; // 基本类型 string
const strObject = new String("hello"); // 包装对象 String

console.log(typeof strPrimitive); // "string"
console.log(typeof strObject); // "object"

console.log(Object.prototype.toString.call(strPrimitive)); // [object String]
console.log(Object.prototype.toString.call(strObject)); // [object String]
```

为什么 `new String("hello")` 的 `typeof` 是 `"object"`？

当你使用 `new String("hello")` 时，你显式地创建了一个 `String` 包装对象。这个对象是一个实例化的对象，而不是一个基本类型的字符串。因此，`typeof` 会将其识别为 `"object"`。

换句话说：

- `new String("hello")` 创建的是一个对象，它的类型是 `String` 包装对象。
- `typeof` 对于所有对象（包括包装对象）都会返回 `"object"`。

```js
console.log(new String("1")) // String{"1"}
```

还有一些其他的判断类型的方式，如 Array.isArray 判断数组类型、isNaN 判断是否为 NaN、Number.isInteger 判断是否为整数等。