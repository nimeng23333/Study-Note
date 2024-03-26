---
title: Day15 - Incrementing counter
---

### JS - 字符串转数字

```js
const number = +"1000";
const number = Number("1000");
const number = ParseInt("1000");
```

### JS - 优化代码

傻逼写法：
```js
const counters = document.querySelectorAll(".counter");
const times = 200

counters.forEach((counter) =>{
    const target = +counter.getAttribute("data-target");
    let currentCount = 0;
    const increment = Math.ceil(target / times);
    function updateCounter(currentCount,increment,target){
        if(currentCount < target){
            setTimeout(()=>{
                counter.innerHTML = currentCount;
                currentCount +=increment;
                updateCounter(currentCount,increment,target);
            },1)
        }else{
            counter.innerHTML = target;
        }
    }
    updateCounter(currentCount,increment,target);
})
```

优化写法：
```js
const counters = document.querySelectorAll(".counter");
const times = 200

counters.forEach((counter) =>{
    const target = +counter.getAttribute("data-target");
    let currentCount = 0;
    const increment = Math.ceil(target / times);
    const updateCounter = () =>{  //用const 替代function，在使用的时候可以不传参
        if(currentCount < target){
            setTimeout(()=>{
                counter.innerHTML = currentCount;
                currentCount +=increment;
                updateCounter();
            },1)
        }else{
            counter.innerHTML = target;
        }
    }
    updateCounter();
})
```

用const functionName = ()=>{} 替代function functionName(){}，避免传参