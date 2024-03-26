---
title: CSS - Bootstrap - 2023-10-13
---
bootstrap有很多内置预设的css框架

include via CDN (content delivery network)

bootstrap自带的container会根据屏幕尺寸不同修改自己的大小
![[../../note_images/Pasted image 20231013194237.png]]

grid布局自动是一个row，row里面很多个column
自动将整个屏幕宽度分为12份
在class直接命名 
```
<div class="container">
	<div class="row">
		<div class="col-2">content</div>
		<div class="col-4">content</div>
		<div class="col-6">content</div>
	</div>
</div>
```
就可以直接得到3个1：2：3占据整个屏幕的格子

设置不同尺寸屏幕的格子需要设置多个数值
class="col-sm-12 col-md-8 col-lg-4"
在小尺寸屏幕时占据整个屏幕，中尺寸占2/3，大尺寸占1/3

在bootstrap里 margin和padding都可以用简写
mt-5 就是margin-top: spacer * 3
px-1 就是padding-left + padding-right : spacer * 0.25
![[../../note_images/Pasted image 20231013211445.png]]

### Darkmode
只需要添加一个标签
```
<html lang="en" data-bs-theme="dark">
```

更多模板
[Bootstrap Templates (w3schools.com)](https://www.w3schools.com/bootstrap/bootstrap_templates.asp)
[Examples · Bootstrap v5.3 (getbootstrap.com)](https://getbootstrap.com/docs/5.3/examples/)
