---
title: 深拷贝vs浅拷贝
---

javascript分原始类型与引用类型。Array是引用类型，直接用“=”号赋值的话，只是把源数组的地址(或叫指针)赋值给目的数组，指向的是同一个内存地址，其中一个改变另一个也会改变。并没有实现数组的数据的拷贝。这种方式的实现属于浅拷贝。

[js 数组的浅拷贝和深拷贝 - 小明明同学 - 博客园 (cnblogs.com)](https://www.cnblogs.com/xym0710/p/14508043.html)


例如

concat连接数组的时候，它的拷贝方式取决于数组的元素类型。如果数组的元素是基本类型（如字符串，数字，布尔值），那么concat方法是深拷贝，即新数组和原数组不会相互影响。如果数组的元素是引用类型（如对象，数组），那么concat方法是浅拷贝，即新数组和原数组会共享同一个对象的引用，如果修改了对象的属性，那么新数组和原数组都会受到影响

下面代码演示了concat方法的深拷贝和浅拷贝的区别：

```javascript
// 深拷贝
var arr1 = [1, 2, 3];
var arr2 = [4, 5, 6];
var arr3 = arr1.concat(arr2);
console.log(arr3); // [1, 2, 3, 4, 5, 6]
arr1[0] = 10;
console.log(arr1); // [10, 2, 3]
console.log(arr3); // [1, 2, 3, 4, 5, 6]

// 浅拷贝
var obj1 = {name: "Alice"};
var obj2 = {name: "Bob"};
var arr4 = [obj1, obj2];
var arr5 = arr4.concat();
console.log(arr5); // [{name: "Alice"}, {name: "Bob"}]
obj1.name = "Charlie";
console.log(obj1); // {name: "Charlie"}
console.log(arr4); // [{name: "Charlie"}, {name: "Bob"}]
console.log(arr5); // [{name: "Charlie"}, {name: "Bob"}]
```

深拷贝嵌套数组：
```js
const array = [[1,2,3],[4,5,6],[7,8,9]]
const copyArray = [...array.map((innerArray)=> [...innerArray])];
```
