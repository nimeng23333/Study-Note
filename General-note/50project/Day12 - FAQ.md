### CSS - Font Awesome

[Font Awesome 方向图标 | 菜鸟教程 (runoob.com)](https://www.runoob.com/font-awesome/fontawesome-icons-directional.html)
![[Pasted image 20231230162653.png]]

_CHEVRON_翻译：（尤指警服或军装袖子上表示等级的）V形图案；（英国用作路标、表示急转弯的）V形标记。

[CSS Pseudo-elements | Font Awesome Docs](https://fontawesome.com/docs/web/setup/upgrade/pseudo-elements)

font awesome在css里的语法：
version5：
```css
  .login::before {
    font-family: "Font Awesome 5 Free";  //字体名字
    font-weight: 900;
    content: "\f007";  //编码
  }
```


### CSS - overflow:hidden
![[Pasted image 20231230171322.png]]
![[Pasted image 20231230171337.png]]

在元素上设置overflow:hidden才能让超出元素边界的东西隐藏

### CSS - transition生效范围

`transition` 属性对 `display` 属性的变化没有效果。这是因为 `display` 属性不是一个可以过渡的属性。换句话说，`display` 属性的变化是立即生效的，不会有过渡效果。

### JS - 打开关闭思路

用classList.toggle和一个container里的两个标签的隐藏+显示来解决

```js
const toggles = document.querySelectorAll(".faq-toggle");
toggles.forEach((toggle) =>{
    toggle.addEventListener("click",()=>{
        toggle.parentNode.classList.toggle("active");
    })
})
```
```html
<div class="faq-toggle">
	<i class="fas fa-times"></i>
	<i class="fas fa-chevron-down"></i>
</div>
```

原生js为父元素添加class用parentNode.classList.toggle()