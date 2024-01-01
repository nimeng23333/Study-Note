
Ternary Operator三元运算符

if、switch、for都是statement 而不是expression，expression能得出一个值
在jsx的{}里面不能加statement，因此也不能在里面写if条件句

这里就需要用到三元运算符

```js
CONDITION ? DO IT TRUE : DO IT FALSE
```

eg:
```js
isTrue === true ? Yes() : No()
```
这样能把一个if statement变成三个expression

不显示内容直接用null
```js
currentTime > 12 ? <h1>Hello</h1> : null
```



&& 
（expression && expression）当两个都成立的时候做某些事

在React里根据这个特性会写如下语句：

```JS
(CONDITION && EXPRESSION)
(TRUE && EXPRESSION) //当True的时候会计算这个expression
(FALSE && EXPRESSION) //当false的时候就不会计算这个expression
```

上面的三元运算可以简化如下：
```jsx
currentTime > 12 && <h1>Hello</h1>   // 前面true的时候第二个也会跟着运算，从而达成if判断
```


## State

UI = f(state)

之前的编程方式是imperative progamming命令式编程，在这部分用的是declarative programming 声明式编程

Imperative programming specifies and directs the control flow of the program.
Declarative programming specifies the expected result and core logic without directing the program's control flow. The programmer makes the major decisions about how the program works.
![[Pasted image 20231222133701.png]]
![[Pasted image 20231222133909.png]]

SQL的WHERE是一个很典型的声明式编程

React里引入一个很新的东西HOOK
允许我们挂钩到应用程序并读取或修改函数