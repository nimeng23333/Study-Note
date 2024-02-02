
所有 tailwind 提供的所有内置原子 class 都可以配置。
但这些都是全局的更改，有的时候想临时设置一些值，可以用 [] 语法。
比如 `class = "text-[14px]"`，它就会生成 font-size:14px 的样式
`className = "hover: text-[14px]"`会在hover的时候font-size:14px 

断点配置
`className = "md: bg-blue-500"`

### grid

`<div className="grid grid-cols-[300px_1fr]">`
正常情况300px 1fr中间是空格，但在className里空格会认为是两个class
因此在tailwind里用下划线代替

### 组合多个tailwind
tailwind.config.js文件里

```css
@tailwind base;
@tailwind components;
@tailwind utilities;  /*覆盖级下面的utilities等级最高*/

@layer components{   /*创建components级别的layer*/
    .btn{
        @apply bg-lime-600 text-center uppercase w-full text-white font-bold tracking-wider px-4 py-2 rounded-sm hover:bg-lime-800 transition-colors block cursor-pointer
    }
}
```

平时写的className里的都属于utilities级别，因此在组合成components级别以后在行内写className可以覆盖

### 语法

字体相关：
`font-bold`    粗字体
`text-white`  文本颜色

`text-center`   text-align: center

`uppercase`   text-transform: uppercase

`tracking-wider`  letter-spacing 宽一点

`text-transparent bg-clip-text bg-gradient-to-b from-indigo-600 to-sky-600` 文字有渐变颜色

overflow相关：
`overflow-hidden`
`overflow-auto`

背景颜色相关：
`bg-blue-500`
`bg-gradient-to-b from-blue-500 to-white`   背景从上到下渐变

height和width相关：
`h-screen`   高度等于屏幕高度
`max-h-screen`   最大高度等于屏幕高度
`min-w-[30px]`  min-width:30px
`w-full`    width:100%
`h-20`   height:5rem /80px

flex相关：
`items-center`   align-items:center
`gap-2`

border相关：
`border-t`   border-top:1px
`border-black/50`   border-color:black + 50%不透明度
`border-t-black/50`   border-top-color:black + 50%不透明度

圆角相关：
`rounded-full`  border-radius:50%
`rounded-sm`   2px圆角

display相关：
`block`    display:block

指针相关：
`cursor-pointer`    cursor:pointer

过渡相关：
`transition-color`   与color相关的元素都将获得150ms的transition效果

filter相关：
`backdrop-blur-sm`   模糊效果

!important:
直接在className前加感叹号


### 自制ripple animation：

taiwind.config.js
```js
  theme: {
    extend:{
      animation: {
        'ripple': 'ripple 0.5s ease-out',
      },
      keyframes: {
        ripple: {
          '0%': { transform: 'translate(-50%,-50%) scale(0)',opacity:'1' },
          '100%': {transform: 'translate(-50%,-50%) scale(5)',opacity:'0' },
        }
      }
    },
  },
```






