## 一 Document类型的节点

Document是HTML页面对象HTMLDocument的实例，挂载在window对象上，作为一个全局对象，其nodeType为9，nodeName为`"#document"`，该对象的第一个子节点便是html标签！  

`document.doctype`可以访问`<!DOCTYPE>`标签，但是该节点在不同浏览器中表现不同：
- IE8：会将该子节点错误的解释为一个注释并把它当作 Comment节点，document.doctype 的值始终为 null。
- IE9+/Firefox：被作为文档的第一个子节点
- Chrome/Safari：不会被作为文档的子节点

## 二 Element类型

### 2.1 元素类型

即HTML中的元素：
```js
<div id="myDiv" class="bd" title="Body text" lang="en" dir="ltr"></div>

var div = document.getElementById("myDiv");
alert(div.id); //"myDiv""
alert(div.className); //"bd"
alert(div.title); //"Body text"
alert(div.lang); //"en"
alert(div.dir); //"ltr"
```

### 2.2 元素特性

每个元素都有一或多个特性：
```js
var div = document.getElementById("myDiv");
alert(div.getAttribute("id")); //"myDiv"
alert(div.getAttribute("class")); //"bd"
alert(div.getAttribute("title")); //"Body text"
alert(div.getAttribute("lang")); //"en"
alert(div.getAttribute("dir")); //"ltr"

div.setAttribute("id", "someOtherId");
div.setAttribute("class", "ft");
div.setAttribute("title", "Some other text");
div.setAttribute("lang","fr");
div.setAttribute("dir", "rtl");
```

贴士：
- 在 IE7 及以前版本中， setAttribute()存在一些异常行为。通过这个方法设置class 和 style 特性，没有任何效果
- IE6 及以前版本不支持 removeAttribute()。

### 2.3 创建元素
```js
var div = document.createElement("div");
div.id = "myNewDiv";
div.className = "box";

// IE中可以如下方式创建
var div = document.createElement("<div id="myNewDiv" class="box"></div >");
```
### 2.4 元素遍历

元素相关的5个属性：
```
childElementCount：返回子元素（不包括文本节点和注释）的个数。
firstElementChild：指向第一个子元素； firstChild 的元素版。
lastElementChild：指向最后一个子元素； lastChild 的元素版。
previousElementSibling：指向前一个同辈元素； previousSibling 的元素版。
nextElementSibling：指向后一个同辈元素； nextSibling 的元素版。
```
利用这些元素不必担心空白文本节点。

## 三 Text类型

文本节点由 Text 类型表示，包含的是可以照字面解释的纯文本内容。纯文本中可以包含转义后的
HTML 字符，但不能包含 HTML 代码。

文本节点的创建：
```js
var textNode = document.createTextNode("<strong>Hello</strong> world!");
```

## 四 其他类型

## 4.1 Attr类型

Attr节点的类型数值为2，nodeName为其特性名称，nodeValue为其特性值。

注意：特性不被认为是 DOM 文档树的一部分。开发人员最常使用的是 getAttribute()、 setAttribute()和 remveAttribute()方法，很少直接引用特性节点。  

## 4.2 Comment注释类型

Comment表示注释节点

## 4.3 CDATASection类型

这是XML专有的类型

## 4.4 DocumentType类型

该类型只有Firefox/Safari等少数浏览器支持，包含与文档的doctype有关的信息。

## 4.5 DocumentFragment类型

该类型指文档片段，是一种轻量级文档，可包含和控制节点，但不会像完整的文档那样占用额外资源。  
