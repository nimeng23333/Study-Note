---
title: 射线（交互系统）
---
```js
import { Raycaster } from "three";

/*--- 射线交互 ---*/
// 创建射线
const raycaster = new Raycaster();

// 维护可交互物体数组
const interactiveObjects = [mesh1, mesh2, mesh3];

// 将鼠标坐标用向量保存
const pointer = new THREE.Vector2();

//当前交互对象
const INTERSECTED = {};

window.addEventListener("mousemove", onPointerMove);
window.addEventListener("click", onClick);

function onPointerMove(event) {
  //鼠标移动及坐标转化为x:-1到1 y:-1到1
  pointer.x = (event.clientX / window.innerWidth) * 2 - 1;
  pointer.y = -(event.clientY / window.innerHeight) * 2 + 1;

  //通过摄像机和鼠标位置更新射线
  raycaster.setFromCamera(pointer, camera);

  //计算物体和射线的焦点
  const intersects = raycaster.intersectObjects(interactiveObjects);

  if (intersects.length > 0) {
    if (INTERSECTED.obj != intersects[0].object) {
      if (INTERSECTED.obj) { //颜色还原
        INTERSECTED.obj.material.color.set(INTERSECTED.originColor);
      }
      INTERSECTED.obj = intersects[0].object;
      INTERSECTED.originColor = INTERSECTED.obj.material.color.getHex(); 
      // INTERSECTED.originColor = INTERSECTED.obj.material.color.clone; 
      //颜色可以克隆，因为也是实例对象。也可以获取color的具体值，用getHex()
      INTERSECTED.obj.material.color.set(0xff0000);
    }
  } else {
    if (INTERSECTED.obj) { //颜色还原
      INTERSECTED.obj.material.color.set(INTERSECTED.originColor);
    }
    INTERSECTED.obj = null;
  }

  // 可以将物体和颜色记录在一个额外的对象上，以上为写法1，也可以将状态记录在每个mesh，给一个_isHover的私有属性，并记录_originalColor的私有属性，为写法2

  //将射到的物体都变为红色
  // for ( let i = 0; i < intersects.length; i++ ) {
  //   if(intersects[i].object instanceof THREE.Mesh){
  //     intersects[i].object.material.color.set( 0xff0000 );
  //   }
  // }
}
```

可以将物体和颜色记录在一个额外的对象上，以上为写法1，也可以将状态记录在每个mesh，给一个_isHover或_isSelect的私有属性，并记录_originalColor的私有属性，为写法2

```js
function onClick(){
  //计算物体和射线的焦点(raycaster在mousemove里已有)
  const intersects = raycaster.intersectObjects(interactiveObjects);
  if (intersects.length > 0) {
    if(intersects[0].object._isSelect){
      intersects[0].object._isSelect = false
    }else{
      intersects[0].object._isSelect = true
      intersects[0].object.material.wireframe = true
    }
    if(intersects[0].object._isSelect){
      intersects[0].object.material.wireframe = true
    }else{
      intersects[0].object.material.wireframe = false
    }
  }
}
```