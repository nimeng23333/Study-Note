---
title: React
---


### 图片点击放大

https://blog.csdn.net/weixin_42559167/article/details/131175896
手写一个 React 图片预览组件

### React.memo 防止不必要的重新渲染

可能其他组件的改变而造成了组件树的重新渲染，用memo包装组件，可以阻止React的重新渲染，只有当该组件的props发生改变的时候才会触发重新渲染该组件

```jsx
import {memo} from 'react';

const MdxMemo = memo(Mdx) //Mdx是组件名

//在组件中使用MdxMemo标签代替Mdx
<MdxMemo code={project.body.code} />
```