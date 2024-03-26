---
title: React.js - 2023-12-19
---

前端框架，可以对比前后DOM tree的区别并进行局部更新
可以制作组件，每个组件都可以有自己的html css和js，然后进行组合

jsx代码插入html的时候需要声明是jsx
```html
<script src="../src/index.js" type="text/JSX"></script>
``` 

```html
<div id="root"></div> //所有react创建的内容都会被在root里
```

```jsx
var React = require("react");
var ReactDom = require("react-dom");
ReactDom.render(
  <div>
    <h1>Hello World!</h1>
    <p>This is a paragraph.</p>
  </div>,
  document.getElementById("root")
);
```

[Babel · Babel (babeljs.io)](https://babeljs.io/)
ES6的出现给javascript带来了新鲜活力，然而当新技术应用实际，有一种痛叫做“浏览器不支持”。
有问题就得解决，Babel工具就是将ES6的语法转化为浏览器可识别的ES5代码，以便在老旧的浏览器运行。按照其官网上的介绍：“Babel 是一个 JavaScript 编译器”。

JSX
JSX是一种JavaScript的语法扩展，首先运用于React中，其格式比较像是模版语言，但事实上完全是在JavaScript内部实现的。元素是构成React应用的最小单位，JSX就是用来声明React当中的元素。React主要使用JSX来描述用户界面，但React并不强制要求使用JSX ，而JSX也在React之外的框架得到了广泛的支持，包括Vue.js，Solid等。

Babel转译器会把JSX转换成一个名为React.createElement()的方法调用。

```jsx
function formatName(user) { 
	return user.firstName + ' ' + user.lastName; 
} 
const user = { 
    firstName: 'Harper', 
    lastName: 'Perez' 
}; 
const element = ( 
	<h1> 
		Hello, {formatName(user)}! 
	</h1> 
); 
ReactDOM.render( 
	element, 
	document.getElementById('root') 
);
```

JSX本身其实也是一种表达式

在编译之后，JSX会被转化为普通的JavaScript对象。所以可以在if或者for语句里使用JSX，将它赋值给变量，当作参数传入，作为返回值都是允许的：

```jsx
function getGreeting(user) { 
	if (user) { 
		return <h1>Hello, {formatName(user)}!</h1>; 
	} 
	return <h1>Hello, Stranger.</h1>; 
}
```

js变量用{}插入html中

```jsx
var React = require("react");
var ReactDom = require("react-dom");
const fName = "Henry";
const lName = "Dan";
ReactDom.render(
	<h1>Hello { `${fName} ${lName}`}!</h1> //在jsx中插入一个js expression，这个expression用了js里的往字符串里插入变量的`${}`方法
	document.getElementById("root")
);
```

```jsx
import React from "react";
import ReactDom from "react-dom";

const fName = "Angela";
const lName = "Yu";

ReactDom.render(
  <div>
    <p>Created by {fName + "" + lName}</p>
    <p>Copyright {new Date().getFullYear()}.</p>
  </div>,
  document.getElementById("root")
);
```

>!! 注意 jsx里的html只是看起来像html，但实际不一样，在attribute上需要写成camelCase形式的名字比如html的class在jsx里是className，contenteditable在jsx里是contentEditable

```jsx
import React from "react";
import ReactDom from "react-dom";

const fName = "Angela";
const lName = "Yu";

ReactDom.render(
  <div>
    <h1 className="class1"> Title </h1>
  </div>,
  document.getElementById("root")
);
```

```jsx
import React from "react";
import ReactDOM from "react-dom";

ReactDOM.render(
	<h1 contentEditable="true" spellCheck="false">My Favourite Foods</h1>, //camelCase形式写attributes
	document.getElementById("root")
);
```

JSX的语法比HTML严格
`<img className="img" src="https://picsum.photos/id/1/200" alt="" />`最后的/一定要有，否则会报错

JSX里的内联样式
```JSX
<h1 style={{color:"red"}}> Title </h1>
```
第一个花括号是代表插入js，第二个花括号代表一个js object
在jsx里插入style需要用js object
也就是
```JSX
const customStyle = {
	color: "red",
	height: "2px",
	fontSize: "20px"
};

customStyle.color = "blue";
```

### react组件

在同一个文件里：
```jsx
function Heading(){  //一定要大写首字母，以区分原生html标签
	return <h1>Title</h1>
}

ReactDOM.render(
	<Heading />, //function名字+空格+斜杠
	document.getElementById("root")
```

分开成几个文件：
index.js
```jsx
import React from "react";
import ReactDOM from "react-dom";
import Heading from "./Heading.jsx";
import Unorderlist from "./Ul.jsx";
  
ReactDOM.render(
  <div>
    <Heading />
    <Unorderlist />
  </div>,
  document.getElementById("root")
);
```

Heading.jsx
```jsx
import React from "react";
function Heading() {
  return <h1>My Favourite Foods</h1>;
}
export default Heading;
```

Ul.jsx
```jsx
import React from "react";
function Unorderlist() {
  return (
    <ul>
      <li>Bacon</li>
      <li>Jamon</li>
      <li>Noodles</li>
    </ul>
  );
}
export default Unorderlist;
```

更常见做法：将index.js再拆分出一个app.jsx

index.js:
```jsx
import React from "react";
import ReactDOM from "react-dom";
import App from "./App.jsx";
  
ReactDOM.render(
  <app />,
  document.getElementById("root")
);
```

app.jsx:
```jsx
import React from "react";
import Heading from "./Heading.jsx";
import Unorderlist from "./Ul.jsx";
  
function App(){
	return(
	<div>
		<Heading />
		<Unorderlist />
	</div>
	)
}

export default App;
```

报错解决：
```
Error: error:0308010C:digital envelope routines::unsupported
```
[node.js - Error message "error:0308010C:digital envelope routines::unsupported" - Stack Overflow](https://stackoverflow.com/questions/69692842/error-message-error0308010cdigital-envelope-routinesunsupported)

```command
set NODE_OPTIONS=--openssl-legacy-provider
```
```command
npm audit fix --force
```

[npm audit 报错 501 Method Not Implemented npm/v1/security/audits/quick not implemented yet - 糖~豆豆 - 博客园 (cnblogs.com)](https://www.cnblogs.com/sugartang/p/17637689.html)


### 获取元素


`getElementsByTagName` 返回的是一个 `HTMLCollection`，而不是一个数组，所以它没有 `map` 方法。可以使用 `Array.from` 或者扩展运算符 `...` 来将 `HTMLCollection` 转换为数组

```js
const sections = document.getElementsByTagName("section");
Array.from(sections).map((section) =>{
	const rect = section.getBoundingClientRect();
	const isInView = rect.top < window.innerHeight && rect.bottom >= 0;
	console.log(section,isInView);
});
```
