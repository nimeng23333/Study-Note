---
title: instanceof
---
`instanceof`运算符用于检测一个对象是否是另一个对象的实例

`instanceof`专注于对象的类型（即原型链），能够准确判断对象是否为某个构造函数的实例

- 语法：`object instanceof constructor`
    - `object`：某个实例对象
    - `constructor`：某个构造函数

由于instanceof是基于原型链的检查，因此如果某个对象的原型链比较深，那么检查的效率会比较低

# **instanceof的原理**

instanceof运算符用于检测构造函数的 prototype 属性是否出现在某个实例对象的原型链上

### 1. **原型链的基本概念**

在 JavaScript 中，每个对象都有一个隐藏的 `[[Prototype]]` 属性（可通过 `__proto__` 访问），指向它的原型对象。原型对象自身也有 `[[Prototype]]`，形成链式结构，即**原型链**。例如：

```javascript
const obj = {};
// obj 的原型链：obj -> Object.prototype -> null
```

---

### 2. **构造函数与 `prototype` 属性**

构造函数（如 `function Person() {}`）的 `prototype` 属性是一个对象。当通过 `new` 创建实例时，实例的 `[[Prototype]]` 会指向构造函数的 `prototype`：

```javascript
function Person() {}
const p = new Person();
// p 的原型链：p -> Person.prototype -> Object.prototype -> null
```

---

### 3. **`instanceof` 的工作原理**

当执行 `obj instanceof Constructor` 时，`instanceof` 会：

2. 沿着 `obj` 的原型链（通过 `obj.__proto__`）逐层向上查找。
    
3. 检查是否存在一层原型等于 `Constructor.prototype`。
    
4. 如果找到，返回 `true`；否则返回 `false`。
    

**示例：**

```javascript
function Animal() {}
function Dog() {}
Dog.prototype = Object.create(Animal.prototype); // 继承 Animal

const dog = new Dog();

console.log(dog instanceof Dog);    // true（Dog.prototype 在原型链中）
console.log(dog instanceof Animal); // true（Animal.prototype 也在链中）
console.log(dog instanceof Object); // true（Object.prototype 在链中）
```

---

### 4. **动态性：`prototype` 修改的影响**

如果修改构造函数的 `prototype`，已创建实例的原型链不会自动更新：

```javascript
function Foo() {}
const obj = new Foo();

console.log(obj instanceof Foo); // true

Foo.prototype = {}; // 修改 prototype
console.log(obj instanceof Foo); // false（obj 的原型链仍指向旧的 Foo.prototype）
```

---

### 5. **特殊情况与注意事项**

- **原始类型值：** 原始值（如 `"hello"`、`42`）不是对象，`instanceof` 直接返回 `false`：
    
```javascript
console.log("str" instanceof String); // false
console.log(new String("str") instanceof String); // true
```
    
- **手动修改原型链：** 直接操作 `__proto__` 会影响 `instanceof`：
    
```javascript
const a = {};
a.__proto__ = null;
console.log(a instanceof Object); // false（原型链终止于 null）
```
    
- **右侧必须为函数：** 若右侧不是构造函数，会抛出错误：
    
```javascript
[] instanceof {}; // TypeError: Right-hand side of 'instanceof' is not callable
```
    

---

### 总结

- **`instanceof` 的本质**是检查构造函数（右侧）的 `prototype` 是否在对象（左侧）的原型链上。
    
- 依赖 JavaScript 的原型继承机制，通过逐层比对原型实现。
    
- 动态修改 `prototype` 或 `__proto__` 会直接影响结果。

手写instanceof
```js
function MyInsctanceof (obj, constructor) {
	let proto = Object.getPrototypeOf(obj);
	while(proto){
		if(proto === constructor.prototype){
		return true
		}
		proto = Object.getPrototypeOf(proto)
	}
	return false;
}
```

注意注意：Object.getPrototypeOf(obj)和constructor.prototype的区别

### 1. **`Object.getPrototypeOf(obj)`**

- **作用**：  
    返回对象 `obj` 的原型（即它的 `[[Prototype]]` 内部属性，可通过 `obj.__proto__` 访问）。
    
- **适用对象**：  
    所有对象（包括普通对象、函数、数组等）。
    
- **示例**：
    
```javascript
const obj = {};
const proto = Object.getPrototypeOf(obj); // 返回 obj 的原型
console.log(proto === Object.prototype); // true（obj 的原型是 Object.prototype）
```
    

---

### 2. `constructor.prototype`

- **作用**：  
    **只有函数对象才有 `prototype` 属性**。它表示当该函数作为构造函数时，通过 `new` 创建的实例的原型。
    
- **适用对象**：  
    仅函数对象（如构造函数）。
    
- **示例**：
    
```javascript
function Person() {}
console.log(Person.prototype); // 输出 Person 的 prototype 对象

const p = new Person();
console.log(p.prototype); // undefined（普通对象没有 prototype 属性！）
```
    

---

### 3. **关键区别**

| **特性**         | `Object.getPrototypeOf(obj)` | `constructor.prototype`             |
| -------------- | ---------------------------- | ----------------------------------- |
| **目标对象**       | 所有对象（对象、函数、数组等）              | 仅函数对象（构造函数）                         |
| **返回值**        | 对象的原型（`[[Prototype]]`）       | 函数作为构造函数时实例的原型                      |
| **普通对象是否有此属性** | 无（但可通过方法获取原型）                | 无（普通对象的 `.prototype` 为 `undefined`） |

---

### 4. **实际场景对比**

#### 场景 1：构造函数的原型

```javascript
function Animal() {}
const cat = new Animal();

// 1. 构造函数的 prototype 属性
console.log(Animal.prototype); // Animal 的 prototype 对象

// 2. 实例的原型（通过 Object.getPrototypeOf 获取）
console.log(Object.getPrototypeOf(cat) === Animal.prototype); // true
```

#### 场景 2：普通对象没有 `prototype` 属性

```javascript

const obj = { name: "John" };
console.log(obj.prototype); // undefined（普通对象没有 prototype！）
console.log(Object.getPrototypeOf(obj)); // Object.prototype（正确获取原型的方式）
```

---

### 5. **常见误区**

#### 错误用法

```javascript
const arr = [1, 2, 3];
console.log(arr.prototype); // undefined（数组实例没有 prototype 属性！）

// 正确用法：获取数组的原型
console.log(Object.getPrototypeOf(arr) === Array.prototype); // true
```

#### 函数对象的特殊性

```javascript
function Foo() {}
// 函数的 prototype 属性存在（用于构造实例的原型）
console.log(Foo.prototype); // {} 

// 函数本身的原型是 Function.prototype
console.log(Object.getPrototypeOf(Foo) === Function.prototype); // true
```

---

### 6. **总结**

- **`Object.getPrototypeOf(obj)`**：  
    用于获取任意对象（包括函数、数组、普通对象）的原型。
    
- **`constructor.prototype`**：  
    仅存在于函数对象中，表示该函数作为构造函数时，实例的原型。普通对象没有此属性！

1. 对象有`__proto__`属性，函数有`__proto__`属性，数组也有`__proto__`属性，只要是引用类型，就有`__proto__`属性，指向其原型。
2. 只有函数有`prototype`属性，只有函数有`prototype`属性，只有函数有`prototype`属性，指向`new`操作符加调用该函数创建的对象实例的原型对象。

## 原型链顶层

原型链之所以叫原型链，而不叫原型环，说明它是有始有终的，那么原型链的顶层是什么呢？

拿我们的`person`对象来看，它的原型对象，很简单

```javascript
// 1. person的原型对象
person.__proto__ === Person.prototype
```

接着往上找，`Person.prototype`也是一个普通对象，可以理解为`Object`构造函数创建的，所以得出下面结论，

```javascript
// 2. Person.prototype的原型对象
Person.prototype.__proto__ === Object.prototype
```

`Object.prototype`也是一个对象，那么它的原型呢？这里比较特殊，切记！！！

```javascript
Object.prototype.__proto__ === null
```

我们就可以换个方式描述下 **原型链** ：由对象的`__proto__`属性串连起来的直到`Object.prototype.__proto__`（为`null`）的链就是原型链。

**原型对象**中默认有一个`constructor`属性，指回该构造函数。

```javascript
Person.prototype.constructor === Person // true
```

```js
function MyInstanceOf(obj,constructor){
	let proto = Object.getPrototypeOf(obj);
	while(proto){
		if(proto === constructor.prototype){
			return true;
		}
		proto = Object.getPrototypeOf(proto)
	}
	return false
}
```

# 构造函数的继承

1.绑定 用call或者apply方法
```js
function Cat(name, color){
	Animal.apply(this, arguments);
	this.name = name;
	this.color = color;
}
```

2.prototype方法（更常见）
```js
	Cat.prototype = new Animal();
	Cat.prototype.constructor = Cat;
```

如果没有"Cat.prototype = new Animal();"这一行，Cat.prototype.constructor是指向Cat的；加了这一行以后，Cat.prototype.constructor指向Animal。

`alert(Cat.prototype.constructor == Animal); //true`

更重要的是，每一个实例也有一个constructor属性，默认调用prototype对象的constructor属性。

`alert(cat1.constructor == Cat.prototype.constructor); // true`

因此，在运行"Cat.prototype = new Animal();"这一行之后，cat1.constructor也指向Animal！

`alert(cat1.constructor == Animal); // true`

这显然会导致继承链的紊乱（cat1明明是用构造函数Cat生成的），因此我们必须手动纠正，将Cat.prototype对象的constructor值改为Cat。这就是第二行的意思。

这是很重要的一点，编程时务必要遵守。下文都遵循这一点，即如果替换了prototype对象，

`o.prototype = {};`

那么，下一步必然是为新的prototype对象加上constructor属性，并将这个属性指回原来的构造函数。

`o.prototype.constructor = o;`


## 拓展
需要注意的是，instanceof运算符只能用于检查对象是否是某个构造函数的实例，不能用于基本类型（如字符串、数字等）的检查。如果检查的对象不是一个对象类型，instanceof会输出false。此外，由于instanceof是基于原型链的检查，因此如果某个对象的原型链比较深，那么检查的效率会比较低。

instanceof操作符判断的是对象的原型链，因此如果一个对象是某个类的实例，那么它一定是该类的原型链上的某个对象的实例。因此，如果一个对象的原型链上没有该类的原型对象，那么它就不是该类的实例，即使它与该类具有相同的属性和方法。