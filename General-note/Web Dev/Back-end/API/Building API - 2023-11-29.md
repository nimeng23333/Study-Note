---
title: Building API - 2023-11-29
---
API价值：1.数据收集 2.算法或服务 3.简单的接口

REST：（英文：Representational State Transfer，简称REST，直译过来表现层状态转换）是一种软件架构风格、设计风格，而不是标准，只是提供了一组设计原则和约束条件。

Q: 什么API符合 restful？
A: 
1.使用标准的HTTP方法（methods包括get post put patch delete。
2.有一个标准的数据格式来响应，比如使用JSON来响应。
3.客户端与服务器端完全分离，不在同一个系统或文件中，并且能够通过网络互相发送消息。 
4.无状态，即从客户端到服务器的每个请求都应该包含所有信息，服务器不应该在请求之间存储任何类型的客户端、状态或客户端数据；每个请求都可以完成，每个响应也都可以完成，而不需要知道之前发生了什么
5.基于资源的。以资源为中心的api，并以唯一URI标识。URI = Universal Resource Identifier 统一资源标志符，用来标识抽象或物理资源的一个紧凑字符串。URI包括URL和URN，在这里更多时候可能代指URL(统一资源定位符)。RESTful是面向资源的，每种资源可能由一个或多个URI对应，但一个URI只指向一种资源。




制作api的时候服务器端传递出去数据使用`res.json()`

需要注意的是req.params.xx传递出来的是字符串形式，如果是数字需要还原成int

# Get - params查询
```js
app.get('/jokes/:id', (req,res) =>{
  const jokeId = parseInt(req.params.id);  //把paramas里的id转换为int（这里转换的是path paramas，也有query params也就是问号后面的部分
  const fundJoke = jokes.find((joke) => joke.id === jokeId); //在jokes里找到一个id与jokeId相等的元素
  res.json(fundJoke);
})
```

# Get - query查询
```js
app.get('/filer',(req,res) =>{
  const type = req.query.type;
  const filteredJokes = jokes.filter((joke) => joke.jokeType === type);
  res.json(filteredJokes);
})
```

Find()函数与filter()函数的作用类似，但filter的结果是一个数组，每个对象都符合我们指定的条件
查找内容的index用findIndex()函数

# Put
```js
app.put('/jokes/:id',(req,res) =>{
  const id = parseInt(req.params.id);
  const editJoke = {
    id: id,
    jokeText: req.body.jokeText,
    jokeType: req.body.jokeType,
  };

  const jokeIndex = jokes.findIndex((joke)=> joke.id === id);
  jokes[jokeIndex] = editJoke;
  res.json(editJoke);

})
```

# Patch
```js
app.patch('/jokes/:id',(req,res) =>{
  const id = parseInt(req.params.id);
  const existingJoke = jokes.find((joke) => joke.id === id);
  const editJoke = {
    id: id,
    jokeText: req.body.jokeText || existingJoke.jokeText,
    jokeType: req.body.jokeType || existingJoke.jokeType,
  };
  const jokeIndex = jokes.findIndex((joke)=> joke.id === id);
  jokes[jokeIndex] = editJoke;
  res.json(editJoke);
})
```

# Delete
```js
app.delete('/jokes/:id',(req,res) =>{
  const id = parseInt(req.params.id);
  const jokeIndex = jokes.findIndex((joke)=> joke.id === id);
  if (jokeIndex >-1){
    jokes.splice(jokeIndex,1);
    res.sendStatus(200);
  }else{
    res
    .status(404)     //注意这一行没有标点符号
    .json({error:`Joke with id:${id} not found, no jokes were deleted.` });
  }
})
```