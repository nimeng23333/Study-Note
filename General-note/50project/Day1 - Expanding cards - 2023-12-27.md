### CSS - :nth-of-type()
```css
.panel:nth-of-type(4)
```
在CSS中，:nth-of-type()是一个伪类选择器，用于匹配基于它们在同类型兄弟元素中的位置的元素。它的语法如下：

:nth-of-type(an+b) {

}
其中，a和b是整数，n是计数器，从0开始1。例如，:nth-of-type(2n+1)会选择所有奇数位置的元素，:nth-of-type(3n+3)会选择第3、6、9等位置的元素。

请注意，:nth-of-type()只考虑元素类型（标签名），而不考虑类或其他属性



### CSS - transition
```css
transition: flex 0.7s ease-in; 
```
给flex属性一个0.7s的过度效果 

```css
transition: opacity 0.3s ease-in 0.4s; 
```
给opacity属性一个0.7s的过度效果 