## 一 文件模块fs

API地址：<http://nodejs.cn/api/fs.html>

fs模块即：文件模块，是Node的核心模块，提供了操作文件的一些API。  

文件模块针对同一个业务提供了 异步、同步两种操作方式，比如读取文件：`readFile()  readFileSync()`。  

### 1.1 打开文件 open()

> fs.open(path,flags,[mode],callback);
>
> - path：要打开的文件的路径（注意相对路径要 ./开头，为了兼容Linux和Win）
> - flags：打开文件的方式  读/写
> - mode：设置文件的模式  读/写/执行 分别对应 4/2/2
> - callback：回调
>   - err：打开失败后保存的错误对象，如果成功，err为null
>   - fd：被打开文件的标识

案例：

```js
const fs = require('fs');
fs.open('./1.html', 'r', function (err, fd) {
    if (err) {
        console.log('err');
    } else {
        console.log('success');
        console.log(fd);    //输出 3
    }
});
```

### 1.2 读取文件 readFile

Node中读取文件一般使用fs.read()方法，该方法从一个特定的文件描述fd中读取数据。
> fs.read(fd,buffer,offset,length,position,callback);
>
> - fd：通过fs.open()方法返回的文件描述符
> - buffer：数据写入的缓冲区
> - offset：缓冲区写入的写入偏移量
> - length：要从文件中读取的字节数
> - position：文件读取的起始位置，如果为null，就会从当前文件指针的位置读取；
> - callback：有err，bytesRead，buffer三个参数，bytesRead表示读取的字节数，buffer为缓冲区对象。

案例：

```JavaScript
const fs = require('fs');
//可选参数-字符集：{'charset':'utf8'}
fs.readFile('./1.html', function (err, data) {
    if (err) {
            throw err;
        }
    console.log(data.toString());    //data是个Buffer
});
```

注意:readFile会将一个文件的内容全部读取到内存中，适用于体积较小的文本文件。

### 1.3 写入文件 writeFile

异步的将数据写入一个文件，如果文件不存在则创建，如果文件存在，则替换。data参数可以是一个string，也可以是一个buffer。
> fs.writeFile(filename,data,[options],callback);
> fs.appendFile()  该方法也可以将字符串或者缓冲区内容写入文件，

### 1.4 监听文件 watch

> fs.wathc(filename,[options],[listener]);
观察置顶路径的改变，filename可以是文件或者目录，返回的对象是：fs.FSWatcher。第二个参数为布尔值，默认为true，代表只要文件被监听，就继续执行。

```JavaScript
const fs = require('fs');
fs.watch('./1.html', function (ev, fn) {
    console.log(ev);
    if (fn) {
        console.log(fn + '发生了改变');
    } else {
        console.log('...');
    }
});
```

### 1.5 文件元信息 stat

> fs.stat(path,[callback]);

```JavaScript
const fs = require('fs');
fs.stat('./1.html', function (err, data) {
    console.log(data);  //输出一系列文件本身信息
});
```

### 1.6 其他常见API

> fs.appendFile(name,data,[options],callback);
将数据添加到文件末尾（文件不存在则创建）
> fs.unlink(path,callback);   删除一个文件
> fs.exists(path,callback);   判断文件/目录是否存在
> fs.rename(path1,path2,[callback]);重命名文件
> fs.chomd(path,mode,[callback]); 修改权限,如mode取值为：’777’
   获取文件元信息
> fs.mkdir([path,[mode],callback); 创建文件夹
> fs.readdir([path,callback);   读取文件夹
> fs.rmdir([path,callback);   删除文件夹

## 一 path模块

API地址：<http://nodejs.cn/api/path.html>

该API用户获取路径：

```JavaScript
const path = require('path');
let myurl = path.join(__dirname, 'username', '123');
console.log(myurl);     //输出类似这样的绝对路径 E:\ProjectWeb\Test\username\123
```

__dirname 和 path.dirname 的区别:

```js
console.log(__dirname);  // 指向被执行 js 文件的绝对路径,包含的有文件名称
console.log(path.dirname('/abc/www/abc.txt'));  // 只有路径，没有文件名称
```

获取路径的最后一部分：

```js
path.basename('/foo/bar/aaa.html')              // 得到的结果是： aaa.html

path.basename('/foo/bar/aaa.html', '.html')     // 得到的结果是： aaa
```

获取扩展名:

```js
// 如果后面只是个‘.’，那么得到的就是‘.’； 如果没有扩展名，得到的就是空
path.extname('index.html')                      // 得到的结果： ‘.html’
```

路径的格式化处理：

```js
    let obj = {
        root: 'd:\\',
        base: 'abc.txt',
        ext: '.txt',
        name: 'abc'
    }

    console.log(path.format(obj));       //  输出的结果：d:\abc.txt
```

路径字符串转成对象

```js
    console.log(path.parse(__filename));
   //  输出的结果：
    {
        root: '文件根路径',
        dir: '文件的全路径',
        base: '文件名称',
        ext: '扩展名',
        name: '文件名称'
    }
```

连接路径：

```js
path.join('/foo', 'bar', 'baz/asdf', 'quux', '..');
// 返回: '/foo/bar/baz/asdf'

path.join('foo', {}, 'bar');
// 抛出 'TypeError: Path must be a string. Receive


## 一 querystring模块

该模块有2个主要方法：
- querystring.parse()：将查询字符串反序列化为一个对象，类似JSON.parse()
- querystring.stringify()：将一个对象序列化为一个字符串对象，类似JSON.stringify()

```JavaScript
const querstring = require('querystring');
let str1 = 'username=lisi&password=123';
let obj1 = querstring.parse(str1);    //转换为了对象
let obj2 = {username: 'zs', password: '456'};
let str2 = querstring.stringify(obj2);  //重新转换为字符串
```

用户提交的网址一般包含一定的数据，比如 username=lisi 这样的参数，我们可以通过url.query获取到这些字符串数据，Node提供了querystring对象来对这个字符串数据进行实例化处理：

```JavaScript
let querystring = require('querystring');
urlStr = url.parse(req.url);
console.log(querystring.parse(urlStr));
```
