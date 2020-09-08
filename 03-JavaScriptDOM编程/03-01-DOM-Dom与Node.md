## 一 DOM 简介

HTML 加载完毕，渲染引擎会在内存中把 HTML 文档，生成一个 DOM 树，DOM(文档对象模型)是针对 HTML、XML 的 API 接口，描绘了一个层次化的数，允许开发人员对其进行增删改查。

入口函数如下：

```js
window.onload = function () {};
```

该函数解释：页面的结构、样式、节点等加载完毕后才去执行函数体。

## 二 Node 节点

在 HTML 当中一切都是节点，整个文档是一个文档节点，每一个 HMTL 标签都是一个元素节点，标签中的文字则是文字节点，标签的属性是属性节点。

DOM 文档对象是有节点层次的：

- 在 HTML 中：根节点是`<html></html>`，该节点也称为文档元素
- 在 XML 中：没有根节点，任何元素都可能成为文档元素

Node 类型是 DOM 中所有节点的父接口，所有节点均继承自 Node 类型。

## 三 节点基础操作

### 3.1 节点选择

```js
document.getElementById("demo"); //通过 id 找到 HTML 元素
document.getElementsByTagName("div")[0]; //通过标签名找到 HTML 元素	获得的是标签伪数组
document.getElementsByClassName("a"); //通过类名找到 HTML 元素，在部分IE中无效
```

新的扩展选择 API：

```js
document.querySelector(); // 可以传入 CSS选择符,返回与该模式匹配的第一个元素
document.querySelectorAll(); // 可以传入 CSS选择符,返回的是一个 NodeList 的实例

// 该方法浏览器支持率较低
document.matchesSelector(); // 可以传入 CSS选择符,返回是否匹配的布尔
```

### 3.2 节点层次关系

```js
节点.parentNode; //获取父节点，一个节点只有一个父节点

节点.nextSibling; //获取下一个节点（IE678中指下一个元素节点，标准浏览器包括空文档和换行节点）
节点.nextElementSibling; //获取下一个节点（不支持IE678）

节点.previousSibling; //获取前一个节点（用法同nextSibling）
节点.previousElementSibling; //获取前一个节点（用法通nextElementSibling）

父节点.firstChild; //获取第一个子节点（IE678中指获取第一个子元素节点），同理 lastChild
父节点.firstElementChild; //获取第一个子元素节点（不支持IE678）

节点.parentNode.children[index]; //随意得到兄弟节点

父节点.childNodes; //标准属性，获取所有子节点数组，包括：文本、换行
父节点.children; //非标准属性，但用的最多，只返回HTML节点，但是得到了几乎所有浏览器的支持，IE6/7/8中包含注释节点
```

### 3.3 节点的增、删、复制

```js
//创建节点
document.write("节点与内容");     //会抹掉页面已加载的元素
对象.innerHTML = "节点及内容";
document.createElement();

//插入节点
父节点.appendChild(新节点);         //父节点的最后插入一个新节点
父节点.insertBefore(新节点,参考节点); //在参考节点前插入;

//删除节点
父节点.removeChild（子节点);      //必须指定要删除的子节点

//复制节点
新节点=要复制的节点.cloneNode(参数); //参数可选复制节点
```

## 四 节点类型 nodeType

Node 是基本的节点类型，所以所有节点也都拥有 Node 中的基本属性、方法。每个节点都有一个 nodeType 属性，用于表明节点的类型：

```txt
<div id="div">111</div>
<script>
  var div = document.getElementById("div");
  console.log(div.nodeType); // 1
  console.log(div.nodeName); // DIV，元素标签名
  console.log(div.nodeValue); // null，始终为null
</script>
```

节点的类型通常有 12 个，由 12 个数值表示：

```
Node.ELEMENT_NODE(1)；                      // 元素节点
Node.ATTRIBUTE_NODE(2)；                    // 属性节点
Node.TEXT_NODE(3)；                         // 文本节点
Node.CDATA_SECTION_NODE(4)；
Node.ENTITY_REFERENCE_NODE(5)；
Node.ENTITY_NODE(6)；
Node.PROCESSING_INSTRUCTION_NODE(7)；
Node.COMMENT_NODE(8)；
Node.DOCUMENT_NODE(9)；
Node.DOCUMENT_TYPE_NODE(10)；
Node.DOCUMENT_FRAGMENT_NODE(11)；
Node.NOTATION_NODE(12)
```

但是推荐使用数字，因为名称在 IE 中往往不兼容：

```js
if (someNode.nodeType == Node.ELEMENT_NODE) {
  //在 IE 中无效
  alert("Node is an element.");
}

// 推荐写法
if (someNode.nodeType == 1) {
  //适用于所有浏览器
  alert("Node is an element.");
}
```

## 五 childNodes 属性

每个节点都有 childNodes 属性，保存着一个 NodeList 对象：

```txt
<div id="div">
  <div>111</div>
  <div>222</div>
</div>
<script>
  let div = document.querySelector("#div");

  console.log(div.childNodes.length); // 5

  console.log(div.childNodes[1]); // <div>111</div>

  // 也可以使用item方法
  console.log(div.childNodes.item(1)); // <div>111</div>
</script>
```

childNodes 对象成员是可以枚举的，所以可以通过`Array.prototype.slice.call(someNode.childNodes,0)`生成一个数组，但是在 IE8 中这是无效的，因为 IE8 将 NodeList 实现为了 COM 组件，无法使用 JS 调用，必须手动一一复制：

```js
function convertToArray(nodes) {
  var array = null;
  try {
    array = Array.prototype.slice.call(nodes, 0); //针对非 IE 浏览器
  } catch (ex) {
    array = new Array();
    for (var i = 0, len = nodes.length; i < len; i++) {
      array.push(nodes[i]);
    }
  }
  return array;
}
```
