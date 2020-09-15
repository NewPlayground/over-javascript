# Electron 测试与发版

## 一 测试

### 1.1 单元测试

Electron 应用本身可以使用 Mocha 作为单元测试用工具。

### 1.2 界面测试

Electron 提供了界面测试框架 Spectron，内部封装了 ChromeDriver WebdriverIO。

安装

```txt
# 注意： spectron9  对应 Electron7，依次递增类推
npm i spectron -D
```

在 test 文件夹内创建 test.js 测试文件：

```js
const { Application } = require("spection");
const assert = require("assert");
const electronPath = require("electron");
const path = require("path");

describe("开始执行界面测试", () => {
  this.timeout(10000);

  beforeEach(() => {
    this.app = new Application({
      path: electronPath, // 导入 electron，其实是导出了本项目下 Electron的课执行文件安装路径
      args: [path.join(__dirname, "..")],
    });
    return this.app.start();
  });

  afterEach(() => {
    if (this.app && this.app.isRunning()) {
      return this.app.stop();
    }
  });

  it("测试窗口是否启动", async function () {
    let count = await this.app.client.getWindowCount();
    assert.equal(count, 1);
  });
});
```

运行测试：

```txt
mocha
```

## 二 调试
