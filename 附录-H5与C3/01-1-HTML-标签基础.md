## 一 网页基础

### 1.1 网页标准

网页的标准：

- 结构：HTML，即 `Hyper text markup language` ，超文本标记语言，能够实现网页跳转的文本
- 表现：CSS，即 `Cascading Style Sheets`，层叠样式表
- 行为：由 JavaScript 编程语言控制

HTML 标准由 W3C 组织制定，经过多年发展，其标准已经到达了 HTML5，当然其兼容性仍然较差，市面上现有的 HTML 标准有：

- HTML4：被大多数浏览器所兼容，推荐使用
- XHTML：是 HTML4 与 XML 的过渡版本，不推荐使用
- XML：可以自定义标签，但是未被浏览器标准接受，被广泛应用于通信协议领域
- HTML5：具备划时代意义的 HTML 版本，已经被 Chrome、FireFox 等浏览器接受，但是大多数场合兼容度较低

### 1.2 标准 HTML4 网页结构

```txt
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
  <head>
    <title></title>
  </head>
  <body></body>
</html>
```

### 1.3 标准 HTML5 网页结构

```txt
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <title>Title</title>
  </head>
  <body></body>
</html>
```

## 二 常见标签

### 2.0 标签分类

网页的内容都是由大量的 HTML 标签构成，按照标签的开合可以将标签分为分类：

- 单标签：只有开始没有结束，如：`<hr> <!-- --> <br> <hr>`
- 双标签：有开始有结束，如：`<p></p> <em></em> `

### 2.1 图片标签 img 与路径

图片标签 img 示例：

```txt
<img src="1.jpg" atl="我是图片" title="图片标题" width="200" height="300" />
```

img 标签常用属性：

- alt：当图片无法显示时显示 alt 内的文字
- src：图片路径，可以是绝对路径（/开头）或者相对路径（./开头，可以省略./）

### 2.2 a 链接

a 链接示例：

```txt
<a href="http://www.baidu.com/">百度</a>
```

a 标签常用属性：

- href：值为#时不能跳转，值为一个文件时则可以实现下载功能
- title：鼠标划过时显示的文字
- target：网页打开方式，常见打开方式有
  - "\_self"：默认值，在当前页打开
  - "\_blank"：在新窗口中打开

锚链接位置跳转演示：

```txt
<p id="test">你好</p>
<a href="#test">查看问候语</a>
```

a 链接特殊写法：可以制造点击不执行，一般在 tab 栏切换中使用

```txt
<a href="”javascript:;”"></a>
<a href="”javascript:void(0);”"></a>
```

### 2.3 特殊字符

Html 中的特殊字符需要使用转义字符书写：

| 特殊字符 | HTML 中的表示 |
| -------- | ------------- |
| 空格     | `&nbsp;`      |
| <        | `&lt;`        |
| >        | `&gt`         |
| &        | `&amp;`       |

### 2.4 meta 标签

meta 标签用来设置字符集、关键字、描述、重定向等：

```txt
<meta charset="utf-8" />
<meta name="keywords" content="流行资讯" />
<meta name="description" content="最新服装设计流行信息" />
<meta http-equiv="refresh" content="2; url=1.html" />
<!-- 2秒后跳转到1.html -->
```

### 2.5 link 标签

link 标签必须放在 head 中，用来引入外部样式表和网页标题小图标：

```txt
<link rel="stylesheet" href="1.css" /> <link rel="icon" href="favicon.ico" />
```

### 2.6 背景音乐标签 embed

引入音乐文件：

```txt
<embed src="1.mp3" hidden="true"></embed>
```

### 2.7 滚动标签 marquee

```txt
<marquee
  behavior="slide"
  direction="up"
  width="280"
  height="300"
  bgcolor="blue"
>
  <img src="1.jpg" />
</marquee>
```

中间的内容可以是文字、图片，也可以是由程序生成的文字、图片，behavior 可以设置滚动方式：

- slide： 一端滚到另一端不会重复
- scroll： 一端滚到另一端会重复
- alternate： 两端之间来回滚动
- direction： 设置滚动方向，值分别有 left、right、up、down
- loop： 设置滚动次数，-1 为一直滚动下去

### 2.8 注释

```txt
<!-- 这里书写注释 -->
```

### 2.9 列表标签

无序列表 ul：

```txt
<ul>
  新闻1
</ul>
<ul>
  新闻2
</ul>
<ul>
  新闻3
</ul>
```

无序列表，默认无任何显示样式，但是可以添加一些样式：

- type="square"：列表样式为小方块
- type="circle"：列表样式为小圆圈

有序列表 ol:

```txt
<ol type="a" start="3">
  <li>新闻1</li>
  <li>新闻2</li>
</ol>
```

有序列表的样式：

- A,a：分别以 A 或者 a 字幕顺序排序
- I,i ：分别以大小写罗马数字排列
- start="3" ：li 前面的显示从第几个开始计数

自定义列表 dl：

```txt
<dl>
  <dt>新闻汇总</dt>
  <dd>新闻1</dd>
  <dd>新闻2</dd>
</dl>
```

## 三 标签语义化

标签语义化概念：根据内容的结构化（内容语义化），选择合适的标签（代码语义化）。

标签语义化可以让网页结构更加合理，且利于 SEO。

标签语义化方式：

- 尽可能少的使用无语义的标签 div 和 span；
- 在语义不明显时，既可以使用 div 或者 p 时，尽量用 p, 因为 p 在默认情况下有上下间距，对兼容特殊终端有利；
- 不要使用纯样式标签，如：b、font、u 等，改用 css 设置。
- 需要强调的文本，可以包含在 strong 或者 em 标签中 strong 默认样式是加粗（不要用 b），em 是斜体（不用 i）；

## 四 表格标签 table

### 4.1 table 组成

```txt
<table border="1" width="300" height="100" cellspacing="0" cellpadding="5" align="center" bgcolor="pink">
    <tr>
        <th>姓名</th>
        <th>性别</th>
    </tr>
    <tr>
        <td>111</td>
        <td>122</td>
    </tr>
    <tr>
        <td>211</td>
        <td>222</td>
    </tr>
</table>
```

表格结构:thead（表头）、tbody（主体）、tfoot（结尾）

- cellspacing：用来设置单元格与单元格的距离（td），默认值为 2
- cellpadding：设置内容距边框的距离（文字距离 td 左侧）
- align：设置对齐方式，包括 left、right、center

注意：

- table 标签的标题标签是 caption，不能使用 title
- td 内容垂直对齐使用 valign

```txt
<!-- valign的其他值有 top middle bottom -->
<td valign="bottom">123</td>
```

### 4.2 合并单元格

- 横向合并 colspan：设置 td 的横向合并
- 纵向合并 rowspan：设置 td 的纵向合并

```txt
<table width="300" height="200" cellspacing="0" border="1">
    <tr>
        <td colspan="2">111</td>
        <td>122</td>
        <td>133</td>
    </tr>
    <tr>
        <td>211</td>
        <td>222</td>
        <td rowspan="2">233</td>
    </tr>
    <tr>
        <td>311</td>
        <td>322</td>
        <td>333</td>
    </tr>
</table>
```
