---
title: Next.js & React 路由
---

## useRouter hook 与 redirect

### useRouter

useRouter：在react生成component的时候能用
```jsx
import { useRouter } from "next/router";
export default function NewPost(props) {
	const router = useRouter();
	const handleSubmit = async (e) =>{
      e.preventDefault();
      const res = await fetch("/api/generatePost",
      {
        method:"POST",
        headers:{"content-type":"application/json"},
        body:JSON.stringify({topic,keywords}),
      });
      const data = await res.json();
      console.log(data)
      
      if(data?.postID){
        router.push(`/post/${data.postID}`)
      }
    }
    return (
    <div>
    </div>
      )
}}
```
1. `useRouter` 是 Next.js 提供的一个 React Hook，用于访问 `router` 对象。
2. `const router = useRouter();`这行代码调用了 `useRouter` Hook，并将返回的 `router` 对象保存在 `router` 变量中。`router` 对象包含了当前路由的信息，以及一些方法，如 `push`、`replace` 等，用于进行路由跳转。


### useRouter().push VS. useRouter().replace

在 Next.js 中，`useRouter().push` 和 `useRouter().replace` 是两种不同的路由跳转方法，它们的区别主要体现在对浏览器历史记录的处理上：

useRouter().push：这个方法会在浏览器的历史记录中追加一个新的记录，你可以通过 window.history 看到这个记录。当你使用 push 方法跳转到一个新的路由后，可以通过浏览器的后退按钮返回到之前的路由。

useRouter().replace：这个方法会替换当前的路由记录。也就是说，当你使用 replace 方法跳转到一个新的路由后，如果你试图通过浏览器的后退按钮返回，你会发现上一个路由地址已经消失了

==push适用于一般路径，replace适用于delete以后的路径跳转==

### redirect

redirect：在服务器端跳转重定向用
```jsx
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
        redirect:{
          destination:"/post/new",
          permanent:false,
        }
      }
    }
    return{
      props:{}
    }
}
});
```

比较：
`useRouter` 是一个 React Hook，只能在 React 组件或其他 Hook 中使用。重定向是发生在 Next.js 的 `getServerSideProps` 或 `getStaticProps` 函数中使用的，这些函数在服务器端运行，不是 React 组件，因此不能使用 React Hook。

如果想在服务器端进行路由跳转，可以使用 Next.js 提供的 `redirect` 对象。如果你想在客户端进行路由跳转，可以使用 `useRouter` Hook。但是，不能在服务器端的函数中使用 `useRouter`，也不能在 React 组件中使用 `redirect` 对象。这两者各有用途，不能混用。
