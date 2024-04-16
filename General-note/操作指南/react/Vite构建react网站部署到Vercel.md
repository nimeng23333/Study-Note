---
title: Vite构建的React网站部署到Vercel
date: 2024-03-27
description: Vite构建的React网站部署到Vercel指南
---

###### 与node.js部署类似，也踩了不少坑，因此发现好像成功的秘诀在用Vercel的CLI。因为一开始是在网页版直接链接github的仓库进行部署，找了很多攻略跟着操作都不行。后来放弃了以后直接在terminal用vercel的CLI部署，自动生成了.vercel这个文件夹，然后一下就成功了。

第一步：新建`vercel.json`文件

```json
  {
    "rewrites": [
      {
        "source": "/(.*)",
        "destination": "/index.html"
      }
    ]
  }
```

第二步：Vercel网页上通过链接github，import 仓库来新建项目，构建设置选Vite

![](/note_images/Pasted%20image%2020240327185458.png)

第三步：setting里设置环境变量（这一步还未验证是否为必须）

`VITE_BASE_PATH` 为 `/`

![](/note_images/Pasted%20image%2020240327185617.png)

将`vite.config.js`文件内容添加如下
```js
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [react()],
  base: process.env.VITE_BASE_PATH,
})
```

第四步：构建失败以后在项目文件的终端部署
```console
vercel
```

自动创建`.vercel`（合理认为这一步是成功的关键）
会弹出来问你是否要链接到已发现项目。
预览部署是否成功

成功后
```console
vercel --prod
```

![](/note_images/Pasted%20image%2020240327190154.png)