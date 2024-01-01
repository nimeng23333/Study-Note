
数组扩散
```js
const array1 = ["a", "b", "c"];
const array2 = ["d", "e", "f", ...array1];
const array3 = ["g", ...array1, "h", "i"];

console.log(array2) //"d", "e", "f", "a", "b", "c"
console.log(array3) //"g", "a", "b", "c", "h", "i"
```
对象扩散会让对象里的属性值插入到新的对象里
```js
const fullName = {
	fName : "James",
	lName : "Bond"
};

const user1 = {
	...fullName,
	id: 1,
	username:"007"
}
const user2 = {
	fullName,
	id: 1,
	username:"007"
}

console.log(user1) //{fName : "James", lName : "Bond", id: 1, username:"007"}
console.log(user2) //{fullName:{fName : "James", lName : "Bond"}, id: 1, username:"007"}
```

因此用spread operator可以将多值存储简化：
原代码：
```jsx
import React, { useState } from "react";

function App() {
  const [fullName, setFullName] = useState({ fName: "", lName: "" });
 
  function nameChange(event) {
	const {value : newValue, name : newName} = event.target;  //一定要在setFullName()之外用event.target！！！
	setFullName(prevValue =>{
		if ({newName == "fName") {
			return{
				fName : event.newValue,
				lName : prevValue.lName
			  }
		}else{
			return{
				fName : newValue,
				lName : prevValue.lName
		  }
		}
	})
  }
  return (
    <div className="container">
      <h1>
        Hello {fullName.fName} {fullName.lName}
      </h1>
      <form>
        <input
          name="fName"
          placeholder="First Name"
          onChange={nameChange}
          value={fullName.fName}
        />
        <input
          name="lName"
          placeholder="Last Name"
          onChange={nameChange}
          value={fullName.lName}
        />
        <button>Submit</button>
      </form>
    </div>
  );
}

export default App;
```

简化：
```jsx
import React, { useState } from "react";

function App() {
  const [fullName, setFullName] = useState({ fName: "", lName: "" });
 
  function nameChange(event) {
	const {value : newValue, name : newName} = event.target;  //一定要在setFullName()之外用event.target！！！
	setFullName(prevValue =>{
		return {
		...prevValue,
		[newName] : newValue //这个在之前的js对象点方法与方括号方法里总结过，要想对对象的属性值进行传递需要用中括号
		}
	})
  }
  return (
    <div className="container">
      <h1>
        Hello {fullName.fName} {fullName.lName}
      </h1>
      <form>
        <input
          name="fName"
          placeholder="First Name"
          onChange={nameChange}
          value={fullName.fName}
        />
        <input
          name="lName"
          placeholder="Last Name"
          onChange={nameChange}
          value={fullName.lName}
        />
        <button>Submit</button>
      </form>
    </div>
  );
}

export default App;
```

甚至可以进一步简化，因为只返回一个对象，可以不用return
```js
  function nameChange(event) {
	const {value : newValue, name : newName} = event.target;  //一定要在setFullName()之外用event.target！！！
	setFullName(prevValue =>({...prevValue,[newName] : newValue }))   
	//这个在之前的js对象点方法与方括号方法里总结过，要想对对象的属性值进行传递需要用中括号
  }
```


一个待办列表的例子：
```jsx
import React, { useState } from "react";

function App() {
  const [item, setItem] = useState("");
  const [listItem, setListItem] = useState([]);
  
  function itemInput(event) {
    const newItem = event.target.value;
    setItem(newItem);
  }
  function addItem() {
    setListItem([...listItem, item]);   //无法用push来改变数组，数组的改变也需要用useState
    setItem("");
  }
  
  return (
    <div className="container">
      <div className="heading">
        <h1>To-Do List</h1>
      </div>
      <div className="form">
        <input type="text" onChange={itemInput} value={item} />
        <button onClick={addItem}>
          <span>Add</span>
        </button>
      </div>
      <div>
        <ul>
          {listItem.map((item) => {
            return <li>{item}</li>;
          })}
        </ul>
      </div>
    </div>
  );
}

export default App;
```
![[Pasted image 20231224000634.png]]