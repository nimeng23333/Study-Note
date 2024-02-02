
[Next.js 备忘清单 & nextjs cheatsheet & Quick Reference](https://quickref.me/zh-CN/docs/nextjs.html)


Node.js 是一个 JavaScript 运行时环境，它允许你在服务器端运行 JavaScript 代码。它提供了操作系统和网络的低级 API，让你能够创建服务器、处理HTTP请求和其他服务器端任务。
Next.js 是一个基于 Node.js 的 React 框架，它提供了服务器渲染、静态站点生成、路由、优化等高级功能，让你能够快速构建现代 web 应用。


### Route

[看了就会的next.js路由 - 掘金 (juejin.cn)](https://juejin.cn/post/6844903827615776775)

next.js的路由相对后端框架或者是react-router都要简单很多，本质上就只是pages路径下对应的js文件的目录和文件名称直接作为路由，然后就是支持了query传递参数，路由跳转是在本地浏览器上操作的，所以如果不借助外力，可能会有404错误，因此要借助后端框架实现路由映射。

在next.js中，无法通过`/test/:id`这种参数路由的方式获取到参数，它只能通过`query`的方式获取参数，即`/test?id=xx`的方式

### 动态路由
![[Pasted image 20240102142631.png]]
用方括号表示动态路由
![[Pasted image 20240102142651.png]]
在post后面任意值都可以跳转到这个页面


### `<Link>`
next.js内置的组件，用来组织网站内部链接

![[Pasted image 20240102152041.png]]


### Auth0

auth0自带hook，useUser
console后可得到nickname，name，picture、sid、sub等信息，sub是每个用户的唯一标识，这个sub将用来识别MongoDB数据库里的用户
![[Pasted image 20240102154656.png]]

index.js:
```jsx
import Link from "next/link";
import {useUser} from "@auth0/nextjs-auth0/client";  //导入useUser

export default function Home() {
  const {user} = useUser();  //构造user
  console.log(user);
  return <div>
    <div>
      <Link href="/api/auth/login">Login</Link>
    </div>
</div>;

}
```

注意：要用useUser需要将app包裹在UserProvider里，因为Auth0使用了react的hook

`_app.js`:
```jsx
import '../styles/globals.css'
import { UserProvider } from '@auth0/nextjs-auth0/client'//导入UserProvider

function MyApp({ Component, pageProps }) {
  return (
    <UserProvider>
      <Component {...pageProps} />
    </UserProvider>
  )
}
export default MyApp
```


总结：next.js通过Auth0设置登录要点：
1.在api文件夹里新建文件夹auth并新建`[...auth0].js`（文件名大小写都必须一致）

```jsx
import { handleAuth } from "@auth0/nextjs-auth0";
export default handleAuth();
```
2.index.js
```jsx
import Link from "next/link";
import {useUser} from "@auth0/nextjs-auth0/client";
  
export default function Home() {
  const {user} = useUser();
  return <div>
    <div>
    This is the homepage
    </div>
    <div>
      {!!user ? <div><div>user info</div><Link href="/api/auth/logout">Logout</Link></div> : <Link href="/api/auth/login">Login</Link>}
    </div>
</div>;
}
```

login和logout的href写"/api/auth/login"和"/api/auth/logout"

3.需要useUser hook，并用UserProvider包裹所有component


### 外链图像

next.js里外链图片需要再next.config.js里添加域名，否则不会生效

![[Pasted image 20240102160658.png]]

gravatar在国内被墙了，各种国内镜像试过以后唯一能用的是cravata
`<Image src={user.picture.replace('s.gravatar.com', 'cravatar.cn')} alt={user.nickname} height={50} width={50} />`
config里
`  images:{domains:['s.gravatar.com','cravatar.cn'],},`

### getServerSideProps

`getServerSideProps`是 `Next.js` 中的一个生命周期函数，它允许你在服务器端获取数据，并作为 props 传递给你的 pages 组件
`getServerSideProps`函数需要返回一个对象，该对象可以包含props、redirect等属性，其值是一个对象或一个 Promise。在函数中，你可以使用任何你需要的API 或服务来获取数据，然后将数据分配给 props对象。

`getServerSideProps` 是定义在页面中的 `API`，但是其执行环境是 `node` 端，而不是客户端，一般常见使用场景为：

- 页面前置权限校验
- 页面必备参数获取

使用时需要在对应的 `page` 文件中 `export getServerSideProps`

```jsx
// This gets called on every request  
export async function getServerSideProps() {  
// Fetch data from external API  
const res = await fetch(`https://.../data`)  
const data = await res.json()  
// Pass data to the page via props  
return { props: { data } }  
}

或

export const getServerSideProps = ((content) =>{  
// Fetch data from external API  
const res = await fetch(`https://.../data`)  
const data = await res.json()  
// Pass data to the page via props  
return { props: { data } }  
}

export default function Page({ data }) {  
// Render data...  
}
```

这样便可以从页面组件中直接使用 `props` 来获取 `getServerSideProps` 注入的 `props` 了。

nextJS中getServerSideProps和getStaticProps的区别
1.next 通过导出的函数来区分这个页面是哪种染，所以这两个函数在一个页面里面只能存在
2.这两个方法调用的时机，都是在浏览器染之前，也就是说没有 document 、 window 之类的对象
3.getstaticProps 是在打包的时候将外部数据传入组件 
getserversideProps 每次在服务器接到请求的时候更新
4.特别要注意这些方法运行环境的问题，比如，baseur1 不能写相对地址，因为getserversideProps 执行的时候是在服务器上进行的，所以相对地址也不对


与 getStaticProps 只在build时生成一遍静态页面，或者定期重新生成页面不同，使用getServerSideProps，页面在每接收到一条传入请求就重新生成一遍。
因为每次请求都是从数据库读数据重新生成页面，页面加载速度会变慢。但是使用getServerSideProps 可以访问 request 对象。
如果页面不是需要每秒更新几次，或者需要访问 request 对象，那么使用getStaticProps 函数更好。


注意事项：
`getServerSideProps` 只能从 **page** 导出。 你无法从非页面文件导出它。
必须将 `getServerSideProps` 导出为独立函数 — 如果添加 `getServerSideProps` 作为页面组件的属性，它将不工作。

### 为页面添加Auth登录验证

```jsx
import { withPageAuthRequired } from "@auth0/nextjs-auth0";

export default function Page() {
    return <div>page content</div>;
  }

export const getServerSideProps = withPageAuthRequired(() =>{
  return {
    props:{},
  };
});
```


### 三元操作
重要：
在三元操作中true和false的expression里都只能有一个东西，一般用div包裹，但也存在设置了父div而没法在三元里再装一个div的情况，不能直接将div删掉，而需要加一个空的`<> </>`

#错误代码 

```jsx
<div className="bg-slate-700 flex items-center gap-2 border-t border-t-black/50 h-20 px-2">
{!!user ? (
			<div className="min-w-[30]">
				<Image src={user.picture.replace('s.gravatar.com', 'cravatar.cn')} alt={user.name} height={50} width={50} className="rounded-full" />
			</div>
			<div className="flex-1">
				<div>{user.name}</div>
				<Link href="/api/auth/logout">Logout</Link>
			</div>
			) : (<Link href="/api/auth/login">Login</Link>)}
</div>
```

在true的时候有两个div，代码会报错，而我们不允许div 元素里再有个div包含这两个div的话就用空的

正确：
```jsx
<div className="bg-slate-700 flex items-center gap-2 border-t border-t-black/50 h-20 px-2">
{!!user ? (<> //用空标签包裹，最好加括号
			<div className="min-w-[30]">
				<Image src={user.picture.replace('s.gravatar.com', 'cravatar.cn')} alt={user.name} height={50} width={50} className="rounded-full" />
			</div>
			<div className="flex-1">
				<div>{user.name}</div>
				<Link href="/api/auth/logout">Logout</Link>
			</div>
			</>) : (<Link href="/api/auth/login">Login</Link>)}
</div>
```



