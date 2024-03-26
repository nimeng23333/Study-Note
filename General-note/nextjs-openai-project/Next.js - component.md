---
title: Next.js - component
---

所有的components
都需要在components文件夹里新建一个新建夹，并包含两个文件，一个是组件名字的js文件，一个是index.js的文件

index.js里只有`export * from "./folder";`
## Layout component
nextjs的页面的公用组件需要设置在layout里

#### step1.在根目录新建Components文件夹，并在里面建AppLayout文件夹
#### step2.在AppLayout文件夹里建index.js和AppLayout.js两个文件

AppLayout.js
```jsx
export const AppLayout = ({children}) =>{
    return(
        <div>
            AppLayout
            <div>{children}</div>
        </div>
    )
}
```

index.js
```jsx
export * from "./AppLayout";
```

#### step3.在需要加layout的页面添加代码

new.js
```jsx
import { withPageAuthRequired } from "@auth0/nextjs-auth0";
import { AppLayout } from "../../components/AppLayout"; //导入AppLayout

export default function NewPost() {
    return <div>this is new post page</div>;
}

NewPost.getLayout = function getLayout(page, pageProps){
  return(
    <AppLayout {...pageProps}>{page}</AppLayout>
  )
}     //在需要layout部件的页面加上getLayout函数，这个函数传递两个参数，page和pageProps，传递pageProps的时候需要加spread

export const getServerSideProps = withPageAuthRequired(() =>{
  return {
    props:{},
  };
});
```

#### step4.更新_app.js

判断component里是否有getLayout函数，如果有就赋值给getLayout，否则就给原页面
在`<component>`外添加getLayout(page,pageProps)函数
```jsx
import '../styles/globals.css'
import { UserProvider } from '@auth0/nextjs-auth0/client'

function MyApp({ Component, pageProps }) {
  const getLayout = Component.getLayout || ((page) =>page) //渲染之前先查找要渲染的component是否含有getLayout，如果有就用，没有就用原本的page，回调函数千万不要加花括号，否则没有数据return出来。
  return (
    <UserProvider>
      {getLayout(<Component {...pageProps} />, pageProps) /* getLayout有两个参数,一个page一个props，这里的page也就是component */}  
    </UserProvider>
  )
}

export default MyApp
```


## logo component