手写 Promise:

```js
class Promise {
  constructor(executor) {
    this.state = "pending"; // 初始化时，状态为pending
    this.value = undefined; // 成功时候的值
    this.reason = undefined; // 失败时候的值

    // 对异步的支持
    this.onResolveCallbacks = []; // 存放成功时的回调
    this.onRejectedCallbacks = []; // 存放失败时的回调

    const resolve = (value) => {
      if (this.state !== "pending") {
        console.log("错误进入resolve状态");
        return;
      }
      this.state = "fulfilled";
      this.value = value;
      this.onResolveCallbacks.forEach((fn) => fn());
    };

    const reject = (reason) => {
      if (this.state !== "pending") {
        console.log("错误进入reject状态");
        return;
      }
      this.state = "rejected";
      this.reason = reason;
      this.onRejectedCallbacks.forEach((fn) => fn());
    };

    try {
      executor(resolve, reject);
    } catch (e) {
      // 若 executor执行出现异常，那么就让该异常作为promise失败的原因
      reject(e);
    }
  }

  then(onfulfilled, onRejected) {
    if (this.state === "fulfilled") {
      onfulfilled(this.value);
    }

    if (this.state === "rejected") {
      onRejected(this.reason);
    }

    if (this.state === "pending") {
      this.onResolveCallbacks.push(() => {
        onfulfilled(this.value);
      });
      this.onRejectedCallbacks.push(() => {
        onRejected(this.reason);
      });
    }
  }
}

// 测试
const p = new Promise((resolve, reject) => {
  setTimeout(() => {
    reject("失败...");
  }, 2000);
  // resolve("成功...");
  // throw new Error("抛出失败....");
});

p.then(
  (data) => {
    console.log("成功数据：", data);
  },
  (err) => {
    console.log("失败数据：", err);
  }
);
```
