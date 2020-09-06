# Node 基本使用

## 一 开发工具

笔者推荐的 NodeJS 开发工具是 VScode/WebStorm。

如果是使用 VSCode 开发，推荐安装插件：

- Code Runner：用于运行 node 代码
- Search node_modules：用于 node 包快速提示

如果使用 WebStorme（或者 IDEA+Node 插件），则可能需要配置下 node 环境。打开 WebStorme，在设置中界面中选择：`File-setting-Languages&Frameworks-Node`，对比下图进行勾选：  
![WebStorme配置Node环境](../images/node/idea.jpg)

## 二 使用 Node

### 2.1 REPL 环境

Node 默认提供了一个命令环境用来基础使用，类似 Chrome 浏览器中的 Console 模块：

```html
# 打开命令行窗口，输入 node 命令，即可进入 REPL 环境
node

# 输入
1+1

# 回车后界面将会显示
2
```

### 2.2 Node 执行 JS 文件

REPL环境过于简单，Node 是可以直接运行 JS 文件的，创建一个 `app.js` 文件，内容如下:

```js
console.log(1+1);
```

在学习Node之前，该 JS 文件只能嵌入在 HTML 文档中，由浏览器来运行，现在 Node 命令可以直接运行：

```html
node app.js                 # 可以省略 .js 后缀

# 输出2
```

## 三 全局对象

### 3.0 Node全局对象汇总

Node默认提供了一些全局对象：

- global：用于挂载全局变量
- __filename：用于获取当前文件路径
- __dirname：用于获取当前目录路径
- process：用于获取系统运行信息
- buffer：缓存模块
- stream: 流模块
- exports：用于支持模块导出
- module：用于支持文件模块化
- require()：该函数用于导入模块

### 3.1 全局对象global

在原生js中定义全局变量，浏览器中可以通过window顶层对象访问（即全局变量是顶层对象的属性）：

```js
let a = 100;
console.log(window.a);
```

Node没有window这个顶层对象，Node自身的顶层对象是 global，定义全局变量方式：

```js
a = 3;            // 第一种定义全局变量方式，如果使用var a = 3; global是无法访问到的。
global.b = 3;     // 第二种定义全局变量方式
```

### 1.2 模块属性 __filename__dirname  

- __filename：返回当前模块的文件的解析后的绝对路径（包含文件名），该属性并非全局的（不能global.__filename），而每个模块都有该属性，所以直接可以使用。
- __dirname：返回当前模块文件所在目录解析后的绝对路径（不包含文件名），返回的是文件夹。同__filename一样，其也非全局对象，而是每个模块都有的属性。  
