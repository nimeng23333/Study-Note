---
title: new
---
# new 的原理
new 运算符创建一个用户定义的对象类型的实例或具有构造函数的内置对象的实例

```js
function Person(age, name){
 this.age = age;
 this.name = name;
}

var john = new Person(18, "John");
console.log(john)
```

```js
function myNew(Constructor, ...args){
	var obj = Object.create(Constructor.prototype);
	var result = Constructor.apply(obj, args);
	return (typeof result === "object" && result !== null )? result : obj
}
```

```js
function Person(name, age) {
 this.name = name;
 this.age = age;
}
var john = myNew(Person, 'John', 30);
console.log(john); // {name: 'John', age: 30}
```

new操作符
创建一个由用户自定义对象类型的实例或者含有构造函数的内置对象类型的实例

具体：
1.创建一个新的空对象，对象的prototype是constructor的prototype
2.this绑定为空对象
3.执行函数
4.显式返回对象则返回这个对象，否则返回空对象

手写new
```js
function myNew(Constructor, ...args){
	const obj = Object.create(Constructor.prototype)
	const result = Constructor.apply(obj, args)
	return (typeof result === "object" && result !== null ) ? result : obj
}
```