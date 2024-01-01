### 资源 - 音频下载

[20,000+ Free Background Music MP3 Download - Pixabay](https://pixabay.com/music/search/theme/background%20music/)

### JS - 存储循环里的变量到全局的思路
```js
const container = document.querySelector("#audio-container");
const audios = document.querySelectorAll("audio");
let playControlAll = false;
let playControls = {};

audios.forEach(audio =>{
    playControls[audio.getAttribute("id")] = false;
    const btn = document.createElement("button");
    btn.classList.add("btn");
    const text = audio.getAttribute("id").replace(/\-/g , ' ')
    btn.innerText = text.slice(0,1).toUpperCase()+text.slice(1).toLowerCase();
    container.appendChild(btn);
    btn.addEventListener("click",()=>{
        if(!playControls[audio.getAttribute("id")] && !playControlAll){
            audio.play();
            playControls[audio.getAttribute("id")]=true;
            playControlAll=true;
        }else if(playControlAll && !playControls[audio.getAttribute("id")]){
            stopPlay();
            audio.play();
            playControls[audio.getAttribute("id")]=true;
        }else if(playControlAll && playControls[audio.getAttribute("id")]){
            stopPlay();
            playControls[audio.getAttribute("id")]=false;
            playControlAll=false;
        }
    })
})

function stopPlay(){
    audios.forEach(audio =>{
        audio.pause();
        audio.currentTime=0;
        playControls[audio.getAttribute("id")] = false;
    })
}
```

重点需要注意的错误思路：
在每一个audio里都需要一个是否已播放的flag，而这个flag一开始我写成了在循环里，`let play=false`，而这样写的play不能带入下一个stopPlay()的函数里，因为作用域不是全局
因此需要在全局里存储这个变量，而存储多个变量的方法可以用object的属性+值来做到

因此是全局`let playControls = {};`，在forEach循环里添加属性和值`            playControls[audio.getAttribute("id")]=true;`

### 音频播放

```js
audio.play();   //播放
audio.pause();  //暂停
audio.currentTime=0;  //让播放时间归零

audio.addEventListener("ended", () => {
	playControls[audio.getAttribute("id")] = false;
	playControlAll = false;
});  //监控音频是否播放完毕
```
