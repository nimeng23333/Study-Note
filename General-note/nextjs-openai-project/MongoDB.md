---
title: MongoDB
---

[How to Integrate MongoDB Into Your Next.js App | MongoDB](https://www.mongodb.com/developer/languages/javascript/nextjs-with-mongodb/)

next.js 链接mongoDB
lib文件夹下mongodb.js
```js
import { MongoClient } from 'mongodb';

if (!process.env.MONGODB_URI) {
  throw new Error('Invalid/Missing environment variable: "MONGODB_URI"');
}

const uri = process.env.MONGODB_URI;

let client;
let clientPromise;
  
if (process.env.NODE_ENV === 'development') {
  // In development mode, use a global variable so that the value
  // is preserved across module reloads caused by HMR (Hot Module Replacement).
  if (!global._mongoClientPromise) {
    client = new MongoClient(uri);
    global._mongoClientPromise = client.connect();
  }
  clientPromise = global._mongoClientPromise;
} else {
  // In production mode, it's best to not use a global variable.
  client = new MongoClient(uri);
  clientPromise = client.connect();
}
// Export a module-scoped MongoClient promise. By doing this in a
// separate module, the client can be shared across functions.
export default clientPromise;
```

使用

```js
import { getSession } from "@auth0/nextjs-auth0";  
import clientPromise from "../../lib/mongodb";

export default async function handler(req, res) {
    const {user} = await getSession(req,res); //auth0会话
    const client  = await clientPromise;  //连接mongoDB
    const db = client.db("BlogGenerator");    //db名字
    const userProfile = await db.collection("users").updateOne( //updateOne更新一条数据
    { auth0ID:user.sub},  //检测的内容
    {
        $inc:{availableTokens:10},  //数值增加的项目
        $setOnInsert:{auth0ID:user.sub}  //没有就插入的内容
    },
    {upsert:true})  //update+intert打开 检测有没有，有就修改，没有就插入
    res.status(200).json({ name: 'John Doe' })
  }
```

其他还有updateMany()、find()、findOne()、insertOne()、replaceOne()、deleteOne()等等
具体操作方法由$加query语句组成的object
比如array用`$push:{ category: "art"}`
设值用`$set:{"rating": 9.5}`等等
`$lt: new Date(lastDate)` lt是小于，可以筛选小于lastDate的值


[Quick Reference — Node.js Driver (mongodb.com)](https://www.mongodb.com/docs/drivers/node/current/quick-reference/)


```js
import { getSession, withApiAuthRequired } from "@auth0/nextjs-auth0";
import { Configuration, OpenAIApi } from "openai";
import clientPromise from "../../lib/mongodb";

export default withApiAuthRequired(async function handler(req, res) {
    const {user} = await getSession(req,res); //auth0会话得到用户
    const client = await clientPromise;  //连接mongodb
    const db = client.db("BlogGenerator");
    const userProfile = await db.collection("users").findOne({auth0ID:user.sub});
    if(!userProfile?.availableTokens){    //用了一个?.的链式，如果userProfile不存在就直接不存在，如果userProfile存在但availableTokens为0也不存在，等于!userProfile || userProfile.availableTokens==0
        res.status(403);  //403代表没有权限
        return;
    }

    const config = new Configuration({
        apiKey: process.env.OPENAI_API_KEY,
        basePath:"https://api.openai-proxy.com/v1"
    });

    const openai = new OpenAIApi(config);
    const {topic , keywords} = req.body;

    const postRes = await openai.createChatCompletion({
        model:"gpt-3.5-turbo",
        temperature: 0,
        messages:[{
            role:"system",
            content:"You are a blog post generator",
        },{
            role:"user",
            content:`Write a long and detailed SEO-friendly blog post about ${topic}, that targets the following comma-separated keywords:${keywords}.
            The content should be formatted in SEO-friendly HTML, limited to the following HTML tags: p, h1, h2, h3, h4, h5, strong, i, ul, li, ol.`
        }]
    })

    const post = postRes.data.choices[0]?.message?.content || "";

    await db.collection("users").updateOne({
        auth0ID:user.sub
    },{
        $inc:{
            availableTokens:-1
        }
    }); //成功就让users里的token-1，这里只有inc，减少都用负数

    const postStored = await db.collection("posts").insertOne({
        userID:userProfile._id,  //注意，这里用的userProfile是mongoDB查找的东西，通过加._id可以很轻松的实现foreign key
        post:post,
        topic,
        keywords,
        createdDate: new Date()
    })  //在posts collection里添加新post
    
    res.status(200).json({post:post})
  })
```

`const userProfile = await db.collection("users").findOne({auth0ID:user.sub});`
`userID:userProfile._id`,  这里用的userProfile是mongoDB查找的东西，通过加`._id`可以很轻松的实现foreign key


### object ID

mongoDB存储的id是objectID，带有时间戳，不能直接通过输入同样的一个字符串查询

post详情页面：
`/post/[postid]`

`[postid].js`
```jsx
import { getSession, withPageAuthRequired } from "@auth0/nextjs-auth0";
import { ObjectId } from "mongodb";
import { AppLayout } from "../../components/AppLayout";
import clientPromise from "../../lib/mongodb";

export default function Post() {
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
      _id: new ObjectId(ctx.params.postid), //_id是ObjectId,需要new一个，与普通node.js的/post/:id 类似，req.params.id
      userID : userProfile._id,
    })
}
});
```
