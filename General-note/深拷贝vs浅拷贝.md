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

方法 1：递归方法
概述
由于这是一个深度嵌套结构，解决方法中将涉及一些重复步骤，因此递归是解决此问题的合适方法。

算法
我们创建一个递归函数 flattening，该函数接受数组 nums 和层级 l 作为参数。

在 flattening 中，我们使用 for...of 循环来迭代 nums 数组的元素。

对于每个元素，我们检查它是否是数组以及层级 l 是否在指定范围内（l > 0 且 l <= n）。

如果元素是数组且满足层级条件，我们会递归调用 flattening，并将嵌套数组和层级减小 1（即 l - 1）传递给它。

如果元素不是数组或层级条件不满足，我们将元素推送到结果数组 res 中。

最初，我们使用数组 arr 和深度 n 作为参数调用 flattening 函数以启动扁平化过程。

最后，将包含扁平化元素的 res 数组作为结果返回。

```js
var flat = function(arr,n){
	const res = [];

	const flatten = (nums, level) =>{
		for(const num of nums){
			if(Array.isArray(num) && level > 0){
				flatten(num,level - 1 )
			}else{
				res.push(num)
			}
		}
	}
	flatten(arr,n)
	return res
}
```

# 手写深拷贝

```js
function deepClone(obj, map = new WeakMap()){
	//1.判断是否为对象
	if(obj === null || typeof obj !== "object"){
		return obj
	}
	//2.判断特殊对象
	if(obj instanceof Date){
		return new Date(obj)
	}
	if(obj instanceof RegExp){
		return new RegExp(obj)
	}
	
	//3.处理循环引用，避免无限递归
	if(map.has(obj)){
		return map.get(obj)
	}
	
	//4.初始化对象，数组为[]，对象为[]
	const cloneObj = Array.isArray(obj) ? [] : {};
	
	//保持引用关系
	map.set(obj, cloneObj)
	
	//5.递归拷贝
	for(const key in obj){
		if(Object.prototype.hasOwnProperty.call(obj, key)){ //检查一个对象自身是否包含某个特定的属性，而不是继承自原型链的属性。
			cloneObj[key] = deepClone(obj[key], map)
		}
	}
	
	return cloneObj
}
```

### Object.prototype.hasOwnProperty.call(obj, key)为什么需要这个检查？

在 JavaScript 中，当你使用 `for...in` 循环遍历一个对象时，它不仅会遍历对象自身的属性，还会遍历其**原型链**上可枚举的属性。
`Object.prototype.hasOwnProperty.call(obj, key)` 是一种安全且通用的写法，它能够**只遍历对象自身的属性**，从而确保深拷贝的正确性。它能有效地避免原型链上的属性被错误地拷贝到新对象中，保证了函数的健壮性。

`Object.prototype.hasOwnProperty.call(obj, key)` 等价于`obj.hasOwnProperty(key)` 
`obj.hasOwnProperty(key)` 这种写法依赖于 `obj` 对象本身是否拥有 `hasOwnProperty` 这个方法。如果 `obj` 对象是一个自定义的对象，并且它重写（覆盖）了 `hasOwnProperty` 方法，那么这种写法可能会得到错误的结果。当然在大多数现代项目中，可能很少会遇到重写 `hasOwnProperty` 的情况。使用 `Object.prototype.hasOwnProperty.call()` 是一种**防御性编程**。

```js
function deepClone(obj, map = new WeakMap()){
	//1.检查是否为非对象
	if(obj === null || typeof obj !== "object"){
		return obj
	}
	//2.检查特殊对象
	if(obj instanceof Date){
		return new Date(obj)
	}
	if(obj instanceof RegExp){
		return new RegExp(obj)
	}
	
	//3.避免循环引用
	if(map.has(obj)){
		return map.get(obj)
	}
	
	//4.初始化
	const cloneObj = Array.isArray(obj) ? [] : {}
	map.set(obj, cloneObj)
	
	//5.递归拷贝
	for(const key in obj){
		if(obj.hasOwnProperty(key)){
			cloneObj[key] = deepClone(obj[key], map)
		}
	}
	
	return cloneObj
}
```