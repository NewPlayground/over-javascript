## 一 H5原生拖放

### 1.1 拖放事件

在H5规范中，拖动元素，将依次触发：`dragstart`，`drag`，`dragend`三个事件。  

当某个元素被拖动到一个有效的放置目标上时，将依次触发：`dragenter`，`dragover`，`dragleave/drop`三个事件。如果拖拽元素离开了目标元素位置，则触发 dragleav 事件，如果放置到了目标元素位置，则触发 drop 事件。  

### 1.2 自定义放置目标

如果拖动元素经过不允许放置的元素，无论用户如何操作，都不会发生 drop 事件。不过，任何元素都可以被设置为放置目标元素。  

设置元素可以放置方式是重写dragenter、dragover事件的默认行为：
```js
var droptarget = document.getElementById("droptarget");
EventUtil.addHandler(droptarget, "dragover", function(event){
    EventUtil.preventDefault(event);
});

EventUtil.addHandler(droptarget, "dragenter", function(event){
    EventUtil.preventDefault(event);
});
```

在Firefox中，若拖拽图像，则页面会转向图像文件，若拖拽文本，则会导致无效URL错误，所以这里需要取消Firefox的drop事件的默认行为，阻止其打开URL：
```js
EventUtil.addHandler(droptarget, "drop", function(event){
    EventUtil.preventDefault(event);
});
```

### 1.3 dataTransfer对象

dataTransfer对象是拖拽事件对象的属性，用于存储数据：
```js
//设置和接收文本数据
event.dataTransfer.setData("text", "some text");
var text = event.dataTransfer.getData("text");
//设置和接收 URL
event.dataTransfer.setData("URL", "http://www.wrox.com/");
var url = event.dataTransfer.getData("URL");
```

### 1.4 可拖动

默认情况下，图像、链接和文本是可以拖动的，也就是说，不用额外编写代码，用户就可以拖动它们。文本只有在被选中的情况下才能拖动，而图像和链接在任何时候都可以拖动。  

HTML5 为所有 HTML 元素规定了一个 draggable 属性，表示元素是否可以拖动。图像和链接的 draggable 属性自动被设置成了 true，而其他元素这个属性的默认值都是 false。  




