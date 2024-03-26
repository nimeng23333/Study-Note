---
title: Day21 - Drag & Drop
---

拖拽事件
拖的东西有两个监听 dragstart和dragend（注意都没有大写！！）
拖东西后拖到的地方的监听有dragover、dragenter、dragleave及drop


```js
const fills = document.querySelectorAll(".fill");
const empties = document.querySelectorAll(".empty");

var fillClass;
var key = 1;

for(let i=0; i<fills.length;i++){
    fills[i].addEventListener("dragstart", dragStart);
    fills[i].addEventListener("dragend",dragEnd);
}

for(const empty of empties){
    empty.addEventListener("dragover",dragOver);
    empty.addEventListener("dragenter",dragEnter);
    empty.addEventListener("dragleave",dragLeave);
    empty.addEventListener("drop",dragDrop);
    empty.setAttribute("dragFrom",false);
}

function dragStart(){
    fillClass = this.className;
    this.className += " draged";
    key = this.getAttribute("key");
    this.parentElement.setAttribute("dragFrom",true);
    setTimeout(()=>this.className="invisible",0);
}


function dragEnd(){
    this.className = fillClass;
}

function dragOver(e){
    e.preventDefault();
}

function dragEnter(e){
    e.preventDefault();
    this.className +=" hovered";
}

function dragLeave(e){
    e.preventDefault();
    this.className = "empty";
}

function dragDrop(){
    this.className = 'empty';
    if(this.children.length == 0){
        fills[key-1].className = fillClass;
        this.append(fills[key-1]);
    }else{
        const formerFill = this.children[0];
        for(const empty of empties){
            if(empty.getAttribute("dragFrom") === "true"){
                empty.append(formerFill);
                empty.setAttribute("dragFrom",false);
            }
        }
        this.append(fills[key-1]
    }
}
```

className会返回一个string，包含所有class
classList则会返回一个array