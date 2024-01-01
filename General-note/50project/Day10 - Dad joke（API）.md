
fetch是内置API，用来异步抓取api

async/await 是一种建立在Promise之上的编写异步或非阻塞代码的新方法，被普遍认为是 JS异步操作的最终且最优雅的解决方案。相对于 Promise 和回调，它的可读性和简洁度都更高。

async 是异步的意思，而 await 是 async wait的简写，即异步等待
从语义上就很好理解 async 用于声明一个 function 是异步的，而await 用于等待一个异步方法执行完成。

普通的fetch写法：

```js
fetch(url,config)
	.then(res => res.json())
	.then(data => console.log(data))
```

async/await写法：

```js
async function fetchData(){
	const res = await fetch(url,config);
	const data = res.json();
	console.log(data)
}
```

`async/await` 的优势在于处理 then 的调用链，能够更清晰准确的写出代码，并且也能优雅地解决回调地狱问题。当然也存在一些缺点，因为 await 将异步代码改造成了同步代码，如果多个异步代码没有依赖性却使用了 await 会导致性能上的降低

#扩展阅读
promise
[使用 Promise - JavaScript | MDN (mozilla.org)](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Using_promises)

[Promise不会？？看这里！！！史上最通俗易懂的Promise！！！-阿里云开发者社区 (aliyun.com)](https://developer.aliyun.com/article/610350)
> Promise 是异步编程的一种解决方案： 从语法上讲，promise是一个对象，从它可以获取异步操作的消息；从本意上讲，它是承诺，承诺它过一段时间会给你一个结果。 promise有三种状态： **pending(等待态)，fulfiled(成功态)，rejected(失败态)**；状态一旦改变，就不会再变。创造promise实例后，它会立即执行。

我相信大家经常写这样的代码：

```
// 当参数a大于10且参数fn2是一个方法时 执行fn2
function fn1(a, fn2) {
 if (a > 10 && typeof fn2 == 'function') {
 fn2()
 }
}
fn1(11, function() {
 console.log('this is a callback')
})复制代码
```

一般来说我们会碰到的回调嵌套都不会很多，一般就一到两级，但是某些情况下，回调嵌套很多时，代码就会非常繁琐，会给我们的编程带来很多的麻烦，这种情况俗称——回调地狱。  

这时候我们的promise就应运而生、粉墨登场了

promise是用来解决两个问题的：

- 回调地狱，代码难以维护， 常常第一个的函数的输出是第二个函数的输入这种现象
- promise可以支持多个并发的请求，获取并发请求中的数据
- 这个promise可以解决异步的问题，本身不能说promise是异步的


### CSS - transform 写两个属性

```css
    transform: translateX(-50%) scale(1.01);
```

### CSS - 字符间距
```css
    letter-spacing: 1.5px;
```

#错误代码

```js
async function getJoke(){
    const config = {
        Headers:{   //错误1 headers 小写
            "Accept": "application/json",  //错误2 Accept作为属性名不加引号
        },
    }
    const res  = await fetch(API_url, config);
    const data = res.json();  //错误3 res是promise对象，也要加await来完成
    jokeElement.innerHTML = data.joke;
}
```
正确：
```js
async function getJoke(){
    const config = {
        headers:{
            Accept: "application/json",
        },
    }
    const res  = await fetch(API_url, config);
    console.log(res);
    const data = await res.json();
    console.log(data);
    jokeElement.innerHTML = data.joke;
}
```

#错误代码 

```js
function getJoke(){
    fetch(API_url,config)
    .then((res) =>{res.json()}) //这里不应该加花括号
    .then((data) => {console.log(data);jokeElement.innerHTML = data.joke})
}
```

`.then((res) =>res.json())`和 `.then((res) =>{res.json()})`的区别
主要区别在于它们是否返回一个值。
- `.then((res) => res.json())`：这种写法会返回 `res.json()` 的结果。在这种情况下，`res.json()` 的结果（一个 Promise）会被传递给下一个 `.then()`
- `.then((res) => {res.json()})`：这种写法不会返回任何值。因为在 `{}` 中的代码被视为一个语句块，除非明确使用 `return` 语句，否则不会有任何值被返回。所以，下一个 `.then()` 将接收到 `undefined`。

因此，如果你想要在链式调用的下一个 `.then()` 中使用 `res.json()` 的结果，你应该使用 `.then((res) => res.json())` 这种写法。