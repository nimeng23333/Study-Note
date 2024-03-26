---
title: Day8 - Form wave animation
---

思路：
通过css的input的focus与否来写与他临近的label里的span的css样式，包括transform:translateX及颜色的变换
js则是将label切成span，并在里面加上css的transition-delay属性

```js
labels.forEach((label)=>{
    label.innerHTML = label.innerText
        .split('')
        .map((letter,idx) => `<span style='transition-delay:${idx * 50}ms'>${letter}</span>`)
        .join('');
})
```

```css
.form-control input + label span{
    display: inline-block;
    transition: 0.3s cubic-bezier(0.68, -0.55, 0.265, 1.55);
    font-size: 18px;
    min-width: 5px;
    transform: translateY(0);
    color: white;
}

.form-control input:focus + label span,
.form-control input:valid + label span{
    transform: translateY(-20px);
    color: rgb(123, 147, 255);
}
```

CSS里+是临近的兄弟元素

transition里的贝塞尔曲线

#重要工具 贝塞尔曲线可视化工具
[cubic-bezier(.68,-0.55,.26,1.55) ✿ cubic-bezier.com](https://cubic-bezier.com/#.68,-0.55,.26,1.55)
![[../note_images/Pasted image 20231229185301.png]]