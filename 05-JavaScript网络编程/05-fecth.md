## 一 fetch

fetch是W3C最新的标准网络API，因为XMLHttpRequest其实是一个设计粗糙的API，但是fetch在很多浏览器中支持度较低。  

示例：
```js
fetch("/test1").then(res=>{
    console.log(res);
});
```

贴士：fetch请求默认不会懈怠cookie，需要设置：
```js
fetch(cur, {
    credentials: 'include'
})
```