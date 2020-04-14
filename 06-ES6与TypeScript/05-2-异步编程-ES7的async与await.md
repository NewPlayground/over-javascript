## 一 ES7 的 async/await

Promise虽然给异步编程带来了便利，但是在大型项目上用起来仍然较为繁琐。过渡方案generator也因为引入了一些新语法而未被社区完全接受。  

ES7的async/await引入后，JS的异步编程体验才得到的质的提升：
```js
async function(){
    let 结果1 = await 异步操作(Promise generator async)       // await是等待的意思，等待异步操作返回的结果，会阻塞代码
    let 结果2 = await 异步操作(Promise generator async)
....
}
```

基本示例：
```js
async function show(params) {
    console.log(1);
    await new Promise(function (resolve, rejec) {
        setTimeout(function () {
            resolve();
        }, 3000);
    });
    console.log(2);
    await new Promise(function (resolve, rejec) {
        setTimeout(function () {
            resolve();
        }, 3000);
    });
    console.log(3);
}
show();

function sleep(sec) {
    return new Promise((resolve, reject) => {
        setTimeout(function () {
            resolve();
        }, sec * 1000);
    })
}
```
上面的写法已经符合正常人的顺序思维了。不过要理解的是：async/await不过是语法糖罢了，本质上仍然是Promise。  

## 二 async/await的异常捕获

在Promise中，通过catch或者then的第二个参数捕获异常。在async中，通过try/catch捕获异常：
```js
const foo = async () => {
    try {
        await fn();
    } catch (e) {
        console.log(e);
    } 
} 

foo();
```

如果有多个await函数，那么只返回第一个捕获到的异常：
```js
function fn1(){
    return new Promise((resolve, reject)=>{
        setTimeout(()=>{
            reject('fn1 err!')
        }, 1000);
    });
}

function fn2(){
    return new Promise((resolve, reject)=>{
        setTimeout(()=>{
            reject('fn2 err!')
        }, 1000);
    });
}

const foo = async ()=>{
    try {
        await fn1();
        await fn2();
    } catch(e) {
        console.log(e);     // fn1 err!
    }
}

foo();
```