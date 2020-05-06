## 一 Electron简介

Electron 是一款基于Node.js的跨平台桌面应用开发框架，由Github开发，通过该框架可以利用 HTML、CSS、JavaScript 等web前端技术来开发跨平台桌面应用。   

Electron的著名实现案例是VSCode。  

Electron内部集成了`Chromium Content Module`与Node.js运行时，前者无法访问本地数据，但是可以创建GUI界面，后者能够访问本地数据，但是不能创建GUI界面。Electron结合二者后，开发者无需关心底层，直接使用原生操作系统的功能，创建桌面应用：
- Chromium Content Module：Chromium的核心组件，包含浏览器核心功能：Blink渲染引擎、JS的V8引擎，以及提供在单独进程渲染Web页面、加速GPU等功能
- Node.js运行时：可以访问本地文件系统、创建服务器、从外部模块加载文件 

在使用Electron创建应用时，会把一个指定版本的Chromium和Node.js打包进应用，虽然让开发者可以充分使用二者的功能，但是也造成了软件包体积的庞大（主要因为node_modules问题）。

## 二 Electron安装

```
npm i electron -g
```
全局安装后，electron 才能提供 electron 命令。   

## 三 HelloWorld

### 3.1 手动书写一个HelloWorld

初始化一个node.js项目，分别包含：
- main.js：项目入口文件
- /views/index.html：软件打开时默认显示的页面，该文件内容书写一个 `hello world` 即可
- package.json：node项目初始化后默认配置文件

main.js内容：
```js
const { app, BrowserWindow } = require("electron");

// 软件窗口对象
let mainWindow;

function createWindow(){

    mainWindow = new BrowserWindow({ width: 800, height: 600 });

    // 加载初始界面
    mainWindow.loadFile("./views/index.html");

    // 打开开发者工具。
    mainWindow.webContents.openDevTools();

    mainWindow.on("closed", ()=>{
        mainWindow = null;
    });
}

app.on("ready", createWindow);

// 当全部窗口关闭时退出。
app.on('window-all-closed', () => {
    // 在 macOS 上，除非用户用 Cmd + Q 确定地退出，否则绝大部分应用及其菜单栏会保持激活。
    if (process.platform !== 'darwin') {
      app.quit();
    }
});
  
app.on('activate', () => {
    // 在macOS上，当单击dock图标并且没有其他窗口打开时，通常在应用程序中重新创建一个窗口。 
    if (mainWindow === null) {
      createWindow();
    }
});
```

运行：
```
electron .
```

### 3.2 使用官方示例

直接使用官网的quick start案例：
```
git clone https://github.com/electron/electron-quick-start
cd electron-quick-start
npm install && npm start
```

## 四 Electron特性

- 支持多视窗，每个视窗都有自己独立的JavaScript上下文
- 通过 shell 和 screen API 整合桌面操作系统特性
- 支持常见的桌面端软件需求，如：菜单、快捷键、托盘
- 支持自动更新、汇报程序崩溃、操作系统通知等

## 五 打包

electron有两种打包方式：electron-packager及electron-builder。electron-builder更加高效方便。这里只介绍该打包方式。  

```
yarn add electron-builder --save-dev

"build": {
    "appId": "com.xxx.app",
    "mac": {
      "target": ["dmg","zip"]
    },
    "win": {
      "target": ["nsis","zip"]
    }
},
"scripts": {
    "dist": "electron-builder --win --x64"
},
```
## 六 主进程和渲染进程

主进程：
- 核心进程：在Electron中运行package.json和app脚本的进程为主进程
- 渲染进程：electron使用Chromium的多进程机制来渲染页面，每个页面拥有一个自己的进程，该进程称为渲染进程。如app.js文件中加载的index.html、index.js

主进程和渲染进行之间通信的常见方式：
- **ipcRenderer用于渲染进程**
  - ipcRenderer.on(channel, listener) --> channel为事件频道,字符串.listener为触发回调函数,用于响应webContent.send()
  - ipcRenderer.send(channel, data) --> 概念同上,用于向ipcMain发送异步信息
- **ipcMain用于主进程,响应从渲染进程中发送的消息**
  - ipcMain.on(channel,listener) --> 响应从ipcRender中相同channel
- **event.sender**：主进程监听到渲染进程发送的消息，在响应事件回调函数中使用event.sender.send()方法可以向渲染进程发送消息
- **webContent**：ipcMain本身是无法直接发送事件的,只能通过响应事件回调的event来发送,那如果我们想先让主进程发送消息就可以使用webcontent.这个webcontent是在BrowserWIndow实例中的方法
    - webContent.send(channel,data) --> 主进程向渲染进程发送消息
- 不同渲染进程之间共享数据
  - 可以简单的使用HTML5中提供的API来实现，如localStorage和sessionStorage
  - 在electron中，我们可以在主进程中将一个对象存储为全局变量，在渲染进程中通过remote模块进行操作