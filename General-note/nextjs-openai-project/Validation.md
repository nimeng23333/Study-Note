---
title: Validation
---

对网站权限进行验证：
除了之前的验证用户，在new页面验证是否有token，没有就redirect到topup页面

在new页面验证是否有topic和keywords的输入，没有则不允许提交，且不允许超过200字符，需要注意这里是在前端验证，而需要防止有人绕过前端，对后端进行攻击，因此需要在后端也写验证

前端：
new.js
```jsx
<textarea maxLength={200} />
<button disabled={!topic.trim() || !keywords.trim()}>
```
trim()将前后空格去掉，防止只有空格的输入

后端：
generatePost.js
```js
if(!topic || !keywords){
	res.status(422);
	return;
}

if(topic.length >200 || keywords.length >200){
	res.status(422);
	return;
}
```