一个异步 事件驱使  的js 运行环境 runtime

输入node进入node
ctrl + c 或 .exit 退出node

## Native Modules

#### 文件系统 file system fs

node.js能访问本地文件

```
const fs = require("fs");

fs.writeFile("message.txt", "Hello from nodeJS!", (err) => {if (err) throw err;
console.log("The file has been saved!")})
```
第一个是文件名，第二个是文件内容，第三个是回调函数（当发生错误的时候抛出错误，否则consolelog

## NPM
node package manager
外部包

把自己的东西做成npm包
```
npm init
```
init以后会出现一个package.json的配置文件

安装新npm包
```
npm i xxxx xxx xxxx
```

在CJS模式下，导入文件用require `const fs = require("fs");`
在ESM模式下，导入文件用import

默认是CJS模式，要想修改模式需要在package.json文件里main下面添加`"type":"module",`

```
import xxx from "module-name";
```

`let data;`可以直接构造data，同时还为空，如果没有数据locals.data也是判定为空
`let data ={};`则不行locals.data判定存在
