## 一 前端与服务端通信方式

- 同步请求
- iframe 局部更新
- flash 请求
- ajax 异步请求

## 二 Ajax 与异步编程

异步与同步：

- 异步：某段程序执行时不会阻塞其它程序执行，其表现形式为程序的执行顺序不依赖程序本身的书写顺序，相反则为同步
- 同步：必须等待前面的任务完成，才能执行后面的任务

现实生活中的一个例子：银行排队取款是同步，排队时玩手机是异步。

Ajax 即 Asynchronous Javascript And XML（异步 JavaScript 和 XML）。

Ajax 实现了页面的无刷新数据变化，其本质上仍然是在 HTTP 协议的基础上通过异步的方式与服务器进行通信。

Ajax 技术的核心是 XMLHttpRequest 对象（简称 XHR），

## 三 Ajax 示例

```js
// 1-创建 Ajax 实例
var request;
if (XMLHttpRequest) {
  request = new XMLHttpRequest();
} else {
  //兼容 IE6
  request = new ActiveXObject("Microsoft.XMLHTTP");
}

// 2-设置请求行：第三个参数可选，默认 true 异步
request.open("get", "./test.php", true);

// 3-如果是 POST 请求则需设置请求头
request.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");

// 4-设置请求体，仅用于 POST 请求，GET 请求时可以不写 send，或者参数为 null
request.send(null); //参数是字符串：'name=zs&age=10'

// 5-接受服务器端响应
request.onreadystatechange = function () {
  console.log(request.readyState);

  //判断服务器是否正确响应
  if (request.readyState == 4 && request.status == 200) {
    console.log(request.responseText);
  }
};
```

ajax 的五步步骤：

- 1 建立 XMLHTTPRequest 对象
- 2 注册回调函数：当服务器回应我们了，我们想要执行什么逻辑
- 3 使用 open 方法设置和服务器端交互信息：提交的网址，数据
- 4 设置发送的数据，开始和服务器端交互，发送数据
- 5 更新界面：在注册的回调函数中，获取返回的数据，更新界面

## 四 Ajax 的对象

```
request.statusText; // 获取状态信息
request.getAllResponseHeaders(); //获取全部响应头
request.getResponseHeader(); 		//获取 MIME 类型响应头
request.responseText;			//获得字符串形式的响应数据
request.responseXML;			//获得 XML 形式的响应数据
```

每当 readyState 属性改变，就会调用 onreadystatechange 函数：
```
//readyState 值
0:XMLHTTPRequest 对象创建完成
1: 发送请求准备完毕
2: 已经完成发送
3: 服务器已经返回数据
4: 服务器返回的数据已经可以使用
```

## 一 原生 Ajax 封装

```js
var option = {
  url: "",
  method: "",
  params: {},
  error: null,
  success: null,
};

function ajax(option) {
  var xmlRequest;

  if (XMLHttpRequest) {
    xmlRequest = new XMLHttpRequest();
  } else {
    xmlRequest = new ActiveXObject("Microsoft.XMLHTTP");
  }

  if (option.method.toUpperCase() == "POST") {
    xmlRequest.setRequestHeader(
      "Content-type",
      "application/x-www-form-urlencoded"
    );
  }

  // 格式化传入的数据为 name=fox&age=18 这样的格式
  var formatStr = "";
  for (let item in option.data) {
    formatStr += item; // 属性名
    formatStr += "="; //等号
    formatStr += option.data[item]; //属性值
    formatStr += "&"; //分隔符
  }
  // 去除最后一个&
  formatStr = formatStr.slice(0, -1);

  // open 方法 如果是 get 方法，那么 url 之后需要添加数据
  if (option.method == "get") {
    option.url = option.url + "?" + formatStr;
    option.params = null;
  }

  xmlRequest.send(option.params);

  xmlRequest.onreadystatechange = function () {
    // 这步为判断服务器是否正确响应
    if (xhr.readyState == 4 && xhr.status == 200) {
      option.success(ajax 获取的数据）;
    }
  };
}
```

## 二 Ajax 中的进度事件封装

## 三 jQuery 中的 ajax

```javascript
$.ajax({
  type: "post",
  dataType: "html",
  url: "",
  data: dataurl,
  success: function (data) {},
});
```
