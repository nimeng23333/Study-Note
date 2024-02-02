

#### 1.从网页获取图片存入本地文件夹

```js
async function cacheImage(isbn) {
  // 使用 try...catch 语句来处理可能出现的异常
  try {
    const isbnPath = isbn+".jpg"
    const filePath = path.join(cacheDir, isbnPath);
    const ejsPath = "/cache/"+isbnPath;
    // 判断图片是否已经存在于缓存目录中
    if (fs.existsSync(filePath)) {
      return ejsPath;
    } else {
      // 如果图片不存在，从网站下载图片
      const url = cover_API_URL + isbn + "-M.jpg";
      // 使用 axios.get 方法来发送一个 GET 请求，获取图片的响应
      // 使用 await 关键字来等待响应的返回，返回一个 response 对象
      // 使用 responseType 选项来指定响应的数据类型为 "stream"，表示图片的二进制流
      const response = await axios.get(url, { responseType: "stream" });
      // 检查响应的状态码和内容类型
      // 使用 response.status 属性来获取响应的状态码，应该为 200 表示成功
      // 使用 response.headers['content-type'] 属性来获取响应的内容类型，应该以 "image/" 开头表示图片
      if (response.status !== 200 || !response.headers['content-type'].startsWith('image/')) {
        // 如果响应的状态码或者内容类型不符合预期，说明图片无效或者不存在
        // 直接返回空值，结束函数的执行
        return null;
        // 或者抛出一个自定义的异常，结束函数的执行，并将异常传递给调用者
      }
      // 创建一个可写流，用来保存图片
      // 使用 fs.createWriteStream 方法来创建一个可写流，传入 filePath 作为参数，表示图片的保存路径
      const writer = fs.createWriteStream(filePath);
      // 将图片的响应流写入到本地文件中
      // 使用 response.data 属性来获取响应的数据，即图片的二进制流
      // 使用 pipe 方法来将图片的流连接到可写流中，实现数据的传输
      response.data.pipe(writer);
      // 等待图片写入完成
      // 使用 await 关键字来等待一个 promise 的解决或者拒绝
      // 使用 new Promise 构造函数来创建一个 promise 对象
      // 传入一个执行器函数，接受两个参数 resolve 和 reject，分别表示 promise 的解决和拒绝的回调函数
      await new Promise((resolve, reject) => {
        // 使用 writer.on 方法来监听可写流的事件
        // 监听 "finish" 事件，表示图片写入完成，调用 resolve 函数，解决 promise
        writer.on("finish", resolve);
        // 监听 "error" 事件，表示图片写入出错，调用 reject 函数，拒绝 promise
        writer.on("error", reject);
      });
      // 返回图片的相对路径
      return ejsPath;
    }
  } catch (error) {
    console.error(error);
    return null;
  }
}
```

response.data.pipe 是一个方法，用来将一个可读流的数据传输到一个可写流中。在这段代码中，response.data 是一个可读流，表示从网站获取的图片的二进制流。fs.createWriteStream 是一个可写流，表示一个可以写入数据的文件。所以，response.data.pipe (fs.createWriteStream (filePath)) 的作用就是将图片的数据写入到指定的文件中。

promise 是一个对象，用来表示一个异步操作的最终结果。一个 promise 可以有三种状态：pending（等待中），fulfilled（已成功），或者 rejected（已失败）。一个 promise 可以使用 then 方法来添加成功或者失败的回调函数，或者使用 catch 方法来添加失败的回调函数。在这段代码中，new Promise 是一个构造函数，用来创建一个新的 promise 对象。传入一个执行器函数，接受两个参数 resolve 和 reject，分别表示 promise 的解决和拒绝的回调函数。

这段代码中使用 promise 的目的是为了确保图片写入完成后，才返回图片的相对路径。因为 response.data.pipe 和 fs.createWriteStream 都是异步的操作，所以不能直接在它们之后返回路径，否则可能会返回一个不完整的文件。所以，使用 promise 来等待可写流的 “finish” 或者 “error” 事件，然后根据事件的结果，调用 resolve 或者 reject 函数，来解决或者拒绝 promise。使用 await 关键字来等待 promise 的解决或者拒绝，然后再返回路径或者空值


#### 2.容易踩坑！重点！JS-对象（js object属性）终于弄清楚了


2.1点方法与方括号方法总结：
```js

let obj={}
obj.music = [1,2,3]
obj.math = [11,12]
//构建一个obj，有music和math两个属性，每个属性都是一个数组

 var keys = Object.keys(obj); //获取obj的属性名作为一个数组 keys=["music","math"]
 for (var i = 0; i<keys.length ; i++){
	var prop = keys[i];
	console.log(obj.prop);    //错误!因为js对象点方法后面只能跟已知属性名，而不能跟变量，因此输出为undefined，因为obj没有prop这个属性
	
	console.log(obj.music);    //正确!js对象点方法后面跟已知属性名，获得[1,2,3]的结果
	
	console.log(obj.keys[i]);  //错误!因为js对象点方法后面只能跟已知属性名，而不能跟变量，因此输出为undefined
	
	console.log(obj[keys[i]]);  //正确！js对象要传递变量属性名需要用方括号的方法，每次for循环输出相应的数组，得到结果为[1,2,3] 及 [11,12]
  }
```

也就是说获取存储在变量里的key的object对应值：
```js
object[key]
```


2.2创建未初始化
错误代码：
```js
  let categories = [];
  for (var i = 0; i<10 ; i++){
    categories[i].name.push(name[i])
    categories[i].length.push(length[i])
  }
```

在这里我希望构建一个数组categories，它的数据结构是整体是个数组，每个数组内容是一个js对象，都包含name和length两个属性，如下：
```js
[
{name : name1,
length :length1
},

{name : name2,
length :length2
},

{name : name3,
length :length3
},
]
```

在错误的代码里面由于先只创建了一个空的数组，所以在for循环里面categories[i]都是undefined状态，因此向undefined的name和length属性push内容页不可能。
修改方法有两种：

第一种，先创建对象，再将对象赋给categories[i]
```js
let categories = [];
for (var i = 0; i < 10; i++) {
	let obj = {};    // 创建一个空对象
	obj.name = name[i];    // 设置对象的 name 属性
	obj.length = length[i];    // 设置对象的 length 属性
	categories[i] = obj;    // 将对象赋值给 categories 数组中的位置
}
```

第二种，直接往数组里push对象
```js
let categories = [];
for (var i = 0; i < keys.length; i++) {
  // 使用数组的 push 方法，向 categories 数组中添加一个对象
  categories.push({
    name: name[i],
    length: length[i]
  });
}
```


#### 3.变量名作用域，也容易踩坑

错误代码：
```js
app.get("/filter",async(req,res)=>{
  console.log(req.query);
  currentCategory = parseInt(req.query.category);
  const allNotes = await checkNotesAndTags(noteSQL,tagSQL);
  const categories = chekCategories(allNotes);
  if (currentCategory == 0){
    const notes = allNotes;       //if块里定义的传递不出去
    const title = "All notes";
  }else{
    const categoryNoteSQL = noteSQL + ` WHERE category_id = ${currentCategory}`;
    const notes = await checkNotesAndTags(categoryNoteSQL,tagSQL);
    const title = notes[0].category;   //if块里定义的传递不出去
  }
  for (const note of notes){
    if(!note.img){
      note.img = await cacheImage(note.isbn)
    }
  }
  res.render("index.ejs",{notes : notes, total:allNotes.length, categories: categories, title: title, currentCategory: currentCategory});
})
```
在if里的==`const notes`==和 ==`const title`==
都只能作用在if这个块里，而传递不出去，在下面的notes和title都会是undefined

而如果直接把const去掉
```js
app.get("/filter",async(req,res)=>{
  console.log(req.query);
  currentCategory = parseInt(req.query.category);
  const allNotes = await checkNotesAndTags(noteSQL,tagSQL);
  const categories = chekCategories(allNotes);
  if (currentCategory == 0){
    notes = allNotes;     //未定义
    title = "All notes";  
  }else{
    const categoryNoteSQL = noteSQL + ` WHERE category_id = ${currentCategory}`;
    notes = await checkNotesAndTags(categoryNoteSQL,tagSQL);
    title = notes[0].category;   //未定义
  }
  for (const note of notes){
    if(!note.img){
      note.img = await cacheImage(note.isbn)
    }
  }
  res.render("index.ejs",{notes : notes, total:allNotes.length, categories: categories, title: title, currentCategory: currentCategory});
})
```
这里notes和title又都是未定义了

因此需要在if之外，有var或者let来提前定义notes和title，在if里只给他们赋值，这样才能正确传递。


正确代码：
```js
app.get("/filter",async(req,res)=>{
  console.log(req.query);
  currentCategory = parseInt(req.query.category);
  const allNotes = await checkNotesAndTags(noteSQL,tagSQL);
  const categories = chekCategories(allNotes);
  let notes, title;
  if (currentCategory == 0){
    notes = allNotes
    title = "All notes";
  }else{
    const categoryNoteSQL = noteSQL + ` WHERE category_id = ${currentCategory}`;
    notes = await checkNotesAndTags(categoryNoteSQL,tagSQL);
    title = notes[0].category;
  }
  for (const note of notes){
    if(!note.img){
      note.img = await cacheImage(note.isbn)
    }
  }
  res.render("index.ejs",{notes : notes, total:allNotes.length, categories: categories, title: title, currentCategory: currentCategory});

})
```

#### 4.判断是否是数组
Array.isArray()
[判断是否为数组的 JavaScript 方法总结 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/402456307?utm_id=0)

但在if里会报错：if (!tags.isArray()){ ^ TypeError: tags.isArray is not a function


```js
if (tags instanceof Array) {
  // tags is an array, do something
} else {
  // tags is not an array, do something else
}
```

或者：
```js
if (Object.prototype.toString.call(tags) === "[object Array]") {
  // tags is an array, do something
} else {
  // tags is not an array, do something else
}
```

#### 5.检查数组是否包含某个值

- 使用`Array.prototype.includes()`方法，它会返回一个布尔值，表示数组是否包含给定的值。例如：
```js
var arr = ["a", "b", "c"];
console.log(arr.includes("b")); // true
console.log(arr.includes("d")); // false
```

- 使用`Array.prototype.indexOf()`方法，它会返回给定值在数组中的第一个索引，如果不存在则返回-1。例如：

```js
var arr = ["a", "b", "c"];
console.log(arr.indexOf("b")); // 1
console.log(arr.indexOf("d")); // -1
```

- 使用`Array.prototype.find()`方法，它会返回数组中满足给定测试函数的第一个值，如果没有则返回undefined。例如：

```js
var arr = [1, 2, 3, 4];
console.log(arr.find(x => x > 2)); // 3
console.log(arr.find(x => x > 5)); // undefined
```

#### 6.按字符字典顺序排序
```js
arr.sort(function(a,b){return a.localeCompare(b)})
```
  
`a.localeCompare(b)`方法会返回一个数字，表示`a`和`b`的排序关系，使用`return`语句来返回这个数字，以对数组排序


