---
title: useRouter - next.js hook
---

router是Next.js里的hook，而非react hook
用于处理与路由相关的操作


在react组件里使用uesRouter()

```js
export const DashboardLayout = ({children,...rest}) =>{
    const {asPath} = useRouter();
    console.log(asPath); // 得到router地址 /dashboard/orderList
    const path= asPath.split(/\//g).slice(-1);
    console.log(path);  //orderList
 }
```