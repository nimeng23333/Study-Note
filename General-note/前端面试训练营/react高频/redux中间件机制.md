---
title: redux中间件机制
---
派发action之后，在action到达reducer之间，对action进行拦截、解释或更改。通常有异步操作、日志记录、异常处理等。
通过对dispatch函数包装来实现。

每个中间件函数接收dispatch和getState函数作为参数，并返回一个函数。这个函数接受next为参数，并返回一个函数，这个函数接受action为参数。

这个返回的函数是增强版dispatch
```js
const middleware = store => next => action =>{
	//对action进行处理
	//调用next（action)将action传递给下一个中间件或reducer
	return next(action)
}
```

在redux里，通过applyMiddleware函数将中间件添加到store里。
applyMiddleware函数接收一系列中间件函数作为参数，返回一个增强版的createStore函数。这个函数会对dispatch进行包装，并将所有的中间件函数链式调用起来。
```js
import {applyMiddleware, createStore} from 'redux'
import thunk from 'redux-thunk' //中间件

const middleware = [thunk] //将多个中间件放一个数组里

const store = createStore(
	reducer,
	applyMiddleWare(...middleware)
)
```

这个代码中，thunk被应用在了store里，是我们可以在aciton里执行异步操作，例如调用一个api或者执行定时器函数。

当应用`applyMiddleware(mid1, mid2)` 时，Redux 会构建这样的调用链：

```js
// 伪代码表示执行顺序
store.dispatch = mid1(store)(mid2(store)(store.dispatch))(action)
```

代码等价于
```js
function middleware(store) {
  return function(next) {
    return function(action) {
	  //在这里处理action
      return next(action);
    };
  };
}
```

当箭头函数满足以下条件时，可以省略 `return` 和大括号 `{}`：

1. 函数体只有**单个表达式**
2. 该表达式就是返回值
当函数体包含**多条语句**时，必须使用大括号 `{}` 和 `return`：

```js
const middleware = store => {
  return next => {
    return action => {
      return next(action);
    };
  };
};
```

中间件不做任何处理可以简写为
```js
const middleware = store => next => action => next(action);
```