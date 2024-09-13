---
title: 特殊关键字 - this
---
JS中this是特殊关键字，它的值取决于函数的调用方式，简单来说通常指向调用函数的对象。

### this的值
#### 全局作用域中 
this指向全局对象浏览器中是window
```js
console.log(this); //输出Window
```
#### 对象方法中 
当函数作为对象方法调用，this指向该对象
```js
const obj = {
	name:"Alice",
	greet: function(){
		console.log(this.name) //Alice
	}
}
```
#### 构造函数中 
this指向新创建的对象
```js
function Person(name){
	this.name = name
}
const alice = new Person("Alice");
console.log(alice.name) //Alice
```
#### `apply`指定this值
js中允许使用`apply`显式的设置this，无论函数是怎么定义的
```js
function greet(){
	console.log(this.name)
}
const alice = {name:"Alice"};
greet.apply(alice) //Alice
```
在上面这个例子中，函数构造的时候没有提供参数，而是在函数内写了this，如果直接调用`greet()`将会是Window.name会是空字符串（因为Window对象的name是''），而`apply`将`alice`作为`this`传递给函数，`this = alice`，`this.name = Alice`

##### `Function.apply(this,arguments)`
`apply()`方法会以给定this和作为数组提供的参数来调用函数。
非严格模式下下this提供为`null`和`undefined`会被替换为全局对象
```js
const numbers = [5, 6, 2, 3, 7];
const max = Math.max.apply(null, numbers);
```



# 扩展阅读

在 JavaScript 中，`this` 的行为与其他编程语言稍有不同。它的值由它的使用上下文决定，这对初学者来说可能会让人感到困惑。因此，了解上下文和 `this` 在不同情况下所指的对象是至关重要的。

### 全局上下文

在全局执行上下文中（即，在任何函数之外），this 无论在严格模式还是非严格模式下，都引用全局对象。

在 web 浏览器中，全局对象是 `window`，所以 `this` 将引用 `window` 对象：

```js
console.log(this); // 在浏览器上下文中会记录 "[object Window]"
```
在 Node.js 环境中，全局对象不是 `window` 而是 `global`。因此，如果在 Node.js 上下文中运行相同的代码，`this` 将引用全局对象：

```js
console.log(this); // 在 Node.js 上下文中会记录 "[object global]"
```
### 函数上下文

在普通函数内部，`this` 的值取决于函数的调用方式。如果函数在全局上下文中调用，`this` 在严格模式下将为 `undefined`，在非严格模式下将引用全局对象。

```js
function func() {
  console.log(this);
}

func(); // 在非严格模式的浏览器上下文中记录 "[object Window]"，在严格模式下会记录 "undefined"
```
但是，当函数充当对象的方法时，`this` 将引用调用该方法的对象。这展示了 `this` 的值不绑定于函数本身，而是由函数被调用的方式和位置决定，这个概念称为执行上下文：

```js
let obj = {
  prop: "Hello",
  func: function() {
    console.log(this.prop);
  }
}

obj.func(); // 记录 "Hello"
```
然而，箭头函数不具有自己的 `this`。相反，它们从创建时的父作用域继承 `this`。换句话说，箭头函数内部的 `this` 值不由它的调用方式决定，而是由它的定义时的外部词法上下文决定：

```js
let obj = {
  prop: "Hello",
  func: () => {
    console.log(this.prop);
  }
}

obj.func(); // 记录 "undefined"，因为箭头函数内部的 `this` 不绑定到 `obj`，而是绑定到其外部词法上下文
```
这在某些情况下可能很有用，但它也使得箭头函数不适合需要访问它们被调用的对象的其他属性的方法。

### 事件处理程序

在事件处理程序的上下文中，`this` 引用附加了事件监听器的元素，与 `event.currentTarget` 相同。

```js
button.addEventListener('click', function() {
  console.log(this); // 记录按钮的整个 HTML 内容
});
```
重要的是注意，它不引用常用的 `event.target` 属性。让我们澄清 `event.currentTarget` 和 `event.target` 之间的区别。

`event.currentTarget`：该属性引用附加了事件处理程序（如 addEventListener）的元素。这是在事件处理程序函数的上下文中 `this` 引用的内容。

`event.target`：该属性引用引发事件的实际 DOM 元素。对于会冒泡的事件特别重要。如果你点击内部元素，事件将冒泡到外部元素，触发它们的事件监听器。对于这些外部元素，`event.target` 将是实际被点击的最内层元素，而 `event.currentTarget`（或 `this`）将是当前处理程序附加到的元素。

```html
<div id="outer">点击我
  <div id="inner">或者点击我</div>
</div>

<script>
document.getElementById('outer').addEventListener('click', function(event) {
  console.log("currentTarget: ", event.currentTarget.id);
  console.log("this: ", this.id);
  console.log("target: ", event.target.id);
});
</script>
```
在这种情况下，如果你点击外部 `div`，所有三个日志都将打印 "outer"，因为点击的元素（`target`）和处理程序附加的元素（`currentTarget` 或 `this`）是相同的。但是，如果你点击内部 `div `中的 "或者点击我" 文本，`event.target` 将是 "inner"（因为这是你点击的元素），而 `event.currentTarget`（或 `this`）仍将是 "outer"（因为这是事件处理程序附加的元素）。

### 构造函数上下文

在构造函数内部，`this` 引用新创建的对象。但是，这里的“新创建”是什么意思呢？要理解这一点，我们需要探讨 JavaScript 中的 `new` 关键字。当你在函数调用之前使用 `new` 时，它告诉 JavaScript 进行四个操作：

1. 创建一个新的空对象。这不是一个函数、数组或 null，只是一个空对象。
2. 使函数内部的 this 引用这个新对象。新对象与构造函数内的 this 关联起来。这就是为什么 Person(name) 内的 this.name 实际上修改了新对象。
3. 正常执行函数。它像通常情况下执行函数代码一样执行。
4. 如果函数没有返回自己的对象，则返回新对象。如果构造函数返回一个对象，那个对象将被返回，而不是新对象。如果返回其他任何内容，将返回新对象。

new 关键字允许 JavaScript 开发者以面向对象的方式使用语言，从构造函数中创建实例，就像其他语言中的类一样。这也意味着构造函数内部的 this 关键字将像从基于类的语言中转换的开发者所期望的那样引用对象的新实例。

```js
function Person(name) {
  // 当使用 `new` 调用时，这是一个新的、空的对象
  this.name = name; // `this` 现在有一个 `name` 属性
  // 函数结束后，将返回 `this`，因为没有其他对象被函数返回
}

let john = new Person('John'); // `john` 现在是函数 `Person` 返回的对象，包含一个值为 'John' 的 `name` 属性
console.log(john.name); // 记录 "John"
```
### 类上下文

在类中，方法内部的 this 引用类的实例：

```js
class ExampleClass {
  constructor(value) {
    this.value = value;
  }

  logValue() {
    console.log(this.value);
  }
}

const exampleInstance = new ExampleClass('Hello');
exampleInstance.logValue(); // 记录 "Hello"
```
### 显式 / 隐式绑定

你还可以使用函数上的 .call()、.apply() 或 .bind() 方法来明确设置 this 的上下文：

```js
function logThis() {
  console

.log(this);
}

const obj1 = { number: 1 };
const obj2 = { number: 2 };

logThis.call(obj1); // 记录 obj1
logThis.call(obj2); // 记录 obj2

const boundLogThis = logThis.bind(obj1);
boundLogThis(); // 记录 obj1
```
### 绑定方法和永久 this 上下文

JavaScript 提供了一个名为 bind 的内置方法，允许我们设置方法中的 this 值。这个方法创建一个新函数，当调用时，将其 this 关键字设置为提供的值，以及在调用新函数时提供的一系列参数。

bind 方法的独特之处在于它创建了一个永久绑定的 this 值，无论后来如何调用该函数，都不会更改 this 的值。下面的示例演示了 bind 如何提供一种锁定函数中的 this 值的方法，在各种情况下都很有帮助，例如在设置事件处理程序时，希望 this 值始终引用特定对象，或者在使用调用回调函数的库或框架时，希望在回调中控制 this 引用的对象。

```js
function greet() {
  return `你好，我是 ${this.name}`;
}

let person1 = { name: 'Alice' };
let person2 = { name: 'Bob' };

// 创建一个与 `person1` 绑定的函数
let greetPerson1 = greet.bind(person1);

console.log(greetPerson1()); // 你好，我是 Alice

// 尝试使用 `call` 方法更改上下文；但是，它仍然使用 `person1` 作为 `this` 上下文
console.log(greetPerson1.call(person2)); // 你好，我是 Alice

// 相比之下，正常函数调用允许使用 `call` 方法设置 `this` 上下文
console.log(greet.call(person2)); // 你好，我是 Bob
```
在 JavaScript 中，了解 this 关键字的上下文对于操作和与对象交互非常重要，特别是在处理面向对象编程、事件处理程序和函数调用的某些方面。了解 this 的行为有助于改善代码的结构，并使其更可预测和更容易调试。此外，某些设计模式，如工厂模式和装饰器模式，大量使用 this，因此了解其行为对于有效实现这些模式至关重要。

JavaScript 中的一个关键概念是函数对象中的 this 值通常不是固定的 - 它通常是根据函数的执行上下文而确定的，而不是根据其定义的时刻。然而，也有例外情况。使用函数上的 bind()、call() 或 apply() 方法时，这些方法允许你显式设置函数调用的 this 值，从而覆盖其默认行为。此外，JavaScript 中的箭头函数行为不同。它们不绑定自己的 this 值。相反，它们从定义它们的外部词法环境中捕获 this 的值，并且这个值在函数的整个生命周期内保持不变。这些差异使得理解和使用 JavaScript 中的 this 既具有挑战性又非常重要。

