---
title: for...in与for...of
---
for...in 和 for...of 都是 JavaScript 中的循环语句，但它们的作用和使用方式略有不同。

暂时可以理解为for in用于对象，其他的用for of。
特别的：map是用for of
`for(const [key, value] of map)`

**for...in** 循环用于遍历对象的可枚举属性，它会将对象的每个属性名称（或键名）作为迭代变量来遍历。以下是 for...in 的基本语法：

```js
for (variable in object) {
 // 在此处编写循环体语句
}
```

  

其中，variable 表示每次迭代中当前属性的名称（或键名），object 表示要遍历的对象。例如：

```js
const obj = { a: 1, b: 2, c: 3 };
for (const key in obj) {
 console.log(key, obj[key]);
}
```

  

输出结果：

```js
a 1b 2
c 3
```

需要注意的是，for...in 循环遍历的是对象的可枚举属性，包括自有属性和继承属性。因此，它并不适用于遍历数组和类数组对象。

**for...of** 循环用于遍历可迭代对象的元素，它会将对象的每个元素作为迭代变量来遍历。以下是 for...of 的基本语法：

```js
for (variable of iterable) {
 // 在此处编写循环体语句
}
```


其中，variable 表示每次迭代中当前元素的值，iterable 表示要遍历的可迭代对象，如数组、字符串、Set、Map 等。例如：

```js
const arr = [1, 2, 3];
for (const item of arr) {
 console.log(item);
}
```

  
输出结果：

```js
1
2
3
```


需要注意的是，for...of 循环只能遍历实现了迭代器接口（Iterator）的对象，因此它不适用于普通的对象。此外，它遍历的是对象的元素值，而不是键名或属性名。

总结一下，for...in 适用于遍历对象的属性名，而 for...of 适用于遍历数组、字符串等可迭代对象的元素值。

- `for...of` 不能直接用于普通对象，因为普通对象不是可迭代的。
    
- 如果需要遍历对象的属性值，可以先将对象转换为数组（如使用 `Object.values()`）：
    
```js
const obj = { a: 1, b: 2, c: 3 };

for (let value of Object.values(obj)) {
  console.log(value); // 输出: 1, 2, 3
}
```