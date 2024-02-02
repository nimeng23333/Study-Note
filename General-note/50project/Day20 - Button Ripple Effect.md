
整体思路：在button overflow为hidden的情况下，在button上点击的时候在点击的坐标点添加一个向外scale且透明度变低的圆形元素
### CSS - animation


```css
.ripple{
    background-color:midnightblue;
    border: none;
    outline: none;
    color: white;
    padding: 10px 20px;
    font-size: 20px;
    cursor: pointer;
    position: relative;
    overflow: hidden;
}

.effect{
    background-color: white;
    width: 20px;
    height: 20px;
    position: absolute;
    border-radius: 50%;
    transform: translate(-50%, -50%) scale(0);
    animation: scale 0.5s ease-out;
}
@keyframes scale{
    to{
        transform: translate(-50%, -50%) scale(10);
        opacity: 0;
    }
}
```

类似于transition，给元素添加动画效果，但不一样的是animation需要添加关键帧

### JS - e.clientX / e.clientY / offsetLeft / offsetTop

添加click事件监听后可以获取鼠标点击相对于视口的位置
offsetLeft 和 offsetTop可以获取当前物体的左上坐标

```js
const rippleBtn = document.querySelectorAll(".ripple");

rippleBtn.forEach((btn)=>{
    btn.addEventListener(("click"), function(e){
        let leftInside = e.clientX-e.target.offsetLeft;
        let topInside = e.clientY-e.target.offsetTop;
        const effect = document.createElement("span");
        effect.classList.add("effect");
        effect.style.top = topInside + "px";
        effect.style.left = leftInside + "px";
        this.appendChild(effect);
        setTimeout(()=>{
            this.removeChild(effect);
            // effect.remove();
        },500)
    })
})
```

element.remove()可以直接将这个元素去掉

### JS - this 在箭头函数里不能用