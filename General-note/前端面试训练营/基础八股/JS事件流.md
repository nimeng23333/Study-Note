---
title: JS事件流
---
JS事件流 event flow是指浏览器处理事件的方式

事件流分为三个阶段：捕获阶段、目标阶段、冒泡阶段。按顺序依次进行。
从文档开始由外到内，再由内到外的顺序。
捕获阶段：从文档对象开始 window、document、html、body、div、span的顺序。直到事件到达目标节点。
目标阶段：事件到达目标节点，触发事件处理函数。
冒泡阶段：从内到外，事件向上传播，直到到达文档对象。

如果想要停止事件的传播可以用`event.stopPropagation()`
需要注意的是你的事件绑定的监听器是捕获阶段还是冒泡阶段。监听冒泡阶段是默认设置
`element.addEventListener(evnent, handler, useCaputer)`
当有4个嵌套的div1-div4
```js
div1.addEventListener("click", () => {console.log("div1")},true)
div2.addEventListener("click", () => {console.log("div2")})
div3.addEventListener("click", () => {
	console.log("div3");
	event.stopPropagation();
	})
div4.addEventListener("click", () => {console.log("div4")})
```

输出div1、div4、div3。因为div1设置了捕获阶段，而div2是冒泡阶段，被div3的监听器阻止了事件的传播。

- `event.stopPropagation()` 在 `div3` 的监听器中调用：
    
    - 仅阻止事件向**父元素继续传播**
        
    - **不影响**同一元素（`div3`）上的监听器执行
        
    - **不影响**捕获阶段或目标阶段的执行

与`event.stopPropagation()`类似还有`event.stopImmediatePropagation()`
区别在于如果一个元素上绑定了多个事件处理程序，调用该方法会立即停止事件传播，并且不会触发同一元素上的其他事件处理程序。

```js
div4.addEventListener("click", ()=>{
	console.log("div4");
	event.stopPropagation();
})
div4.addEventListener("click", ()=>{
	console.log("div4-1");
})
```
输出div4、div4-1
```js
div4.addEventListener("click", ()=>{
	console.log("div4");
	event.stopImmediatePropagation();
})
div4.addEventListener("click", ()=>{
	console.log("div4-1");
})
```
输出div4

事件监听器的执行顺序是按照它们添加的顺序进行的
```js
div4.addEventListener("click", ()=>{
	console.log("div4-1");
})
div4.addEventListener("click", ()=>{
	console.log("div4");
	event.stopImmediatePropagation();
})
```
输出div4-1、div4

如果要取消默认事件行为使用`event.preventDefault()`




