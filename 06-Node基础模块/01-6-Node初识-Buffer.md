# Buffer

## 一 Buffer 概念

JavaScript 不具备处理网络、文件的能力，所以其语言本身在以前也没有提供操作二进制数据的 API，但是这是 Java 等语言的核心之一。Node 为了弥补这个不足，提供了全新的对象 Buffer，用来操作字节。由于 Buffer 对象在 Node 中非常常用，所以在 Node 进程启动时就会被加载进内容，位于全局对象 global 中，可以直接使用。

Buffer 在 Node 中由两部分源码实现：

- Buffer/SlowBuffer：js 核心模块，主要用于实现 Buffer 在业务上的一些 API
- node_buffer：C++ 内建模块，主要用于实现字节处理的性能相关部分

可以将 Buffer 理解为 Node 扩充的数据类型，其作用类似 Array，用于操作二进制数据。在 ES6 中增加了 ArrayBuffer 类型，Node 中可以直接使用。

## 二 Buffer 的基本使用

### 2.1 Buffer 的创建

`new Buffer()` 可以用来创建对象，但是该由于安全原因已经过期，Node8 之后推荐使用以下方式创建：

```js
// 编码格式为可选参数，若不传入，则默认按照 'utf-8 编码进行转码
var buf = Buffer.from('test','utf-8');
console.log(bf);                                    // 输出 <Buffer 74 65 73 74>

for(let i = 0; i < bf.length; i++){                 // 此 length 长度和字符串的长度有区别，指 buffer 的 bytes 大小
    console.log(bf[i].toString(16));                // buffer[index]: 获取或设置在指定 index 索引未知的 8 位字节内容
    console.log(String.fromCharCode(bf[i]));        // 依次输出 t e s t
    console.log(bf.toString());                     // 输出 test
}
```

贴士：Node 在文件、网络操作中，如果没有显示声明编码格式，默认返回的数据类型都是 Buffer，比如 readFile 回调中的 data。

Buffer 的实例方法：

```js
// 根据参数 offset，将参数 string 数据写入 buffer
buf.write(string,[offset],[length],[encoding])

// 返回一个解码的 string 类型
buf.toString([encoding],[length])

// 返回一个 JSON 表示的 Buffer 实例，JSON.stringify 将会默认调用来字符串序列化这个 Buffer 实例
buf.toJSON()

// 返回一个新的 buffer，这个 buffer 和老的 buffer 引用相同的内存地址
buf.slice([start],[end])

// 进行 buffer 的拷贝，拷贝不会影响老的 buffer
buf.copy(targetBuffer,[targetStart],[sourceStart],[sourceEnd])
```

Buffer 的静态方法：

```JavaScript
Buffer.isBuffer(buf);              // 判断是不是 Buffer
Buffer.byteLength(str);                 // 获取字节长度，第二个参数为字符集，默认 utf8
Buffer.concat(list[, totalLength])     // Buffer 的拼接
```

## 三 Buffer 的转换

### 2.1 字符串转 Buffer

Buffer 对象可以与字符串之间相互转换，如下所示：

```js

```

一个 Buffer 对象可以存储不同编码类型的字符串转码的值，调用 write() 方法可以实现该目标，代码如下：

```js
buf.write(string, [offset], [length], [encoding]);
```

由于可以不断写入内容到 Buffer 对象中，并且每次写入可以指定编码，所以 Buffer 对象中可以存在多种编码转换后的内容，需要小心的是，每种编码所用的字节长度不同，将 buffer 反转回字符串时需要谨慎处理。

### 2.2 Buffer 转字符串

```js
buf.toString([encoding], [start], [end]);
```

### 2.3 Buffer 不支持的编码类型

Node 的 Buffer 不支持中国的 GBK，GB2312，BUG-5 等编码格式。判断 Buffer 是否支持该编码格式：

```js
Buffer.isEncoding(encodibg);        // 返回 true、false
```

对于不支持的编码格式，Node 有第三方模块如 iconv 和 iconv-liten。

## 三 Buffer 乱码

### 3.1 乱码的产生

在 Buffer 使用场景中，通常是以一段一段的方式传输，常见从输入流中读取内容的示例如下：

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

上述代码在读取全英文格式内容时，不会有任何问题，但是一旦输入流中存在宽字节编码，就会产生乱码问题。问题来自于 `data += chunk`，该句隐藏了 `toString()` 操作，其内部等价于：

```js
data = data.toString() + chunk.toString();
```

下面模拟宽字节文字读取场景：

```js
var fs = require('fs');

var buf = Buffer.from("白银之手骑士团");

// <Buffer e7 99 bd e9 93 b6 e4 b9 8b e6 89 8b e9 aa 91 e5 a3 ab e5 9b a2 ef bc 81>
console.log("buf:", buf);
console.log("buf.length:", buf.length);                        // 21
console.log("start:", buf.toString("UTF-8", 0, 3));             // 白  e7 99 bd
console.log("start:", buf.toString("UTF-8", 3, 6));             // 银  e9 93 b6
console.log("start:", buf.toString("UTF-8", 6, 9));             // 之  e4 b9 8b，e6 89 8b，e9 aa 91，e5 a3 ab，e5 9b a2，ef bc 81

var data = "";
var rs = fs.createReadStream("./demo.txt", {highWaterMark: 4});
rs.on("data", function(chunk) {
    data += chunk;
});
rs.on("end", function(){
    console.log("流式读取：", data);                        // 白�����手骑�����
});
```

在上述案例中，每 3 个长度能够读取到一个汉字，但是在使用流式读取时，每 4 个长度读取一次，在第一读取时，就会读取到多余的数据了，也即输出了  `白�`，在第 4 次读取时，正好又读取了原始数据的存储要求，输出了 `�手`，依次类推。

### 3.2 乱码解决

流式读取可以设置编码：

```js
var rs = fs.createReadStream("./demo.txt", {highWaterMark: 4});
rs.setEncoding('utf8');
```

此时程序就能正常输出数据！但是这并不是直接说明了输出没有收到 Buffer 大小的影响。在实际运行过程中，无论如何设置编码，触发的 data 事件次数都仍然是相同的。但是在每次 data 事件都会额外通过一个 decoder 对象对 Buffer 进行转换到字符串的解码，然后传递给调用者。而这个 decoder 对象正是 `setEncoding()` 方法时在可读流对象内部设置的。此时 data 收到的不再是原始的 Buffer 对象。decoder 对象会被未转码的部分保留在 StringDecode 实例内部，再下一次 write 的时候，会将上次的剩余字节和后续的新读入的字节进行组合！

setEncding 只能解决 UTF-8，Base64 等带来的编码问题，没有从根本上解决问题。正确的 Buffer 拼接方式应该是用一个数组来存储接收到的所有 Buffer 片段并记录下所有的片段总长度，然后调用 Buffer.concat() 方法生成一个合并的 Buffer 对象。

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

Buffer.concat() 方法封装了从小 Buffer 对象向大 Buffer 对象复制过程：

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

## 一 Buffer 内的内存分配

### 1.0 Buffer 内存分配概念

Buffer 的内存申请并不是靠 V8，而是 C++ 层面原生实现的，采用了 slab 分配机制（一种动态内存管理机制）。该机制简单来说，就是一块申请好的固定大小的内存区域，具有如下状态：

- full：完全分配状态
- partial：部分分配状态
- empty：没有被分配状态

Node 以 8KB 位接线区分 Buffer 是大对象还是小对象，这个 8KB 就是每个 slab 的大小，Node 以其为基础单元进行内存分配。

### 1.1 分配小 Buffer 对象

在 `new Buffer(1024)` 时，此时构造对象小于 8KB，Node 会检查一个对象 pool 有没有创建，没有则会创建。创建后，处于分配状态的 slab 单元都会指向它：

![buffer 原理 1](../images/node/buffer-01.svg)

在上图中，这个 slab 处于 empty 状态

```js
var pool;
if (!pool || pool.length - pool.used < this.length) alloclPool();

function allocPool(){                           // 申请内存函数
    pool = new SlowBuffer(Buffer.poolSize);     // 将新申请的 SlowBuffer 对象指向 pool
    pool.used = 0;
}
```

构造完 pool 后，当前 Buffer 对象的 parent 属性也会指向该 slab，并记录下从这个 slab 的哪个位置开始开始使用，slab 对象自身也会记录被使用了多少字节：

```js
    this.parent = pool;
    this.offset = pool.used;
    pool.used += this.length;
    if (pool.used & 7) pool.used = (pool.used + 8) & ~ 7
```

![buffer 原理 2](../images/node/buffer-02.svg)

此时 slab 状态为 partial，如果这时候再次创建一个 Buffer，构造中会检查这个 slab 的剩余空间是否足够，如果足够，则使用剩余空间，并更新 slab 分配情况。如下所示，再次 `new Buffer(3000)` ：

![buffer 原理 3](../images/node/buffer-03.svg)

如果 slab 剩余空间不够，则会构造新的 slab。

某些情况下会造成 slab 剩余空间的浪费，第一次构造 1 字节 Buffer，第二次构造 8192 字节 Buffer，第二次分配时 slab 剩余空间不够，就会创建新的 slab，则刚才的 slab 将会被一个 1 字节的 Buffer 对象独占，也就会造成一个极端情况：这个 1 字节的 Buffer 一直没有释放，相应的这 8KB 也一直没有释放。

### 1.2 分配大 Buffer 对象

超过 8KB 的大 Buffer 对象，将会直接分配一个 SlowBuffer 对象作为 slab 单元，这个 slab 单元会被这个大 Buffer 对象独占：

```js
// Big buffer, just alloc one
this.parent = new SlowBuffer(this.length);          // SlowBuffer 是 C++ 模块
this.offset = 0;
```

## 二 Buffer 的应用

由于 Buffer 能在有效对数据进行拆分传输，在文件 I/O，网络 I/O 中占据很大使用场合。网络传输中的字符串一般都会转换为 Buffer，以二进制方式进行传输，这种转换在网络中的饿请求响应中几乎时时刻刻在发生。

如果预先将普通的内容转换为 Buffer 对象，就可以有效减少服务器资源浪费。实践中的案例如动静分离，静态内容往往会先转换为 Buffer 格式，这样浏览器在访问网页内容时，服务器就无需转换再传输了！
