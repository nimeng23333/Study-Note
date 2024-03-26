---
title: html和css部分
---

#### 1.radio或者checkbox的文字也可以选择
让radio或checkbox放在lable里面，而非lable放在input里面

```html

<div><label for="category1"><input type="radio" name="category" id="category1" >Category1</label></div>
<div><label for="category2"><input type="radio" name="category" id="category2" >Category2</label></div>

```
额外注意 input的name是这些选项的总名称，多个选项都是同一个name
id是选项的单独名称
lable的for对应的是input的id，不要弄混了

#### 2.row布局
一个div class=“col-12”的时候能占满
class=“col-10”的时候会空缺后面，要让这个div居中可以设置为
```html
<div class="col-lg-10 offset-lg-1">
```

#### 3.表单相关

3.1不可嵌套，不能在form里再来一个form

3.2表单里的button如果没有设置将会默认提交表单，需要加type="button"属性
如：
`<button id="newCategory-done" class="btn btn-outline-primary btn-new" type="button" hidden><i class="ti-check"></i></button>`

[HTML5之表单验证 - 简书 (jianshu.com)](https://www.jianshu.com/p/cd119ff28659)

表单默认样式，比如input 的type=search的时候默认的关闭按钮是`::-webkit-search-cancel-button`
[修改常见的原生表单控件的默认样式_-webkit-inner-spin-button-CSDN博客](https://blog.csdn.net/Android_boom/article/details/128430948)

#### 4.CSS
```CSS
/*去掉数字input后面的上下箭头*/
input[type="number"]::-webkit-inner-spin-button,
input[type="number"]::-webkit-outer-spin-button {
    -webkit-appearance: none;
}
```


### TailwindCSS相关

tailwindcss插件typography

The `@tailwindcss/typography` plugin adds a set of `prose` classes that can be used to quickly add sensible typographic styles to content blocks that come from sources like markdown or a CMS database.