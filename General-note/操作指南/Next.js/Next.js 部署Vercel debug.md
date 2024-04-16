---
title: Next.js 部署Vercel debug
date: 2024-04-01T00:00:00
description: Next.js 部署Vercel debug
---

![](/note_images/Pasted%20image%2020240401181523.png)

部署时遇到如上问题，在本地直接运行`npm run build`命令

![](/note_images/Pasted%20image%2020240401181705.png)

看出来报错是由于构建json文件时产生的错误。

经过查找资料，应该是代码压缩造成的，解决方法为在`next.config.js`中禁用代码压缩
```javascript
module.exports = {
  // ...其他配置
  webpack(config) {
    // ...其他webpack配置
    config.optimization.minimize = false;
    return config;
  },
};
```

禁用后构建成功。

### ESLint error
![](/note_images/Pasted%20image%2020240401181929.png)
[reactjs - React: `'` can be escaped with `'` - Stack Overflow](https://stackoverflow.com/questions/73952513/react-can-be-escaped-with-apos)

1. Disable ESLint in your `next.config.js`

```javascript
module.exports = {
  eslint: {
    // Warning: This allows production builds to successfully complete even if
    // your project has ESLint errors.
    ignoreDuringBuilds: true,
  },
}
```

2. Disable rule in `.eslintrc.*`:

```javascript
"rules": {
  "react/no-unescaped-entities": "off"
}
```

3. Disable in specific file with comment at top of the file

```javascript
1. /* eslint react/no-unescaped-entities */
```