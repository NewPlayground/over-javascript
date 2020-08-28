## 一 fetch

fetch 是 W3C 最新的标准网络 API，因为 XMLHttpRequest 其实是一个设计粗糙的 API，但是 fetch 在很多浏览器中支持度较低。

示例：

```js
fetch("/test1").then((res) => {
  console.log(res);
});
```

贴士：fetch 请求默认不会懈怠 cookie，需要设置：

```js
fetch(cur, {
  credentials: "include",
});
```
