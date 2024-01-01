### CSS - white-space
CSS white-space 属性用于设置如何处理元素内的空白字符。
```
/* 单个关键字值 */
white-space: normal; 正常
white-space: nowrap; 不换行
white-space: pre;
white-space: pre-wrap;
white-space: pre-line;
white-space: break-spaces;
```

`white-space` 属性可以被指定为从下面的值列表中选择的单个关键字，或者是表示 [`white-space-collapse` (en-US)](https://developer.mozilla.org/en-US/docs/Web/CSS/white-space-collapse "Currently only available in English (US)") 和 [`text-wrap` (en-US)](https://developer.mozilla.org/en-US/docs/Web/CSS/text-wrap "Currently only available in English (US)") 属性的简写的两个值。

[`normal`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/white-space#normal)
连续的空白符会被[合并](https://developer.mozilla.org/zh-CN/docs/Web/CSS/white-space#%E5%90%88%E5%B9%B6%E7%A9%BA%E7%99%BD%E5%AD%97%E7%AC%A6)。源码中的换行符会被当作空白符来处理。并根据填充行框盒子的需要来换行。

[`nowrap`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/white-space#nowrap)
和 `normal` 一样[合并](https://developer.mozilla.org/zh-CN/docs/Web/CSS/white-space#%E5%90%88%E5%B9%B6%E7%A9%BA%E7%99%BD%E5%AD%97%E7%AC%A6)空白符，但阻止源码中的文本换行。

[`pre`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/white-space#pre)
连续的空白符会被保留。仅在遇到换行符或 [`<br>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/br) 元素时才会换行。

[`pre-wrap`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/white-space#pre-wrap)
连续的空白符会被保留。在遇到换行符或 [`<br>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/br) 元素时，或者根据填充行框盒子的需要换行。

[`pre-line`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/white-space#pre-line)
连续的空白符会被[合并](https://developer.mozilla.org/zh-CN/docs/Web/CSS/white-space#%E5%90%88%E5%B9%B6%E7%A9%BA%E7%99%BD%E5%AD%97%E7%AC%A6)。在遇到换行符或 [`<br>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/br) 元素时，或者根据填充行框盒子的需要换行。

[`break-spaces`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/white-space#break-spaces)
与 `pre-wrap` 的行为相同，除了：

- 任何保留的空白序列总是占用空间，包括行末的。
- 每个保留的空白字符后（包括空白字符之间）都可以被截断。
- 这样保留的空间占用空间而不会挂起，从而影响盒子的固有尺寸（最小内容——`min-content`——大小和最大内容——`max-content`——大小）。

### CSS - root
root是冒号，不是点
:root{}

### JS - hover事件
用js为元素添加hover监控用mouseenter和mouseleave
`element.addEventListener("mouseenter", function)`
`element.addEventListener("mouseleave", function)`