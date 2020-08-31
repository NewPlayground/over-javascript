## 一 Node与TCP编程

### 1.1 TCP简介

TCP 即传输控制协议，在 OSI 七层模型中属于传输层协议，大多应用层协议都是基于 TCP 构建而来，如：HTTP/SMTP/IMAP 等，可以说 TCP 协议是现代网络编程最重要的基石之一。

TCP 是面向连接的协议，其显著的特征是在传输之前需要进行 3 次握手形成会话：

![](../images/node/node&tcp-01.svg)

当会话形成后，服务器和客户端之间分别提供一个套接字，这 2 个套接字形成一个连接，服务端和客户端通过套接字实现了二者的连接操作。

### 1.2 TCP 服务端

Node 使用 net 模块的 createServer 方法构建 TCP 服务器。

```js
var net = require("net");

var server = net.createServer(function (socket) {
  // 创建一个套接字socket，作为参数传递给回调函数

  console.log("Connect..."); // 有访问就会输出

  socket.write("hello");

  socket.on("data", function (data) {
    socket.write("hi");
  });

  socket.on("end", function () {
    console.log("connect shut down");
  });
});

server.listen(3000, function () {
  console.log("Listen..."); //服务器启动就会输出
});
```

TCP 服务端还可以这样构建：

```js
var net = require("net");

var server = net.createServer();

server.on("connection", function (socket) {
  console.log("new connection comming");
});

server.listen(3001, function () {
  console.log("Listen...");
});
```

### 1.2 TCP 客户端

Node 创建 TCP 客户端只需要创建一个连接 TCP 客户端的 socket 对象即可，示例如下：

```js
var net = require("net");

//新建的client对象（就是socket）用来支持数据交互
var client = new net.Socket();

client.connect(3000, "localhost", function () {
  console.log("connect..."); //运行后输出该句，证明回调已经执行

  client.write("msg of client"); //发送数据
});

//接收数据
client.on("data", function (data) {
  console.log("the data come from server is " + data.toString());
});
```

创建 client 时候可以传入一个 json 对象，这个 json 对象有以下属性：

```
fd：置顶一个存在的文件描述，默认为null
readable：是否允许在这个socket上读，默认为false
writeable：是否允许在这个socket上写，默认为false
allowHalfOpen：该属性为false时，TCP服务器接收到客户端发送的一个FIN包后将会回发一个FIN包，该属性为true时，TCP服务器接收客户端发送的一个FIN包后不会回发FIN包。
```


## 二 Node与UDP编程

### 2.1 UDP简介

TCP 数据传输是一种可靠的数据传输方式，在数据传输之前必须建立客户端与服务端之间的连接。UDP 是一种面向非连接的协议，所以其传输速度比 TCP 更加快速。

### 2.2 UPD 服务端

Node 使用 dgram 模块中的 createSocket()方法创建一个 UDP 服务器，这个方法接收一个必须参数和一个可选参数，必须参数是一个标识 UDP 协议的类型，可指定为 udp4 或者 udp6，可选参数是一个回调函数，即 UDP 服务器接收数据时触发的回调函数，回调函数有 2 个参数，一个是接收的数据，一个是存放发送者信息的对象。

```JavaScript
var dgram = require('dgram');

var socket = dgram.createSocket('udp4',function (msg,rinfo) {
    // code
});

socket.bind(3000,'localhost',function () {
    console.log('bind 3000....');
});
```

这里与 TCP 的 createServer 一样，返回的都是 socket 对象，且回调函数就是监听 message 事件，因此使用 createServer 也可以不指定回调函数，直接显示的监听 message 事件就可以。

```JavaScript
const dgram = require('dgram');

let socket = dgram.createSocket('udp4');

socket.bind(3000,'localhost',function () {
    console.log('bind 3000....');
});

socket.on('message',function (msg,rinfo) {
    console.log(msg.toString());
});
```

### 2.3 UDP 客户端

```JavaScript
var dgram = require('dgram');

var message = new Buffer('msg from client');

var socket = dgram.createSocket('udp4');

socket.send(message,0,message.length,3000,'localhost',function (err,bytes) {
    if(err){
        console.log(err);
        return;
    } else {
        console.log('client send ' + bytes + 'message')
    }
});

socket.on('message',function (msg,rinfo) {
    console.log('msg from server');
});
```
