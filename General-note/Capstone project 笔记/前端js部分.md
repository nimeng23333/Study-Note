
#### 1.on('click')绑定函数
错误代码：
```js
    $("#newCategory").on('click',handler("#newCategory"));

    function handler(lable) {
        $(lable).setAttribute("hidden", true);
        $(lable + "-input").removeAttribute("hidden");
        $(lable + "-done").removeAttribute("hidden");
        $(lable + "-close").removeAttribute("hidden");
    }
```

要传递参数的函数这里会导致handler函数在最开始执行一次
修改如下：
```js
$("#newCategory").on('click',() => handler("#newCategory"))
```

其他可以的用法：
不带参数的函数
```js
    $("input[type = 'checkbox']").on('click',showFilter); //注意回调函数不加括号，否则只会在一开始调用而不是每次点击
```
```js
    $('body').on('click', 'a[href^="#"]', function(event) {
    代码实现
    });
```

#### 2.jQuery与原生js getElement混用

错误代码：
```js
$(lable).setAttribute("hidden", true);
```

setAttribute和removeAttribute都是原生js，attr才是jquery

正确代码：

原生js
```js
document.getElementById("title" + id).setAttribute("hidden", true)
document.getElementById("edit" + id).setAttribute("hidden", true)
document.getElementById("done" + id).removeAttribute("hidden")
```

jquery
```js
$(lable).attr("hidden", true);
```

#### 3.事件监听
```js
input.on('input', checkInput); //检测input输入变化

input.on('blur', function(){xxx}); //检测input失去焦点

button.on('click', function () {xxx});  //检测button被点击

input.on('keyup', function() {xxx});  //检测input上的键盘事件
```

#### 4.JQuery选择器

```js
$("[id^='tag-']")  //选择所有以tag-开头的id的元素
```

#### 5.function(){} 与()=>{}的区别及this的作用域

- function() 是一种函数声明，可以有自己的名称，也可以是匿名的。()=> 是一种函数表达式，也叫做箭头函数，它是一种简洁的写法，可以省略function关键字和return语句。
- function() 的this指向是动态的，取决于函数的调用方式。()=> 的this指向是静态的，继承自定义时的上下文。
- function() 可以作为构造函数，使用new关键字创建对象。()=> 不能作为构造函数，使用new关键字会报错。
- function() 可以使用arguments对象获取函数的参数。()=> 不能使用arguments对象，但可以使用剩余参数（…args）代替。

```javascript
$("[id^='tag-']").on("click", ()=> {
  console.log(this); // undefined  //当使用箭头函数时，this的值是继承自外层作用域的，而不是绑定到当前的元素上。所以，需要使用一个普通的函数表达式，或者使用event.target来代替this
});

$("[id^='tag-']").on("click", function() {
  console.log(this.id); // this指向当前元素
});

// 或者使用event.target
$("[id^='tag-']").on("click", (event) => {
  console.log(event.target.id); // event.target指向当前元素
});
```

#### 6.ajax
```js
$.ajax({
	url: "/endpoint",
	type: "POST",
	data: {input: ids},
	dataType: "json",
	success: function(data) {
	},
	error: function(xhr, status, error) {
	  console.error(error);
	}
});
```