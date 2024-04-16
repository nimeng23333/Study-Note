---
title: Express EJS引擎模板下部署Vercel
date: 2024-03-27
description: Node.js Express EJS 部署Vercel指南
---
###### Vercel作为前端快速部署网站，并不太支持node.js express下的部署，正常情况下localhost能打开的网站到vercel上部署不是404就是505。505的主要原因是看了vercel的官方文档把index.js的路径改到api，试图通过serverless function来部署，但实际还是不行，因为我们需要渲染ejs模板而不是做一个后端api服务。

官方Express.js X Vercel链接[Using Express.js with Vercel](https://vercel.com/guides/using-express-with-vercel)

找了很多篇攻略，都说重点在根目录创建一个`vercel.json`文件
然后填写如下配置：
```json
{
  "builds": [{ "src": "index.js", "use": "@vercel/node" }],
  "routes": [{ "src": "(.*)", "dest": "index.js" }]
}
```

![](/note_images/Pasted%20image%2020240327142825.png)
此时的文件夹结构如上，仍然不行。

最终发现了一个可以的解决方法，重点：1.用express generator产生项目，2. `vercel.json`。

第一步：装express generator

```console
npm install express-generator -g
```

第二步：创建项目

```console
express --view=ejs express-vercel
```

出来的项目结构如下：
![](/note_images/Pasted%20image%2020240327143447.png)

第三步：在根目录新建`vercel.json`文件，并填写配置

```json
{
  "builds": [{ "src": "app.js", "use": "@vercel/node" }],
  "routes": [{ "src": "(.*)", "dest": "app.js" }]
}
```

ps:这里额外提一嘴，有的时候会看到攻略里写`@now/node`亲测和`@vercel/node`是一个东西，但填now的时候会提示你已经过时了，最新用vercel。

第四步：部署vercel

这一步可以通过上传到github然后链接给vercel，也可以通过vercel CLI直接部署

这里通过CLI直接部署

```console
npm i vercel -g
```

然后登陆

```console
vercel login
```

出来一条链接，在网页里直接登录

登录完成后

```console
vercel dev
```

然后就可以在localhost看网站，同时也会给你一个网页链接部署成功。

文件有更新后重新部署

```console
vercel
```

![](/note_images/Pasted%20image%2020240327144123.png)

![](/note_images/Pasted%20image%2020240327144215.png)
