
alert("内容"); 弹出一个警告窗口
alert(2+3); 弹出一个显示5的警告窗口

console.log()在控制台输出内容

typeof(...)控制台输出内容的类型

prompt("提示词"); 弹出一个带提示词的表单窗口

var 变量名 = "变量内容"; 给变量赋值
eg:
```
var myNumber = "1";
var yourNumber = prompt("Your Number");
alert("My number is " + myNumber + " and your number is " + yourNumber);
```

# string
string.length; 显示字符的长度

slice(x, y); 截取从x到y（但不包含y）的内容
eg:
```
tweet.slice(0,3);
/*截取0、1、2三个字*/
```

toUpperCase(); 大写
toLowerCase(); 小写
eg:
```
tweet.toUpperCase();
```

字符中插入内容用``` `${}` ```
eg:
```
console.log(`My name is ${nameList[0]}.!`)
```
注意这里的引号不是用正常的引号，是``` ` ```~这个键

# 计算
```
var a = 3*3;
var a = 3/3;
var a = 9%6; 得到除法的余数 modulo 结果得到3
```

增量和减量
```
var x = 5;
x++; /*结果x是6，等价于x=x+1*/
x--;
x +=2; 结果是7

var y = 3;
x +=y; 结果是8

x -=
x *=
x /=
```

特别需要注意：
![[Pasted image 20231016140100.png]]
在 var y = x++; 里面y先被赋予的x的值，然后x再增加，因此在这个等式时候y=x，x=x+1

取整：
var result = Math.floor(8/1.5) //向下取整得到5
var result = Math.round(8/1.5) //四舍五入取整

平方：
var result = Math.pow(6,2)   //得到36

随机：
var result = Math.random()   //0-1之间的16位小数随机数，不包含1
```
var n = Math.random()
n = n*6
n = Math.floor(n) + 1;
///取得1-6的随机数

var n = Math.random();
n = Math.floor(n*6)+1;
```
但这个是伪随机，非真随机

# 数组
```
var array = [1,2,6]; //设定一个数组
array[0]; //数组的第1个内容
array.iucludes(1); //检测数组内容是否包含，返回true和false
```

向数组里面增加东西用push
```
var output = [];
output.push(1);
```
将数组里的东西拿出来用pop
```
output.pop(0);
```

