## 一 DOM 与 Bom 概述

ECMAScript 是 JavaScript 的语法核心，但是仅仅有语法，没有一些库的帮助，JS 也只是一个玩具。在浏览器环境中，浏览器为 JavaScript 提供了两大对象：

- DOM：`Document Object Model`，JavaScript 操作网页元素的 API
- BOM：`Browser Object Model`，JavaScript 操作浏览器部分功能的 API

由于浏览器厂商、版本的不同。DOM 和 BOM 对象的一些 API 往往使用方式不同，或者不兼容。当然，现在 DOM 和 BOM 已经被纳入 HTML5 规范，相信未来会逐渐统一。

HTML5：HTML5 在从前 HTML4.01 的基础上进行了一定的改进，是专门针对 Web 平台 API 的一系列规范，包括：视频、音频、图像、动画以及与设备的交互，将 Web 带入了一个成熟的应用平台。

## 二 客户端检测

### 2.1 客户端能力检测

为了应对不同浏览器之间 API 的兼容性不同，前端开发者必须对客户端进行能力检测（特性检测），这也是使用最广泛的检测方式。

能力检测的目标不是用来识别浏览器，而是为了识别浏览器是否具备该能力：

```js
if (objec.propertyInQuestion) {
  // 使用 objec.propertyInQuestion
}
```

当然上述使用也会有一些误区要注意，比如检测对象某个成员是否存在，如：

```js
// 查看对象是否有sort() 方法，如果对象内部包含sort属性，就会让下列判断为真
if (typeof object.sort) {
}

// 正确的判断方式
if (typeof object.sort == "function") {
}
```

### 2.2 怪癖检测

怪癖检测（ quirks detection）的目标是识别浏览器的特殊行为，用于了解浏览器存在什么缺陷（“怪癖”也就是 bug）。

如 IE8 中，如果某个实例属性与`[[Enumerable]]`标记为 false 的某个原型属性同名，则无法通过 for in 遍历到，可以通过下列方式检验怪癖：

```js
function hasDontEnumQuirk() {
  var o = {
    toString: function () {},
  };
  for (var prop in o) {
    if (prop == "toString") {
      return false;
    }
  }
  return true;
}
```

### 2.3 用户代理检测

用户代理检测即通过浏览器端的`navigator.userAgent`属性进行浏览器判断，这是最不推荐的方式，因为会浏览器端容易因为攻击产生欺骗性。

具体章节可以查看红宝书。
