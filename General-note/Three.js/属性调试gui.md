---
title: 属性调试gui
---
```js
/* ---GUI--- */

const panel = new GUI({ width: 310 });

// 全屏
let fullscreenBtn = {
  Fullscreen: function () {
    // renderer.domElement.requestFullscreen();//全屏后会遮挡gui
    document.body.requestFullscreen();
  },
  ExitFullscreen: function () {
    document.exitFullscreen(); //退出全屏
  },
};
// 重置
let resetBtn = {
  Reset: function () {
    //重置相机位置及相机轨道控制器
    cameraControl.reset();
    camera.position.set(5, 2, 5);

    //重置物体
    mesh.position.set(0, 0, 0);
    mesh.scale.set(1, 1, 1);
    mesh.rotation.set(0, 0, 0);
    mesh.material.color.set("pink")
  },
};

panel.add(fullscreenBtn, "Fullscreen").name("全屏");
panel.add(fullscreenBtn, "ExitFullscreen").name("退出全屏");
panel.add(resetBtn, "Reset").name("重置物体及相机")

// 控制物体
let positionFolder = panel.addFolder("立方体位置")
// panel.add(mesh.position, "x", -5, 5).name("正方体X轴位置")
positionFolder.add(mesh.position, "x").min(-10).max(10).step(1).name("正方体X轴位置").onChange((val) => console.log("正方体X轴位置",val))
positionFolder.add(mesh.position, "y").min(-10).max(10).step(1).name("正方体y轴位置").onFinishChange((val) => console.log("正方体Y轴位置",val))
positionFolder.add(mesh.position, "z").min(-10).max(10).step(1).name("正方体z轴位置")

panel.add(material, "wireframe").name("线框模式")

// 颜色
let colorParams = {
  color : "pink"
}
panel.addColor(colorParams, "color").name("立方体颜色").onChange((val) =>{
  mesh.material.color.set(val)
})

/* ---GUI--- */
```