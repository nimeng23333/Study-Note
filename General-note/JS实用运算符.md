---
title: JS实用运算符
---
# `??`非空运算符

在 JS 中，`?? `运算符被称为非空运算符。如果第一个参数不是 null/undefined（这里只有两个假值，但是 JS 中假值包含：未定义 undefined、空对象 null、数值 0、空数字 NaN、布尔 false，空字符串''），将返回第一个参数，否则返回第二个参数。比如，

```js
null ?? 5 // => 5
3 ?? 5 // => 3
```

给变量设置默认值时，以前常用 ||逻辑或运算符，例如，

```js
var prevMoney = 1
var currMoney = 0
var noAccount = null
var futureMoney = -1
function moneyAmount(money) {    return money || `账户未开通`
}
console.log(moneyAmount(prevMoney)) // => 1
console.log(moneyAmount(currMoney)) // => 账户未开通
console.log(moneyAmount(noAccount)) // => 账户未开通
console.log(moneyAmount(futureMoney)) // => -1
```
上面我们创建了函数 moneyAmount，它返回当前用户余额。我们使用` || `运算符来识别没有帐户的用户。

然而，当用户没有帐户时，这意味着什么？将无账户视为空而不是 0 更为准确，因为银行账户可能没有（或负）货币。

在上面的例子中，|| 运算符将 0 视为一个虚假值，不应该包括用户有 0 美元的帐户。让我们使用?? 非空运算符来解决这个问题：

```js
var currMoney = 0
var noAccount = null
function moneyAmount(money) {
  return money ?? `账户未开通`
}
 
moneyAmount(currMoney) // => 0
moneyAmount(noAccount) // => `账户未开通`
```

概括地说 ?? 运算符允许我们在忽略错误值（如 0 和空字符串）的同时指定默认值。

# `??=`空赋值运算符

`??=` 也被称为空赋值运算符，与上面的非空运算符相关。看看它们之间的联系：

```js
var x = null
var y = 5
 
console.log(x ??= y) // => 5
console.log(x = (x ?? y)) // => 5
```

仅当值为 null 或 undefined 时，此赋值运算符才会赋值。上面的例子强调了这个运算符本质上是空赋值的语法糖（译者注，类似的语法糖：a = a + b 可写成 a += b ）。接下来，让我们看看这个运算符与默认参数（默认参数是 ES6 引入的新语法，仅当函数参数为 undefined 时，给它设置一个默认值）的区别：
```js
function gameSettingsWithNullish(options) {
  options.gameSpeed ??= 1
  options.gameDiff ??= 'easy' 
  return options
}
function gameSettingsWithDefaultParams(gameSpeed=1, gameDiff='easy') {
  return {gameSpeed, gameDiff}
}
 
gameSettingsWithNullish({gameSpeed: null, gameDiff: null}) // => {gameSpeed: 1, gameDiff: 'easy'}
gameSettingsWithDefaultParams(undefined, null) // => {gameSpeed: null, gameDiff: null}
```

# `?.`链判断运算符

链判断运算符?. 允许开发人员读取深度嵌套在对象链中的属性值，而不必验证每个引用。当引用为空时，表达式停止计算并返回 undefined。比如：
```js
var travelPlans = {
  destination: 'DC',
  monday: {
    location: 'National Mall',
    budget: 200
  }
}
console.log(travelPlans.tuesday?.location) // => undefined
```

# `?:`三元运算符

不多赘述，参考以前笔记