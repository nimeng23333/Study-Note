---
title: ES6新特性
---
1.块级作用域
2.箭头函数
3.模版字符串
4.解构赋值
5.类和继承，class和extends
6.Promise和async/await

# 箭头函数与普通函数的区别

1.没有自己的上下文，无法使用call、apply、bind改变上下文
2.没有自己的arguments对象，只能用rest参数或者展开运算符
3.不能作为构造函数，不能用new创建一个新的实例

**关于箭头函数能否作为构造函数的问题，根据规范来说，箭头函数是没有 Construct方法的，因此不能使用 new 关键字创建对象**。如果强制使用 new 关键字调用箭头函数，会抛出一个类型错误。因此，一般来说箭头函数不应该用于创建对象，而应该用于函数式编程和简化回调函数等场景。

## 类和继承

ES6 类继承主要使用两个关键字：

| **关键字** | **作用** | **描述**                 |
| ------- | ------ | ---------------------- |
| class   | 定义类    | 用于声明一个类（构造函数和方法）。      |
| extends | 建立继承关系 | 用于指定一个类继承自另一个父类（或基类）。  |
| super   | 调用父类   | 在子类中用于调用父类的构造函数或父类的方法。 |
```js
// ----------------- 父类（基类） -----------------
class Animal {
  constructor(name) {
    this.name = name;
  }

  // 父类方法
  speak() {
    console.log(`${this.name} 发出声音。`);
  }
}

// ----------------- 子类（派生类） -----------------
class Dog extends Animal {
  constructor(name, breed) {
    // 1. 必须首先调用 super()
    // 它调用父类（Animal）的构造函数，并初始化继承自父类的属性（如 this.name = name）
    super(name); 

    // 2. 才能访问或设置 this
    this.breed = breed; 
  }

  // 子类特有的方法
  wagTail() {
    console.log(`${this.name} (一只 ${this.breed} 狗) 摇尾巴。`);
  }
  
  // 3. 方法重写（Override）
  speak() {
    // 调用父类的 speak 方法
    super.speak();
    console.log(`${this.name} 汪汪叫！`);
  }
}

// 实例化子类
let myDog = new Dog("Buddy", "金毛");

myDog.speak(); 
// 输出:
// Buddy 发出声音。
// Buddy 汪汪叫！

myDog.wagTail(); 
// 输出:
// Buddy (一只 金毛 狗) 摇尾巴。

console.log(myDog instanceof Dog);    // true
console.log(myDog instanceof Animal); // true
```

`super(...)` 就是将子类接收到的参数**转发**给父类构造函数。
也就是说super的参数是父类constructor需要的所有参数