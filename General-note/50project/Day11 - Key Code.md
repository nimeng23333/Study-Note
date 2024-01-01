
### CSS - display:flex 和display: inline-flex的区别
display:flex;不会让容器本身取消它的块装的属性，但它的子元素会变成行内块的的属性 。 
display:inline-flex;父级是变成行内块元素，他的子元素也是行内块元素，并且自动换行。

1. 如果display对应的值是flex的话，那么`flex container`是以`block-level`的形式存在的，相当于是一个块级元素
    
2. 如果display的值设置为`inline-flex`的话，那么`flex container`是以`inline-level`的形式存在的，相当于是一个行内块元素
    
3. 这两个属性值差异的影响在设置了属性值的元素上面，它们在子元素上的效果都是一样的
    
4. 如果一个元素的父元素开启了flex布局；那么其子元素的display属性对自身的影响将会失效，但是对其内容的影响依旧存在的；

也就是说flex作用在父元素上，父元素本身的block不变，让它里面的子元素变成flex布局
而inline-flex能让父元素自己就变成flex，子元素也是flex

### JS - 键盘监听
keypress 事件无法监听 Tab、Shift这类按键。 keypress 事件主要用于监听可以产生可打印字符的按键，而 Tab、Shift 键并不属于这类按键。
如果要监听 Tab 键，你应该使用 keydown 或 keyup 事件。这两个事件可以捕获所有的按键事件。