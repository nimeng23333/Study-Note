---
title: JS如何实现异步加载脚本，有什么区别？
---
1.动态创建script标签，给元素的src属性设置为需要加载的URL，检测可以用onload或者onreadystatechange事件来检测脚本是否加载完成。
```js
const script = document.createElement('script');
script.src = "path/script.js";
script.onload = function(){
	//回调函数
}
document.body.appendChild(script);
```

2.用XMLHttpRequest对象或Fecth API发送异步请求，在请求成功后将响应文本解析成为js代码，然后使用eval()或function()构造函数来执行脚本

```js
const xhr = new XMLHttpRequest();
xhr.open('GET','path/script.js')
xhr.onload = function(){
	const script = document.createElement('script');
	script.textContent = xhr.responseText;
	document.head.appendChild(script)
}
xhr.send()
```

### onload
当页面载入完毕后执行Javascript代码：

`<body onload="myFunction()">`

定义和用法

onload 事件会在页面或图像加载完成后立即发生。

onload 通常用于 `<body>` 元素，在页面完全载入后(包括图片、css文件等等。)执行脚本代码。

在 HTML 中:

`<body onload="_SomeJavaScriptCode_">`

在 JavaScript 中:

`window.onload=function(){_SomeJavaScriptCode_};`