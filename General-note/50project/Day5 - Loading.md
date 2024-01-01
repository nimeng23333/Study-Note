### CSS - background
background:url(""); } 在不写repeat与no-repeat的情况下，默认的是平铺。repeat平铺的效果，就是一张图，可以铺满整个屏幕。

background：url() no-repeat center center/cover
第一段：url 表示指定图片位置，路径
第二段：no-repeat 表示图片不重复
第三段：表示水平方向上，显示图片的中间，垂直方向上，也显示图片的中央。
第四段：cover，表示自动调整，以覆盖整个块元素区域

### CSS - filter滤镜
```css
filter: blur(30px);
```

[`blur()`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/filter-function/blur)
模糊图像

[`brightness()`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/filter-function/brightness
让图像更明亮或更暗淡

[`contrast()`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/filter-function/contrast)
增加或减少图像的对比度

[`drop-shadow()`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/filter-function/drop-shadow)
在图像后方应用投影

[`grayscale()`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/filter-function/grayscale)
将图像转为灰度图

[`hue-rotate()` (en-US)](https://developer.mozilla.org/en-US/docs/Web/CSS/filter-function/hue-rotate "Currently only available in English (US)")
改变图像的整体色调

[`invert()` (en-US)](https://developer.mozilla.org/en-US/docs/Web/CSS/filter-function/invert "Currently only available in English (US)")
反转图像颜色

[`opacity()`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/filter-function/opacity)
改变图像透明度

[`saturate()` (en-US)](https://developer.mozilla.org/en-US/docs/Web/CSS/filter-function/saturate "Currently only available in English (US)")
超饱和或去饱和输入的图像

[`sepia()` (en-US)](https://developer.mozilla.org/en-US/docs/Web/CSS/filter-function/sepia "Currently only available in English (US)")
将图像转为棕褐色

```js
let timer = 0;

let int = setInterval(updateDisplay, 30);

function updateDisplay(){
    timer++;
    text.innerHTML = timer + "%";
    if(timer == 100){
        clearInterval(int);
    }
    text.style.opacity = scale(timer,0,100,1,0);
    bg.style.filter = `blur(${scale(timer,0,100,30,0)}px)`
}

const scale = (num, in_min, in_max, out_min, out_max) =>{
    return (num - in_min) * (out_max - out_min) / (in_max - in_min)+out_min;
}
```

### JS - 实用小函数：将数字从一个range映射到另一个range
```js
const scale = (num, in_min, in_max, out_min, out_max) =>{
    return (num - in_min) * (out_max - out_min) / (in_max - in_min)+out_min;
}
```

### JS - 清除interval的函数clearInterval
```js
let int = setInterval(function, 30);
clearInterval(int);
```