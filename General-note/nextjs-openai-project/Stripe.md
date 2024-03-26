---
title: Stripe
---

在stripe网站需要两个env参数，STRIPE_SECRET_KEY及产品的价格id

```jsx
import { getSession } from "@auth0/nextjs-auth0";
import clientPromise from "../../lib/mongodb";
import stripeInit from "stripe";

const stripe = stripeInit(process.env.STRIPE_SECRET_KEY);//初始化stripe只需要一个参数

export default async function handler(req, res) {
    const lineItems = [{
        price: process.env.STRIPE_PRODUCT_PRICE_ID,
        quantity:1
    }] //产品，接受参数price和quantity
    const protocol = process.env.NODE_ENV === "development"? "http://" : "https://"; //开发模式的协议是http，反之https
    const host = req.headers.host;
    const checkoutSession = await stripe.checkout.sessions.create({
        line_items:lineItems,
        mode:"payment",
        success_url:`${protocol}${host}/success`
    })  //创建付款会话

    const {user} = await getSession(req,res);
    const client  = await clientPromise;
    const db = client.db("BlogGenerator");
    const userProfile = await db.collection("users").updateOne({
        auth0ID:user.sub,
    }, {
        $inc:{
            availableTokens:10
        },
        $setOnInsert:{
            auth0ID:user.sub
        }
    },{upsert:true})
    res.status(200).json({ session:checkoutSession }) //将会话传递出去，这session里有个url就是付款链接，在页面定向过去
  }
```

nextjs定向外部链接
window.location.href = data.session.url

### webhook

stripe提供webhook，来告诉商家是否付款完成
启动cli
```cmd
stripe login

stripe listen --forward-to localhost:3000/api/webhook/stripe
```

正常情况外部实例无法对我们nextjs内部hook进行处理，我们需要额外将其允许
==micro-cors==这个nextjs包让我们可以打开端点，让外部实例进行调用


[Verify Stripe webhook signature in Next.js API Routes | Max Karlsson](https://maxkarlsson.dev/blog/verify-stripe-webhook-signature-in-next-js-api-routes)
next js 使用stripe webhook


stripe.js
```jsx
import Cors from "micro-cors";
import stripeInit from "stripe";
import verifyStripe from "@webdeveducation/next-verify-stripe";
import clientPromise from "../../../lib/mongodb";

const cors = Cors({
    allowMethods:['POST','HEAD']
});
//用Cors打开端口，允许外部操作

export const config = {
    api:{
        bodyParser: false
    }
}
//打开端口后的config都用这个config，这里的config是让default的不传递，只传递stripe过来的内容

const stripe = stripeInit(process.env.STRIPE_SECRET_KEY);
const endpointSecret = process.env.STRIPE_WEBHOOK_ENDPOINTSECRET;

const handler = async(req,res) =>{
    if(req.method === "POST"){
        let event;
        try{
            event = await verifyStripe({
                req,
                stripe,
                endpointSecret
            })
        }catch(e){
            console.log("Error:",e);
        }
        switch(event.type){
            case "payment_intent.succeeded":{
                const client  = await clientPromise;
                const db = client.db("BlogGenerator");
                const paymentIntent = event.data.object;
                const auth0ID = paymentIntent.metadata.sub;
                const userProfile = await db.collection("users").updateOne({
                    auth0ID,
                }, {
                    $inc:{
                        availableTokens:10
                    },
                    $setOnInsert:{
                        auth0ID
                    }
                },{upsert:true})
            }
            default:
                console.log("Unhandled event",event.type);
        }
        res.status(200).json({received:true})
    }
}

export default cors(handler);
```

addToken.js
```js
import stripeInit from "stripe";
import { getSession } from "@auth0/nextjs-auth0";

const stripe = stripeInit(process.env.STRIPE_SECRET_KEY);

export default async function handler(req, res) {
    const {user} = await getSession(req,res);
    const lineItems = [{
        price: process.env.STRIPE_PRODUCT_PRICE_ID,
        quantity:1
    }]

    const protocol = process.env.NODE_ENV === "development"? "http://" : "https://"; //开发模式的协议是http，反之https
    const host = req.headers.host;

    const checkoutSession = await stripe.checkout.sessions.create({
        line_items:lineItems,
        mode:"payment",
        success_url:`${protocol}${host}/success`,
        payment_intent_data:{metadata:{sub:user.sub}},
        metadata:{sub:user.sub},
    })
    /*lineItems：line_items是用户购买的商品列表。每个商品都是一个对象，包含商品的价格、数量等信息。mode: "payment"：这表示结账会话的模式是“payment”，即一次性付款。
    success_url如果付款成功，用户将被重定向到这个URL。
    payment_intent_data: { metadata: { sub: user.sub } }：payment_intent_data是一个对象，它包含了创建PaymentIntent时要使用的参数。
    metadata: { sub: user.sub }：这是结账会话的metadata，它也是一个可以存储额外信息的对象，这些信息会与结账会话一起保存。
    metadata出现了两次，一次在payment_intent_data中，一次在结账会话的顶层。这是因为Stripe允许在两个地方都添加metadata。一个是在PaymentIntent级别（即payment_intent_data.metadata），另一个是在CheckoutSession级别（即metadata）。让两个级别都能访问到这个数据
    */
    res.status(200).json({ session:checkoutSession })
  }
```