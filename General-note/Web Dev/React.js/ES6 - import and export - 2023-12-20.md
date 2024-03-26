---
title: ES6 - import and export - 2023-12-20
---

es6支持import和export
在export文件里至少有一个default export，如果你有额外需要export的内容，需要加花括号

如自创一个math.js的内容，要将它运用到index.js里

math.js
```js
const pi = 3.1415926;

function doublePi(){
	return pi*2;
}

function triplePi(){
	return pi*3;
}

export default pi;
export {doublePi, triplePi};
```
index.js
```js
import pi,{doublePi, triplePi} from "./math.js" //这里的pi可以取自己随意的名字，因为是default，但后面两个花括号里的必须保持一致

{pi}
{doublePi()}
{triplePi()} //后面两个是function，需要加括号，第一个输出是个常量，不需要括号
```
```js
import * as pi from "./math.js" //这里的pi可以取自己随意的名字，因为是default，但后面两个花括号里的必须保持一致

{pi.default}
{pi.doublePi()}
{pi.triplePi()} //后面两个是function，需要加括号，第一个输出是个常量，不需要括号
```