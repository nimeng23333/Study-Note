---
title: Component tree - 2023-12-26
---

[Managing a Component Tree (forked) - CodeSandbox](https://codesandbox.io/p/sandbox/managing-a-component-tree-forked-y4hd8s?file=%2Fsrc%2Fcomponents%2FToDoItem.jsx%3A7%2C25)
用useState/ ternary operator/ inline style完成的点击划线

```jsx
import React, { useState } from "react";

function ToDoItem(props) {
  const [isItemCheck, setCheck] = useState(false);
  function handleClick() {
    setCheck((prevValue) => {
      return !prevValue;  //只涉及到true false的不要傻傻用if，用return !
    });
  }
  return (
    <li
      onClick={handleClick}
      style={{ textDecoration: isItemCheck ? "line-through" : "none" }} //三元操作+inline style
    >
      {props.value}
    </li>
  );
}

export default ToDoItem;
```

==注意！！==传递函数不能加括号，调用函数才加括号，因此如果在想传递的地方加了括号会在渲染的时候就调用
比如上文的onClick={handleClick}，这里是传递handleClick这个函数，只有在点击的时候才会调用，而如果加了括号将会在初始渲染就调用

父组件的props可以传递函数

例子：
app.jsx
```jsx
import React, { useState } from "react";
import ToDoItem from "./ToDoItem";

function App() {
  const [inputText, setInputText] = useState("");
  const [items, setItems] = useState([]);

  function handleChange(event) {
    const newValue = event.target.value;
    setInputText(newValue);
  }

  function addItem() {
    setItems((prevItems) => {
      return [...prevItems, inputText];
    });
    setInputText("");
  }

  function deleteItem(itemId) { //括号内是由子组件传递过来的参数
    setItems((prevItems) => {
      return prevItems.filter(function (item) {
        item.id !== itemId;
      });
    });
  }
  
  return (
    <div className="container">
      <div className="heading">
        <h1>To-Do List</h1>
      </div>
      <div className="form">
        <input onChange={handleChange} type="text" value={inputText} />
        <button onClick={addItem}>
          <span>Add</span>
        </button>
      </div>
      <div>
        <ul>
          {items.map((todoItem, idx) => (
            <ToDoItem
              key={idx}
              id={idx}
              value={todoItem}
              onChecked={deleteItem} //将deleteItem通过onChecked传递到子组件内
            />
          ))}
        </ul>
      </div>
    </div>
  );
}

export default App;
```

ToDoItem.jsx
```jsx
import React from "react";

function ToDoItem(props) {
  return (
    <li
      onClick={() => {
        props.onChecked(props.id); //这里一定要用匿名箭头函数，并且在函数里面进行调用，而不能写成onClick={props.onChecked(props.id)},写成有括号的参数会在渲染的时候就调用这个函数
      }}
    >
      {props.value}
    </li>
  );
}

export default ToDoItem;
```

