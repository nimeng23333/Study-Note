---
title: 补间动画Tween
---
[tween.js 用户指南 | tween.js](https://tweenjs.github.io/tween.js/docs/user_guide_zh-CN.html)
github仓库：
[tween.js/README_zh-CN.md at main · tweenjs/tween.js · GitHub](https://github.com/tweenjs/tween.js/blob/main/README_zh-CN.md)


补间动画的运动曲线
[Tween.js / graphs](https://tweenjs.github.io/tween.js/examples/03_graphs.html)
[Tween.js / array interpolation](https://tweenjs.github.io/tween.js/examples/06_array_interpolation.html)

repeat / yoyo / start / update
```js
import * as TWEEN from "three/examples/jsm/libs/tween.module"
/*--- 补间动画 --- */
const tween = new TWEEN.Tween(mesh.position)
tween.to({ x : 2}, 1000).onComplete(()=>{
  console.log("tween1结束")
});

//设置循环
tween.repeat(Infinity)
// tween.repeat(2) //重复2次

// 设置往返
tween.yoyo(true)

tween.easing(TWEEN.Easing.Bounce.Out)
tween.delay(100)

//启动补间动画
tween.start()


// 渲染函数

function animate() {
  cameraControl.update(); //一定不要忘记这个update，不然会出现错误
  TWEEN.update()//补间动画更新
  requestAnimationFrame(animate); //请求下一个动画帧
  renderer.render(scene, camera);
}
```

链式动画（如果1设置了repeat或者repeat+yoyo将永远不完成，就不会出现2的动画）
```js
import * as TWEEN from "three/examples/jsm/libs/tween.module"
/*--- 补间动画 --- */
const tween = new TWEEN.Tween(mesh.position)
tween.to({ x : 2}, 1000).onComplete(()=>{
  console.log("tween1结束")
});

// 链式动画(注意：2链在1的结尾，如果1设置了repeat或者repeat+yoyo将永远不完成，就不会出现2的动画)
const tween2 = new TWEEN.Tween(mesh.position)
tween2.to({y : 2}, 1000).onComplete(()=>{
  console.log("tween2结束")
});
tween.chain(tween2)
tween2.chain(tween)

//启动补间动画
tween.start()

//停止
tween.stop()

// 渲染函数

function animate() {
  cameraControl.update(); //一定不要忘记这个update，不然会出现错误
  TWEEN.update()//补间动画更新
  requestAnimationFrame(animate); //请求下一个动画帧
  renderer.render(scene, camera);
}
```

## 回调函数
onStart 、 onUpdate 、 onComplete 、 onStop