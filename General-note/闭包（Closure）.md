---
title: 闭包
---
[函数式编程 | 高阶函数 | 纯函数 | 闭包 | 柯里化 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/430126285)
## 闭包（Closure）

- **定义**：闭包是一个函数以及其捆绑的周边环境状态（词法环境）的引用的组合。换句话说，闭包允许内部函数访问外部函数的作用域。
- **作用**：
    - 延长外部函数内部变量的作用域范围，使得这些变量不会被释放。
    - 允许在另一个作用域中调用一个函数的内部函数，并访问到该函数的作用域中的成员。

### 示例


```javascript
function makePower(power) {
	return (number) => {
		return Math.pow(number, power);
	};
}

const power2 = makePower(2);

console.log(power2); //(number) => {return Math.pow(number, power);} 输出一个匿名函数，接受一个number的参数

console.log(power2(3)); // 输出 9
console.log(makePower(2)(3)) // 输出 9
```


1. 首先，我们定义了 `makePower` 函数，它接受一个参数 `power`。
2. 在函数体内，我们返回了一个新的函数，这个新函数是一个闭包（closure）。闭包是一个函数，它“记住”了它被创建时的环境，包括外部函数的参数和局部变量。
3. 新函数接受一个参数 `number`。
4. 在新函数的函数体内，我们使用 `Math.pow(number, power)` 来计算 `number` 的 `power` 次方。
5. 最后，我们返回计算结果。

## Thunk

Thunk 是一类函数的别名，主要特征是对另外一个函数添加了一些额外的操作，类似装饰器。其主要用途为延迟函数执行（惰性求值）或者给一个函数执行前后添加一些额外的操作。

thunk本质上是一个封装了闭包函数的高阶函数

```js
function fetchUser(id) {
  // 返回的这个函数既是一个 Thunk, 或者叫 ThunkAction
  return async ( dispatch ) => {
    // 额外的异步API调用
    const user = await api.getUser(id);
    // 此时才真正dispatch action
    dispatch({ type: 'UPDATE_USER', payload: user });
  }
}
```

```js
dispatch(fetchUser(1))
```
只有在redux可以用这种语法，否则是`fetchUser(1)(dispatch)`