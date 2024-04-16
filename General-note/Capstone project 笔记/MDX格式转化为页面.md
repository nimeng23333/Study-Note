---
title: MDX格式转化为页面
---

[Markdown语法 (homeway.github.io)](https://homeway.github.io/tutorial/markdown.html)
### Next.js框架

可以用contentlayer

`npm i @next/mdx`

`npm i contentlayer next-contentlayer`

`npm i markdown-wasm`


安装其他插件

`npm i remark-gfm` 用于支持table渲染
`npm i rehype-pretty-code`用于高亮代码块
`npm i rehype-slug`
`npm i rehype-autolink-headings`

`npm i remark-gfm rehype-pretty-code rehype-slug rehype-autolink-headings`

在开发前端项目的过程中，我们可能会碰到需要对文档或文章做一些优化的情况。其中一个较为常见的需求是为文档或文章中的标题生成锚点链接。这样可以提高用户阅读体验，并且有助于搜索引擎的优化。npm 包 rehype-slug 就是帮助我们快速生成锚点链接的工具。
rehype-slug 是一个基于 Unist（抽象语法树）的 rehype 插件，用于从标题中生成唯一的 slug（即标题的标识符）。rehype-slug 不仅可以为标题生成唯一的 slug，还可以为文档中的链接、代码块等元素生成唯一的标识符。
autolink-heading则用于添加链接

让tailwind应用于markdown还需要安装
`npm i @tailwindcss/typography`
并配置tailwind.config.js

```
	plugins: [
		require("@tailwindcss/typography"),
	],
```

将 Contentlayer 连接到 Next.js 的两个构建过程中:`next dev`和`next build`。

为此,将 Next.js 配置包装在`withContentlayer`方法中。

导航到`next.config.mjs`修改如下：(注意，后缀改为mjs)或者在package.json里添加type:"module"
```js
const { withContentlayer } = require('next-contentlayer');

/** @type {import('next').NextConfig} */

const nextConfig = {
  pageExtensions:["js", "jsx", "ts", "tsx", "md", "mdx"],
  reactStrictMode: true,
}

module.exports = withContentlayer(nextConfig)
```

创建一个文件`jsconfig.json`并添加以下代码：
```json
{
    "compilerOptions": {
      "baseUrl": ".",
      "paths": {
        "@/*": ["./*"],
        "contentlayer/generated": ["./.contentlayer/generated"],
      }
    },
    "include": ["next-env.d.js", "**/*.js", "**/*.jsx", ".next/types/**/*.js",".contentlayer/generated"],
    "exclude": ["node_modules"],
}
```

创建一个名为`contentlayer.config.js`的新文件。

添加配置
```js
import { defineDocumentType, makeSource } from "contentlayer/source-files";
import remarkGfm from "remark-gfm";
import rehypePrettyCode from "rehype-pretty-code";
import rehypeSlug from "rehype-slug";
import rehypeAutolinkHeadings from "rehype-autolink-headings";

/** @type {import('contentlayer/source-files').ComputedFields} */
const computedFields = {
	path: {
		type: "string",
		resolve: (doc) => `/${doc._raw.flattenedPath}`,
	},
	slug: {
		type: "string",
		resolve: (doc) => doc._raw.flattenedPath.split("/").slice(1).join("/"),
	},
};

export const Project = defineDocumentType(() => ({
	name: "Project",
	filePathPattern: "./projects/**/*.mdx",
	contentType: "mdx",

	fields: {
		published: {
			type: "boolean",
		},
		title: {
			type: "string",
			required: true,
		},
		description: {
			type: "string",
			required: true,
		},
		date: {
			type: "date",
		},
		url: {
			type: "string",
		},
		repository: {
			type: "string",
		},
	},
	computedFields,
}));

export const Page = defineDocumentType(() => ({
	name: "Page",
	filePathPattern: "pages/**/*.mdx",
	contentType: "mdx",
	fields: {
		title: {
			type: "string",
			required: true,
		},
		description: {
			type: "string",
		},
	},
	computedFields,
}));

export default makeSource({
	contentDirPath: "./content", //文件存放的位置
	documentTypes: [Page, Project],
	mdx: {
		remarkPlugins: [remarkGfm],
		rehypePlugins: [
			rehypeSlug,
			[
				rehypePrettyCode,
				{
					theme: "github-dark",
					onVisitLine(node) {
						// Prevent lines from collapsing in `display: grid` mode, and allow empty
						// lines to be copy/pasted
						if (node.children.length === 0) {
							node.children = [{ type: "text", value: " " }];
						}
					},
					onVisitHighlightedLine(node) {
						node.properties.className.push("line--highlighted");
					},
					onVisitHighlightedWord(node) {
						node.properties.className = ["word--highlighted"];
					},
				},
			],
			[
				rehypeAutolinkHeadings,
				{
					properties: {
						className: ["subheading-anchor"],
						ariaLabel: "Link to section",
					},
				},
			],
		],
	},
});
```
1. `defineDocumentType`函数用于定义文档类型的结构。这里定义了`Project`和`Page`两种类型，每种类型都有自己的字段和计算字段。
2. `computedFields`是计算字段，它们不直接在内容文件中定义，而是根据其他字段计算得出。例如，`path`字段基于文档的`flattenedPath`计算得出。
3. `makeSource`函数用于告诉Contentlayer在哪里找到内容文件，并指定使用哪些文档类型

创建`mdx-components.jsx`文件

```jsx
export default function useMDXComponents(components){

    return {
        // Allows customizing built-in components, e.g. to add styling.
        h1: ({ children }) => (
          <h1 className="mt-2 text-3xl font-bold tracking-tight text-zinc-100 md:text-center sm:text-4xl">
            {children}
          </h1>
        ),
        h2: ({ children }) => (
          <h2 className="text-zinc-50">{children}</h2>
        ),
        ...components,
      };
}
```


==markdown格式里可以直接写HTML标签==


#### 视频嵌入iframe标签，自适应高度
正常情况下可以设置width为100%，但height不方便计算，可以用以下取巧的办法：

```jsx
<div style={{width:"100%", height:0, paddingBottom:"56.25%", position:"relative", marginTop:"40px", marginBottom:"40px"}}>
    <iframe src="https://www.youtube.com/embed/nxFj2GDBZsU?wmode=opaque&amp;enablejsapi=1" frameBorder="0" allowFullScreen="" style={{width:"100%",height:"100%",position:"absolute",top:0,left:0}} />
</div>
```
包裹一个div后，用padding来计算高度，设置iframe 为absolute后height为100%即可

[如何使youtube iframe嵌入的视频自适应 （100%宽度） – 微言 (cellmean.com)](https://www.cellmean.com/%E5%A6%82%E4%BD%95%E4%BD%BFyoutube-iframe%E5%B5%8C%E5%85%A5%E7%9A%84%E8%A7%86%E9%A2%91%E8%87%AA%E9%80%82%E5%BA%94-%EF%BC%88100%E5%AE%BD%E5%BA%A6%EF%BC%89)


## Markdown 语法
### 图片

`[![public_apis](/images/public_apis/截图.png )](https://public-apis-search-tool.vercel.app/)`
带链接的图片

`![arch](/images/arch/封面.jpg)`
普通图片
