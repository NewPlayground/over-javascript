## 一 CSS3 简介

CSS3 在 CSS2 基础上，增强或新增了许多特性，目前浏览器支持程度差，需要添加私有前缀，但是移动端支持率较高。

## 二 CSS3 中的选择器

### 2.0 CSS3 普通选择器

```
div         标签选择器
.box        类名选择器
div p       后代选择器
div.box     交集选择器
div,p,span  并集选择器
div>p       子代选择器
*           通配符选择器
div+p       选中div后面的第一个p
div~p       选中div后面所有p
```

### 2.1 属性选择器

通过属性来选择元素，具体有以下 5 种形式：

```
div[attr]				  	属性名 = attr 的div
div[attr="value"]		  	属性attr值 = value 的div
div[attr~="value"]			属性值包含value字符
div[attr*="value"]			属性值包含value字符并且在“任意”位置
div[attr^="value"]			属性值包含value字符并且在“开始”位置
div[attr$="value"]			属性值包含value字符并且在“结束”位置
div[attr|="value"]			属性值是value或以“value-”开头的值（比如说zh-cn）
```

示例一：选择 text 输入框的背景色

```txt
<style>
  input[type="text"] {
    color: red;
  }
</style>

<input type="text" name=" value="v1"> <input type="search" name=" value="v11">
```

示例二：选择以 icon 开头的

```txt
<style>
  div[class=^"incon"] {
    color: red;
  }
</style>

<div class="icon1">图标1</div>
<div class="icon2">图标2</div>
<div class="myicon">图标3</div>
```

### 2.3 结构伪类选择器

以前的伪类选择器有：`:link`、`:active`、`:visited`、`:hover`等。CSS3 中新增了结构伪类选择器，以某元素相对于其父元素或兄弟元素的位置来获取元素：

```
E:first-child			第一个子元素
E:last-child			最后一个子元素

E:nth-child(n) 		    E元素的所有子元素，n为第几个
E:nth-last-child(n) 	同E:nth-child(n) 相似，只是倒着计算；

E:nth-of-type(n)  	    E元素的所有子元素中，所在类型的第n个元素
E:nth-last-of-type(n)   同上，从后向前计算

E:empty 			    没有任何子节点（包括空格）的E元素,(子节点包含文本节点)
```

注意：

- n 遵循线性变化，其取值是：0、1、2、3、4....，n<=0 时选取无效。
- n 可是多种形式：nth-child(2n)、nth-child(2n+1)、nth-child(-1n+5)等；
- 参数 n 还可以是特殊字符，如：even 表示所有偶数，odd 表示所有奇数

代码示例一：

```js
ul li:nth-child(2n-1){           //选中所有的奇数的li
    color: red;
}

ul li:nth-child(7n){             //选中所有的7 的倍数的li
    color: red;
}

ul li:nth-child(-1n+5){         //选中前面五个
    color: red;
}

ul li:nth-last-child(-1n+5){    //选中后面五个
    color: red;
}
```

代码示例二：

```txt
<style>
  div span:nth-of-type(1) {
    background-colr: pink;
  }
</style>

<div>
  <p>第一个</p>
  <span>111</span>
  <span>222</span>
  <span>333</span>
</div>
```

input 相关伪类

```
结合锚点进行使用，处于当前锚点的元素会被选中
E:target
E:enabled
E:disabled
E:checked
```

:not 伪类：

```js
p:not(.p0) {
    	background-color: #2aabd2;
}
<p>1111</p>
<p>1111</p>
<p class="p0">1111</p>
```

### 2.4 伪元素选择器

CSS3 新增了 `::before`、`::after` 两个伪元素选择器，两个冒号是为了和 CSS2 中的 `:before`等伪类选择器做区别。

伪元素选择器用于在选中元素的前、后插入一个行内元素！！

```txt
<style>
  div::before {
    content: "你";
  }
</style>

<div>好</div>
```

在上述示例中，浏览器会显示："你好"。

伪元素与伪类选择器的权重都为 1。
