
HTML的表单中的button会默认进行提交，尤其是当button的type=submit的时候
当不想触发默认提交的时候需要写function event.preventDefault()

```jsx
import React, { useState } from "react";

function App() {
  const [heading, setHeading] = useState("");
  function buttonClick(event) {
    setHeading(name);
    event.preventDefault();  //阻止默认的提交表单操作
  }
  const [name, setName] = useState("");
  function textInput(event) {
    setName(event.target.value);
  }
  return (
    <div className="container">
      <form onSubmit={buttonClick}>  //将button提交的函数写在form上
        <h1>Hello {heading}</h1>
        <input
          type="text"
          placeholder="What's your name?"
          value={name}
          onChange={textInput}
        />
        <button type="Submit">Submit</button>
      </form>
    </div>
  );
}

export default App;
```