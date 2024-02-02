
懒加载，延迟加载

不像传统分页，先加载前五个，用户点击后再加载剩下的

### 1.React.creatContext({})
react的一个api，用于创建一个context对象，当 React 渲染一个订阅了这个 Context 对象的组件时，这个组件会从组件树中离自身最近的那个匹配的 Provider 中读取到当前的 context 值。

step1 创建context对象

step2 使用provider提供context值

step3 使用useContext读取context的值

示例：
```jsx
import React, { createContext, useState } from 'react';

// 创建一个 Context 对象
const ThemeContext = createContext('light');

function App() {
  const [theme, setTheme] = useState('light');

  return (
    // 使用 Provider 提供 context 的值
    <ThemeContext.Provider value={theme}>
      <Page />
    </ThemeContext.Provider>
  );
}

function Page() {
  // 使用 useContext 读取 context 的值
  const theme = useContext(ThemeContext);

  return <div className={theme} />;
}
```


### 2.useCallback()

useCallback 是 React Hooks 中的一种，它允许我们缓存函数，从而避免因为函数引用的变化而导致不必要的子组件重渲染1。在函数式组件中，由于状态更新导致的函数引用变化，可能会使组件无法正确地复用已渲染的 DOM，从而降低性能。

useCallback 接收一个函数和一个依赖项数组作为参数，然后返回一个 memoized 版本的函数。如果依赖项数组中的任何一个依赖项发生更改，则 useCallback 会返回一个新的函数。如果依赖项数组不变，则 useCallback 将返回上次 memoized 的函数。

使用 useCallback 的好处是可以避免在每次重新渲染时都创建新的回调函数，从而减少不必要的组件重新渲染。尤其是当传递给子组件的回调函数发生更改时，这种优化可以显著提高性能。

例子：
```jsx
import React, { useState, useCallback } from 'react';
function Counter() {  
  const [count, setCount] = useState(0);
  const increment = useCallback(() => {  
    setCount((prevCount) => prevCount + 1);  
  }, []);
  const decrement = useCallback(() => {  
    setCount((prevCount) => prevCount - 1);  
  }, []);
  return (  
    <>  
      <button onClick={decrement}>-</button>  
      <span>{count}</span>  
      <button onClick={increment}>+</button>  
    </>  
  );  
}
export default Counter;  
```

在这个例子中，我们使用了 useCallback 来缓存 increment 和 decrement 函数。由于 count 状态的变化，这两个函数会被重新生成。但是，由于我们并没有在依赖项数组中传入任何变量，所以即使 count 发生变化，increment 和 decrement 函数的内部实现也不会发生变化。这就实现了性能优化。

扩展阅读
[深入剖析 React Hooks 中的 useCallback - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/665759859)
useCallback主要存储函数，useMemo可以用来缓存一个值

useCallback 可以帮助我们优化组件性能。在使用 useCallback 时，要注意确保依赖项数组完整，避免循环引用，以及不要滥用 useCallback。

[React Hooks - useCallback讲解与使用场景 - 掘金 (juejin.cn)](https://juejin.cn/post/7229573641371942971)
[React 函数组件更新过程 - 掘金 (juejin.cn)](https://juejin.cn/post/7225178562549415994)

[useMemo, useCallback, useEffect 三者区别 - 掘金 (juejin.cn)](https://juejin.cn/post/7008433550307360798)