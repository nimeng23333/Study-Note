
map、filter、reduce函数类似，可以看成加强版forEach循环，但里面的function需要加return

比如:

map一对一映射
```jsx
const numbers = [5,26,53,106]

var newNumbers = numbers.map(function(x) {return x*2});

console.log(newNumbers); //输出[10,52,106,212]
```
filter一对一映射并筛选
```jsx
const numbers = [5,26,53,106]

var newNumbers = numbers.filter(function(x) {return x>30}); //这个return千万千万不能忘记！！

console.log(newNumbers); //输出[53,106];
```
reduce自带一个可以传递给每次循环累加的参数
```jsx
const numbers = [5,26,53,106]

var newNumbers = numbers.reduce(function(accumulator, currentNumber) {
return accumulator + currentNumber;
});

console.log(accumulator); //输出190;
```

find与findindex函数可以找到数组里符合条件的==第一个==item

find
```jsx
const numbers = [5,26,53,106]

var newNumber = numbers.find(function(x) {
return x>30;
});

console.log(newNumber); //输出53;
```
findindex
```jsx
const numbers = [5,26,53,106]

var newNumberIdx = numbers.findIndex(function(x) {
return x>30;
});

console.log(newNumberIdx); //输出2;
```

这里的箭头函数不要括号也不用return
```js
const newNumber = numbers.map(x => x*x);
```

### concat()
合并两个数组并返回一个新的数组
```js
const array1 = ['a', 'b', 'c'];
const array2 = ['d', 'e', 'f'];
const array3 = array1.concat(array2);

console.log(array3);
// Expected output: Array ["a", "b", "c", "d", "e", "f"]
```

`concat() `方法是一种复制方法。它不会更改 this 或作为参数提供的任何数组，而是返回包含与原始数组中的元素相同的元素的浅拷贝。

它的拷贝方式取决于数组的元素类型。如果数组的元素是基本类型（如字符串，数字，布尔值），那么concat方法是深拷贝，即新数组和原数组不会相互影响。如果数组的元素是引用类型（如对象，数组），那么concat方法是浅拷贝，即新数组和原数组会共享同一个对象的引用，如果修改了对象的属性，那么新数组和原数组都会受到影响

例如，下面的代码演示了concat方法的深拷贝和浅拷贝的区别：

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

### slice()
slice() 方法返回一个新的数组对象，这一对象是一个由 start 和 end 决定的原数组的浅拷贝（包括 start，不包括 end），其中 start 和 end 代表了数组元素的索引。原始数组不会被改变。
```js
const animals = ['ant', 'bison', 'camel', 'duck', 'elephant'];

console.log(animals.slice(2));
// Expected output: Array ["camel", "duck", "elephant"]

console.log(animals.slice(2, 4));
// Expected output: Array ["camel", "duck"]

console.log(animals.slice(1, 5));
// Expected output: Array ["bison", "camel", "duck", "elephant"]

console.log(animals.slice(-2));
// Expected output: Array ["duck", "elephant"]

console.log(animals.slice(2, -1));
// Expected output: Array ["camel", "duck"]

console.log(animals.slice());
// Expected output: Array ["ant", "bison", "camel", "duck", "elephant"]

```

### splice()

splice() 方法通过移除或者替换已存在的元素和/或添加新元素就地改变一个数组的内容。

要创建一个删除和/或替换部分内容而不改变原数组的新数组，请使用 toSpliced()。要访问数组的一部分而不修改它，参见 slice()。

```js
const months = ['Jan', 'March', 'April', 'June'];
months.splice(1, 0, 'Feb');
// Inserts at index 1
console.log(months);
// Expected output: Array ["Jan", "Feb", "March", "April", "June"]

months.splice(4, 1, 'May');
// Replaces 1 element at index 4
console.log(months);
// Expected output: Array ["Jan", "Feb", "March", "April", "May"]

```