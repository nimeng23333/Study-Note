---
title: interface
---
`interface` 是一种**类型定义**，用于描述对象或类的结构。

可以理解为：

 “我定义了一个接口，任何东西想用这个接口，就必须遵守它的规则。”
 
`class A implements B` 表示类 A **实现了接口 B**，也就是说它必须满足接口 B 的所有要求。

```ts
interface Animal {
  name: string;
  speak(): void;
}

class Dog implements Animal {
  name: string;

  constructor(name: string) {
    this.name = name;
  }

  speak() {
    console.log(`${this.name} says woof`);
  }
}

```


## interface vs type：核心区别

| 特性                       | `interface` | `type` |
| ------------------------ | ----------- | ------ |
| 可扩展（extends）             | 支持          | 支持     |
| 可合并（declaration merging） | ✅ 支持        | ❌ 不支持  |
| 表示对象结构                   | 更推荐         | 可以     |
| 联合类型 / 映射类型              | ❌ 不支持       | ✅ 强项   |
| 性能（编译时）                  | 更快          | 稍慢     |

## 为什么这里推荐用 `interface`


```ts
export interface AppGlobalState {
  locale: Locale;
}
```

是完全正确的，但也可以用 `type` 来写：

```ts
export type AppGlobalState = {
  locale: Locale;
};
```

定义的是一个对象结构：

```ts
{
  locale: Locale;
}
```

这是典型的“接口式”数据结构，**interface 更语义化**，也更容易被扩展和合并。

### 举个例子：你可以在多个地方扩展它

```ts
// appGlobal.ts
export interface AppGlobalState {
  locale: Locale;
}

// theme.ts
declare module './appGlobal' {
  interface AppGlobalState {
    theme: 'light' | 'dark';
  }
}
```

这种“声明合并”只有 `interface` 支持，`type` 是不行的。

## 用 `type` 的风险（在大型项目中）

- 不支持合并，扩展性差
    
- 如果你用 `type` 定义 Redux 的 state，后期加字段可能要重写整个类型
    
- 某些库（比如 styled-components）对 `interface` 的兼容性更好
    

## 总结建议

如果定义的是对象结构，尤其是 Redux 的 state，推荐用 `interface`。 如果你需要联合类型、映射类型、复杂类型组合，用 `type`。

## 联合类型（Union Type）

这是 TypeScript 的一种类型表达方式，用 `|` 表示“可以是 A 或 B”。
### 示例：

```ts
type Theme = 'light' | 'dark';
type Status = 'loading' | 'success' | 'error';

function setTheme(theme: Theme) {
  // theme 只能是 'light' 或 'dark'
}
```

### 用途：

- 表示值的可能性
    
- 枚举式类型
    
- 控制分支逻辑（比如 switch）
    

## 类型合并（Declaration Merging）

这是 `interface` 独有的能力：**多个 interface 同名时自动合并为一个**。

### 示例：

```ts
interface User {
  name: string;
}

interface User {
  age: number;
}

// 实际上等同于：
interface User {
  name: string;
  age: number;
}
```