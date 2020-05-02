## 一 前端与服务端通信方式

- 同步请求
- iframe局部更新
- flash请求
- ajax异步请求


## 二 Ajax与异步编程

异步与同步：
- 异步：某段程序执行时不会阻塞其它程序执行，其表现形式为程序的执行顺序不依赖程序本身的书写顺序，相反则为同步
- 同步：必须等待前面的任务完成，才能执行后面的任务

现实生活中的一个例子：银行排队取款是同步，排队时玩手机是异步。  

Ajax即 Asynchronous Javascript And XML（异步JavaScript和XML）。   

Ajax实现了页面的无刷新数据变化，其本质上仍然是在HTTP协议的基础上通过异步的方式与服务器进行通信。  

Ajax 技术的核心是 XMLHttpRequest 对象（简称 XHR），


## 三 Ajax示例

```js
// 1-创建Ajax实例
var request;
if(XMLHttpRequest){     
    request = new XMLHttpRequest();
} else {    //兼容IE6
    request = new ActiveXObject("Microsoft.XMLHTTP");
}  

// 2-设置请求行：第三个参数可选，默认true异步
request.open('get', './test.php', true);    

// 3-如果是POST请求则需设置请求头
request.setRequestHeader('Content-Type','application/x-www-form-urlencoded');

// 4-设置请求体，仅用于POST请求，GET请求时可以不写send，或者参数为null
request.send(null);     //参数是字符串：'name=zs&age=10'

// 5-接受服务器端响应
request.onreadystatechange = function () {  

    console.log(request.readyState);

    //判断服务器是否正确响应
    if (request.readyState == 4 && request.status == 200) {
        console.log(request.responseText);
    }
}
```
ajax的五步步骤：
- 1 建立XMLHTTPRequest对象
- 2 注册回调函数：当服务器回应我们了,我们想要执行什么逻辑
- 3 使用open方法设置和服务器端交互信息：提交的网址,数据
- 4 设置发送的数据，开始和服务器端交互，发送数据
- 5 更新界面：在注册的回调函数中,获取返回的数据,更新界面

## 四 Ajax的对象

```
request.statusText; // 获取状态信息
request.getAllResponseHeaders(); //获取全部响应头
request.getResponseHeader(); 		//获取MIME类型响应头
request.responseText;			//获得字符串形式的响应数据
request.responseXML;			//获得XML形式的响应数据
```

每当readyState属性改变，就会调用onreadystatechange函数:
```
//readyState值
0:XMLHTTPRequest对象创建完成
1:发送请求准备完毕
2:已经完成发送
3:服务器已经返回数据
4:服务器返回的数据已经可以使用
```