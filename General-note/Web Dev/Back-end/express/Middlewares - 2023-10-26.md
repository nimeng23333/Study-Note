---
title: Middlewares - 2023-10-26
---
中间件用来处理客户端与服务器端之间的内容，
一般有四个功能：预处理、日志、报错、秘钥权限

## Body Parser - 表单预处理中间件
可以将html提交的内容传递给后端

HTML文件：
```
  <form action="/submit" method="POST">
    <label for="street">Street Name:</label>
    <input type="text" name="street" required>
    <label for="pet">Pet Name:</label>
    <input type="text" name="pet" required>
    <input type="submit" value="Submit">
  </form>
```
![[../../../note_images/Pasted image 20231026154726.png]]

JS文件：
```
import bodyParser from "body-parser";
import express from "express";
import { dirname } from "path";
import { fileURLToPath } from "url";

const __dirname = dirname(fileURLToPath(import.meta.url));
  
const app = express();
const port = 3000;
  
app.use(bodyParser.urlencoded({extended:true}));
  
app.get("/", (req, res) => {
  res.sendFile(__dirname + "/public/index.html");
});
  
app.post("/submit", (req, res) =>{
  console.log(req.body);
});
  
app.listen(port, () => {
  console.log(`Listening on port ${port}`);
});
```

## Morgan - 记录服务器请求

客户端请求后morgan能记录请求的时间、类型、HTTP代码等等

注意：morgan代码一定要在回应代码之前，否则无法记录，注意顺序

```
import express from "express";
import morgan from "morgan";

const app = express();
const port = 3000;

app.use(morgan('tiny'));
  
app.get("/", (req, res) => {
  res.send("Hello");
});
```

## 自制中间件

```
function 名字(req, res, next){
  //功能
  next();
}
```

类似password check之类的最好可以写成中间件，而不要做成post以后的功能