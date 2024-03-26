---
title: React
---

[React JS crash course 2024 for beginners - YouTube](https://www.youtube.com/watch?v=HVFSgIVXcD4)

扩展阅读
[一文讲通React的diff过程 - 掘金 (juejin.cn)](https://juejin.cn/post/7212918899867041849)
[漫谈 react 系列(一)：初探 react 的工作过程 - 掘金 (juejin.cn)](https://juejin.cn/post/7007622432013942798)
[vkboo/react-interview-qa: [React 328道最全面试题(持续更新)](https://juejin.cn/post/6844903892853981198) 个人答案📝 (github.com)](https://github.com/vkboo/react-interview-qa)


### Context、State、Porps

- **Props**是一种用于向组件传递数据的机制。它们是只读的，因此不能在组件内部更改。相反，它们是由父组件传递给子组件的。您可以将它们视为组件之间通信的一种方式。例如，您可以将一个字符串或一个对象作为prop传递给另一个组件，以便该组件可以使用该数据进行渲染。
    
- **State**是一种用于管理组件内部数据的机制。与props不同，state是可变的。当组件的状态发生变化时，React会自动重新渲染组件。您可以将其视为组件的内部存储器。例如，您可以使用state来存储用户在表单中输入的数据。
    
- **Context**是一种用于在组件树中共享数据的机制。它允许您将数据传递给组件树中的所有组件，而不必手动将其传递给每个组件。这对于在应用程序中共享全局数据非常有用。例如，您可以使用context来共享当前用户的身份验证状态。


### 调用组件
调用组件时组件不要self-closing tag而是正常写成两个，并在里面加东西，东西传递给组件是props.children
如下：

```jsx
<Card> This is a card. </Card>
```

```jsx
function Card(props){
	return <div>{props.children}</div>  //props.children = This is a card.
}
```

在组件里不光可以加string还可以加react组件、html标签


![[Next.js & React 路由#useRouter]]
### useEffect hook

`useEffect(()=>{},[])`
这个hook第一个是回调函数，第二个是array，每次array改变都将重新调用一次前面的回调函数

将useEffect包含在组件里，每次这个组件被调用渲染的时候相当于重新传入了一次空数组，因此都会调用这个回调函数

扩展阅读
[useMemo, useCallback, useEffect 三者区别 - 掘金 (juejin.cn)](https://juejin.cn/post/7008433550307360798)


### useReducer hook

useState 升级版，更高级的存储状态
语法：
useState:
```js
const [state, setState] = useState(initialState)
function handler(){
	setState(newState)
}
```

useReducer:
```js
const initialState = 0;
const reducer = (state, action) => {
  switch (action) {
    case 'increment': return state + 1;
    case 'decrement': return state - 1;
    case 'reset': return 0;
    default: throw new Error('Unexpected action');
  }
};

const Example01 = () => {
  const [count, dispatch] = useReducer(reducer, initialState);
  return (
    <div>
      {count}
      <button onClick={() => dispatch('increment')}>+1</button>
      <button onClick={() => dispatch('decrement')}>-1</button>
      <button onClick={() => dispatch('reset')}>reset</button>
    </div>
  );
};

```

扩展阅读
[React useReducer 终极使用教程 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/559921051)
### 注入HTML - dangerouslySetInnerHTML

`dangerouslySetInnerHTML` is React’s replacement for using `innerHTML` in the browser DOM. In general, setting HTML from code is risky because it’s easy to inadvertently expose your users to a [cross-site scripting (XSS)](https://en.wikipedia.org/wiki/Cross-site_scripting) attack. So, you can set HTML directly from React, but you have to type out `dangerouslySetInnerHTML` and pass an object with a `__html` key, to remind yourself that it’s dangerous.

```jsx
function createMarkup() {
  return {__html: 'First &middot; Second'};
}

function MyComponent() {
  return <div dangerouslySetInnerHTML={createMarkup()} />;
}
```

The immediate effect of using `innerHTML` versus `dangerouslySetInnerHTML` is identical -- the DOM node will update with the injected HTML.

_However_, behind the scenes when you use `dangerouslySetInnerHTML` it lets React know that the HTML inside of that component is not something it cares about.

Because React uses a virtual DOM, when it goes to compare the diff against the actual DOM, it can straight up bypass checking the children of that node _because it knows the HTML is coming from another source_. So there's performance gains.

**More importantly**, if you simply use `innerHTML`, React has no way to know the DOM node has been modified. The next time the `render` function is called, **React will overwrite the content** that was manually injected with what it thinks the correct state of that DOM node should be.


原生innerHTML有安全风险，REACT的dangerouslySetInnerHTML可以避免，同时由于REACT使用的是虚拟DOM，dangerouslySetInnerHTML会告诉REACT这部分是从别的地方插进来的，而不需要进行对比，如果不用这个有可能跟原生DOM比较发现有不一致从而把这一段修正了。
dangerouslySetInnerHTML需要跟`__html`使用
`<div dangerouslySetInnerHTML={{__html:postContent}}></div>`


### 点击复制到剪贴板
```jsx
import { useState } from "react";
import { faCheck } from "@fortawesome/free-solid-svg-icons";
import { faCopy } from "@fortawesome/free-solid-svg-icons";
import { FontAwesomeIcon } from "@fortawesome/react-fontawesome"

export const Copy = ({ copyText }) => {
    const [isCopied, setIsCopied] = useState(false);
    const handleCopyClick = async () => {
      try {
        await navigator.clipboard.writeText(copyText);
        setIsCopied(true);
        setTimeout(() => {
          setIsCopied(false);
        }, 1000);
      } catch (err) {
        console.log(err);
      }
    }
    return (
      <>
        <input className="whitespace-pre-line" type="hidden" value={copyText}/>
        <button onClick={handleCopyClick}>
          <span className="text-white">{isCopied ? <FontAwesomeIcon icon={faCheck}/> : <FontAwesomeIcon icon={faCopy}/>}</span>
        </button>
      </>
    );
  }
```


### 点击事件 - currentTarget

希望无论点击按钮的哪个部分，`e.target`都是按钮本身，而不是内部的图标或文本。这样，`e.target.offsetLeft`和`e.target.offsetTop`就会始终基于按钮的位置。

通过使用`e.currentTarget`来替代`e.target`来实现这个目标。`e.currentTarget`始终指向添加事件监听器的元素，而`e.target`则是触发事件的元素。
