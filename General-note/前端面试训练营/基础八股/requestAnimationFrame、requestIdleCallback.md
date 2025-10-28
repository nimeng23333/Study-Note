---
title: requestAnimationFrame、requestIdleCallback
---
# requestAnimationFrame
这个api是告诉浏览器你希望完成一段动画，在计算机开始下一帧重绘前完成给定的回调函数
requestAnimationFrame(callback)
这个callback函数只接受一个参数，那就是requestAnimationFrame传递过去的时间戳
如果要持续的更新动画callback函数里面也要调用requestAnimationFrame
以下是一个2s内元素移动200px的动画
```js
const element = document.getElementById("some-element-you-want-to-animate");
let start, previousTimeStamp;
let done = false;

function step(timestamp) {
  if (start === undefined) {
    start = timestamp;
  }
  const elapsed = timestamp - start;

  if (previousTimeStamp !== timestamp) {
    // 这里使用 Math.min() 确保元素在恰好位于 200px 时停止运动
    const count = Math.min(0.1 * elapsed, 200);
    element.style.transform = `translateX(${count}px)`;
    if (count === 200) done = true;
  }

  if (elapsed < 2000) {
    // 2 秒之后停止动画
    previousTimeStamp = timestamp;
    if (!done) {
      window.requestAnimationFrame(step);
    }
  }
}

window.requestAnimationFrame(step);

```

# requestIdleCallback
是一个比较新的api，用于在浏览器空闲的时候执行某些不重要费时间的任务。好处是可以提高网页性能和响应速度，避免阻塞主进程

```js
function doWork(deadline) {
 while (deadline.timeRemaining() > 0) {
 // 在这里编写任务逻辑
 }
 if (还有任务需要执行) {
 requestIdleCallback(doWork);
 }
}
requestIdleCallback(doWork);
```

需要注意的是，requestIdleCallback 的回调函数接受一个 IdleDeadline 参数，它包含了当前空闲时间的相关信息。开发者可以通过这个参数，根据浏览器的空闲时间进行任务调度和优化。