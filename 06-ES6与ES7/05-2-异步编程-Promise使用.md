## 一 异步问题

异步虽然带来了性能上的好处，但是在代码书写上，会产生回调地狱，如常见的ajax：
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

异步的优化方案很多，包括将回调函数命名，全部采用函数名方式调用，优化代码结构。但是这些在书写上都是治标不治本。ECMAScript提出了三个解决方案：
- Promise：基本的异步解决方案
- generator：ES6过渡方案
- async与await：ES7提出的方案，配合Promise能够完美解决JS异步问题

## 二 使用Promise

```js
function fn(num){

    return new Promise(function(resolve, reject){

        if (typeof num == 'number'){
            resolve("OK");
        } else {
            reject("NO");
        }

    }).then(function(data){
        console.log("正常执行: ", data);
    }).catch(function(err){
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

## 三 理解Promise

Promise其实可以认为是一种任务分发器，将任务分配到Promise队列中：
```js
var flag = true;

// 构造函数参数为回调函数 executor，在该函数内执行请求操作，并修改结果的状态值。
var p = new Promise(function(resolve, reject){      
    if(flag){
        resolve('true...');
    } else {
        reject('false...');
    }
});

// 实例方法 then：用于处理状态改变后的业务，该方法返回的仍然是一个Promise实例，因此可以无限使用then方法进行链式调用。  
p.then(function(res){               
    console.log(res);
})
 .catch(function(err){              // 实例方法 catch： 用于捕获错误
     console.log(err);
 })
```

构造函数执行的请求结果有三种状态：
- pending：默认结果，等待中，没有得到结果
- resolved：得到结果，继续执行，使用resolve函数可以修改该值
- rejected：得到错误，拒绝执行，使用reject函数可以修改该值


贴士：then方法可以接收两个参数，第一个参数用来处理resolved，第二个参数处理rejected，后者和catch相同：
```js
.then(
    function(resp){

    },
    function(err){

    }
)
```


## 三 Promise其他API

- Promise.all：参数数组中**所有**Promise实例的状态为resolved 或者 rejected 时，调用then方法
  - 示例：`Promise.all([fn1, fn2])`
- Promise.race：参数数组中**任一**Promise实例的状态修改，调用then方法
  - 示例：`Promise.all([fn1, fn2])`
  - 示例：`Promise.race([fn1, fn2])`