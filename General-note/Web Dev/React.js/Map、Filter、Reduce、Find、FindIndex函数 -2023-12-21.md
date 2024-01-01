
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