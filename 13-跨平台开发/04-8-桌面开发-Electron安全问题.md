# Electron 应用安全问题

## 一 常见保护应用手法

### 1.1 生产环境关闭开发者工具

```js
new BrowserWindow({
  // 其他配置
  webPreferces: {
    devTools: false,
  },
});
```

上述方式会让任何手段都无法打开开发者工具。而在传统 web 项目中，则会变得复杂，需要禁用 F12，禁用右键，禁用 Ctrl+Shift+I。

### 1.2 源码压缩与混淆

常用的压缩工具为：uglify-js<https://github.com/mishoo/UglifyJS>

Webpack 同时也自带了压缩功能。

Electron 提供了工具 asar 用来保护前端源码。在 Electron 开发者工具控制台中输入 `__filename` 就可以看到资源目录，但是在资源目录却只有 `.asar` 的文件（非文件夹）。Electron 无需解压该文件即可读取文件内容，只有在一些特殊场景，如处理依赖真实文件路径的底层系统方法时，才会解压，如：`child_process.execFile`。

此外，该库还能解决 Win 下路径名过长的问题。

使用方式：

```txt
npm i asar -D
npx asar pack your-app app.asar
```

对一些核心代码，我们可以考虑将其直接编译为 V8 字节码文件，工具为：<https://github.com/OsamaAbbas/bytenode>
