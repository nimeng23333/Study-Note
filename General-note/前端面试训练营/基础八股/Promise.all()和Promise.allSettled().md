---
title: Promise.all()和Promise.allSettled()
---
Promise.all()和Promise.allSettled()都用于处理多个promise对象的并发执行。但它们之间有些区别。
Promise.all()返回的Promise对象会在所有promise对象都resolve之后才会resolve并返回一个由所有结果组成的数组，而如果一旦有一个reject，那么会立刻reject并返回相应的错误信息。因此只要有一个reject就会失败，全部resolve才会成功。
Promise.allSettled()方法返回的Promise对象会在所有的promise对象都resolve或reject后resolve。并返回一个由所有Promise状态对象组成的数组，每个状态包含一个status字段表示Promise状态，和一个value或reason表示返回值或错误信息。

因此，Promise.all() 方法在需要所有 Promise 都成功的情况下使用，而 Promise.allSettled() 方法则适用于需要知道每个 Promise 的执行结果的情况下使用。