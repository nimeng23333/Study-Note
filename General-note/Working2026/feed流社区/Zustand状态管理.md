---
title: Zustand状态管理
---
Zustand 的核心只有一句话：

> **用** **`create`** **创建 store，用 hook 直接取状态**

### 创建 store

```TypeScript
// store/useCounterStore.ts
import { create } from 'zustand'

type CounterState = {count: number
  inc: () => void
  dec: () => void
}

export const useCounterStore = create<CounterState>((set) => ({count: 0,inc: () => set((state) => ({ count: state.count + 1 })),dec: () => set((state) => ({ count: state.count - 1 })),
}))
```

---

### 在组件中使用

```TypeScript
function Counter() {const { count, inc, dec } = useCounterStore()
return (<>
      <p>{count}</p><button onClick={inc}>+</button><button onClick={dec}>-</button></>
  )
}
```

**不需要 Provider** **任何组件都能直接用**

---

## 只订阅一部分状态（性能关键）

不推荐：

```TypeScript
const store = useCounterStore() // 状态多时会导致多余渲染
```

推荐（selector）：

```TypeScript
const count = useCounterStore((state) => state.count)
const inc = useCounterStore((state) => state.inc)
```

**只有 count 变化才会触发重渲染**

---

## 异步操作

Zustand 里**action 可以直接写 async**

```TypeScript
type UserState = {user: null | { name: string }fetchUser: () => Promise<void>
}

export const useUserStore = create<UserState>((set) => ({user: null,fetchUser: async () => {const res = await fetch('/api/user')const data = await res.json()set({ user: data })
  },
}))
```

组件中：

```TypeScript
const { user, fetchUser } = useUserStore()

useEffect(() => {fetchUser()
}, [])
```

---

## Store 拆分

### 一个 store = 一个领域

`useAuthStore` `useCartStore` `useThemeStore`

Zustand **不鼓励一个巨大 root store**（和 Redux 相反）

---

## 跨组件 & 非 React 使用

### 在 React 外部读取状态

`useCounterStore.getState().count` `useCounterStore.getState().inc()`

### 监听状态变化

```TypeScript
const unsub = useCounterStore.subscribe((state) => state.count,(count) => {console.log('count changed:', count)
  }
)

// 取消监听
unsub()
```

---

## 持久化（localStorage）

```TypeScript
import { persist } from 'zustand/middleware'

export const useCounterStore = create(persist((set) => ({count: 0,inc: () => set((s) => ({ count: s.count + 1 })),
    }),
    {name: 'counter-storage',
    }
  )
)
```

刷新页面数据不会丢

---

## 常见坑

不要在 store 里直接改对象：

```TypeScript
// ❌
state.user.name = 'xxx'

// ✅
set((state) => ({user: { ...state.user, name: 'xxx' }
}))
```

selector 写错会导致多余渲染

  

# 项目中应用

- 基础模式：每个状态切片都是一个自包含的 hook，通过 create 定义 state 与 action，没有 reducer/dispatch 的分层，直接在 action 里调用 set。
    

```JavaScript
30:104:packages/liblib/src/store/aiInfo.ts
export const AiInFo = create<AiStore>((set) => {
  return {
    apiName: '',
    power: 0,
    // ...
```

- 调试支持：部分 store 包了 devtools 中间件，可在 Redux DevTools 中查看。
    

```JavaScript
37:130:packages/liblib/src/store/message.ts
export const useMessageStore = create<MessageState>()(
  devtools((set, get) => {
    // state + 异步 action，如 fetch/markRead
  }),
);
```

- 持久化：使用 zustand/middleware 的 persist 将状态写入 localStorage。
    

```JavaScript
16:46:packages/liblib/src/pages/uploadworkflow/store.ts
export const useModelStore = create<ModelStore>()(
  persist((set, get) => ({
    values: { /* 默认表单值 */ },
    setValues: (values) => set({ values: { ...get().values, ...values } }),
  }), { name: 'preference' }),
);
```

  

类似的还有 packages/web-sdlite/src/store/index.js 的任务、用户信息，带 partialize 仅存部分字段。

  

- 性能优化与工具封装：在 AI 工具页使用 createWithEqualityFn + 自定义 createStoreUtils，提供 useStore('field')/getState('field') 这样的便捷选择器，减少重复渲染。
    

```JavaScript
55:94:packages/liblib/src/features/ai-tool/store/temp.ts
export const _useTempState = createWithEqualityFn<TempState>()(() => ({
  imageStyleModalOpen: false,
  tasks: [],
  // ...
}));
const { useStore: useTempState, getState: getTempState, setState: setTempState } = createStoreUtils(_useTempState);
```

`createStoreUtils` 允许字符串/数组选择器和直接获取非响应式 state：

```JavaScript
19:80:packages/liblib/src/utils/createStoreUtils.ts
// useStore('a') / useStore(['a','b']); getState('a'); setState(partial | draftFn)
```

  

- 其他切片：`useLinkageStore`（页面联动，devtools 包装）、`ApiInFo`（接口配置状态）、`useIndexStore`（_stores 目录的基础切片）。_
    

  

- 无 reducer/action type：定义 state 和修改函数同处一个对象，直接 set({ key })；异步 action 里可 get() 读取当前 state。
    
- 组件中使用：`const power = AiInFo((s) => s.power); const setPower = AiInFo((s) => s.setPower);`；在带 `createStoreUtils` 的切片可简写 `const power = useTempState('power')`。
    
- DevTools：使用 devtools 包装的 store 可在 Redux DevTools 里观察变化；持久化的 store 自动写 localStorage。
    
- 选择器与性能：建议始终传选择器函数或字符串字段，避免无选择器导致组件全量重渲染；createWithEqualityFn 已为部分大状态启用浅比较。