---
title: Auth0
---

### Auth0配置

.env.local文件
```.env.local
AUTH0_SECRET=
AUTH0_BASE_URL=http://localhost:3000/
AUTH0_ISSUER_BASE_URL=
AUTH0_CLIENT_ID=
AUTH0_CLIENT_SECRET=
```

![[../note_images/Pasted image 20240102143933.png]]
AUTH0_SECRET可以是随机值，建议在termimal通过`openssl rand -hex 32`生成一个随机32字节值

AUTH0_BASE_URL是网站部署地址

AUTH0_ISSUER_BASE_UR是domain，需要在前面加https://

![[../note_images/Pasted image 20240102152741.png]]
http://localhost:3000/api/auth/callback

在pages文件夹里的api文件夹里新建auth文件夹并新建`[...auth0].js`文件
用来处理auth相关业务

`[...auth0].js`文件：
```jsx
import { handleAuth } from "@auth0/nextjs-auth0";
export default handleAuth();
```


### withPageAuthRequired

withPageAuthRequired是一个高阶函数，可以传递一个函数作为参数并返回一个函数
它有两个可选内容getServerSideProp和returnTo
```jsx
WithPageAuthRequiredPageRouterOptions<P, Q>: {  
    getServerSideProps?: GetServerSideProps<P, Q>;  
    returnTo?: string;  
}
```

在需要传参的时候代码如下：
```jsx
// pages/protected-page.js
import { getSession, withPageAuthRequired } from '@auth0/nextjs-auth0';

export default function ProtectedPage({ user, customProp }) {
  return <div>Protected content</div>;
}

export const getServerSideProps = withPageAuthRequired({    //注意这里传参，小括号里跟花括号
  // returnTo: '/unauthorized',
  async getServerSideProps(ctx) {
    // access the user session if needed
    // const session = await getSession(ctx.req, ctx.res);
    return {
      props: {
        // customProp: 'bar',
      }
    };
  }
});
```

如果不需要传参那就可以使用默认值
```jsx
export const getServerSideProps = withPageAuthRequired(()=>{    
return {
      props: {}
    };
});
```