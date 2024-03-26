---
title: Next.js - getServerSideProps getStaticProps
---

[Next学习(一)：getServerSideProps、getStaticProps与SSR、SSG、ISR - 掘金 (juejin.cn)](https://juejin.cn/post/7211422882251931707)

- SSR（Server-Side Rendering）：指的是在服务端将 React 组件渲染成 HTML 字符串，并将其传输到浏览器端进行展示。使用 SSR 可以加快首屏渲染速度，提高 SEO。
    
- SSG（Static Site Generation）：指的是在构建时预渲染应用，生成静态 HTML 文件，并在请求时直接返回这些文件。使用 SSG 可以加速页面加载速度，减轻服务器压力。
    
- ISR（Incremental Static Regeneration）：是一种在构建时部分预渲染，同时在用户请求时进行动态渲染的技术。它会根据一定规则将预渲染的页面分为多个小块，并分别进行缓存。当用户请求访问时，只需要重新生成需要更新的部分，可以加快页面展示速度，减轻服务器压力。
    
getStaticProps仅仅只在页面初次构建时执行一次。getServerSideProps每次页面请求都会执行

```jsx
import { getSession, withPageAuthRequired } from "@auth0/nextjs-auth0";
import { ObjectId } from "mongodb";
import { redirect } from "next/dist/server/api-utils";
import { AppLayout } from "../../components/AppLayout";
import clientPromise from "../../lib/mongodb";

export default function Post(props) {
    return <div>this is post page</div>;
}

Post.getLayout = function getLayout(page, pageProps){
  return(
    <AppLayout {...pageProps}>{page}</AppLayout>
  )
}//在需要layout部件的页面加上getLayout函数

export const getServerSideProps = withPageAuthRequired({
  async getServerSideProps(ctx) {
    const {user} = await getSession(ctx.req,ctx.res);
    const client = await clientPromise;
    const db = await client.db("BlogGenerator");
    const userProfile = await db.collection("users").findOne({
      auth0ID: user.sub,
    })

    const post = await db.collection("posts").findOne({
      _id: new ObjectId(ctx.params.postid),
      userID : userProfile,
    })

    if(!post){
      return{
        redirect:{     //临时重定向
          destination:"/post/new",
          permanent:false,
        }
      }
    }
    return{
      props:{
        title:post.title,
        keywords: post.keywords,
        postContent: post.postContent,
        createdDate: post.createdDate,
        metaDescription: post.metaDescription,
        tags:post.tags,
        topic:post.topic
      }
    }
}
});
```

使用了`getServerSideProps`函数来获取服务器端的属性。这个函数在每次请求时运行，并返回一个对象，该对象将被传递给页面的props。
这里的`getServerSideProps`出现了两次，但它们的含义是不同的：
1. 第一次出现的`getServerSideProps`是一个导出的函数，它是Next.js预定义的一个特殊函数，Next.js会在服务器端调用这个函数，然后把返回的结果作为props传递给React组件。
2. 第二次出现的`getServerSideProps`是`withPageAuthRequired`函数的一个参数，这是一个回调函数，它在`withPageAuthRequired`函数内部被调用。
至于为什么使用`ctx`而不是`req`和`res`，这是因为`ctx`（上下文）对象包含了`req`和`res`，以及其他一些有用的字段。在Next.js中，`getServerSideProps`函数的参数是一个上下文对象，它包含了`req`（请求对象）、`res`（响应对象）、`query`（查询字符串参数）等字段。所以，使用`ctx`可以让我们在函数内部访问到更多的信息。

```jsx
    if(!post){
      return{   //不要忘记return！！！
        redirect:{     //临时重定向
          destination:"/post/new",
          permanent:false,
        }
      }
    }
```