---
title: Navigator对象
---
Navigator可以获取浏览器信息，包括appName、appVersion、geolocation、language等

`navigator.geolocation`返回一个geolocation对象

eg：
```js
if (navigator.geolocation) {
  navigator.geolocation.getCurrentPosition(showPosition);
} else {
  document.getElementById("demo").innerHTML =
  "Geolocation is not supported by this browser.";
}

function showPosition(position) {
  document.getElementById("demo").innerHTML =
  "Latitude: " + position.coords.latitude +
  "Longitude: " + position.coords.longitude;
}
```


```jsx
	navigator.geolocation.getCurrentPosition((position)=>
	{console.log(position.coords.latitude, position.coords.longitude)
	});
```
接受一个函数，该函数将在将来一旦位置被提取的时候立刻由浏览器执行。
这里不能用async await只需要一个回调函数