---
title: splice和slice会改变原数组吗？怎么删除数组最后一个元素？
---
splice() 和 slice() 是 JavaScript 数组的方法，两者作用不同。  
1splice() 方法可以在数组中添加、删除或替换元素，并返回被删除的元素，它会改变原数组。  
2slice() 方法是从原数组中返回指定开始和结束位置的元素组成的新数组，它不会改变原数组。  
所以，使用 splice() 方法可能会改变原数组，而 slice() 方法则不会。  
要删除数组中的最后一个元素，有几种方法：  
1使用 pop() 方法删除并返回数组的最后一个元素，即可删除数组的最后一个元素：  

```js
const arr = [1, 2, 3, 4];

const lastElement = arr.pop(); // 返回被删除的元素 4console.log(arr); // [1, 2, 3]
```

  
2使用 splice() 方法删除数组的最后一个元素：  

```js
const arr = [1, 2, 3, 4];

arr.splice(-1, 1); // 从倒数第一个位置开始删除一个元素console.log(arr); // [1, 2, 3]
```
  
3使用 slice() 方法和展开运算符 (...) 创建一个新的数组，包含除了最后一个元素以外的所有元素：  

```js
const arr = [1, 2, 3, 4];

const newArr = [...arr.slice(0, -1)];

console.log(newArr); // [1, 2, 3]
```
  
需要注意的是，以上方法都会改变或创建一个新的数组，原数组不会被保留最后一个元素。如果要仅仅取得最后一个元素，可以使用下标或者数组方法 slice()：  
```js
const arr = [1, 2, 3, 4];

const lastElement = arr[arr.length - 1]; // 4const lastElement2 = arr.slice(-1)[0]; // 4
```

# splice

语法
```js
splice(start)
splice(start, deleteCount)
splice(start, deleteCount, item1)
splice(start, deleteCount, item1, item2)
splice(start, deleteCount, item1, item2, /* …, */ itemN)
```

返回值

一个包含了删除的元素的数组。

如果只移除一个元素，则返回一个元素的数组。

如果没有删除任何元素，则返回一个空数组。

示例
```js
const myFish = ["angel", "clown", "mandarin", "sturgeon"];
const removed = myFish.splice(2, 0, "drum");

// myFish 是 ["angel", "clown", "drum", "mandarin", "sturgeon"]
// removed 是 []，没有移除的元素
```