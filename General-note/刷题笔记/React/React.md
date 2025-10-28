---
title: React
---
# 1. 在下面的 React 组件中，如果用户多次点击按钮，控制台每秒会打印出什么？
```JSX
function Counter() {
  const [count, setCount] = useState(0);
  useEffect(() => {
    const intervalId = setInterval(() => {
      console.log(count);
    }, 1000);
    return () => clearInterval(intervalId);
  }, []); // 注意此处的空依赖数组
  return <button onClick={() => setCount((c) => c + 1)}>Increment</button>;
}
```

这是一个非常经典的 React 闭包陷阱问题

- `UseEffect(..., [])` 中的空依赖数组意味着这个 effect **只会在组件挂载时执行一次**。
    
- `setInterval(() => console.log(count), 1000)` 中的 `count` 是 **闭包捕获的初始值**，即 `0`。
    
- 即使你点击按钮更新了 `count`，这个 `setInterval` 中的函数 **不会重新捕获新的 count 值**，因为 effect 没有重新运行。

如果你希望每秒打印的是最新的 `count`
方法一：把 `count` 加入依赖数组
```JSX
useEffect(() => {
  const intervalId = setInterval(() => {
    console.log(count);
  }, 1000);
  return () => clearInterval(intervalId);
}, [count]); // 每次 count 变化都会重新设置 interval
```

但这会导致 `setInterval` 每次都被清除和重建。
方法二：使用 ref 保存最新值

```JSX
const countRef = useRef(count);
useEffect(() => {
  countRef.current = count;
}, [count]);

useEffect(() => {
  const intervalId = setInterval(() => {
    console.log(countRef.current);
  }, 1000);
  return () => clearInterval(intervalId);
}, []);
```

这样 `setInterval` 只设置一次，但每次读取的是最新的 `countRef.current`。

核心原理：ref 是一个可变的容器对象

在 React 中，`useRef(initialValue)` 返回的是一个**持久的对象**，它的结构是：

```JSX
{ current: initialValue }
```

这个对象在组件的整个生命周期中**不会变**，但它的 `.current` 属性是**可变的**。这就像你有一个盒子（ref 对象），盒子本身不会换，但你可以随时往里面放不同的东西（更新 `.current`）。
闭包 vs ref：谁能看到“现在”的值？

|特性|闭包中的变量（如 `count`）|`ref.current`|
|---|---|---|
|捕获时间|创建闭包时（一次性）|始终指向同一个对象|
|是否会更新|不会自动更新|可以手动更新 `.current`|
|是否触发重新渲染|会|不会|
|用于定时器/事件监听|容易过期（stale）|始终最新|

ref 就是“稳定引用 + 可变内容”，可以类比为const arr，可以通过push或者pop改变arr里的内容，但不会改变本身

# 2. JSX 仅仅只是 React.createElement(component, props, ...children) 函数的语法糖。

# 3.严格模式检查仅在开发模式下运行，它们不会影响生产构建。

# 4.从 DOM 中卸载组件，可以使用ReactDOM.unmountComponentAtNode（已废弃）


### React 常见 API（核心逻辑层）

这些 API 来自 `react` 包，主要用于构建组件、管理状态和生命周期：

#### 组件与元素构建

|API|作用|
|---|---|
|`React.createElement`|手动创建虚拟 DOM（JSX 的底层实现）|
|`React.cloneElement`|克隆已有元素并注入新 props|
|`React.isValidElement`|判断某个对象是否是合法的 React 元素|

#### Hooks（函数组件的核心）

|Hook|作用|
|---|---|
|`useState`|添加本地状态|
|`useEffect`|副作用处理（如订阅、定时器）|
|`useRef`|创建可变引用，不触发重渲染|
|`useContext`|读取上下文值|
|`useMemo` / `useCallback`|性能优化，缓存值或函数|
|`useReducer`|类似 Redux 的状态管理方式|
|`useLayoutEffect`|类似 `useEffect`，但同步触发，适合 DOM 测量|
|`useImperativeHandle`|暴露自定义 ref 方法给父组件（配合 `forwardRef`）|

#### 结构辅助

|API|作用|
|---|---|
|`React.Fragment` / `<>...</>`|返回多个元素而不添加额外 DOM|
|`React.StrictMode`|开启开发时额外检查|
|`React.Suspense`|异步组件加载占位符|
|`React.lazy`|懒加载组件|

### ReactDOM 常见 API（DOM 渲染层）

这些 API 来自 `react-dom` 或 `react-dom/client`，用于将组件挂载到浏览器 DOM：

#### 渲染与卸载

|API|作用|
|---|---|
|`ReactDOM.createRoot(container)`|创建根节点（React 18+ 推荐）|
|`root.render(<App />)`|渲染组件|
|`root.unmount()`|卸载组件（替代旧的 `unmountComponentAtNode`）|
|`ReactDOM.unmountComponentAtNode(container)`|卸载组件（旧 API）【已废弃】|

####  DOM 操作

|API|作用|
|---|---|
|`ReactDOM.findDOMNode(component)`|获取组件对应的真实 DOM 节点（不推荐）|
|`ReactDOM.createPortal(child, container)`|将子组件渲染到 DOM 的其他位置（如模态框）|

#### 同步刷新

|API|作用|
|---|---|
|`ReactDOM.flushSync(fn)`|强制同步刷新状态更新（用于动画、测量）|

### 小贴士：React vs ReactDOM 的职责分离

- **React**：负责组件逻辑、状态、生命周期、虚拟 DOM。
    
- **ReactDOM**：负责把虚拟 DOM 映射到浏览器真实 DOM。


# 5.  在 React 的并发模式 (Concurrent Mode) 下，一个计算量较大的渲染任务正在进行中。此时，一个高优先级的用户交互事件（如输入框输入）触发了一个新的状态更新。React 的调度器会如何响应？

立即中断当前的低优先级渲染任务，优先处理高优先级更新，完成后再从头开始或继续之前的渲染任务。

React 的并发模式（Concurrent Mode）是一种新的渲染策略，允许 React 在渲染过程中中断、恢复、跳过或重新安排任务，从而提升响应速度和用户体验。它不是并行执行，而是更智能的任务调度。

# 6.  Class内部有一handleClick方法如下代码块，在点击事件中触发handleClick的正确方法是？
```jsx
handleClick(){
	console.log()
}

onClick = {()=> this.handleClick()}
```

事件绑定this的方法有四种： （1）render方法中使用bind； （2）render方法中使用箭头函数；（3）constructor中bind； （4）定义阶段使用箭头函数绑定；

选项 B：onClick={ this.handleClick()}点击之前就会触发handleClick()
选项 C：onClick={ this.handleClick}handleClick 方法中的 this 指向会丢失。
选项 D：onclick={ handleClick}应该是onClick

# 7. React复合输入事件有onCompositionEnd，onCompositionStart，onCompositionUpdate

在 input 标签下我们有三个事件名称可以监听组合输入的事件，不论是简体中文或是繁体中文或是其他任何输入会被替换成最终输入的文字的语言都一样

compositionstart：组合输入开始
compositionupdate：组合输入更新
compositionend：组合输入结束

# 8.在 React 中，JSX 无法直接渲染 JavaScript 对象（如 { name: 'John' }），因为对象不是有效的 React 子元素。React 会在控制台抛出错误，例如 'Uncaught Error: Objects are not valid as a React child'

# 9.<Route />中的path属性可以使用的通配符
|语法|含义|示例|匹配路径|
|---|---|---|---|
|`:param`|动态参数|`/user/:id`|`/user/123`、`/user/abc`|
|`:param?`|可选参数|`/user/:id?`|`/user`、`/user/123`|
|`*`|匹配任意路径段（非贪婪）|`/files/*`|`/files/a`、`/files/a/b`|
|`**`|匹配任意路径（贪婪）|`/docs/**`|`/docs/a/b/c/d`|
|`*.*`|匹配文件名和扩展名|`/files/*.*`|`/files/cat.jpg`、`/files/doc.pdf`|
|`()`|可选路径段（旧版语法）|`/hello(/:name)`|`/hello`、`/hello/dai`|
|`:param(\\d+)`|正则匹配参数（v6 支持）|`/user/:id(\\d+)`|`/user/123`（仅数字）|

# 10.在如下 React 类组件中，为 props 进行类型检查，要求 name 为必填字符串。
```jsx
import PropTypes from "prop-types"

class Greeting extends React.Component {
	render(){
		return (
			<h1>Hello, {this.props.name} </h1>
		)
	}
}

Greeting.propTypes = { name: PropTypes.string.isRequired };
```

| Hook 名称               | 返回值 / 功能描述                                                | 常见用途示例                              |
| --------------------- | --------------------------------------------------------- | ----------------------------------- |
| `useNavigate()`       | 返回一个函数，可用于编程式导航                                           | 跳转页面、后退、替代导航等                       |
| `useLocation()`       | 返回当前 location 对象 `{ pathname, search, hash, state, key }` | 获取当前路径、查询参数、页面状态等                   |
| `useParams()`         | 返回当前匹配路由的动态参数对象 `{ id, slug, ... }`                       | 获取 URL 中的动态参数                       |
| `useSearchParams()`   | 返回 `[searchParams, setSearchParams]`，用于读写 URL 查询参数        | 操作 `?key=value` 查询字符串               |
| `useMatch(pattern)`   | 返回匹配结果对象或 `null`，用于判断当前路径是否匹配某个模式                         | 条件渲染、激活状态判断                         |
| `useOutlet()`         | 返回嵌套路由的渲染结果（React 元素），用于布局组件中插入子路由                        | 实现嵌套布局，如 sidebar + main content     |
| `useRoutes(routes)`   | 返回根据路由配置渲染的元素（通常用于自定义路由结构）                                | 动态路由渲染，替代 `<Routes>` + `<Route>` 写法 |
| `useNavigationType()` | 返回当前导航类型：`"POP"`、`"PUSH"` 或 `"REPLACE"`                   | 判断用户是点击链接、浏览器后退，还是编程式跳转             |
| `useFetcher()`        | 返回一个对象用于发起异步请求（Remix 风格）                                  | 用于数据获取、表单提交等（React Router v6.4+ 支持） |
| `useFormAction()`     | 返回当前表单的 action URL，用于构建表单提交路径                             | 与 `<Form>` 组件配合使用                   |

# 11.可以使子节点渲染到存在于父组件以外的DOM节点上的方法是
  
ReactDOM.createPortal

# 12.在React上下文(Context)中，若Provider的value属性使用字面量对象直接赋值，如`value={{ theme: 'dark' }}`，会导致消费组件必然每次渲染都重新渲染

在React Context中，当Provider的value属性使用对象字面量直接赋值（如`value={{ theme: 'dark' }}`）时，每次组件渲染都会创建一个新对象。React Context通过引用比较来判断值是否变化，因此新对象会导致引用不同，从而触发所有消费该Context的组件重新渲染。

# 13.在一个使用 `useEffect` 的函数组件中，如果 `useEffect` 的依赖项数组发生了变化，导致 effect 需要重新执行，那么上一次 effect 返回的清理（cleanup）函数会在什么时机被调用？

在组件完成渲染且 DOM 更新后，但在下一次 effect 执行之前。

`useEffect` 的返回值是一个 **清理函数（cleanup function）**，它的作用是在以下时机执行“副作用的清理”逻辑：

- 组件卸载时（unmount）
- 或者依赖项变化前（下一次执行 effect 前）

# 14.在一个包含大量输入框的复杂表单中，输入频繁且仅在提交时需要读取值。为了获得更好的性能与更少的重渲染，以下哪种设计更合适？
使用非受控组件，通过 ref 在提交时读取值，减少渲染与状态更新开销

# 15.验证对象是否为 React 元素可以使用 React.isValidElement 方法

# 16.你在实现一个“输入即搜索”的页面：输入框的值需要即时回显，同时根据输入渲染一个代价较大的结果列表。当前输入会明显卡顿。要求：不牺牲输入框的即时回显，也不改变接口/请求策略，优先利用 React 18 的能力改善交互流畅度。应优先采用哪种做法？

  
将触发列表更新的 setState 包裹在 startTransition 中（或使用 useTransition 提供的 startTransition）以标记为非紧急更新

在 React 18 中，**优先采用** `startTransition` **包裹“代价较大的渲染逻辑”** 是最推荐的做法，可以显著提升交互流畅度，尤其是在“输入即搜索”场景下。

```jsx
import { useState, useTransition } from 'react';

function SearchPage() {
  const [input, setInput] = useState('');
  const [results, setResults] = useState([]);
  const [isPending, startTransition] = useTransition();

  const handleChange = (e) => {
    const value = e.target.value;
    setInput(value); // ✅ 立即回显输入框

    startTransition(() => {
      // ✅ 异步触发代价较大的搜索逻辑（如过滤、渲染）
      const newResults = expensiveSearch(value);
      setResults(newResults);
    });
  };

  return (
    <>
      <input value={input} onChange={handleChange} />
      {isPending && <p>加载中...</p>}
      <ResultList data={results} />
    </>
  );
}

```

# 17.**React 支持的动画事件包括：**`onAnimationStart`**、**`onAnimationEnd` **和** `onAnimationIteration`**，它们是对原生 CSS Animation 事件的封装。**

| 事件名                    | 触发时机      | 对应原生事件               | 常见用途示例           |
| ---------------------- | --------- | -------------------- | ---------------- |
| `onAnimationStart`     | 动画开始时触发   | `animationstart`     | 设置初始状态、记录动画开始时间等 |
| `onAnimationEnd`       | 动画完成时触发   | `animationend`       | 清理动画类名、触发后续逻辑等   |
| `onAnimationIteration` | 动画每次重复时触发 | `animationiteration` | 统计循环次数、触发中间状态更新等 |
| `onTransitionEnd`      |           | `transitionend`      | transition       |
| `onAnimationStart`     |           | `beginEvent` 等       | SVG动画            |

# 18.你在实现一个弹出层，打开时需要测量元素尺寸并同步计算定位，要求避免首次出现时的可见“跳动”。应使用哪种 Hook 与时机？
题目要求避免弹出层首次打开时的可见跳动，这意味着必须在浏览器绘制屏幕前完成尺寸测量和定位计算，以确保元素首次渲染时位置正确。在React中，useLayoutEffect Hook 在DOM变更后、浏览器绘制前同步运行，允许同步读取布局信息（如元素尺寸）并更新位置，从而避免视觉跳动。