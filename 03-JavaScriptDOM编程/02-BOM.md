## 一 window 对象

### 1.1 window 对象的使用

window 对象是 BOM 的核心对象，代表浏览器的一个实例。

在浏览器中，window 对象也可以理解为 ECMAScript 的 Global 对象，所以在网页中定义的所有变量都是以 window 作为其 Global 对象的：

```js
var age = 29;
function sayAge() {
  alert(this.age);
}

alert(window.age); //29
sayAge(); //29
window.sayAge(); //29
```

注意：

- window 对象可以省略书写
- 在 window 上定义的属性可以使用 delete 删除，但是直接定义的全局变量不能使用 delete 删除。
- 在 window 上访问未定义的属性，只是一次查询，不会报错，但是其他地方直接使用未定义的变量，如：`var num = oldNum`，若 oldNum 未定义则报错！

```js
var age = 29;
window.color = "red";

//在 IE < 9 时抛出错误，在其他所有浏览器中都返回 false
delete window.age;

//在 IE < 9 时抛出错误，在其他所有浏览器中都返回 true
delete window.color; //returns true

alert(window.age); //29
alert(window.color); //undefined
```

使用 var 语句添加的 window 属性有一个名为[[Configurable]]的特性，这个特性的值被设置为 false，因此这样定义的属性不可以通过 delete 操作符删除。IE8 及更早版本在遇到使用 delete 删除 window 属性的语句时，不管该属性最初是如何创建的，都会抛出错误，以示警告。 IE9 及更高版本不会抛出错误。

### 1.2 frame

如果页面中包含框架，则每个框架都有自己的 window 对象，并且保存在 frames 集合中：

```js
<frameset rows="160,*">
    <frame src="frame.htm" name="topFrame">
    <frameset cols="50%,50%">
        <frame src="anotherframe.htm" name="leftFrame">
        <frame src="yetanotherframe.htm" name="rightFrame">
    </frameset>
</frameset>
```

上述页面可以通过
window.frames[0]或者 window.frames["topFrame"]来引用上方的框架。与窗口相关的对象还有：

- top：指向最外层框架，即浏览器窗口，上述代码最好使用 top，而不是 window
- parent：始终指向当前框架的
  直接上层框架

### 1.3 窗口位置

IE、 Safari、 Opera 和 Chrome 都提供了 screenLeft 和 screenTop 属性，分别用于表示窗口相对于屏幕左边和上边的位置。 Firefox 则在 screenX 和 screenY 属性中提供相同的窗口位置信息， Safari 和 Chrome 也同时支持这两个属性。

使用下列代码可以跨浏览器取得窗口左边和上边的位置:

```js
var leftPos =
  typeof window.screenLeft == "number" ? window.screenLeft : window.screenX;

var topPos =
  typeof window.screenTop == "number" ? window.screenTop : window.screenY;
```

IE、 Opera 中， screenLeft 和 screenTop 中保存的是从屏幕左边和上边到由 window 对象表示的页面可见区域的距离。换句话说，如果 window 对象是
最外层对象，而且浏览器窗口紧贴屏幕最上端——即 y 轴坐标为 0，那么 screenTop 的值就是位于页面可见区域上方的浏览器工具栏的像素高度。但是，在 Chrome、 Firefox 和 Safari 中， screenY 或 screenTop 中保存的是整个浏览器窗口相对于屏幕的坐标值，即在窗口的 y 轴坐标为 0 时返回 0。

Firefox、 Safari 和 Chrome 始终返回页面中每个框架的 top.screenX 和 top.screenY 值。即使在页面由于被设置了外边距而发生偏移的情况下，相对于 window 对象使用 screenX 和 screenY 每次也都会返回相同的值。而 IE 和 Opera 则会给出框架相对于屏幕边界的精确坐标值。

所以无法在跨浏览器的条件下取得窗口左边和上边的精确坐标值。moveTo()和 moveBy()方法倒是有可能将窗口精确地移动到一个新位置：

```js
//将窗口移动到屏幕左上角
window.moveTo(0, 0);
//将窗向下移动 100 像素
window.moveBy(0, 100);
//将窗口移动到(200,300)
window.moveTo(200, 300);
//将窗口向左移动 50 像素
window.moveBy(-50, 0);
```

注意：这两个方法可能会被浏览器禁用；而且，在 Opera 和 IE 7（及更高版本）中默认就是禁用的。另外，这两个方法都不适用于框架，只能对最外层的 window 对象使用。

### 1.4 导航和打开窗口

```js
//等同于< a href="http://www.wrox.com" target="topFrame"></a>
window.open("http://www.wrox.com/", "topFrame");
```

## 二 location 对象

location 对象提供了窗口中加载的文档信息，以及 URL 解析功能。要注意的是 location 对象既是 window 对象的属性，也是 document 对象的属性，即 window.location 和 document.location 引用的是同一个对象。

```js
location.href; // "http:/www.wrox.com"
location.pathname; // /user/order
location.search; // "?name=lisi"
```

解析查询参数：

```js
function getQueryStringArgs() {
  //取得查询字符串并去掉开头的问号
  var qs = location.search.length > 0 ? location.search.substring(1) : "",
    //保存数据的对象
    args = {},
    //取得每一项
    items = qs.length ? qs.split("&") : [],
    item = null,
    name = null,
    value = null,
    //在 for 循环中使用
    i = 0,
    len = items.length;
  //逐个将每一项添加到 args 对象中
  for (i = 0; i < len; i++) {
    item = items[i].split("=");
    name = decodeURIComponent(item[0]);
    value = decodeURIComponent(item[1]);
    if (name.length) {
      args[name] = value;
    }
  }
  return args;
}
```

### 三 navigator 对象

navigator 对象用于标识浏览器：

```js
navigator.appCodeName; // 浏览器名称，通常为Mozilla
navigator.userAgent; // 用户代理字符串
```

## 四 history 对象

history 对象保存的是网页浏览器历史记录：

```js
//后退一页
history.go(-1);
//前进一页
history.go(1);
//前进两页
history.go(2);

//跳转到最近的 wrox.com 页面
history.go("wrox.com");
//跳转到最近的 nczonline.net 页面
history.go("nczonline.net");

//后退一页
history.back();
//前进一页
history.forward();
```
