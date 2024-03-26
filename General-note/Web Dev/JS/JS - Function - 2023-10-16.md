---
title: JS - Function - 2023-10-16
---
```
function functionName(){
内容
}
//创建函数
functionName()
//调用函数

function functionName(){
内容
return 输出;
}
var result = functionName(5);
```

## if和while

if条件句判断是否相等用三个等于号
!== 不等于
```
if (n === 100){

}else{

}
if (n !== 100){

}else{

}
```
两个等于符号的时候不会检查数据类型
eg：
```
var a = 1;
var b = "1";
if (a == b){
"yes"
}else{
"no"
}

//输出结果将会是yes
```

&& 和
|| 或
! 非

while 循环
```
while (i < 2 ){
i++;
}
//中间的判断成立运行while，运行后回到while前再次判断
```

for 循环
```
for (var i=0; i<2; i++){

}
```

while循环用来确定状态，for循环用来迭代

forEach循环(EJS示例)
```
<ul>
    <% items.forEach((fruit)=>{ %>
      <li>
        <%= fruit %>
      </li>
    <% }) %>
 </ul>

//items是数组的名字，fruit相当于是i，感觉可以理解为回调函数里的传参名
```

## switch

Switch条件句用来切换
```
switch(判断的东西){
	case "判断的条件1":
		代码1;
	break;
	case "判断的条件2":
		代码2;
	break;
	case "判断的条件3":
		代码3;
	break;
	default:
		代码4;
}
```


# 函数嵌套函数

```
function add (num1, num2){
	return num1+num2;
}
function multiply (num1, num2){
	return num1*num2;
}
function calculator (num1, num2, operator){
	return operator(num1,num2);
}
```

# 音频
```
 var audio = new Audio("地址");
     audio.play();
```

# 创建对象

```
var box1 = {
	attribute1:10,
	attribute2:true,
	attribute3: [1,2,3],
}
```
注意：这里用逗号分隔

使用函数构造对象Construction Function
```
function Box(attribute1,attribute2,attribute3){
this.attribute1= attribute1;
this.attribute2= attribute2;
this.attribute3= attribute3;
}
```
注意：！！构造对象必须大写Box

构造完以后调用该对象创建实例：
```
var box1 = new Box("hello",true,[1,2,3]);
```

在构造对象的时候也可以给对象函数：
```
var box1 = {
	attribute1:10,
	attribute2:true,
	attribute3:function(){...}
}

box1.attribute3();
```

```
function Box(attribute1,attribute2,attribute3){
this.attribute1 = attribute1;
this.attribute2 = attribute2;
this.attribute3 = attribute3;
this.attribute4 = function(){...}
}
```

## 字符串数组去重


通过对象属性标记为1来去重，时间为O(n)
```js
function getCategory (data) {
    var obj = {};
    var category = [];
    for (var i = 0; i < data.length; i++) {
      if (!obj[data[i]]) {
        category.push(data[i]);
        obj[data[i]] = 1;
      }
    }
    return category;
  }
  var api = ['bilibililalala','bilibililalala',"sdfw","eawfaw"]
  console.log( getCatagery(api)) ;
```

### 正则表达式 regular expression RegExp

正则表达式（Regular Expression），在代码中常简写为regex、regexp或RE，是一种用于匹配和操作文本的工具。它是由一系列字符和特殊字符组成的模式，用于描述要匹配的文本模式。正则表达式可以在文本中查找、替换、提取和验证特定的模式。

在JavaScript中，RegExp对象用于将文本与一个模式匹配。例如，`var rex = new RegExp($(this).val(), 'i'); `这行代码创建了一个新的正则表达式对象。正则表达式的模式是用户在输入框中输入的文本，'i'标志表示不区分大小写。

去掉所有的空格：
```js
xxx.replace(/[^a-zA-Z0-9]/g , '')
```

将/替换为-：
```js
xxx.replace(/\//g,'-')
```
注意，正则表达式中的 / 需要用反斜杠 \ 转义，因为 / 本身是正则表达式的界定符

##### 转义

反斜杠在正则表达式中是用来转义的，也就是让特殊字符失去它们的特殊含义，变成普通字符。

除了转义正则表达式中的元字符，如 `. $ ^ { [ ( | ) * + ? \ `等，还有一些情况需要用到转义，例如：
转义字符串中的特殊字符，如换行符 `(\n) `，制表符` (\t) `，引号` (\")` 等。
转义 Unicode 字符，如中文字符 `(\u4E2D) `，表情符号` (\uD83D\uDE00)` 等。
转义 HTML 标签，如` < (\<) `，`> (\>) `，`& (\&) `等。
[正则表达式 转义字符|极客笔记 (deepinout.com)](https://deepinout.com/regexp/regexp-tutorials/10_regular_expression_escape_character.html)

[最实用的正则表达式整理 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/30573054)

## JS 时间
[Date - JavaScript | MDN (mozilla.org)](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date)

```js
const day = new Date();
const jsonDate = day.toJSON(); //将时间转化为JSON也就是iso标准格式
console.log(jsonDate);
console.log(new Date(jsonDate).toUTCString());
```
输出内容：
> "2023-12-01T09:21:02.178Z" 
> "Fri, 01 Dec 2023 09:21:02 GMT"

[Intl.DateTimeFormat - JavaScript | MDN (mozilla.org)](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Intl/DateTimeFormat)

时间格式化
```JS
let date = new Date('2023-12-07T16:00:00.000Z');
let options = { year: 'numeric', month: '2-digit', day: '2-digit' };
let formatter = new Intl.DateTimeFormat('zh-CN', options);
console.log(formatter.format(date)); // 输出: '2023/12/07'
```

```HTML
<%=  new Intl.DateTimeFormat('zh-CN', { year: 'numeric', month: '2-digit', day: '2-digit'}).format(item.date) %>

<%= new Intl.DateTimeFormat('zh-CN', { year: 'numeric', month: '2-digit', day: '2-digit'}).format(new Date()) %> //输出成今天 2023/12/15

<%= new Intl.DateTimeFormat('zh-CN', { dateStyle: 'short' }).format(new Date()) %> //输出成今天 2023/12/15

<%= new Intl.DateTimeFormat().format(new Date()) %> //输出成今天 2023/12/15
<%= new Intl.DateTimeFormat().format(new Date()).replace(/\//g,'-') %> //输出成今天 2023/12/15

```

### 字符相关

xxx.trim()函数可以去掉字符串前后的空格
xxx.toLowerCase()将字符全小写

### async 与 await

在async里才能用await
```js
async function showDisplay(){
  const result = await db.query("SELECT country_code FROM visited_countries");
  let countries = [];
  result.rows.forEach((country)=>{
    countries.push(country.country_code);
  })
  return countries; //返回的数值return
}


app.post("/add", async(req,res) =>{
  const newCountry = req.body.country.slice(0,1).toUpperCase() + req.body.country.slice(1).toLowerCase();
  const newCountryCode = await db.query("SELECT country_code FROM countries WHERE country_name LIKE $1", [newCountry + '%']);
  await db.query("INSERT INTO visited_countries(country_code) VALUES ($1)" ,[newCountryCode.rows[0].country_code]);
  const countries = await checkVisited();   //注意！！async的函数调用时也要await
  res.render("index.ejs", {countries : countries, total: countries.length})
  res.redirect("/");

})
```

## 排序和比大小

`arr.sort()`排序函数
localCompare比较字符串大小
```js
//定义一个函数，比较两个js对象的name属性在字典排序先后
function compareByName(a, b) {
  // 获取 a 和 b 的 name 属性的值，分别赋值给变量 nameA 和 nameB
  var nameA = a.name;
  var nameB = b.name;
  // 使用 nameA 和 nameB 的 localeCompare 方法，来比较两个字符串的字典顺序
  // 返回比较的结果，作为比较函数的返回值
  return nameA.localeCompare(nameB);
}
  arr.sort(compareByName); //将内容按照name属性名排序
```


### 可选链运算符( ?.)

可选链运算符（?.）允许读取位于连接对象链深处的属性的值，而不必明确验证链中的每个引用是否有效。?. 运算符的功能类似于 . 链式运算符，不同之处在于，在引用为空 (nullish ) (null 或者 undefined) 的情况下不会引起错误，该表达式短路返回值是 undefined。与函数调用一起使用时，如果给定的函数不存在，则返回 undefined。

当尝试访问可能不存在的对象属性时，可选链运算符将会使表达式更短、更简明。在探索一个对象的内容时，如果不能确定哪些属性必定存在，可选链运算符也是很有帮助的。

```js
const adventurer = {
  name: 'Alice',
  cat: {
    name: 'Dinah',
  },
};

const dogName = adventurer.dog?.name;
console.log(dogName);
// Expected output: undefined

console.log(adventurer.someNonExistentMethod?.());
// Expected output: undefined
```


### 表单清空

当主体是form的时候，整体form清空用reset()方法

`formRef.current.reset();`


### 获取地址、获取坐标 （浏览器内置行为）

```js
navigator.geolocation.getCurrentPosition( (position) => {
	//回调函数
	console.log(position.coords.latitude, position.coords.longitude)
});
```

当获取到坐标的时候，就能执行里面的回调函数
