---
title: Symbol
---
ES6 引入了一种新的原始数据类型 Symbol ，表示独一无二的值，最大的用法是用来定义对象的唯一属性名

```js
let sy = Symbol("KK");
console.log(sy);   // Symbol(KK)
typeof(sy);        // "symbol"
 
// 相同参数 Symbol() 返回的值不相等
let sy1 = Symbol("kk"); 
sy === sy1;       // false
```

Symbol 值作为属性名时，该属性是公有属性不是私有属性，可以在类的外部访问。但是不会出现在 for...in 、 for...of 的循环中，也不会被 Object.keys() 、 Object.getOwnPropertyNames() 返回。如果要读取到一个对象的 Symbol 属性，可以通过 Object.getOwnPropertySymbols() 和 Reflect.ownKeys() 取到。

因此可以用来定义一些不希望被遍历到的属性，例如一些内部实现细节或隐藏属性
```js
let syObject = {};
syObject[sy] = "kk";
console.log(syObject);
 
for (let i in syObject) {
  console.log(i);
}    // 无输出
 
Object.keys(syObject);                     // []
Object.getOwnPropertySymbols(syObject);    // [Symbol(key1)]
Reflect.ownKeys(syObject);                 // [Symbol(key1)]
```

由于 Symbol 是一种基础数据类型，所以它具有很高的性能和可靠性，可以用于需要高效和安全的标识符创建和使用的场景。