---
title: Day3 - Rotating navigation
---

### CSS - transform
transform有translateX、translateY
transform-origin: top left左上角做变换起点

### CSS - 改变字母大小写
text-transform: uppercase; 全大写


### CSS - position总结
position: fixed 相对于屏幕固定
position: absolute 相对于父容器固定

```css
.circle-container{
    position: fixed;
    top: -100px;
    left: -100px;
    z-index: 1;
}
```
元素的左上角固定在屏幕的左上-100px的位置