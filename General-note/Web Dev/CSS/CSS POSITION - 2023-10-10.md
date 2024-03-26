---
title: CSS POSITION - 2023-10-10
---
css一共有四种位置
static position 静态，默认，会忽略任何top、bottom、left、right和z-index的声明
relative position 相对位置，相对于其正常位置进行定位
absolute position 相对于最近一级的父级位置，但如果父级没有特别标明position那将会相对webpage
fixed position 相对于浏览器的位置，即滑动也还是在相对浏览器的固定位置
z-index决定了遮挡关系，默认都是0