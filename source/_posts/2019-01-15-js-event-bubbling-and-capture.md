---
layout: post
title:  "JavaScript事件冒泡和捕获"
date:   2019-01-15
tags: JavaScript
---

# JavaScript事件冒泡和捕获
- 事件捕获指的是从document到触发事件的那个节点，即自上而下的去触发事件。
- 事件冒泡是自下而上的去触发事件。
- 绑定事件方法的第三个参数，就是控制事件触发顺序是否为事件捕获。true,事件捕获；false,事件冒泡。默认false,即事件冒泡。
## 例子
```html
<div id='id1'>
    <div id="id2">
        <button id='id3'>id3 按钮</button>
    </div>
</div>
```

```js
var id1 = document.querySelector('#id1')
var id2 = document.querySelector('#id2')
var id3 = document.querySelector('#id3')

// 事件冒泡
id1.addEventListener('click', function(event){
    console.log('click id1')
})
id2.addEventListener('click', function(event){
    console.log('click id2')
})
id3.addEventListener('click', function(event){
    console.log('click id3')
    // 取消冒泡事件
    // event.cancelBubble = true
})
// 点击id3输出
// click id3 
// click id2 
// click id1 

// 事件捕获
id1.addEventListener('click', function(event){
    console.log('capture click id1')
}, true)
id2.addEventListener('click', function(event){
    console.log('capture click id2')
}, true)
id3.addEventListener('click', function(event){
    console.log('capture click id3')
}, true)
// 点击id3输出
// capture click id1
// capture click id2
// capture click id3

```


