---
title: Next.js - 调用API
---

step 1 在api文件夹下新建文件
generatePost.js

```jsx
export default function handler(req,res){
	res.status(200).json({data:"generatedPost"})
}
```

step 2 在要调用api的文件里添加代码先添加button，button的onClick传函数 `onClick={handleClick}`

```jsx
const handleClick = async (props) =>{
	console.log(props)
	const res = await fetch("/api/generatePost",{method:"POST"})
	const data = await res.json();
	console.log(data);
}
```

data能传递出`{data:"generatedPost"}`

step3 加入open ai的api

new一个配置和openai
const res 调用openai.createChatCompletion
里面需要配置model temprature message
message里需要role和content，role有system也就是gpt，user及assistant
`res.data.choices[0].massage.data`是最终的回答

```jsx
import { Configuration, OpenAIApi } from "openai"  //导入api和config组件

export default async function handler(req, res) {
    const config = new Configuration({
        apiKey: process.env.OPENAI_API_KEY
    });
    const openai = new OpenAIApi(config)
  
    const postContentRes = await openai.createChatCompletion({
        model:"gpt-3.5-turbo",
        temperature: 0, //temperature指的创造力，越高越超越你的prompt
        messages:[{
            role:"system",
            content:"You are a blog post generator",
        },{
            role:"user",
            content:`Write a long and detailed SEO-friendly blog post about ${topic}, that targets the following comma-separated keywords:${keywords}.
            The content should be formatted in SEO-friendly HTML, limited to the following HTML tags: p, h1, h2, h3, h4, h5, h6, strong, i, ul, li, ol.`
        }]
    })

    const postContent = postContentRes.data.choices[0]?.message?.content || "";
  
    const titleRes = await openai.createChatCompletion({
        model:"gpt-3.5-turbo",
        temperature: 0,
        messages:[{
            role:"system",
            content:"You are a blog post generator",
        },{
            role:"user",
            content:`Write a long and detailed SEO-friendly blog post about ${topic}, that targets the following comma-separated keywords:${keywords}.
            The content should be formatted in SEO-friendly HTML, limited to the following HTML tags: p, h1, h2, h3, h4, h5, h6, strong, i, ul, li, ol.`
        },{
            role:"assistant",
            content: postContent
        },{
            role:"user",
            content:"Generate appropriate title tag text for the above blog post"
        }]
    })
    const metaDescriptionRes = await openai.createChatCompletion({
        model:"gpt-3.5-turbo",
        temperature: 0,
        messages:[{
            role:"system",
            content:"You are a blog post generator",
        },{
            role:"user",
            content:`Write a long and detailed SEO-friendly blog post about ${topic}, that targets the following comma-separated keywords:${keywords}.
            The content should be formatted in SEO-friendly HTML, limited to the following HTML tags: p, h1, h2, h3, h4, h5, h6, strong, i, ul, li, ol.`
        },{
            role:"assistant",
            content: postContent
        },{
            role:"user",
            content:"Generate SEO-friendly meta description content for the above blog post."
        }]
    })
  
    const title = titleRes.data.choices[0]?.message?.content;
    const meta = metaDescriptionRes.data.choices[0]?.message?.content;

    res.status(200).json({ post: {
        postContent,
        title,
        meta,
    } })
  }
```

step4 将gpt生成的json注入网页
