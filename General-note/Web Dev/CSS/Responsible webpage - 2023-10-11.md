---
title: Responsible webpage - 2023-10-11
---
1.媒体查询 media queries
2.CSS Grid
3.CSS Flexbox
4.外部框架

媒体查询：
```
@media(max-width: 600px){
	链接一个窗口大小小于600px时候应用的css
}
```

Grid：2d
![[../../note_images/Pasted image 20231011160801.png]]
display改为gid，设置column及row

Flexbox：1d，可以制作横列表，竖列表
![[../../note_images/Pasted image 20231011161022.png]]
不固定的长或短用flex表示

Bootstrap Framework: 预设了很多类的框架


# Media query
```
@media (max-width: 600px) and (min-width: 900px){
	css语句
}
@media screen(orientation:landscape){
	css语句
}
@media print {

}
@media not screen and (color), print and (color) { ... }

```
[Using media queries - CSS: Cascading Style Sheets | MDN (mozilla.org)](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_media_queries/Using_media_queries)

Mobile Devices: 319px — 480px
iPads and Tablets: 481px — 1200px
Laptops: 1201px — 1600px
Desktops: 1601px and more

使用object-fit可以让图片或视频根据block的大小进行缩放
```
object-fit: contain;
object-fit: cover;
object-fit: fill;
object-fit: none;
object-fit: scale-down;
```
contain长宽比不变，根据最大边填充
cover根据最短边填充，多余的进行裁剪
fill不管长宽比直接拉伸填充
none直接不变
scale-down对比contain和none，取小值