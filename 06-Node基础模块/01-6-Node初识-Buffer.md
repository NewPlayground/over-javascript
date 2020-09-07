## 一 Buffer概念

JavaScript在前端中处理字符串相关的API非常强大，好用，但是Node还需要额外处理网络、文件中的一些二进制数据，ECMAScript标准中没有提供这样的API。  

Buffer是Node提供的一个全新对象，非常类似JS中的Array，但是主要用来操作字节。Buffer在Node中由两部分源码实现：

- Buffer/SlowBuffer：js核心模块，主要用于实现Buffer在业务上的一些API
- node_buffer：C++内建模块，主要用于实现字节处理的性能相关部分

注意：Buffer由于在Node中使用场景非常广泛，所以在Node进程启动时就已经加载，位于全局对象global中。  

## 二 Buffer的基本使用

可以将Buffer理解为Node扩充的数据类型，其作用类似Array，用于操作二进制数据。  

Buffer的创建：

```js
// new Buffer() 的方式由于安全原因已经过期，Node8之后推荐使用以下方式创建
let buf = Buffer.from('test','utf-8');
console.log(bf);                                    // 输出 <Buffer 74 65 73 74>

for(let i = 0; i < bf.length; i++){                 //此length长度和字符串的长度有区别，指buffer的bytes大小
    console.log(bf[i].toString(16));                // buffer[index]:获取或设置在指定index索引未知的8位字节内容
    console.log(String.fromCharCode(bf[i]));        //依次输出 t e s t
    console.log(bf.toString());                     //输出 test  ，可选参数是 [encoding, start, end]，默认使用UTF-8
}
```

Node在文件、网络操作中，如果没有显示声明编码格式，默认返回的数据类型都是Buffer，比如readFile回调中的data。

注意：在ES6中增加了ArrayBuffer类型，Node中可以直接使用

Buffer的实例方法：

```js
buf.write(string,[offset],[length],[encoding]):根据参数offset，将参数string数据写入buffer
buf.toString([encoding],[length]):返回一个解码的string类型
buf.toJSON():返回一个JSON表示的Buffer实例，JSON.stringify将会默认调用来字符串序列化这个Buffer实例
buf.slice([start],[end]):返回一个新的buffer，这个buffer和老的buffer引用相同的内存地址
buf.copy(targetBuffer,[targetStart],[sourceStart],[sourceEnd])：进行buffer的拷贝，拷贝不会影响老的buffer。
```

Buffer的静态方法：

```JavaScript
Buffer.isBuffer(buf);              // 判断是不是Buffer
Buffer.byteLength(str);                 // 获取字节长度，第二个参数为字符集，默认utf8
Buffer.concat(list[, totalLength])     // Buffer的拼接
```

## 三 Buffer的转换

### 2.1 字符串转Buffer

Buffer对象可以与字符串之间相互转换，如下所示：

```js
new Buffer(str, [encoding]);            // 可选参数编码格式若不传入，则默认按照UTF-8编码进行转码和存储
```

一个Buffer对象可以存储不同编码类型的字符串转码的值，调用write()方法可以实现该目标，代码如下：

```js
buf.write(string, [offset], [length], [encoding]);
```

由于可以不断写入内容到Buffer对象中，并且每次写入可以指定编码，所以Buffer对象中可以存在多种编码转换后的内容，需要小心的是，每种编码所用的字节长度不同，将buffer反转回字符串时需要谨慎处理。  

### 2.2 Buffer转字符串

```js
buf.toString([encoding], [start], [end]);
```

### 2.3 Buffer不支持的编码类型

Node的Buffer不支持中国的GBK，GB2312，BUG-5等编码格式。判断Buffer是否支持该编码格式：

```js
Buffer.isEncoding(encodibg);        // 返回 true、false
```

对于不支持的编码格式，Node有第三方模块如 iconv 和 iconv-liten。

## 三 Buffer乱码

### 3.1 乱码的产生

在Buffer使用场景中，通常是以一段一段的方式传输，常见从输入流中读取内容的示例如下：

```js
var fs = require('fs');

var rs = fs.createReadStream('./demo.md');
var data = '';

rs.on("data", function(chunk) {
    data += chunk;
});

rs.end("end", function(){
    console.log(data);
});
```

上述代码在读取全英文格式内容时，不会有任何问题，但是一旦输入流中存在宽字节编码，就会产生乱码问题。问题来自于`data += chunk`，该句隐藏了 `toString()`操作，其内部等价于：

```js
data = data.toString() + chunk.toString();
```

下面模拟宽字节文字读取场景：

```js
var fs = require('fs');

var buf = Buffer.from("白银之手骑士团");

// <Buffer e7 99 bd e9 93 b6 e4 b9 8b e6 89 8b e9 aa 91 e5 a3 ab e5 9b a2 ef bc 81>
console.log("buf:", buf);
console.log("buf.length: ", buf.length);                        // 21
console.log("start: ", buf.toString("UTF-8", 0, 3));             // 白  e7 99 bd
console.log("start: ", buf.toString("UTF-8", 3, 6));             // 银  e9 93 b6
console.log("start: ", buf.toString("UTF-8", 6, 9));             // 之  e4 b9 8b，e6 89 8b，e9 aa 91，e5 a3 ab，e5 9b a2，ef bc 81

var data = "";
var rs = fs.createReadStream("./demo.txt", {highWaterMark: 4});
rs.on("data", function(chunk) {
    data += chunk;
});
rs.on("end", function(){
    console.log("流式读取：", data);                        // 白�����手骑�����
});
```

在上述案例中，每3个长度能够读取到一个汉字，但是在使用流式读取时，每4个长度读取一次，在第一读取时，就会读取到多余的数据了，也即输出了  `白�`，在第4次读取时，正好又读取了原始数据的存储要求，输出了`�手`，依次类推。  

### 3.2 乱码解决

流式读取可以设置编码：

```js
var rs = fs.createReadStream("./demo.txt", {highWaterMark: 4});
rs.setEncoding('utf8');
```

此时程序就能正常输出数据！但是这并不是直接说明了输出没有收到Buffer大小的影响。在实际运行过程中，无论如何设置编码，触发的data事件次数都仍然是相同的。但是在每次data事件都会额外通过一个decoder对象对Buffer进行转换到字符串的解码，然后传递给调用者。而这个decoder对象正是 `setEncoding()`方法时在可读流对象内部设置的。此时data收到的不再是原始的Buffer对象。decoder对象会被未转码的部分保留在StringDecode实例内部，再下一次write的时候，会将上次的剩余字节和后续的新读入的字节进行组合！

setEncding只能解决UTF-8，Base64等带来的编码问题，没有从根本上解决问题。正确的Buffer拼接方式应该是用一个数组来存储接收到的所有Buffer片段并记录下所有的片段总长度，然后调用Buffer.concat()方法生成一个合并的Buffer对象。

```js
fs.createReadStream("./test.txt",{highWaterMark: 10});

var dataArr = [];

rs.on("data", function(chunk){
    dataArr.push(chunk);
});

rs.on("end", function(){
    var buf = Buffer.concat(dataArr);
    console.log(buf.toString());
});
```

Buffer.concat()方法封装了从小Buffer对象向大Buffer对象复制过程：

```js
Buffer.concat = function(list, length) {

    if (!Array.isArray(list)) {
        throw new Error('Usage: Buffer.concat(list, [length])');
    }
    if (list.length === 0) {
        return new Buffer(0);
    } else if (list.length === 1) {
        return list[0];
    }

    if (typeof length !== 'number') {
        length = 0;
        for (var i = 0; i < list.length; i++) {
            var buf = list[i];
            length += buf.length;
        }
    }

    var buffer = new Buffer(length);
    var pos = 0;
    for (var i = 0; i < list.length; i++) {
        var buf = list[i];
        buf.copy(buffer, pos);
        pos += buf.length;
    }
    return buffer;
};
```

## 一 Buffer内的内存分配

### 1.0 Buffer内存分配概念

Buffer的内存申请并不是靠V8，而是C++层面原生实现的，采用了slab分配机制（一种动态内存管理机制）。该机制简单来说，就是一块申请好的固定大小的内存区域，具有如下状态：

- full：完全分配状态
- partial：部分分配状态
- empty：没有被分配状态

Node以8KB位接线区分Buffer是大对象还是小对象,这个8KB就是每个slab的大小，Node以其为基础单元进行内存分配。

### 1.1 分配小Buffer对象

在`new Buffer(1024)`时，此时构造对象小于8KB，Node会检查一个对象pool有没有创建，没有则会创建。创建后，处于分配状态的slab单元都会指向它：

![](../images/node/buffer-01.svg)  

在上图中，这个slab处于empty状态

```js
var pool;
if (!pool || pool.length - pool.used < this.length) alloclPool();

function allocPool(){                           // 申请内存函数
    pool = new SlowBuffer(Buffer.poolSize);     // 将新申请的SlowBuffer对象指向pool
    pool.used = 0;
}
```

构造完pool后，当前Buffer对象的parent属性也会指向该slab，并记录下从这个slab的哪个位置开始开始使用，slab对象自身也会记录被使用了多少字节：

```js
    this.parent = pool;
    this.offset = pool.used;
    pool.used += this.length;
    if (pool.used & 7) pool.used = (pool.used + 8) & ~ 7
```

![](../images/node/buffer-02.svg)  

此时slab状态为partial，如果这时候再次创建一个Buffer，构造中会检查这个slab的剩余空间是否足够，如果足够，则使用剩余空间，并更新slab分配情况。如下所示，再次`new Buffer(3000)` ：  

![](../images/node/buffer-03.svg)

如果slab剩余空间不够，则会构造新的slab。

某些情况下会造成slab剩余空间的浪费，第一次构造1字节Buffer，第二次构造8192字节Buffer，第二次分配时slab剩余空间不够，就会创建新的slab，则刚才的slab将会被一个1字节的Buffer对象独占，也就会造成一个极端情况：这个1字节的Buffer一直没有释放，相应的这8KB也一直没有释放。  

### 1.2 分配大Buffer对象

超过8KB的大Buffer对象，将会直接分配一个SlowBuffer对象作为slab单元，这个slab单元会被这个大Buffer对象独占：

```js
// Big buffer, just alloc one
this.parent = new SlowBuffer(this.length);          // SlowBuffer是C++模块
this.offset = 0;
```

## 二 Buffer的应用

由于Buffer能在有效对数据进行拆分传输，在文件I/O，网络I/O中占据很大使用场合。网络传输中的字符串一般都会转换为Buffer，以二进制方式进行传输，这种转换在网络中的饿请求响应中几乎时时刻刻在发生。  

如果预先将普通的内容转换为Buffer对象，就可以有效减少服务器资源浪费。实践中的案例如动静分离，静态内容往往会先转换为Buffer格式，这样浏览器在访问网页内容时，服务器就无需转换再传输了！  
