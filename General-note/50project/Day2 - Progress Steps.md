---
title: Day2 - Progress Steps
---

### CSS - 字体导入也可以在css里导入
```css
@import url('https://fonts.googleapis.com/css2?family=Muli&display=swap');
```

### CSS - 用var设置颜色
```css
:root{
    --line--border--empty:#e0e0e0;
    --line--border--fill:#636cc0;
}

*{
	color: var(--line--border--fill);
}
```

### CSS - 父容器 display设置成flex，position设置成relative，子元素才能用position: absolute
```css
.progress{
    background-color: var(--line--border--fill);
    position: absolute; /*相对于父容器的位置是绝对的*/
    top: 50%; /*父容器上下的中间（需要额外注意的这里子元素的起始点是左上角，因此50%实际上是子元素的左上角在中间*/
    transform: translateY(-50%); /*因此需要transform，对子元素本身进行y方向上移自身的50% */
    left: 0;
    height: 4px;
    width: 0%; /*初始为0*/
    z-index: -1;
    transition: 0.4s ease; /*添加动画过渡效果*/
}
```

### JS - disabled属性可以直接用点方法
```js
prevBtn.disabled = true;
prevBtn.disabled = false;
```

### JS - 延迟触发
```js
setTimeout(function(){circles[i].classList.add("active")},220)
```