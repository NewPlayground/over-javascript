## 一 异步问题

异步虽然带来了性能上的好处，但是在代码书写上，会产生回调地狱，如常见的 ajax：

```js
XHR.onreadystatechange = function(){
    if(){
        XHR2.onreadystatechange = function(){

            if(){

                XHR2.onreadystatechange = function(){

                    if(){
                        XHR2.onreadystatechange = function(){

                        }
                    }

                }
            }
        }
    }
}
```

异步的优化方案很多，包括将回调函数命名，全部采用函数名方式调用，优化代码结构。但是这些在书写上都是治标不治本。ECMAScript 提出了三个解决方案：

- Promise：基本的异步解决方案
- generator：ES6 过渡方案
- async 与 await：ES7 提出的方案，配合 Promise 能够完美解决 JS 异步问题

## 二 使用 Promise

```js
function fn(num) {
  return new Promise(function (resolve, reject) {
    if (typeof num == "number") {
      resolve("OK");
    } else {
      reject("NO");
    }
  })
    .then(function (data) {
      console.log("正常执行: ", data);
    })
    .catch(function (err) {
      console.log("发生错误: ", err);
    });
}

fn("12");
console.log("这是最后执行的？");
```

输出结果：

```
这是最后执行的？
发生错误:  NO
```

## 三 理解 Promise

Promise 其实可以认为是一种任务分发器，将任务分配到 Promise 队列中：

```js
var flag = true;

// 构造函数参数为回调函数 executor，在该函数内执行请求操作，并修改结果的状态值。
var p = new Promise(function (resolve, reject) {
  if (flag) {
    resolve("true...");
  } else {
    reject("false...");
  }
});

// 实例方法 then：用于处理状态改变后的业务，该方法返回的仍然是一个Promise实例，因此可以无限使用then方法进行链式调用。
p.then(function (res) {
  console.log(res);
}).catch(function (err) {
  // 实例方法 catch： 用于捕获错误
  console.log(err);
});
```

每个 Promise 都会经过一个短暂的生命周期：未决，已决，在这个生命周期内，Promise 会有三种可能的状态：

- 未决（unsettled）：表示异步操作尚未结束，此时的 Promise 只有挂起态一种状态
  - 挂起态（pending）此时 Promise 的状态为 ，
- 已决（settled）：此时 Promise 已经执行结束，但是可能绵连执行成功、执行失败两种状态
  - 已完成（fulfilled）：Promise 的异步操作成功结束，对应着完成处理函数 `fulfillment handler `
  - 已拒绝（rejected）： Promise 的异步操作未成功结束，可能是由于错误、其他原因导致，对应着错误处理函数 `rejection handler `

Promise 的状态属性`[[PromiseState]]`并未暴露给开发者，所以无法通过编程方式判断 Promise 状态，但是可以使用 `then()` 方法在 Promise 状态改变时执行特定操作。

then 方法可以接收两个参数，第一个参数用来处理 resolved，第二个参数处理 rejected，后者和 catch 相同：

```js
.then(
    function(res){

    },
    function(err){

    }
)
```

## 三 Promise 其他 API

- Promise.all：参数数组中**所有**Promise 实例的状态为 resolved 或者 rejected 时，调用 then 方法
  - 示例：`Promise.all([fn1, fn2])`
- Promise.race：参数数组中**任一**Promise 实例的状态修改，调用 then 方法
  - 示例：`Promise.all([fn1, fn2])`
  - 示例：`Promise.race([fn1, fn2])`

Promise.resolve() / Promise.reject() 可以直接创建一个已决、未决 Promise 实例。

```js
let p = Promise.resolve(42);

p.then(function (value) {
  console.log(value); // 42
});
```

## 四 Promise 的拒绝处理争议

Promise 的最大争议是：Promise 被拒绝时，若缺少拒绝处理函数，会静默失败：

```js
let p = Promise.reject(42);

// 此时 p 不会被处理

// 一段时间之后
p.catch((value) => {
  // 现在 p 才被处理
  console.log(value);
});
```

在未来的 ES 版本中才会解决该问题，Node 和浏览器目前已经做出了支持：若没有拒绝处理事件，会执行默认的错误处理函数。

- unhandledRejection ： 当一个 Promise 被拒绝，而在事件循环的一个轮次中没有任何拒绝处理函数被调用， 该事件就会被触发；
- rejectionHandled ： 若一个 Promise 被拒绝，并在事件循环的一个轮次之后有了拒绝处理函数被调用，该事件就会被触发。

这两个事件旨在共同帮助识别已被拒绝但未曾被处理 promise。
