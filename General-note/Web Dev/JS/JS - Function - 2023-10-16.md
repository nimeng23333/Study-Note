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