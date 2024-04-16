---
title: 前端JS部分
---

#### 1.on('click')绑定函数
错误代码：
```js
    $("#newCategory").on('click',handler("#newCategory"));

    function handler(lable) {
        $(lable).setAttribute("hidden", true);
        $(lable + "-input").removeAttribute("hidden");
        $(lable + "-done").removeAttribute("hidden");
        $(lable + "-close").removeAttribute("hidden");
    }
```

要传递参数的函数这里会导致handler函数在最开始执行一次
修改如下：
```js
$("#newCategory").on('click',() => handler("#newCategory"))
```

其他可以的用法：
不带参数的函数
```js
    $("input[type = 'checkbox']").on('click',showFilter); //注意回调函数不加括号，否则只会在一开始调用而不是每次点击
```
```js
    $('body').on('click', 'a[href^="#"]', function(event) {
    代码实现
    });
```

#### 2.jQuery与原生js getElement混用

错误代码：
```js
$(lable).setAttribute("hidden", true);
```

setAttribute和removeAttribute都是原生js，attr才是jquery

正确代码：

原生js
```js
document.getElementById("title" + id).setAttribute("hidden", true)
document.getElementById("edit" + id).setAttribute("hidden", true)
document.getElementById("done" + id).removeAttribute("hidden")
```

jquery
```js
$(lable).attr("hidden", true);
```

#### 3.事件监听
```js
input.on('input', checkInput); //检测input输入变化

input.on('blur', function(){xxx}); //检测input失去焦点

button.on('click', function () {xxx});  //检测button被点击

input.on('keyup', function() {xxx});  //检测input上的键盘事件
```

#### 4.JQuery选择器

```js
$("[id^='tag-']")  //选择所有以tag-开头的id的元素
```

#### 5.function(){} 与()=>{}的区别及this的作用域

- function() 是一种函数声明，可以有自己的名称，也可以是匿名的。()=> 是一种函数表达式，也叫做箭头函数，它是一种简洁的写法，可以省略function关键字和return语句。
- function() 的this指向是动态的，取决于函数的调用方式。()=> 的this指向是静态的，继承自定义时的上下文。
- function() 可以作为构造函数，使用new关键字创建对象。()=> 不能作为构造函数，使用new关键字会报错。
- function() 可以使用arguments对象获取函数的参数。()=> 不能使用arguments对象，但可以使用剩余参数（…args）代替。

```javascript
$("[id^='tag-']").on("click", ()=> {
  console.log(this); // undefined  //当使用箭头函数时，this的值是继承自外层作用域的，而不是绑定到当前的元素上。所以，需要使用一个普通的函数表达式，或者使用event.target来代替this
});

$("[id^='tag-']").on("click", function() {
  console.log(this.id); // this指向当前元素
});

// 或者使用event.target
$("[id^='tag-']").on("click", (event) => {
  console.log(event.target.id); // event.target指向当前元素
});
```

#### 6.ajax
```js
$.ajax({
	url: "/endpoint",
	type: "POST",
	data: {input: ids},
	dataType: "json",
	success: function(data) {
	},
	error: function(xhr, status, error) {
	  console.error(error);
	}
});
```


#### 7.从嵌套对象提取值

```js
const ArchProjectsMenu = {
    work:[
        {slug:"sichuan-mingrenguan",title:"四川名人馆",src:"/images/mingrenguan/东南鸟瞰.jpg"},
        {slug:"chengdusanzhong",title:"成都三中新建教学楼工程",src:"/images/chegndusanzhong/人视点透视图.jpg"},
    ],
    study:[
        {slug:"furniture-store",title:"FURNITURE STORE AS INSTAGRAM MUSEUM (ARCHITECTURE AND MIXED REALITY)",src:"/images/furniture_store/封面.png"},
        {slug:"john-ferraro-building-facade-reconstruction",title:"JOHN FERRARO BUILDING FACADE RECONSTRUCTION (DESIGN BASED ON MACHINE LEARNING)",src:"/images/chegndusanzhong/人视点透视图.jpg"},
        {slug:"vigor-in-library",title:"VIGOR IN LIBRARY(ARCHITECTURE)",src:"/images/library/目录.jpg"},
        {slug:"arch",title:"ARCH (ARCHITECTURE)",src:"/images/arch/目录.jpg"},
        {slug:"zen",title:"ZEN (ARCHITECTURE)",src:"/images/zen/目录.jpg"},
        {slug:"paradox",title:"PARADOX (INTERACTIVE DEVICE DESIGN)",src:"/images/paradox/目录.png"},
        {slug:"art-works",title:"ART WORKS",src:"/images/art_works/目录.jpg"},
    ],
}
const slugs = Object.values(ArchProjectsMenu).reduce((acc, category) => acc.concat(category.map(project => project.slug)), [])
```

这段JavaScript代码是一个函数链，用于从`ArchProjectsMenu`对象中提取所有`slug`属性并将它们组合成一个数组。下面是这段代码的逐步解释：

1. `Object.values(ArchProjectsMenu)`: 这部分代码获取`ArchProjectsMenu`对象中所有值的数组。在这个例子中，它会返回一个包含两个数组（`work`和`study`）的数组。
    
2. `.reduce((acc, category) => acc.concat(category.map(project => project.slug)), [])`: 这是一个`reduce`函数，它用于将多个值合并成一个值。在这个例子中，它用于将多个数组合并成一个数组。
    
    - `acc`（累加器）: 初始值为空数组`[]`，它将用于存储所有的`slug`。
    - `category`: 是从`ArchProjectsMenu`中提取的每个数组（`work`或`study`）。
    - `acc.concat(...)`: 这部分代码将当前`category`中的每个项目的`slug`添加到累加器数组`acc`中。
    - `category.map(project => project.slug)`: 这是一个`map`函数，它用于从当前`category`中的每个项目对象中提取`slug`属性。

整个`reduce`函数遍历`ArchProjectsMenu`对象的每个`category`，使用`map`函数从每个项目中提取`slug`，然后使用`concat`方法将它们添加到一个新数组中。最终，这个新数组包含了所有项目的`slug`，并作为最终结果返回。


#### 8. 重组object结构

```js
const projects = [
    {slug:"test1"},
    {slug:"folder/test2"},
    {slug:"folder/test3"},
    {slug:"folder1/folder2/test4"},
    {slug:"folder1/folder3/test5"},
    {slug:"folder1/folder3/test6"},
]
```

重组为
```js
sorted = { 
test1: [ { slug: 'test1', path: 'test1' } ], 
folder: { test2: [{ slug: 'test2', path: 'folder/test2' }], 
		  test3: [{ slug: 'test3', path: 'folder/test3' } ] }, 
folder1: { 
		folder2: 
				{ test3: [{ slug: 'test4', path: 'folder1/folder2/test4' }] }, 
		folder3: 
				{ test4: [{ slug: 'test5', path: 'folder1/folder3/test5' }], 
				  test5: [{ slug: 'test6', path: 'folder1/folder3/test6' }] } }
```

```javascript
function sortProjects(projects) {
  const result = {};

  projects.forEach(project => {
    const layers = project.slug.split('/');
    let currentLevel = result;

    layers.forEach((layer, index) => {
      if (index === layers.length - 1) {
        if (!currentLevel[layer]) {
          currentLevel[layer] = [];
        }
        currentLevel[layer].push({ slug: layer });
      } else {
        if (!currentLevel[layer]) {
          currentLevel[layer] = {};
        }
        currentLevel = currentLevel[layer];
      }
    });
  });

  return result;
}

const sortedProjects = sortProjects(projects);
console.log(sortedProjects);
```

`sortProjects` 函数的作用是将原始的 `projects` 数组转换成一个嵌套的对象结构。它首先创建一个空对象 `result`，然后遍历 `projects` 数组中的每个项目。对于每个项目，它会：
- 使用 `split('/')` 方法将 `slug` 字符串分割成多个部分，存储在 `layers` 数组中。
- 定义一个 `currentLevel` 变量，它将用来跟踪我们当前在结果对象中的位置。
- 遍历 `layers` 数组，对于数组中的每个元素（除了最后一个），如果在当前层级的对象中不存在该元素作为键的对象，则创建一个新的空对象。然后将 `currentLevel` 更新为这个新对象，这样我们就可以在下一次迭代中向下钻取到更深的层级。
- 对于 `layers` 数组中的最后一个元素，我们检查在当前层级的对象中是否存在一个数组。如果不存在，我们创建一个新数组，并将一个包含 `slug` 的新对象推入这个数组中。


```jsx
export const ListItem = ({items})=>{
    const keys = Object.keys(items);

    return(
        <>
        { keys.map((key)=>{
            const isFolder = typeof items[key] === 'object' && !Array.isArray(items[key]);
            return(
            <li className={`bg-zinc-900 rounded-md px-4 py-2`}>
                {isFolder ? (
                    <Fragment>
                        <button className="flex items-center">            
                            <ChevronDown size={15} className="mr-1"/>
                            {key}
                        </button>
                      <ul><ListItem items={items[key]}/></ul>
                    </Fragment>
                  ) : (
                    <Link href={`/${items[key][0].path}`}>
                      {items[key][0].slug}
                    </Link>
                  )}
            </li>
            )
        })}
        </>
    )
}

export const SideBar = () =>{
    const active = "test1"
    return(
        <aside className="overflow-auto border-r border-r-zinc-800 ">
            <ul className="flex flex-col gap-1 mr-5">
                <ListItem items={sortedProjects} />
            </ul>
        </aside>
    )
}
```


### 9.获得对象的所有键值

`Object.keys(items)`，返回一个数组


### 10.排序算法

系统文件夹使用的自然排序，是一种按照人类阅读习惯对数字、字母等字符进行排序。而通常情况下js的排序不是自然排序
#### 自然排序
```javascript
files.sort((a, b) => a.localeCompare(b, undefined, {numeric: true, sensitivity: 'base'}));
```

`localeCompare()`方法的第二个参数是`undefined`，这表示使用当前的语言环境。第三个参数是一个选项对象，其中`numeric: true`表示启用数字感知的排序，`sensitivity: 'base'`表示只关注基本的字符差异，忽略大小写和重音符号等差异。

#### 文件夹优先情况下的自然排序


```jsx
const slugs = allNotes.map((note) => {return {slug:note.slug}})
                        .sort((a, b) => {
                            // 检查是否包含 '/'
                            let aHasSlash = a.slug.includes('/');
                            let bHasSlash = b.slug.includes('/');
                            // 如果 a 和 b 都有或都没有 '/', 则按照自然排序
                            if ((aHasSlash && bHasSlash) || (!aHasSlash && !bHasSlash)) {
                              return a.slug.localeCompare(b.slug, undefined, {numeric: true, sensitivity: 'base'});
                            }
                            // 如果 a 有 '/' 而 b 没有, 则 a 排在前面
                            if (aHasSlash && !bHasSlash) {
                              return -1;
                            }
                            // 如果 b 有 '/' 而 a 没有, 则 b 排在前面
                            if (!aHasSlash && bHasSlash) {
                              return 1;
                            }
                          });
```

排序前

![](/note_images/Pasted%20image%2020240326170936.png)


排序后

![](note_images/Pasted%20image%2020240326170648.png)