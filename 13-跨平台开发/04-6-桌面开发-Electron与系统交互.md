# Electron 与系统化交互

## 一 系统对话框

### 1.1 文件对话框

软件大部分具备打开本地目录的功能，示例如下：

```js
async function() {
      let filePath = await remote.dialog.showOpenDialog({       // 执行打开对话框
        title: "I need to open a file",
        buttonLabel: "click here to open",
        defaultPath: remote.app.getPath("pictures"),            // 默认打开路径
        properties: [],
        filters: [                                              // 过滤显示文件
          {name: "images", extensions: ["jpg", "png", "gif"] },
          {name: "videos", extensions: ["mp4", "avi", "mkv"] },
        ],
      });
    }
```

### 1.2 关于 对话框

软件一般都有 `关于` 这个选项，Electron 应用默认从 `package.json` 中生成，也可以在手动设置：

```js
// 手动设置 关于
app.setAboutPanelOptions({});

// 在其他地方显示 关于 对话框
app.showAboutPanel();
```

## 二 菜单
