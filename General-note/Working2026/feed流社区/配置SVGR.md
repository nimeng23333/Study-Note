---
title: 配置SVGR
---
1. 安装
    

`npm i @svgr/webpack -D`

2. nextjs配置
    

```JavaScript
/** @type {import('next').NextConfig} */
const nextConfig = {
  reactStrictMode: true,
  webpack(config) {
    // 只对 ?react 后缀的 SVG 文件转换为 React 组件
    config.module.rules.push({
      test: /\.svg$/,
      resourceQuery: /react/,
      use: [
        {
          loader: '@svgr/webpack',
          options: {
            // 移除固定的 width 和 height 属性，让 className 可以控制尺寸
            svgoConfig: {
              plugins: [
                {
                  name: 'removeViewBox',
                  active: false, // 保留 viewBox，这对响应式很重要
                },
                {
                  name: 'removeDimensions',
                  active: true, // 移除 width 和 height 属性
                },
              ],
            },
            // 确保 className 能正确传递
            typescript: true,
          },
        },
      ],
    });
    return config;
  },
}

module.exports = nextConfig
```

3. Svg 配置
    

根目录`svg.d.ts`

```TypeScript
declare module '*.svg?react' {
  import React from 'react';
  const ReactComponent: React.FC<React.SVGProps<SVGSVGElement>>;
  export default ReactComponent;
}
```

## 使用

1. 在icon文件夹里保存svg格式文件
    

注意 `<path>` 的fill参数 `fill="currentColor"`，不然dark mode会有问题

```XML
<svg width="16" height="16" viewBox="0 0 16 16" fill="none" xmlns="http://www.w3.org/2000/svg">
<path d="M12.6663 1.33334C13.7709 1.33334 14.6663 2.22877 14.6663 3.33334V8.66635H13.4671V3.33334C13.4671 2.89152 13.1082 2.53354 12.6663 2.53354H3.33333C2.89151 2.53354 2.53353 2.89152 2.53353 3.33334V6.67026C2.57772 6.66938 2.62196 6.66636 2.66634 6.66635C6.71643 6.66635 10.0003 9.95025 10.0003 14.0003C10.0003 14.2249 9.98884 14.447 9.96908 14.6664H3.33333L3.23079 14.6644C2.20821 14.6126 1.38824 13.7924 1.33626 12.7699L1.33333 12.6664V3.33334C1.33333 2.22877 2.22876 1.33334 3.33333 1.33334H12.6663ZM12.8275 10.0365C12.878 9.94427 13.0102 9.94445 13.0609 10.0365L13.8118 11.4095L15.1849 12.1605C15.2769 12.2112 15.2771 12.3433 15.1849 12.3939L13.8118 13.1449L13.0609 14.5169C13.0102 14.6093 12.878 14.6094 12.8275 14.5169L12.0765 13.1449L10.7044 12.3939C10.612 12.3433 10.6121 12.2111 10.7044 12.1605L12.0765 11.4095L12.8275 10.0365ZM2.53353 7.86948V12.6664C2.53353 13.1082 2.89151 13.4671 3.33333 13.4671H8.77572C8.50542 10.3295 5.87399 7.86655 2.66634 7.86655C2.62195 7.86656 2.5777 7.86849 2.53353 7.86948ZM10.6663 4.00034C11.4027 4.00034 12.0003 4.59696 12.0003 5.33334C12.0003 6.06972 11.4027 6.66635 10.6663 6.66635C9.93011 6.66618 9.33333 6.06961 9.33333 5.33334C9.33333 4.59707 9.93011 4.00051 10.6663 4.00034Z" fill="currentColor"/>
</svg>
```

2. Import svg文件后带?react
    

`import PublishImageIcon from './icons-new/publish-image.svg?react';`

3. 正常使用icon `PublishImageIcon`
    

  

如果打开是图片

右键文件名，reopen选择text