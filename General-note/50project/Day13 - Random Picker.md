
### JS - string.trim()
String 的 trim() 方法会从字符串的两端移除空白字符，并返回一个新的字符串，而不会修改原始字符串。

要返回一个仅从一端修剪空白字符的新字符串，请使用 trimStart() 或 trimEnd()。

```js

const choices = event.target.value.split(",").filter((content)=> content.trim() !== "").map((content)=>content.trim());

```
filter用来将trim后为空的筛选出去，map是用来将去掉前后空格的内容替换成原来的数组。因为trim()只会返回一个新的数组，而不会修改原来的，因此要用map去映射

### JS - setTimeout()与 setInterval()

#错误代码 
```js
function randomPick(){
    const tags = document.querySelectorAll(".tag");
    const interval = setInterval(()=>{
        const pickedTag = tags[Math.floor(Math.random()*tags.length)];
        pickedTag.classList.add("highlighted");
        setTimeout(()=>{
            pickedTag.classList.remove("highlighted");
        },100)
    },100);
    setTimeout(()=>{
        clearInterval(interval); //错误原因：没有在clearInterval后面再加一个timeout
        const pickedTag = tags[Math.floor(Math.random()*tags.length)];
        pickedTag.classList.add("highlighted");
    },times * 100);
}
```

首先setTimeout()和setInterval()里面都需要跟一个function和一个时间值
但这个function不能直接写在里面，不能`setTimeout(console.log(1),100)`
只能写成函数名，或者一个回调函数
`setTimeout(()=>{pickedTag.classList.remove("highlighted");},100)`

其次代码中的最后一个setTimeout里可能存在一个竞态条件，在 `setInterval` 中每 `100ms` 高亮一个标签，然后在 `100ms` 后移除高亮。然后，在 `times * 100ms` 后，停止了间隔，并高亮了一个标签。
在停止间隔并高亮一个标签后的 `100ms` 内，`setInterval` 可能会移除你刚刚添加的高亮。这就是为什么有时元素会高亮，有时不会。因此需要再clearInterval后面再加一个setTimeout()，来确保不会冲突

```js
function randomPick(){
    const tags = document.querySelectorAll(".tag");
    const interval = setInterval(()=>{
        const pickedTag = tags[Math.floor(Math.random()*tags.length)];
        pickedTag.classList.add("highlighted");
        setTimeout(()=>{
            pickedTag.classList.remove("highlighted");
        },100)
    },100);
    setTimeout(()=>{
        clearInterval(interval);
        setTimeout(()=>{
            const pickedTag = tags[Math.floor(Math.random()*tags.length)];
            pickedTag.classList.add("highlighted");
        },100) //clearInerval后设置延迟确保没有冲突
    },times * 100);
}
```

### CSS - box-shadow
box-shadow的数值至少需要2个，offset-x和offset-y，这两个为正值的时候在右下，为负值的时候为左上
spread, radius, inset和color都是optional
box-shadow: offset-x, offset-y, spread(optional), radius(optional), color(optional)
多个阴影可以用逗号分隔
比如`   box-shadow: inset -3px -4px 6px var(--color-1-transparent),inset 2px 2px 6px var(--color-1-transparent);`
![[Pasted image 20231231002904.png]]


