## 一 UDP 简介

TCP 数据传输是一种可靠的数据传输方式，在数据传输之前必须建立客户端与服务端之间的连接。UDP 是一种面向非连接的协议，所以其传输速度比 TCP 更加快速。

## 二 Node 开发 UDP 程序

### 2.1 UPD 服务端

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

### 2.2 UDP 客户端

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
