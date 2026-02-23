---
title: MasonryGrid瀑布流自适应卡片宽度和列数
---
# MasonryGrid 自适应布局实现机制

  

## 概述

  

`MasonryGrid` 是一个基于 `masonic` 库实现的瀑布流网格组件，能够根据容器宽度自动调整列数和卡片宽度，实现响应式布局。

  

## 核心实现原理

  

### 1. 容器尺寸监听

  

组件使用 `useSize` hook 来监听容器尺寸的变化：

  

```typescript

const { width: containerWith } = useSize(containerElement, widthUpdateDeps);

const { height } = useSize(scrollContainer, widthUpdateDeps);

```

  

#### useSize Hook 的工作原理

  

`useSize` hook 通过以下机制实现尺寸监听：

  

- **ResizeObserver API**：监听容器元素的尺寸变化

- **Window 事件监听**：监听 `window` 的 `resize` 和 `orientationchange` 事件

- **防抖优化**：使用 16ms 的防抖延迟（约 60fps），避免频繁更新

- **尺寸计算**：自动排除容器的 padding，获取实际可用宽度

  

当容器尺寸发生变化时，`useSize` 会自动更新返回的 `width` 和 `height` 值，触发组件重新计算布局。

  

### 2. 响应式断点系统

  

组件使用预定义的断点数组来确定不同屏幕宽度下的列数：

  

```typescript

export const columnCountBreakPoints = [

{ minWidth: 0, columnCount: 2 },

{ minWidth: 350, columnCount: 2 },

{ minWidth: 500, columnCount: 3 },

{ minWidth: 800, columnCount: 4 },

{ minWidth: 1164, columnCount: 5 },

{ minWidth: 1471, columnCount: 6 },

{ minWidth: 1758, columnCount: 7 },

].reverse();

```

  

**断点说明**：

- 数组已反转（`.reverse()`），从大到小匹配

- 使用 `find` 方法查找第一个满足 `width > minWidth` 的断点

- 如果找不到匹配的断点，返回默认值 `DEFAULT_COLUMN_COUNT`（4列）

  

**响应式列数规则**：

- 宽度 ≤ 350px：2列

- 宽度 350px - 500px：2列

- 宽度 500px - 800px：3列

- 宽度 800px - 1164px：4列

- 宽度 1164px - 1471px：5列

- 宽度 1471px - 1758px：6列

- 宽度 > 1758px：7列

  

### 3. 列数计算函数

  

```typescript

const calcColumnCount = (width: number) => {

const breakPoint = columnCountBreakPoints.find(({ minWidth }) => width > minWidth);

return breakPoint ? breakPoint.columnCount : DEFAULT_COLUMN_COUNT;

};

```

  

该函数根据可用宽度查找匹配的断点，返回对应的列数。

  

### 4. 宽度和列宽计算

  

组件使用 `useMemo` 来缓存计算结果，避免不必要的重新计算：

  

```typescript

const { width, columnWidthCalculated, columnCount } = useMemo(() => {

// 少量卡片的场景做了 scrollbar 宽度补偿

const addScrollBar =

restProps.items.length < DEFAULT_COLUMN_COUNT * 2 &&

window.innerHeight > document.body.scrollHeight;

  

const calculatedWidth = Math.max(

0,

Math.min(

maxWidth,

containerWith - padding + (addScrollBar ? window.innerWidth - document.body.clientWidth : 0)

),

);

  

const calculatedColumnWidth = columnWidth || DEFAULT_MAX_WIDTH / maxColumnCount;

const calculatedColumnCount = calcColumnCount(calculatedWidth);

  

return {

width: calculatedWidth,

columnWidthCalculated: calculatedColumnWidth,

columnCount: calculatedColumnCount,

};

}, [containerWith, padding, maxWidth, columnWidth, maxColumnCount, restProps.items.length]);

```

  

#### 计算步骤详解

  

1. **可用宽度计算**：

- 基础宽度：`containerWith - padding`

- 滚动条补偿：当卡片数量较少（少于 8 个）且页面高度不足时，补偿滚动条宽度

- 最终宽度：`Math.min(maxWidth, 基础宽度 + 滚动条补偿)`

  

2. **列宽计算**：

- 如果传入了 `columnWidth`，直接使用

- 否则使用默认值：`DEFAULT_MAX_WIDTH / maxColumnCount`（默认 2560 / 4 = 640px）

  

3. **列数计算**：

- 调用 `calcColumnCount(calculatedWidth)` 根据可用宽度计算列数

  

#### 滚动条补偿机制

  

当满足以下条件时，会进行滚动条宽度补偿：

- 卡片数量 < `DEFAULT_COLUMN_COUNT * 2`（即少于 8 个）

- `window.innerHeight > document.body.scrollHeight`（页面高度不足，可能出现滚动条）

  

补偿值 = `window.innerWidth - document.body.clientWidth`（滚动条的实际宽度）

  

这样可以避免在少量卡片场景下，因为滚动条的出现导致布局偏移。

  

### 5. 自动更新流程

  

组件的自适应更新遵循以下流程：

  

```

容器尺寸变化

↓

useSize 检测到变化（ResizeObserver / window resize）

↓

containerWith 更新

↓

useMemo 重新计算 width、columnCount、columnWidthCalculated

↓

positionerConfig 更新

↓

usePositioner 重新创建定位器

↓

Masonry 布局自动更新

```

  

### 6. 性能优化策略

  

组件采用了多种性能优化策略：

  

1. **Memoization**：

- 使用 `useMemo` 缓存计算结果，避免不必要的重新计算

- 使用 `useCallback` 缓存回调函数

  

2. **防抖处理**：

- `useSize` hook 使用 16ms 防抖，减少更新频率

- 仅在尺寸实际变化时更新状态

  

3. **依赖优化**：

- 精确控制 `useMemo` 和 `useCallback` 的依赖数组

- 避免不必要的重新渲染

  

4. **ResizeObserver**：

- 使用现代浏览器的 `ResizeObserver` API，性能优于传统的 `resize` 事件监听

  

## 使用示例

  

```typescript

<MasonryGrid

items={items}

render={renderCard}

widthContainer={containerRef.current}

scrollContainer={window}

maxWidth={1920}

padding={16}

columnGutter={5}

maxColumnCount={6}

/>

```

  

### Props 说明

  

- `widthContainer`：用于计算宽度的容器元素（可选，默认使用 `scrollContainer`）

- `scrollContainer`：滚动容器（可选，默认使用 `window`）

- `maxWidth`：最大宽度限制（默认 `Infinity`）

- `padding`：容器内边距（默认 0）

- `columnGutter`：列间距（默认 5）

- `columnWidth`：固定列宽（可选，不传则自动计算）

- `maxColumnCount`：最大列数（默认 4）

- `widthUpdateDeps`：触发宽度重新计算的依赖数组（可选）

  

## 总结

  

`MasonryGrid` 组件通过以下机制实现自适应布局：

  

1. **实时监听**：使用 `useSize` hook 和 `ResizeObserver` 实时监听容器尺寸变化

2. **响应式断点**：基于预定义的断点数组，根据宽度自动调整列数

3. **智能计算**：综合考虑容器宽度、padding、滚动条等因素，计算最优的列数和列宽

4. **性能优化**：通过 memoization、防抖等技术，确保布局更新的流畅性

  

当屏幕宽度变化时，组件会自动重新计算列数和卡片宽度，实现无缝的响应式布局体验。



# 更详细的MasonryGrid文档

# MasonryGrid 瀑布流组件使用指南

  

## 目录

  

- [概述](#概述)

- [MasonryGrid 组件封装详解](#masonrygrid-组件封装详解)

- [原理](#原理)

- [基础用法](#基础用法)

- [配置项详解](#配置项详解)

- [高度计算](#高度计算)

- [宽度计算](#宽度计算)

- [列数（Column Count）](#列数列-count)

- [响应式设计](#响应式设计)

- [性能优化](#性能优化)

- [实际使用示例](#实际使用示例)

- [常见问题与注意事项](#常见问题与注意事项)

  

## 概述

  

`MasonryGrid` 是基于 [masonic](https://github.com/jaredLunde/masonic) 库封装的瀑布流布局组件，用于在 liblib 项目中实现图片、视频等内容的瀑布流展示。

  

**核心特性：**

  

- 虚拟滚动，支持大量数据渲染

- 响应式布局，自动适配不同屏幕尺寸

- 自动计算列数和列宽

- 支持动态高度卡片

- 性能优化，使用 ResizeObserver 监听尺寸变化

  

## MasonryGrid 组件封装详解

  

### 为什么需要封装？

  

`masonic` 是一个优秀的底层库，但直接使用需要处理很多细节：

  

1. **容器尺寸监听**: 需要手动监听容器尺寸变化

2. **响应式列数计算**: 需要根据断点配置计算列数

3. **滚动处理**: 需要手动处理滚动事件

4. **性能优化**: 需要手动 memoize 各种配置

5. **SSR 兼容**: Next.js SSR 环境下需要特殊处理

  

`MasonryGrid` 封装了这些细节，提供了更简洁的 API。

  

### 封装架构

  

```

MasonryGrid (封装组件)

├── index.ts (动态导入，SSR 禁用)

└── MasonryGrid.tsx (核心实现)

├── 使用 masonic hooks

│ ├── usePositioner (位置计算)

│ ├── useResizeObserver (卡片尺寸监听)

│ └── useMasonry (渲染)

├── 使用项目自定义 hooks

│ ├── useSize (容器尺寸监听)

│ └── useScroller (滚动监听)

└── 封装逻辑

├── 响应式列数计算

├── 宽度计算（含滚动条补偿）

└── 性能优化（memoization）

```

  

### 核心封装逻辑

  

#### 1. 类型定义

  

```typescript

export type MasonryGridProps<T> = Omit<

MasonryScrollerProps<T>,

'containerRef' | 'height' | 'offset' | 'positioner' | 'resizeObserver'

> & {

// 新增的简化 API

widthContainer?: HTMLElement | null;

scrollContainer?: HTMLElement | null;

padding?: number;

maxWidth?: number;

columnGutter?: number;

columnWidth?: number;

maxColumnCount?: number;

widthUpdateDeps?: any[];

onScrollChange?: (scrollTop: number) => void;

};

```

  

**设计思路**:

  

- 隐藏底层实现细节（`positioner`、`resizeObserver` 等）

- 提供更语义化的 API（`widthContainer`、`scrollContainer`）

- 保留 `MasonryScrollerProps` 的其他属性，保持灵活性

  

#### 2. 容器尺寸监听封装

  

```typescript

// 使用 useSize 封装容器尺寸监听

const containerElement = useMemo(() => widthContainer || scrollContainer, [widthContainer, scrollContainer]);

const { width: containerWith } = useSize(containerElement, widthUpdateDeps);

const { height } = useSize(scrollContainer, widthUpdateDeps);

```

  

**封装优势**:

  

- 自动处理容器变化

- 支持依赖数组触发重新计算

- 内置防抖优化

  

#### 3. 响应式列数计算

  

```typescript

const calcColumnCount = (width: number) => {

const breakPoint = columnCountBreakPoints.find(({ minWidth }) => width > minWidth);

return breakPoint ? breakPoint.columnCount : DEFAULT_COLUMN_COUNT;

};

  

// 在宽度计算中使用

const { width, columnWidthCalculated, columnCount } = useMemo(() => {

const calculatedWidth = /* ... */;

const calculatedColumnWidth = columnWidth || DEFAULT_MAX_WIDTH / maxColumnCount;

const calculatedColumnCount = calcColumnCount(calculatedWidth);

return { width, columnWidthCalculated, columnCount: calculatedColumnCount };

}, [/* deps */]);

```

  

**封装优势**:

  

- 统一的断点配置

- 自动计算列数，无需手动处理

- 支持 `maxColumnCount` 限制

  

#### 4. 宽度计算封装（含滚动条补偿）

  

```typescript

const { width, columnWidthCalculated, columnCount } = useMemo(() => {

// 少量卡片的场景做了 scrollbar 宽度补偿

const addScrollBar =

restProps.items.length < DEFAULT_COLUMN_COUNT * 2 && window.innerHeight > document.body.scrollHeight;

  

const calculatedWidth = Math.max(

0,

Math.min(maxWidth, containerWith - padding + (addScrollBar ? window.innerWidth - document.body.clientWidth : 0)),

);

  

const calculatedColumnWidth = columnWidth || DEFAULT_MAX_WIDTH / maxColumnCount;

const calculatedColumnCount = calcColumnCount(calculatedWidth);

  

return { width, columnWidthCalculated, columnCount: calculatedColumnCount };

}, [containerWith, padding, maxWidth, columnWidth, maxColumnCount, restProps.items.length]);

```

  

**封装优势**:

  

- 自动处理滚动条宽度补偿

- 考虑 padding 和 maxWidth

- 自动计算列宽

  

#### 5. Positioner 配置封装

  

```typescript

const positionerConfig = useMemo(

() => ({

columnGutter,

columnWidth: columnWidthCalculated,

columnCount,

maxColumnCount,

width,

}),

[columnGutter, columnWidthCalculated, columnCount, maxColumnCount, width],

);

  

const positioner = usePositioner(positionerConfig, [positionerConfig]);

const resizeObserver = useResizeObserver(positioner);

```

  

**封装优势**:

  

- 自动创建 positioner 和 resizeObserver

- 使用 useMemo 优化性能

- 隐藏底层实现

  

#### 6. 滚动处理封装

  

```typescript

const { scrollTop, isScrolling } = useScroller(scrollContainer || window);

  

const handleScrollChange = useCallback(() => {

// 只在主页触发（历史遗留逻辑）

if (router.asPath !== '/' && router.asPath !== firstPageAsPath) return;

onScrollChange?.(scrollTop);

}, [router.asPath, onScrollChange, scrollTop]);

  

useEffect(() => {

handleScrollChange();

}, [handleScrollChange]);

```

  

**封装优势**:

  

- 自动处理滚动事件

- 提供 `onScrollChange` 回调

- 支持自定义滚动容器

  

#### 7. SSR 处理

  

```typescript

// index.ts

import dynamic from 'next/dynamic';

  

export const MasonryGrid = dynamic(() => import('./MasonryGrid'), { ssr: false }) as MasonryGridComponent;

```

  

**封装优势**:

  

- 禁用 SSR，避免服务端渲染问题

- 使用动态导入，减少初始包大小

  

### 性能优化策略

  

#### 1. Memoization 优化

  

```typescript

// 容器元素 memoization

const containerElement = useMemo(() => widthContainer || scrollContainer, [widthContainer, scrollContainer]);

  

// 宽度计算 memoization

const { width, columnWidthCalculated, columnCount } = useMemo(() => {

// ... 计算逻辑

}, [containerWith, padding, maxWidth, columnWidth, maxColumnCount, restProps.items.length]);

  

// Positioner 配置 memoization

const positionerConfig = useMemo(

() => ({

// ... 配置

}),

[columnGutter, columnWidthCalculated, columnCount, maxColumnCount, width],

);

  

// Masonry 选项 memoization

const masonryOptions = useMemo(

() => ({

// ... 选项

}),

[positioner, resizeObserver, height, scrollTop, isScrolling, render, restProps],

);

```

  

#### 2. 默认性能配置

  

```typescript

const masonryOptions = useMemo(

() => ({

// ...

itemHeightEstimate: 300, // 提供高度估算，提升初始渲染性能

// overscanBy: 2, // 默认值，可覆盖

...restProps,

}),

[

/* deps */

],

);

```

  

### 与原生 masonic 的对比

  

| 特性 | 原生 masonic | MasonryGrid 封装 |

| ---------------- | ---------------- | ------------------------ |

| **容器尺寸监听** | 需要手动实现 | 自动处理（useSize） |

| **响应式列数** | 需要手动计算 | 自动计算（基于断点） |

| **滚动处理** | 需要手动处理 | 自动处理（useScroller） |

| **宽度计算** | 需要手动计算 | 自动计算（含滚动条补偿） |

| **SSR 兼容** | 需要手动处理 | 自动处理（动态导入） |

| **性能优化** | 需要手动 memoize | 内置优化 |

| **API 复杂度** | 较高 | 较低 |

| **灵活性** | 完全灵活 | 平衡灵活性和易用性 |

  

### 设计原则

  

1. **简化 API**: 隐藏底层实现，提供更语义化的 API

2. **保持灵活性**: 保留 masonic 的核心能力，支持高级用法

3. **性能优先**: 内置 memoization 和优化策略

4. **项目适配**: 针对 liblib 项目的特定需求（响应式、SSR 等）

5. **向后兼容**: 支持所有 masonic 的原生属性

  

### 扩展点

  

如果需要扩展功能，可以：

  

1. **修改默认配置**: 调整 `DEFAULT_*` 常量

2. **自定义断点**: 修改 `columnCountBreakPoints`

3. **扩展 Props**: 在 `MasonryGridProps` 中添加新属性

4. **覆盖默认行为**: 通过 `restProps` 传递 masonic 原生属性

  

### 使用建议

  

- **一般场景**: 直接使用 `MasonryGrid`，享受封装带来的便利

- **特殊需求**: 可以通过 `restProps` 传递 masonic 原生属性

- **性能敏感**: 合理使用 `widthUpdateDeps` 和 memoization

- **SSR 场景**: 组件已处理，无需额外配置

  

## 原理

  

### 技术栈

  

- **基础库**: `masonic` - 一个高性能的 React 瀑布流布局库

- **核心 Hooks**:

- `usePositioner`: 计算每个卡片的位置

- `useResizeObserver`: 监听卡片尺寸变化，自动重新布局

- `useMasonry`: 渲染瀑布流布局

- `useScroller`: 监听滚动事件，实现虚拟滚动

- `useSize`: 项目自定义 hook，监听容器尺寸变化

  

### useSize vs useResizeObserver 的区别

  

在 `MasonryGrid` 中，这两个 hook 有不同的用途：

  

| 特性 | useSize | useResizeObserver (masonic) |

| ------------ | ------------------------------------------ | -------------------------------- |

| **来源** | 项目自定义 hook (`@/hooks/useSize`) | masonic 库提供 |

| **监听对象** | 容器元素（widthContainer/scrollContainer） | 每个卡片元素 |

| **用途** | 监听容器尺寸变化，用于计算列数和列宽 | 监听卡片尺寸变化，自动重新布局 |

| **返回值** | `{ width, height }` | ResizeObserver 实例 |

| **触发时机** | 容器尺寸变化时 | 卡片尺寸变化时 |

| **使用场景** | 响应式布局、列数计算 | 动态高度卡片、内容加载后调整布局 |

  

**在 MasonryGrid 中的使用：**

  

```typescript

// 1. 使用 useSize 监听容器尺寸（用于计算列数和列宽）

const { width: containerWith } = useSize(containerElement, widthUpdateDeps);

const { height } = useSize(scrollContainer, widthUpdateDeps);

  

// 2. 使用 useResizeObserver 监听卡片尺寸变化（用于自动重新布局）

const resizeObserver = useResizeObserver(positioner);

```

  

**为什么需要两个？**

  

- **useSize**: 关注的是**容器**的尺寸，用于响应式布局（窗口大小变化、侧边栏折叠等）

- **useResizeObserver**: 关注的是**卡片**的尺寸，用于处理动态内容（图片加载、内容变化等）

  

### 工作流程

  

1. **容器尺寸监听**: 使用 `useSize` hook 监听容器宽度变化

2. **列数计算**: 根据容器宽度和断点配置自动计算列数

3. **列宽计算**: 根据容器宽度、列数和间距计算每列宽度

4. **位置计算**: 使用 `usePositioner` 计算每个卡片的位置

5. **虚拟渲染**: 只渲染可见区域的卡片，提升性能

6. **动态调整**: 使用 `useResizeObserver` 监听卡片高度变化，自动调整布局

  

## 基础用法

  

### 基本示例

  

```typescript

import { MasonryGrid } from '@/components/common/MasonryGrid';

  

interface Item {

uuid: string;

name: string;

// ... 其他字段

}

  

const MyComponent = () => {

const items: Item[] = [/* 你的数据 */];

  

const cardRender = (props: RenderComponentProps<Item>) => {

return (

<div style={{ width: props.width }}>

{/* 你的卡片内容 */}

</div>

);

};

  

return (

<MasonryGrid<Item>

items={items}

render={cardRender}

columnWidth={284}

maxColumnCount={4}

/>

);

};

```

  

### 完整示例（带容器和滚动）

  

```typescript

const MyList = () => {

const widthContainerRef = useRef<HTMLElement>(null);

const scrollContainerRef = useRef<HTMLElement>(null);

const [items, setItems] = useState<Item[]>([]);

  

const cardRender = useMemo(() => {

return (props: RenderComponentProps<Item>) => {

return <MyCard data={props.data} displayWidth={props.width} />;

};

}, []);

  

const handleRender = useCallback((startIndex: number, stopIndex: number) => {

// 加载更多数据的逻辑

if (stopIndex >= items.length - 4) {

loadMore();

}

}, [items]);

  

return (

<MasonryGrid<Item>

items={items}

render={cardRender}

columnWidth={284}

maxColumnCount={4}

widthContainer={widthContainerRef.current}

scrollContainer={scrollContainerRef.current}

onRender={handleRender}

padding={0}

maxWidth={2560}

/>

);

};

```

  

## 配置项详解

  

### 必需属性

  

| 属性 | 类型 | 说明 |

| -------- | ----------------------------------------------- | ------------------ |

| `items` | `T[]` | 要渲染的数据数组 |

| `render` | `(props: RenderComponentProps<T>) => ReactNode` | 渲染每个卡片的函数 |

  

### 可选属性

  

| 属性 | 类型 | 默认值 | 说明 |

| -------------------- | ------------------------------------------------- | -------------- | ------------------------------- |

| `columnWidth` | `number` | `640` (2560/4) | 每列的基础宽度（像素） |

| `maxColumnCount` | `number` | `4` | 最大列数 |

| `columnGutter` | `number` | `5` | 列之间的间距（像素） |

| `padding` | `number` | `0` | 容器内边距（像素） |

| `maxWidth` | `number` | `Infinity` | 容器最大宽度（像素） |

| `widthContainer` | `HTMLElement \| null` | `undefined` | 用于计算宽度的容器元素 |

| `scrollContainer` | `HTMLElement \| null` | `undefined` | 滚动容器元素（默认使用 window） |

| `widthUpdateDeps` | `any[]` | `[]` | 触发宽度重新计算的依赖数组 |

| `onRender` | `(startIndex: number, stopIndex: number) => void` | `undefined` | 渲染范围变化时的回调 |

| `onScrollChange` | `(scrollTop: number) => void` | `undefined` | 滚动位置变化时的回调 |

| `itemHeightEstimate` | `number` | `300` | 卡片高度估算值（用于性能优化） |

| `itemKey` | `(data: T, index: number) => string` | `undefined` | 自定义 key 生成函数 |

  

### 其他 masonic 支持的属性

  

`MasonryGrid` 还支持所有 `MasonryScrollerProps` 的其他属性，例如：

  

- `overscanBy`: 额外渲染的屏幕数量（默认 2）

- `scrollOffset`: 滚动偏移量

- `isScrolling`: 是否正在滚动

- 等等...

  

## 高度计算

  

### 两种高度计算策略

  

#### 1. 固定比例（统一高度）

  

适用于需要统一视觉效果的场景，如视频卡片、模型卡片等。

  

**示例：VideoCard**

  

```typescript

// packages/liblib/src/components/home/Models/VideoCard/index.tsx

  

const INFO_HEIGHT = 34; // 底部信息栏高度

  

// 固定 4:3 比例

const displayHeight = useMemo(() => {

if (displayHeightOverride) return displayHeightOverride;

if (!displayWidth) return undefined;

return (displayWidth * 4) / 3 + INFO_HEIGHT;

}, [displayHeightOverride, displayWidth]);

  

// 封面高度（不含底部信息）

const coverHeight = useMemo(() => {

if (displayHeightOverride) {

return !hideBottomTitle ? displayHeightOverride - INFO_HEIGHT : displayHeightOverride;

}

if (!displayWidth) return undefined;

return (displayWidth * 4) / 3;

}, [displayHeightOverride, displayWidth, hideBottomTitle]);

```

  

**特点：**

  

- ✅ 所有卡片高度一致，视觉整齐

- ✅ 布局稳定，不会因内容变化而抖动

- ❌ 可能裁剪或留白，不适合展示完整内容

  

#### 2. 动态比例（瀑布流效果）

  

适用于需要展示完整内容的场景，如图片卡片。

  

**示例：ImageCard**

  

```typescript

// packages/liblib/src/components/inspiration/Images/Card/CardItem.tsx

  

const itemWidth = displayWidth;

const heightDivideWidth = data.height / data.width; // 图片原始宽高比

const itemHeight = Math.min(580, Math.floor(itemWidth * heightDivideWidth));

```

  

**特点：**

  

- ✅ 完整展示内容，无裁剪

- ✅ 充分利用空间，呈现瀑布流效果

- ❌ 卡片高度不一致，视觉上可能不够整齐

- ❌ 布局可能因内容加载而抖动

  

### 高度计算最佳实践

  

1. **固定比例场景**：使用固定比例计算，确保视觉一致性

  

```typescript

const height = (width * 4) / 3; // 4:3 比例

```

  

2. **动态比例场景**：基于内容实际尺寸计算

  

```typescript

const aspectRatio = data.height / data.width;

const height = Math.min(maxHeight, width * aspectRatio);

```

  

3. **性能优化**：使用 `itemHeightEstimate` 提供高度估算

```typescript

<MasonryGrid

itemHeightEstimate={320} // 提供估算值，提升初始渲染性能

// ...

/>

```

  

## 宽度计算

  

### 宽度计算逻辑

  

```typescript

// 1. 获取容器宽度

const { width: containerWith } = useSize(containerElement, widthUpdateDeps);

  

// 2. 计算实际可用宽度（考虑 padding 和滚动条）

const addScrollBar =

restProps.items.length < DEFAULT_COLUMN_COUNT * 2 && window.innerHeight > document.body.scrollHeight;

  

const calculatedWidth = Math.max(

0,

Math.min(maxWidth, containerWith - padding + (addScrollBar ? window.innerWidth - document.body.clientWidth : 0)),

);

  

// 3. 计算列宽

const calculatedColumnWidth = columnWidth || DEFAULT_MAX_WIDTH / maxColumnCount;

```

  

### 宽度计算要点

  

1. **容器宽度**: 优先使用 `widthContainer`，否则使用 `scrollContainer`

2. **滚动条补偿**: 少量卡片时自动补偿滚动条宽度

3. **最大宽度限制**: 受 `maxWidth` 限制，默认 `2560px`

4. **响应式更新**: 通过 `widthUpdateDeps` 触发重新计算

  

## 列数（Column Count）

  

### 自动计算列数

  

列数根据容器宽度和断点配置自动计算：

  

```typescript

// packages/liblib/src/utils/constants.ts

export const columnCountBreakPoints = [

{ minWidth: 0, columnCount: 2 },

{ minWidth: 350, columnCount: 2 },

{ minWidth: 500, columnCount: 3 },

{ minWidth: 800, columnCount: 4 },

{ minWidth: 1164, columnCount: 5 },

{ minWidth: 1471, columnCount: 6 },

{ minWidth: 1758, columnCount: 7 },

].reverse();

  

const calcColumnCount = (width: number) => {

const breakPoint = columnCountBreakPoints.find(({ minWidth }) => width > minWidth);

return breakPoint ? breakPoint.columnCount : DEFAULT_COLUMN_COUNT;

};

```

  

### 列数断点表

  

| 容器宽度 | 列数 |

| --------------- | ---- |

| < 500px | 2 |

| 500px - 800px | 3 |

| 800px - 1164px | 4 |

| 1164px - 1471px | 5 |

| 1471px - 1758px | 6 |

| ≥ 1758px | 7 |

  

**注意**: 实际列数不会超过 `maxColumnCount` 设置的值。

  

### 自定义列数

  

如果需要自定义列数，可以：

  

1. **修改断点配置**（全局影响）:

  

```typescript

// packages/liblib/src/utils/constants.ts

export const columnCountBreakPoints = [

// 你的自定义断点

];

```

  

2. **限制最大列数**（局部影响）:

```typescript

<MasonryGrid

maxColumnCount={4} // 最多 4 列

// ...

/>

```

  

## 响应式设计

  

### 响应式实现机制

  

1. **容器尺寸监听**: 使用 `useSize` hook 监听容器尺寸变化

2. **断点匹配**: 根据容器宽度匹配对应的列数

3. **自动重新布局**: 尺寸变化时自动重新计算并布局

  

### 响应式配置示例

  

```typescript

const MyComponent = () => {

const { sidebarCollapsed } = usePreference();

  

// 当侧边栏折叠状态变化时，重新计算宽度

const widthUpdateDeps = useMemo(() => {

return [sidebarCollapsed];

}, [sidebarCollapsed]);

  

return (

<MasonryGrid

widthUpdateDeps={widthUpdateDeps}

// ... 其他配置

/>

);

};

```

  

### 响应式最佳实践

  

1. **使用 widthUpdateDeps**: 当影响布局的因素变化时，添加到依赖数组

  

```typescript

const widthUpdateDeps = [sidebarCollapsed, windowWidth /* 其他依赖 */];

```

  

2. **指定容器元素**: 明确指定 `widthContainer` 和 `scrollContainer`

  

```typescript

const containerRef = useRef<HTMLElement>(null);

  

<div ref={containerRef}>

<MasonryGrid

widthContainer={containerRef.current}

scrollContainer={containerRef.current}

/>

</div>

```

  

3. **避免频繁更新**: 使用 `useMemo` 和 `useCallback` 优化回调函数

  

## 性能优化

  

### 虚拟滚动

  

`MasonryGrid` 使用虚拟滚动技术，只渲染可见区域的卡片，大幅提升性能。

  

**原理：**

  

- 计算可见区域范围

- 只渲染可见区域内的卡片

- 滚动时动态加载/卸载卡片

  

### 性能优化配置

  

```typescript

<MasonryGrid

// 1. 提供高度估算值，提升初始渲染性能

itemHeightEstimate={320}

  

// 2. 自定义 key，避免不必要的重新渲染

itemKey={(data, index) => `${data.uuid}-${index}`}

  

// 3. 优化渲染回调，使用 useCallback

onRender={useCallback((start, stop) => {

// 加载更多逻辑

}, [deps])}

  

// 4. 使用 useMemo 优化 render 函数

render={useMemo(() => (props) => {

return <Card {...props} />;

}, [deps])}

/>

```

  

### 性能优化技巧

  

1. **Memoize 渲染函数**: 使用 `useMemo` 缓存 `render` 函数

2. **Memoize 回调函数**: 使用 `useCallback` 缓存 `onRender` 等回调

3. **提供高度估算**: 设置 `itemHeightEstimate` 提升初始渲染性能

4. **自定义 key**: 使用稳定的 key 避免不必要的重新渲染

5. **避免频繁更新**: 合理使用 `widthUpdateDeps`，避免不必要的重新计算

  

## 实际使用示例

  

### 示例 1: 图片列表（动态高度）

  

```typescript

// packages/liblib/src/components/inspiration/Images/List.tsx

  

const ImageList = () => {

const [items, setItems] = useState<ImageFeedItemMini[]>([]);

const widthContainerRef = useRef<HTMLElement>(null);

const scrollContainerRef = useRef<HTMLElement>(null);

  

const IMAGE_MIN_WIDTH = 284;

const MAX_COLUMN_COUNT = 4;

  

const cardRender = useMemo(() => {

return (props: RenderComponentProps<ImageFeedItemMini>) => {

return (

<ImageCard

data={props.data}

displayWidth={props.width}

onClick={onItemClick}

/>

);

};

}, [onItemClick]);

  

const handleRender = useCallback((startIndex: number, stopIndex: number) => {

if (stopIndex >= items.length - 4) {

loadMore();

}

}, [items]);

  

return (

<MasonryGrid<ImageFeedItemMini>

items={items}

render={cardRender}

columnWidth={IMAGE_MIN_WIDTH}

maxColumnCount={MAX_COLUMN_COUNT}

widthContainer={widthContainerRef.current}

scrollContainer={scrollContainerRef.current}

onRender={handleRender}

/>

);

};

```

  

### 示例 2: 视频列表（固定高度）

  

```typescript

// packages/liblib/src/components/home/Models/VideoList.tsx

  

const VideoList = () => {

const [items, setItems] = useState<ModelFeedItemMini[]>([]);

  

const IMAGE_MIN_WIDTH = 284;

const MAX_COLUMN_COUNT = 4;

  

const CardRender = (props: RenderComponentProps<ModelFeedItemMini>) => {

return (

<VideoCard

data={props.data}

displayWidth={props.width}

onClick={handleItemClick}

/>

);

};

  

return (

<MasonryGrid<ModelFeedItemMini>

items={items}

render={CardRender}

columnWidth={IMAGE_MIN_WIDTH}

maxColumnCount={MAX_COLUMN_COUNT}

itemHeightEstimate={320} // 固定高度估算

itemKey={(data, index) => `${data.uuid}-${index}`}

/>

);

};

```

  

### 示例 3: 带加载状态

  

```typescript

const MyList = () => {

const [items, setItems] = useState<Item[]>([]);

const [isLoading, setIsLoading] = useState(false);

const [hasMore, setHasMore] = useState(true);

  

return (

<>

<MasonryGrid<Item>

items={items}

render={cardRender}

// ... 其他配置

/>

{isLoading ? (

<BottomLoading />

) : !hasMore ? (

<NoMoreItems />

) : null}

</>

);

};

```

  

## 常见问题与注意事项

  

### 1. 卡片高度不一致导致布局问题

  

**问题**: 使用动态高度时，如果高度计算不准确，可能导致布局错乱。

  

**解决方案**:

  

- 确保卡片高度计算逻辑正确

- 使用 `ResizeObserver` 自动调整（已内置）

- 对于固定高度卡片，使用固定比例计算

  

### 2. 宽度计算不准确

  

**问题**: 容器宽度变化时，布局没有更新。

  

**解决方案**:

  

- 正确设置 `widthContainer` 和 `scrollContainer`

- 使用 `widthUpdateDeps` 添加影响宽度的依赖

- 确保容器元素已正确挂载

  

### 3. 性能问题

  

**问题**: 大量数据时渲染卡顿。

  

**解决方案**:

  

- 使用虚拟滚动（已内置）

- 提供 `itemHeightEstimate` 提升初始渲染性能

- 使用 `useMemo` 和 `useCallback` 优化回调

- 避免在 `render` 函数中创建新对象

  

### 4. 强制重新渲染

  

**问题**: 数据更新后布局没有刷新。

  

**解决方案**:

  

```typescript

// 当数据长度减少时，强制重新渲染

let forceRerenderKey;

if (items.length < previousLength) {

forceRerenderKey = Date.now();

}

  

<MasonryGrid

key={forceRerenderKey} // 使用 key 强制重新渲染

items={items}

// ...

/>

```

  

### 5. 滚动位置丢失

  

**问题**: 数据更新后滚动位置重置。

  

**解决方案**:

  

- 使用 `keepScrollBack` 工具函数（如果项目中有）

- 在数据更新前保存滚动位置，更新后恢复

  

### 6. 响应式不生效

  

**问题**: 窗口大小变化时，列数没有更新。

  

**解决方案**:

  

- 确保 `widthContainer` 或 `scrollContainer` 正确设置

- 检查 `widthUpdateDeps` 是否包含必要的依赖

- 确认断点配置是否正确

  

### 7. 卡片间距问题

  

**问题**: 卡片之间的间距不符合预期。

  

**解决方案**:

  

- 调整 `columnGutter` 属性

- 注意 `columnGutter` 是列之间的间距，不是行之间的间距

- 行间距由卡片高度和布局算法自动计算

  

### 8. 类型定义

  

**问题**: TypeScript 类型错误。

  

**解决方案**:

  

```typescript

// 明确指定泛型类型

<MasonryGrid<YourItemType>

items={items}

render={(props: RenderComponentProps<YourItemType>) => {

// props.data 类型为 YourItemType

return <Card data={props.data} />;

}}

/>

```

  

## 总结

  

`MasonryGrid` 是一个功能强大且灵活的瀑布流组件，通过合理配置可以实现各种布局需求。关键要点：

  

1. **高度计算**: 根据需求选择固定比例或动态比例

2. **响应式**: 正确设置容器和依赖，确保响应式生效

3. **性能优化**: 使用 memoization 和虚拟滚动优化性能

4. **类型安全**: 使用 TypeScript 泛型确保类型安全

  

如有问题，请参考项目中的实际使用示例或查看 `masonic` 官方文档。

# 组件props
  

## 概述

  

`MasonryGrid` 是一个基于 `masonic` 库封装的瀑布流网格组件，用于高效渲染大量数据的网格布局。组件内部自动处理滚动、尺寸计算和布局定位，提供了简化的 API 接口。

  

## 类型定义

  

```typescript

export type MasonryGridProps<T> = Omit<

MasonryScrollerProps<T>,

'containerRef' | 'height' | 'offset' | 'positioner' | 'resizeObserver'

> & {

widthContainer?: HTMLElement | null;

scrollContainer?: HTMLElement | null;

padding?: number;

maxWidth?: number;

columnGutter?: number;

columnWidth?: number;

maxColumnCount?: number;

widthUpdateDeps?: any[];

onScrollChange?: (scrollTop: number) => void;

};

```

  

### 设计说明

  

- **继承自 `MasonryScrollerProps`**：继承了 `masonic` 库的核心功能

- **排除内部属性**：`containerRef`、`height`、`offset`、`positioner`、`resizeObserver` 由组件内部自动处理，无需外部传入

- **扩展自定义属性**：添加了更易用的布局配置选项

  

## 属性说明

  

### 必需属性

  

#### `items: Item[]`

- **类型**：`Item[]`

- **说明**：要渲染的数据数组，每个元素会渲染为一个网格项

- **示例**：`items={imageList}`

  

#### `render: React.ComponentType<RenderComponentProps<Item>>`

- **类型**：React 组件类型

- **说明**：用于渲染每个网格项的组件，接收以下 props：

- `index: number` - 在数组中的索引

- `width: number` - 渲染的列宽（像素）

- `data: Item` - 当前项的数据

- **示例**：

```typescript

const CardRender = ({ index, width, data }: RenderComponentProps<ImageItem>) => {

return <ImageCard data={data} width={width} />;

};

```

  

### 容器相关属性

  

#### `as?: keyof JSX.IntrinsicElements | React.ComponentType<any>`

- **类型**：HTML 元素标签名或 React 组件

- **默认值**：`"div"`

- **说明**：网格容器的元素类型

- **示例**：`as="section"`

  

#### `id?: string`

- **类型**：`string`

- **说明**：容器的 `id` 属性

- **示例**：`id="image-grid"`

  

#### `className?: string`

- **类型**：`string`

- **说明**：容器的 CSS 类名

- **示例**：`className="my-masonry-grid"`

  

#### `style?: React.CSSProperties`

- **类型**：`React.CSSProperties`

- **说明**：容器的额外内联样式

- **示例**：`style={{ backgroundColor: '#f0f0f0' }}`

  

#### `role?: "grid" | "list"`

- **类型**：`"grid" | "list"`

- **默认值**：`"grid"`

- **说明**：容器的无障碍角色属性

- **示例**：`role="list"`

  

#### `tabIndex?: number`

- **类型**：`number`

- **默认值**：`0`

- **说明**：容器的 `tabIndex` 属性，用于键盘导航

- **示例**：`tabIndex={-1}`

  

### 网格项相关属性

  

#### `itemAs?: keyof JSX.IntrinsicElements | React.ComponentType<any>`

- **类型**：HTML 元素标签名或 React 组件

- **默认值**：`"div"`

- **说明**：每个网格项的元素类型

- **示例**：`itemAs="article"`

  

#### `itemStyle?: React.CSSProperties`

- **类型**：`React.CSSProperties`

- **说明**：网格项的额外内联样式

- **示例**：`itemStyle={{ borderRadius: '8px' }}`

  

#### `itemHeightEstimate?: number`

- **类型**：`number`

- **默认值**：`300`

- **说明**：预估的网格项高度（像素），用于性能优化和初始渲染。设置合理的值可以提升滚动性能

- **示例**：`itemHeightEstimate={400}`

  

#### `itemKey?: (data: Item, index: number) => string | number`

- **类型**：函数 `(data: Item, index: number) => string | number`

- **默认值**：使用索引作为 key

- **说明**：为每个网格项生成唯一 key 的函数。如果数据项有唯一标识符（如 ID），建议使用此属性

- **示例**：

```typescript

itemKey={(data, index) => data.id || index}

```

  

### 性能相关属性

  

#### `overscanBy?: number`

- **类型**：`number`

- **默认值**：`2`

- **说明**：视窗外预渲染的窗口倍数。值越大，预渲染的内容越多，滚动更流畅但可能影响性能

- **示例**：`overscanBy={3}`

  

#### `onRender?: (startIndex: number, stopIndex: number, items: Item[]) => void`

- **类型**：回调函数

- **说明**：当渲染的网格项范围发生变化时触发

- `startIndex` - 当前渲染范围的起始索引

- `stopIndex` - 当前渲染范围的结束索引

- `items` - 当前渲染的项数组

- **示例**：

```typescript

onRender={(start, stop, items) => {

console.log(`渲染了 ${start} 到 ${stop} 项`);

}}

```

  

### 滚动相关属性

  

#### `scrollFps?: number`

- **类型**：`number`

- **默认值**：`12`

- **说明**：滚动位置更新的帧率（FPS）。如果渲染组件较重，可以降低此值以提升性能

- **示例**：`scrollFps={8}`

  

### 布局相关属性（自定义）

  

#### `widthContainer?: HTMLElement | null`

- **类型**：`HTMLElement | null`

- **说明**：用于计算网格宽度的容器元素。如果未指定，将使用 `scrollContainer` 或 `window`

- **使用场景**：当网格需要根据特定容器的宽度自适应时

- **示例**：

```typescript

const containerRef = useRef<HTMLDivElement>(null);

<MasonryGrid widthContainer={containerRef.current} />

```

  

#### `scrollContainer?: HTMLElement | null`

- **类型**：`HTMLElement | null`

- **默认值**：`window`

- **说明**：滚动容器元素。如果网格在自定义滚动容器内，需要指定此属性

- **使用场景**：在固定高度的容器内滚动

- **示例**：

```typescript

const scrollRef = useRef<HTMLDivElement>(null);

<MasonryGrid scrollContainer={scrollRef.current} />

```

  

#### `padding?: number`

- **类型**：`number`

- **默认值**：`0`

- **说明**：网格容器的内边距（像素）

- **示例**：`padding={16}`

  

#### `maxWidth?: number`

- **类型**：`number`

- **默认值**：`Infinity`

- **说明**：网格的最大宽度（像素）。超过此宽度时，网格将不再扩展

- **示例**：`maxWidth={2560}`

  

#### `columnGutter?: number`

- **类型**：`number`

- **默认值**：`5`

- **说明**：列之间的间距（像素）

- **示例**：`columnGutter={10}`

  

#### `columnWidth?: number`

- **类型**：`number`

- **说明**：每列的宽度（像素）。如果未指定，将根据 `maxWidth / maxColumnCount` 计算

- **示例**：`columnWidth={284}`

  

#### `maxColumnCount?: number`

- **类型**：`number`

- **默认值**：`4`

- **说明**：最大列数。实际列数会根据容器宽度和断点配置自动计算，但不会超过此值

- **示例**：`maxColumnCount={6}`

  

#### `widthUpdateDeps?: any[]`

- **类型**：`any[]`

- **默认值**：`[]`

- **说明**：宽度更新的依赖数组。当数组中的值发生变化时，会重新计算网格宽度

- **使用场景**：当侧边栏折叠/展开、窗口大小变化等需要重新计算布局时

- **示例**：

```typescript

const { sidebarCollapsed } = usePreference();

<MasonryGrid widthUpdateDeps={[sidebarCollapsed]} />

```

  

#### `onScrollChange?: (scrollTop: number) => void`

- **类型**：回调函数 `(scrollTop: number) => void`

- **说明**：滚动位置变化时的回调函数。**注意**：此回调仅在主页路径（`/` 或 `firstPageAsPath`）触发

- **参数**：

- `scrollTop` - 当前滚动位置（像素）

- **示例**：

```typescript

const handleScrollChange = (scrollTop: number) => {

console.log('滚动位置:', scrollTop);

// 可以用于埋点、懒加载等

};

<MasonryGrid onScrollChange={handleScrollChange} />

```

  

## 使用示例

  

### 基础用法

  

```typescript

import { MasonryGrid } from '@/components/common/MasonryGrid';

import { ImageCard } from '@/components/inspiration/Images/Card/CardItem';

  

const ImageList = ({ images }: { images: ImageFeedItemMini[] }) => {

return (

<MasonryGrid<ImageFeedItemMini>

items={images}

render={ImageCard}

/>

);

};

```

  

### 完整配置示例

  

```typescript

import { useRef } from 'react';

import { MasonryGrid } from '@/components/common/MasonryGrid';

import { usePreference } from '@/store/preference';

  

const ImageList = ({ images }: { images: ImageFeedItemMini[] }) => {

const containerRef = useRef<HTMLDivElement>(null);

const scrollRef = useRef<HTMLDivElement>(null);

const { sidebarCollapsed } = usePreference();

  

const handleRender = (startIndex: number, stopIndex: number) => {

console.log(`渲染了 ${startIndex} 到 ${stopIndex} 项`);

};

  

const handleScrollChange = (scrollTop: number) => {

// 滚动位置变化处理

};

  

return (

<div ref={containerRef}>

<div ref={scrollRef} style={{ height: '100vh', overflow: 'auto' }}>

<MasonryGrid<ImageFeedItemMini>

items={images}

render={ImageCard}

padding={16}

maxWidth={2560}

columnWidth={284}

maxColumnCount={4}

columnGutter={10}

widthContainer={containerRef.current}

scrollContainer={scrollRef.current}

widthUpdateDeps={[sidebarCollapsed]}

onScrollChange={handleScrollChange}

onRender={handleRender}

itemHeightEstimate={300}

className="image-grid"

overscanBy={2}

/>

</div>

</div>

);

};

```

  

### 实际项目中的使用

  

参考 `packages/liblib/src/components/inspiration/Images/List.tsx` 和 `packages/liblib/src/components/home/Models/List.tsx` 中的实现。

  

## 注意事项

  

1. **动态导入**：组件使用 `dynamic` 导入，服务端渲染（SSR）时不会渲染，仅在客户端渲染

2. **滚动容器**：如果使用自定义滚动容器，必须同时设置 `scrollContainer` 和 `widthContainer`

3. **性能优化**：

- 设置合理的 `itemHeightEstimate` 可以提升初始渲染性能

- 如果渲染组件较重，可以降低 `scrollFps` 或 `overscanBy`

- 使用 `itemKey` 提供稳定的唯一标识符，避免不必要的重新渲染

4. **响应式布局**：列数会根据容器宽度和断点配置自动计算，无需手动设置

5. **滚动回调限制**：`onScrollChange` 仅在主页路径触发，其他路径不会触发

  

## 相关文件

  

- 组件实现：`packages/liblib/src/components/common/MasonryGrid/MasonryGrid.tsx`

- 组件导出：`packages/liblib/src/components/common/MasonryGrid/index.ts`

- 使用示例：

- `packages/liblib/src/components/inspiration/Images/List.tsx`

- `packages/liblib/src/components/home/Models/List.tsx`

  

## 依赖库

  

- [masonic](https://github.com/jaredLunde/masonic) - 核心瀑布流布局库

# useSize Hook 原理解析

  

## 概述

  

`useSize` 是一个高性能的 React Hook，用于实时监听和获取 DOM 元素或浏览器窗口的尺寸变化。它通过多种优化机制确保在频繁的尺寸变化场景下仍能保持流畅的性能。

  

## 核心功能

  

- **元素尺寸监听**：当传入 `container` 参数时，监听指定 DOM 元素的尺寸变化

- **窗口尺寸监听**：当未传入 `container` 时，监听浏览器窗口的尺寸变化

- **内容区域计算**：自动计算元素的内容区域尺寸（排除 padding）

- **依赖触发**：支持通过 `deps` 参数手动触发尺寸重新计算

  

## 技术原理

  

### 1. 尺寸计算逻辑

  

```typescript

const getSize = useCallback((): SizeResult => {

if (container) {

// 获取计算后的样式（包含 padding）

const computedStyle = getComputedStyle(container);

// 提取 padding 值

const paddingTop = parseFloat(computedStyle.paddingTop) || 0;

const paddingBottom = parseFloat(computedStyle.paddingBottom) || 0;

const paddingLeft = parseFloat(computedStyle.paddingLeft) || 0;

const paddingRight = parseFloat(computedStyle.paddingRight) || 0;

// 计算内容区域尺寸 = clientWidth/Height - padding

return {

width: Math.max(0, container.clientWidth - paddingLeft - paddingRight),

height: Math.max(0, container.clientHeight - paddingTop - paddingBottom),

};

}

// 无 container 时返回窗口尺寸

return { width: window.innerWidth, height: window.innerHeight };

}, [container]);

```

  

**关键点**：

- 使用 `clientWidth` 和 `clientHeight` 获取元素的可视区域尺寸（包含 padding，不包含 border 和 scrollbar）

- 通过 `getComputedStyle` 获取实际的 padding 值

- 从可视区域中减去 padding，得到纯内容区域的尺寸

- 使用 `Math.max(0, ...)` 确保尺寸不为负数

  

### 2. 样式缓存机制

  

```typescript

const styleCache = useRef<WeakMap<HTMLElement, CSSStyleDeclaration>>(new WeakMap());

  

// 在 getSize 中使用缓存

let computedStyle = styleCache.current.get(container);

if (!computedStyle) {

computedStyle = getComputedStyle(container);

styleCache.current.set(container, computedStyle);

}

```

  

**优化原理**：

- 使用 `WeakMap` 存储每个元素的 `getComputedStyle` 结果

- `getComputedStyle` 是一个相对昂贵的操作，缓存可以避免重复计算

- `WeakMap` 的优势：当元素被垃圾回收时，对应的缓存也会自动清理，不会造成内存泄漏

- 当元素尺寸变化时（通过 ResizeObserver 检测），会清除对应的缓存

  

### 3. 防抖（Debounce）机制

  

```typescript

const debouncedHandleResize = useCallback(() => {

if (debounceTimerRef.current) {

clearTimeout(debounceTimerRef.current);

}

debounceTimerRef.current = setTimeout(() => {

const newSize = getSize();

// 只在尺寸真正变化时更新

if (newSize.width !== lastSizeRef.current.width ||

newSize.height !== lastSizeRef.current.height) {

lastSizeRef.current = newSize;

setSize(newSize);

}

}, 16); // ~60fps

}, [getSize]);

```

  

**优化原理**：

- **16ms 延迟**：约等于 60fps 的刷新率（1000ms / 60 ≈ 16.67ms），确保更新频率不会超过浏览器渲染帧率

- **防抖策略**：每次触发时清除之前的定时器，只执行最后一次

- **变化检测**：通过 `lastSizeRef` 记录上次的尺寸，只在尺寸真正变化时才更新状态，避免不必要的 re-render

  

### 4. 多重监听机制

  

#### ResizeObserver（元素监听）

  

```typescript

if (typeof ResizeObserver !== 'undefined') {

const resizeObserver = new ResizeObserver(() => {

// 清除缓存，因为样式可能已改变

styleCache.current.delete(container);

debouncedHandleResize();

});

resizeObserver.observe(container);

return () => {

resizeObserver.disconnect();

};

}

```

  

**优势**：

- `ResizeObserver` 是浏览器原生 API，性能优于轮询

- 可以精确监听元素尺寸变化，包括由 CSS 变化、内容变化等引起的尺寸变化

- 当元素尺寸变化时，自动清除样式缓存并触发重新计算

  

#### Window 事件监听（窗口监听）

  

```typescript

useEventListener('resize', debouncedHandleResize, { target: window });

useEventListener('orientationchange', debouncedHandleResize, { target: window });

```

  

**监听的事件**：

- `resize`：窗口大小改变时触发

- `orientationchange`：设备方向改变时触发（移动端）

  

### 5. 生命周期管理

  

#### 初始化阶段

  

```typescript

// 1. useState 初始化（同步）

const [size, setSize] = useState<SizeResult>(() => getSize());

  

// 2. useLayoutEffect 同步更新（在 DOM 更新后，浏览器绘制前）

useLayoutEffect(() => {

const initialSize = getSize();

lastSizeRef.current = initialSize;

setSize(initialSize);

}, []); // 只在挂载时执行一次

```

  

**为什么使用 `useLayoutEffect`**：

- `useLayoutEffect` 在 DOM 更新后、浏览器绘制前同步执行

- 确保初始尺寸在首次渲染时就能正确获取，避免闪烁

  

#### 容器变化处理

  

```typescript

useEffect(() => {

if (container) {

// 清除旧容器的缓存

styleCache.current = new WeakMap();

// 重新计算尺寸

const initialSize = getSize();

lastSizeRef.current = initialSize;

setSize(initialSize);

// 创建新的 ResizeObserver

// ...

}

}, [container, getSize, debouncedHandleResize]);

```

  

**处理逻辑**：

- 当 `container` 变化时，清除所有缓存

- 重新计算新容器的初始尺寸

- 为新容器创建新的 ResizeObserver

  

#### 依赖变化处理

  

```typescript

useEffect(() => {

if (deps && deps.length > 0) {

debouncedHandleResize();

}

}, deps);

```

  

**使用场景**：

- 当某些外部状态变化可能影响元素尺寸时（如 CSS 类名变化、内容变化等）

- 可以通过传入 `deps` 数组手动触发尺寸重新计算

  

#### 清理阶段

  

```typescript

// 组件卸载时清理定时器

useEffect(() => {

return () => {

if (debounceTimerRef.current) {

clearTimeout(debounceTimerRef.current);

}

};

}, []);

```

  

### 6. SSR 兼容性

  

```typescript

if (typeof window === 'undefined') {

return { width: 0, height: 0 };

}

```

  

**处理方式**：

- 在服务端渲染（SSR）环境中，`window` 对象不存在

- 返回默认值 `{ width: 0, height: 0 }`，避免报错

- 在客户端 hydration 后会自动更新为正确的尺寸

  

## 性能优化总结

  

1. **样式缓存**：使用 WeakMap 缓存 `getComputedStyle` 结果

2. **防抖优化**：16ms 防抖，限制更新频率为 60fps

3. **变化检测**：只在尺寸真正变化时更新状态

4. **原生 API**：使用 ResizeObserver 而非轮询

5. **内存管理**：WeakMap 自动清理，组件卸载时清理定时器

  

## 使用示例

  

### 监听元素尺寸

  

```typescript

function MyComponent() {

const containerRef = useRef<HTMLDivElement>(null);

const size = useSize(containerRef.current);

return (

<div ref={containerRef}>

<p>宽度: {size.width}px</p>

<p>高度: {size.height}px</p>

</div>

);

}

```

  

### 监听窗口尺寸

  

```typescript

function MyComponent() {

const size = useSize();

return (

<div>

<p>窗口宽度: {size.width}px</p>

<p>窗口高度: {size.height}px</p>

</div>

);

}

```

  

### 使用依赖触发

  

```typescript

function MyComponent() {

const [isExpanded, setIsExpanded] = useState(false);

const containerRef = useRef<HTMLDivElement>(null);

// 当 isExpanded 变化时，重新计算尺寸

const size = useSize(containerRef.current, [isExpanded]);

return (

<div

ref={containerRef}

className={isExpanded ? 'expanded' : 'collapsed'}

>

{/* ... */}

</div>

);

}

```

  

## 注意事项

  

1. **Ref 传递**：必须传递 DOM 元素引用，不能直接传递 ref 对象

```typescript

// ❌ 错误

const size = useSize(containerRef);

// ✅ 正确

const size = useSize(containerRef.current);

```

  

2. **SSR 环境**：在服务端渲染时，初始值会是 `{ width: 0, height: 0 }`，需要在客户端 hydration 后才能获取正确值

  

3. **浏览器兼容性**：`ResizeObserver` 在较老的浏览器中可能不支持，代码已做了降级处理

  

4. **性能考虑**：虽然已经做了多重优化，但在极端情况下（如快速连续触发大量 resize 事件），仍可能有一定性能开销

  

## 技术栈依赖

  

- **React Hooks**：`useState`, `useEffect`, `useLayoutEffect`, `useCallback`, `useRef`

- **ahooks**：`useEventListener`（用于监听 window 事件）

- **浏览器 API**：`ResizeObserver`, `getComputedStyle`, `window.innerWidth/innerHeight`

```TypeScript
import { useEventListener } from 'ahooks';
import { useState, useEffect, useLayoutEffect, useCallback, useRef } from 'react';

interface SizeResult {
  width: number;
  height: number;
}

export function useSize<T extends HTMLElement>(container?: T | null, deps?: any[]): SizeResult {
  // Cache for computed styles to avoid repeated getComputedStyle calls
  const styleCache = useRef<WeakMap<HTMLElement, CSSStyleDeclaration>>(new WeakMap());
  const lastSizeRef = useRef<SizeResult>({ width: 0, height: 0 });
  const debounceTimerRef = useRef<NodeJS.Timeout>();

  const getSize = useCallback((): SizeResult => {
    // Check if we're in browser environment
    if (typeof window === 'undefined') {
      return { width: 0, height: 0 };
    }

    if (container) {
      // Use cached computed style or create new one
      let computedStyle = styleCache.current.get(container);
      if (!computedStyle) {
        computedStyle = getComputedStyle(container);
        styleCache.current.set(container, computedStyle);
      }

      const paddingTop = parseFloat(computedStyle.paddingTop) || 0;
      const paddingBottom = parseFloat(computedStyle.paddingBottom) || 0;
      const paddingLeft = parseFloat(computedStyle.paddingLeft) || 0;
      const paddingRight = parseFloat(computedStyle.paddingRight) || 0;

      const size = {
        width: Math.max(0, container.clientWidth - paddingLeft - paddingRight),
        height: Math.max(0, container.clientHeight - paddingTop - paddingBottom),
      };

      return size;
    }

    return { width: window.innerWidth, height: window.innerHeight };
  }, [container]);

  const [size, setSize] = useState<SizeResult>(() => getSize());

  // Debounced resize handler to prevent excessive updates
  const debouncedHandleResize = useCallback(() => {
    if (debounceTimerRef.current) {
      clearTimeout(debounceTimerRef.current);
    }

    debounceTimerRef.current = setTimeout(() => {
      const newSize = getSize();

      // Only update if size actually changed (prevent unnecessary re-renders)
      if (newSize.width !== lastSizeRef.current.width || newSize.height !== lastSizeRef.current.height) {
        lastSizeRef.current = newSize;
        setSize(newSize);
      }
    }, 16); // ~60fps throttling
  }, [getSize]);

  // Clear cache when container changes
  useEffect(() => {
    if (container) {
      // Clear cache for the previous container
      styleCache.current = new WeakMap();

      // Initial size calculation
      const initialSize = getSize();
      lastSizeRef.current = initialSize;
      setSize(initialSize);

      // Only create ResizeObserver in browser environment
      if (typeof ResizeObserver !== 'undefined') {
        const resizeObserver = new ResizeObserver(() => {
          // Clear cached style when element resizes as styles might change
          styleCache.current.delete(container);
          debouncedHandleResize();
        });

        resizeObserver.observe(container);

        return () => {
          resizeObserver.disconnect();
          if (debounceTimerRef.current) {
            clearTimeout(debounceTimerRef.current);
          }
        };
      }

      // Fallback cleanup for environments without ResizeObserver
      return () => {
        if (debounceTimerRef.current) {
          clearTimeout(debounceTimerRef.current);
        }
      };
    }
  }, [container, getSize, debouncedHandleResize]);

  // Window resize handlers with debouncing
  useEventListener('resize', debouncedHandleResize, { target: window });
  useEventListener('orientationchange', debouncedHandleResize, { target: window });

  // Handle deps changes (only if deps is provided and has content)
  useEffect(() => {
    if (deps && deps.length > 0) {
      debouncedHandleResize();
    }
  }, deps);

  // Initial size calculation on mount (only once)
  useLayoutEffect(() => {
    const initialSize = getSize();
    lastSizeRef.current = initialSize;
    setSize(initialSize);
  }, []); // Empty dependency array - only run once

  // Cleanup on unmount
  useEffect(() => {
    return () => {
      if (debounceTimerRef.current) {
        clearTimeout(debounceTimerRef.current);
      }
    };
  }, []);

  return size;
}
```

  

暂时无法在飞书文档外展示此内容