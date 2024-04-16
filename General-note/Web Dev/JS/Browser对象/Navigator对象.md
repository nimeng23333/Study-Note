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