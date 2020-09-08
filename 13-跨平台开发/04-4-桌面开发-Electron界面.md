# Electron 界面

窗口类似于整个客户端软件的框架结构，界面则是框架内内容区域。

## 一 页面内容 webContents

### 1.1 获取 webContents 实例

webContents 是 Electron 内真正渲染展示 Web 界面的地方，也即内容区域，大量的交互都在此处。

获取方式：

```js
// 通过窗口可以直接获取 webContents 实例
const webContent = win.webContent;

// 获取处于激活状态的 webContents
const actWebContent = electron.webContents.getFocusedWebContents();

// 通过渲染进程获取 webContents
webContent = remote.getCurrentWebContents();

// 每次创建窗口时，窗口都有一个制度的整型id值，也可以获取页面内容
webContent = electron.webContents.fromId(yourID);

// 下策：还可以通过遍历所有内容，一一对比方式
const webContentArr = electron.webContents.getAllWebContents();
```

### 1.2 事件的加载与触发顺序

Electron 中页面加载的事件顺序：

- id-start-loading： 页面加载中的第一个事件，浏览器的 tab 页图标开始旋转
- page-title-update： 页面标题更新。处理函数的第二个参数为当前页面标题
- dom-ready：页面中的 dom 加载完毕。其本质是网页的事件 DOMContentLoaded。该事件与页面中的 iframe 无关，但是如果有 script 标签则需要等待脚本加载完毕后触发，若 script 标签之前还有 css 资源，则还需要等待 CSS 资源的加载。若没有 script 标签，则无需等待 CSS 资源加载会直接触发事件。
- did-frame-finish-load：框架加载完成。页面中可能有多个 iframe，所以该事件会触发多次，当前页面为 mainFramedid

### 1.3 页面跳转事件

### 1.4 单页面应用中的页内跳转

### 1.5 页面缩放

### 1.6 渲染海量数据元素

## 二 页面容器 webFrame

## 三 脚本注入
