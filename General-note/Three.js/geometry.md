---
title: geometry
---
# 基础组成

geometry由三角形面组成
三角形面由顶点组成
顶点有绘制顺序，逆时针是正面，顺时针是反面。
如果要让两个面都能被看见需要设置material的side为doubleSide

```js
// 自定义geometry
const vertices = new Float32Array([
  -1.0,-1.0,0.0, 1.0,-1.0,0.0, 1.0,1.0,0.0
])//顶点有顺序，逆时针是正面，顺时针是反面
const geometry = new BufferGeometry()
geometry.setAttribute("position", new BufferAttribute(vertices, 3))

// 创建材质
const material = new THREE.MeshBasicMaterial({
  color: "pink",
  side: THREE.DoubleSide,
});

```

形成了3个点一个面。

2个三角形拼成一个正方形
```js
const vertices = new Float32Array([
  -1.0,
  -1.0,
  0.0,
  1.0,
  -1.0,
  0.0,
  1.0,
  1.0,
  0.0, //第一个三角形
  1.0,
  1.0,
  0.0,
  -1.0,
  1.0,
  0.0,
  -1.0,
  -1.0,
  0.0, //第二个三角形
]);
```

一共传递了6个点的信息，但实际上其中有2个点是公用的，可以进行索引绘制

实际上正方体有8个顶点，很多信息可以进行索引绘制，但在threejs里正方体只有同一个面公用顶点，也就是正方体有6个面，每个面有4个顶点，正方体的position信息里就有24个顶点，array有24 x 3个数据

在 Three.js 中，`BoxGeometry` 使用 24 个顶点而非 8 个顶点的原因是为了**支持独立的面法线和纹理坐标**

- 立方体的每个面需要**独立的法线方向**（垂直于各自的面）。
- 如果共享顶点（8个顶点），同一个顶点在不同面上会被赋予**不同的法线方向**（例如一个顶点同时属于前、上、右三个面）。
- 这会导致光照计算错误：法线会被 GPU 插值，使棱角处看起来"平滑"而非硬边。

- 每个面需要**独立的纹理贴图坐标**。
- 共享顶点时，一个顶点在不同面上无法拥有不同的 UV 坐标（例如一个顶点在相邻面的纹理位置不同）。

- GPU 的顶点着色器中，**每个顶点只能有一套属性**（位置、法线、UV 等）。
- 立方体的一个角点（如 `(1,1,1)`）在三个相邻面中：
    - 法线方向不同（前面：`(0,0,1)`，上面：`(0,1,0)`，右面：`(1,0,0)`）。
    - UV 坐标不同（取决于纹理如何映射）。
- **必须拆分成三个独立顶点**，分别携带对应面的属性。

# 材质

```js
//设置顶点索引，设置索引生成面，4个顶点生成2个三角形生成一个面。
const vertices = new Float32Array([
  -1.0, -1.0, 0.0, 1.0, -1.0, 0.0, 1.0, 1.0, 0.0, -1.0, 1.0, 0.0
])
geometry.setAttribute("position", new BufferAttribute(vertices, 3));
const indices = new Uint16Array([0,1,2,2,3,0])
geometry.setIndex(new THREE.BufferAttribute(indices, 1))
geometry.addGroup(0,3,0) //参数分别是start, count, material
geometry.addGroup(3,3,1)

// 创建材质
const material = new THREE.MeshBasicMaterial({
  color: "pink",
});
const material1 = new THREE.MeshBasicMaterial({color:"blue"})
// 创建网格
const mesh = new THREE.Mesh(geometry, [material, material1]); //传递物体和材质
```
![](note_images/Pasted%20image%2020250630150029.png)

可以通过这个方法给box设置不同的材质
```js
// 创建材质
const material = new THREE.MeshBasicMaterial({
  color: "pink",
  // side: THREE.DoubleSide,
});
const material1 = new THREE.MeshBasicMaterial({color:"blue"})
const material2 = new THREE.MeshBasicMaterial({color:"yellow"})
// 创建网格
const mesh = new THREE.Mesh(geometry, [material, material1,material2,material,material1,material2]); //传递物体和材质 //material的顺序可以发现x轴正负为1、2面，y轴正、负为3、4面，z轴正负为5、6面。
```
![](note_images/Pasted%20image%2020250630150635.png)
material的顺序可以发现x轴正负为1、2面，y轴正、负为3、4面，z轴正负为5、6面。

# 常见几何体

## DodecahedronGeometry 十二面体
细分大可以作为球体
[DodecahedronGeometry – three.js docs](https://threejs.org/docs/#api/zh/geometries/DodecahedronGeometry)

## IcosahedronGeometry 二十面体
类似十二面体

## OctahedronGeometry 八面体
两个三棱锥合并，细分也可以作为球体
## edgeGeometry
这可以作为一个辅助对象来查看geometry的边缘。
[EdgesGeometry – three.js docs](https://threejs.org/docs/?q=geomet#api/zh/geometries/EdgesGeometry)

```js
// 线框 WireframeGeometry（包含每个面的2个三角形）
const wireframe = new THREE.LineSegments(
	new THREE.WireframeGeometry(geometry),
	new THREE.LineBasicMaterial({ color: "yellow" })
);
mesh.add(wireframe);

// 边框 EdgesGeometry
const edges = new THREE.EdgesGeometry( geometry );
const line = new THREE.LineSegments( edges, new THREE.LineBasicMaterial( { color: 0xffffff } ) );
mesh.add( line );
```

## LatheGeometry 车削几何体
创建具有轴对称性的网格，比如花瓶。车削绕着Y轴来进行旋转。
sweep出来的内容