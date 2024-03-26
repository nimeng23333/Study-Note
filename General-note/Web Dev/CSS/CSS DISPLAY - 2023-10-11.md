---
title: CSS DISPLAY - 2023-10-11
---
默认一个元素是以block显示
```
h2{
	display: block
}

h2{
	display: inline
}

h2{
	display: inline-block
}

h2{
	display: none
}
```
inline会让两个元素在同一行显示，但不能设置高度和宽度，只会根据内容的大小自动设置
inline-block允许设置元素的高度和宽度，并且能放在同一行
none会不显示

# FLOAT

图片周围围绕文字，将图片设置一个float属性
```
img{
	float: left;
}
```
但如果不想让某些文字围绕，需要设置clear

```
footer{
	clear: left;
}
```

在设计中千万将float只用于文字围绕图片，不要用于布局，布局使用flexbox、grid或框架