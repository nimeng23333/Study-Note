在编写function的时候作为组件，传递property数据


#### level-1 组件card，在index里手动写三个card

Card.jsx
```jsx
import React from "react";

function Card(props) {
  return (
    <div>
      <h2>{props.name}</h2>
      <img src={props.img} alt="avatar_img" />
      <p>{props.tel}</p>
      <p>{props.email}</p>
    </div>
  );
}
export default Card;
```

index.js
```js
import React from "react";
import ReactDOM from "react-dom";
import Card from "./Card.jsx";

ReactDOM.render(
  <div>
    <h1>My Contacts</h1>
    <Card
      name="Beyonce"
      img="https://blackhistorywall.files.wordpress.com/2010/02/picture-device-independent-bitmap-119.jpg"
      tel="+123 456 789"
      emai="b@beyonce.com"
    />
    <Card
      name="Jack Bauer"
      img="https://pbs.twimg.com/profile_images/625247595825246208/X3XLea04_400x400.jpg"
      tel="+987 654 321"
      emai="jack@nowhere.com"
    />
    <Card
      name="Chuck Norris"
      img="https://i.pinimg.com/originals/e3/94/47/e39447de921955826b1e498ccf9a39af.png"
      tel="+918 372 574"
      emai="jgmail@chucknorris.com"
    />
  </div>,
  document.getElementById("root")
);
```

### Level-2组件card，用map函数（类似foreach）将值赋给card

写法1：map里用箭头函数
app.js
```jsx
import React from "react";
import Card from "./Card";
import contacts from "../contacts";

function App() {
  return (
    <div>
      <h1 className="heading">My Contacts</h1>
      {contacts.map((e) => {  //类似foreach
        return (  //不要忘记return，否则不会有输出
          <Card name={e.name} tel={e.phone} img={e.imgURL} email={e.email} />
        );
      })}
    </div>
  );
}

export default App;
```

contact.js
```js
const contacts = [
  {
    name: "Beyonce",
    imgURL:
      "https://blackhistorywall.files.wordpress.com/2010/02/picture-device-independent-bitmap-119.jpg",
    phone: "+123 456 789",
    email: "b@beyonce.com",
  },
  {
    name: "Jack Bauer",
    imgURL:
      "https://pbs.twimg.com/profile_images/625247595825246208/X3XLea04_400x400.jpg",
    phone: "+987 654 321",
    email: "jack@nowhere.com",
  },
  {
    name: "Chuck Norris",
    imgURL:
      "https://i.pinimg.com/originals/e3/94/47/e39447de921955826b1e498ccf9a39af.png",
    phone: "+918 372 574",
    email: "gmail@chucknorris.com",
  },
];

export default contacts;
```

在使用forEach方法时，没有返回任何值。forEach方法只是对数组的每个元素执行一个函数，但不会改变原数组，也不会返回新的数组。如果你想在JSX中渲染一个数组的元素，你应该使用map方法，它会返回一个新的数组，每个元素都是你指定的JSX元素。
[How to use the forEach() method in React | bobbyhadz](https://bobbyhadz.com/blog/react-foreach)
[javascript - How to use forEach in react js - Stack Overflow](https://stackoverflow.com/questions/68199127/how-to-use-foreach-in-react-js)
错误代码：
app.js
```jsx
{contacts.forEach((e) => {<Card name={e.name} tel={e.phone} img={e.imgURL} email={e.email} />})}
```

写法2：额外创建一个函数，在map函数里调用
app.jsx
```jsx
import React from "react";
import Card from "./Card";
import contacts from "../contacts";

function Contact(contact) {
  return (
    <Card
      key={contact.id} //key是必须且唯一的，确保了渲染顺序，并且key不能作为一个prop传递，如果要传递id需要额外再加一个prop进行id传递
      name={contact.name}
      img={contact.imgURL}
      tel={contact.phone}
      email={contact.email}
    />
  );
}

function App() {
  return (
    <div>
      <h1 className="heading">My Contacts</h1>
      {contacts.map(Contact)}
    </div>
  );
}
export default App;
```

>为map函数创建的function里的key是必须且唯一的，确保了渲染顺序，并且key不能作为一个prop传递，如果要传递id需要额外再加一个prop进行id传递

