---
title: break、continue、return 三种跳出循环的方式
---
## Break语句: 立刻终止当前的for循环
break语句会使运行的程序立刻退出包含在最内层的循环或者退出一个switch语句。
由于它是用来退出循环或者switch语句的, 所以只有当它出现在这些语句的时候, 这种形式的break语句才是合法的。
如果一个循环的终止条件非常复杂, 那么使用break语句来实现某些条件比用一个循环表达式所有的条件容易得多。
```js
for(var i = 519; i < 550; i++) {
	if(i == 522) {
		break;
	}
	console.log(i);
	alert(i);
	document.write(i);
```

当i = 521的时候，直接退出for这个循环。这个循环将不再被执行。


## Continue语句: 跳出本次循环，进入下一次循环（不终止当前for循环）
continue语句和break语句相似。所不同的是，它不是退出一个循环，而是开始循环的一次新迭代。
continue语句只能用在while语句、do/while语句、for语句、或者for/in语句的循环体内, 在其他地方使用都会引起错误?
```js
for(var i = 5; i >=0; i--) {
	if(i == 4 || i == 3 || i == 1) {
		continue;
	}
	console.log(i);
	alert(i);
	document.write(i);
}
```

当i = 4、i = 3以及i = 1的时候，直接跳出for循环。下次继续执行。

## Return语句:
return语句就是用于指定函数返回的值。return语句只能出现在函数体内，出现在代码中的其他任何地方造成语法错误！
```js
for(var i = 1; i < 10; i++) {
	if(i == 8) {
		return;
	}
	console.log(i);
	alert(i);
	document.write(i);
}
```

执行结果`Uncaught SyntaxError: illegal return statement(...)`
错误意思是非法捕获的查询返回语句。
当执行return语句时, 即使函数主题中还有其他语句, 函数执行也会停止!
```html
<script type="text/javascript">
	if(username == "") {
		alert("please input your username: ");
		return false;
	} else if (qq == "") {
		alert("please input your qq number: ");
		return false;
	}
</script>
```

上面的实例里，当username为空时，就不会再向下执行，在一些表单提交中，也可以通过return false来阻止默认的提交方式，改用Ajax的提交方式