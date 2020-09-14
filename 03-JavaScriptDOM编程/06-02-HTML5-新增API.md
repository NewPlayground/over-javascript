## 一 Web 存储

传统 web 存储方式以 document.cookie 来进行，但是其存储大小只有 4k 左右，并且解析复杂。

在 HTML5 规范中使用 Storage 存储，分别有两种存储方式：

```
会话存储：
    设置：window.sessionStorage.setItem("name","lisi");
    获取：window.sessionStorage.getItem("name");
    删除：window.sessionStorage.removeItem("name");
    清除：window.sessionStorage.clear();
    说明：生命周期为关闭浏览器窗口,在同一个窗口下数据可以共享，可存储大约5M

本地存储：
    获取：window.localStorage
    获取：window.localStorage.getItem("name");
    删除：window.localStorage.removeItem("name");
    清除：window.localStorage.clear();
    说明：永久生效，除非手动删除,可以多窗口共享,可存储大约20M
```

贴士：一些其他网络存储方案 WebSQL、IndexDB 已经被 w3c 放弃了。

## 二 历史管理

在以前，通过 hashchange 事件，可以知道 URL 的参数什么时候发生了变化。H5 额外提供了 history 状态管理对象。

通过状态管理，可以改变浏览器访问的 URL：

```js
history.pushState({ name: "Nicholas" }, "Nicholas' page", "nicholas.html");
```

要更新当前状态，可以调用 replaceState()，传入的参数与 pushState()的前两个参数相同。调用这个方法不会在历史状态栈中创建新状态，只会重写当前状态。

```js
history.replaceState({ name: "Greg" }, "Greg's page");
```

## 三 地理位置

HTML5 提供了 Geolocation 地理位置支持。目前大多数浏览器都可以支持（IE9+）。  
出于安全考虑，部分最新的浏览器只允许通过 HTTPS 协议使用 GeolocationAPI，在 http 协议下回跑出异常，当然本地开发不会有问题。  
访问地理位置方式：

```javascript
navigator.geolocation; //会提示用户是否允许获取该API的权限
```

示例代码：

```javascript
//判断浏览器是否兼容代码
if (navigator.geolocation) {
  navigator.geolocation.getCurrentPosition(
    function success(position) {
      console.log("获取位置成功，当前位置为：", position);
    },
    function errror(error) {
      console.log("获取位置错误，错误为：", error);
    },
    {
      timeout: 10000,
    }
  );
} else {
  //没有获取到地理位置执行的操作
}
```

获取位置成功后，返回的 position 参数对象，包含下列信息：

```
timestamp： 获取位置时的时间戳
ciirds：    坐标信息对象，内部包含：latitude（纬度），longitude（经度），accuracy（坐标精度，单位为米）
```

获取位置失败后，返回的错误对象，常见为：

```
code:   错误标识，1为用户拒绝分享位置 2为获取用户位置失败 3为获取超时 0为其他错误
```

传入的位置参数对象：

```
timeout:允许获取位置的超时时间，单位为毫秒
enableHighAccuracy： 布尔值是否获取高精度信息
maximumAge：用户位置信息缓存的最大时间，默认为0，单位是毫秒
```

当用户位置发生变化时，可以通过 watchPostion 方法监听用户的位置信息，该方法和 getCurrentPosition 使用方式一致：

```javascript
let watchID = navigator.geolocation.watchPosition(success, error, option);
//取消监听
navigator.geolocation.clearWatch(watchID);
```

## 四 检测用户网络状态

我们可以通过 window.onLine 来检测，用户当前的网络状况，返回一个布尔值

```js
window.online; //用户网络连接时被调用
window.offline; //用户网络断开时被调用
window.addEventListener("online", function () {
  alert("已经建立了网络连接");
});
window.addEventListener("offline", function () {
  alert("已经失去了网络连接");
});
```

## 五 离线应用

离线应用可以帮助用户在没有网络时使用 web 程序，H5 的离线功能包含：离线资源缓存、在线状态监测、本地数据存储等。  
离线 web 应用比普通的 web 应用多了一个描述文件，该文件用来列出需要缓存和永不缓存的资源，描述文件的扩展名为：.manifest 或者 .appcache(推荐使用)。  
首先需要在项目目录下创建 offline.appcache 文件：

```
CACHE MANIFEST      # 说明这是离线应用描述文件
CACHE:              # 会被缓存的资源列表
index.html
index.js
NETWORK:            # 总是从web获取的资源列表
test.js
```

html 文件需要添加如下配置：

```txt
<html lmanifest="./offline.appcache"></html>
```

## 一 跨文档通信

在过去，跨文档通信（跨源、跨窗口，cross-document messaging）往往是与服务端进行数据交互来实现的，并且需要借助轮询或者 Connect 技术来监听消息。

H5 提供了 PostMessages()方法 实现安全的跨源通信：

```js
// 参数一：消息体
// 参数二：消息来自哪个域
// 参数三：可选。是一串和message同时传递的Transferable对象，这些对象的所有权将被转译给消息的接收方，而发送乙方将不再保有所有权

var iframeWindow = document.getElementById("myframe").contentWindow;
iframeWindow.postMessage("A secret", "http://www.demo.com");
```

iframe 应用实例：

```txt
<button id="btn">点击发送消息给iframe</button>
<iframe src="http:127.0.0.1/iframe.html"></iframe>
<script>
  let btn = document.querySelector("#btn");
  let data = ["周一", "周二", "周五"];
  btn.onclick = function () {
    alert("执行发送数据给iframe？");
    window.parent.postMessage(data, "http:127.0.0.1/iframe.html");
  };
</script>
```

iframe 接受数据：

```javascript
    <script>
        window.addEventListener("data", e =>{
            console.log("origin=", e.origin);
            console.log("data=", e.data);
        });
    </script>
```

## 二 Ajax2.0 新特性

设置 HTTP 请求超时时间：

```javascript
xhr.timeout = 3000;
xhr.ontimeout = function (e) {};
```

使用 FormData 表单对象管理表单数据：

```javascript
let formData = new FormData();
formData.append("username", "zs");
formData.append("id", 2331);
xhr.send(formData);
```

ajax2.0 上传文件

```javascript
for (let i = 0; i < files.length; i++) {
  //files是一个选择文件的表单元素
  formData.append("files[]", files[i]);
}
```

全新的跨域请求，需要服务端配合。  
获取服务端的二进制数据：

```javascript
xhr.open("GET", "...");
xhr.responseType = "blob"; //表示服务器传回的数据是二进制对象
```

获取数据传输的进度信息

```javascript

```

## 三 Server Sent Event 服务器主动通信

像邮件这样的应用，服务端发送了邮件后，需要主动通知客户端。传统的做法是，客户端不断的去轮训服务端，这种做法十分不优雅！  
H5 提供了 Server Sent Event 技术解决上述问题：

- 简单轻量
- 单向数据传送（服务端向客户端传送）
- 基于 HTTP 协议
- 默认支持断线重连接
- 自定义发送数据类型

```javascript
if (typeof EventSource !== "undefined") {
  let source = new EventSource("/test");

  source.onmessage = function (e) {
    div.innerHTML = e.data;
  };
} else {
  alert("浏览器不支持服务器发送事件");
}
```

## 一 H5 原生拖放

### 1.1 拖放事件

在 H5 规范中，拖动元素，将依次触发：`dragstart`，`drag`，`dragend`三个事件。

当某个元素被拖动到一个有效的放置目标上时，将依次触发：`dragenter`，`dragover`，`dragleave/drop`三个事件。如果拖拽元素离开了目标元素位置，则触发 dragleav 事件，如果放置到了目标元素位置，则触发 drop 事件。

### 1.2 自定义放置目标

如果拖动元素经过不允许放置的元素，无论用户如何操作，都不会发生 drop 事件。不过，任何元素都可以被设置为放置目标元素。

设置元素可以放置方式是重写 dragenter、dragover 事件的默认行为：

```js
var droptarget = document.getElementById("droptarget");
EventUtil.addHandler(droptarget, "dragover", function (event) {
  EventUtil.preventDefault(event);
});

EventUtil.addHandler(droptarget, "dragenter", function (event) {
  EventUtil.preventDefault(event);
});
```

在 Firefox 中，若拖拽图像，则页面会转向图像文件，若拖拽文本，则会导致无效 URL 错误，所以这里需要取消 Firefox 的 drop 事件的默认行为，阻止其打开 URL：

```js
EventUtil.addHandler(droptarget, "drop", function (event) {
  EventUtil.preventDefault(event);
});
```

### 1.3 dataTransfer 对象

dataTransfer 对象是拖拽事件对象的属性，用于存储数据：

```js
//设置和接收文本数据
event.dataTransfer.setData("text", "some text");
var text = event.dataTransfer.getData("text");
//设置和接收 URL
event.dataTransfer.setData("URL", "http://www.wrox.com/");
var url = event.dataTransfer.getData("URL");
```

### 1.4 可拖动

默认情况下，图像、链接和文本是可以拖动的，也就是说，不用额外编写代码，用户就可以拖动它们。文本只有在被选中的情况下才能拖动，而图像和链接在任何时候都可以拖动。

HTML5 为所有 HTML 元素规定了一个 draggable 属性，表示元素是否可以拖动。图像和链接的 draggable 属性自动被设置成了 true，而其他元素这个属性的默认值都是 false。

## 一 多媒体

H5 增加了音频与视频两个多媒体标签，解决了长久以来以 Flash 为主的网页多媒体技术带来的性能困扰。

```js
<!-- 嵌入视频 -->
<video src="conference.mpg" id="myVideo">Video player not available.</video>
<!-- 嵌入音频 -->
<audio src="song.mp3" id="myAudio">Audio player not available.</audio>
```

poster 属性指定图像的 URI 可以在加载视频内容期间显示一幅图像。另外，如果标签中有 controls 属性，则意味着浏览器应该显示 UI 控件，以便用户直接操作媒体。位于开始和结束标签之间的任何内容都将作为后备内容，在浏览器不支持这两个媒体元素的情况下显示。

但是由于版权原因，不同浏览器支持不同的媒体来源，所以需要这样兼容书写：

```js
<!-- 嵌入视频 -->
<video id="myVideo">
<source src="conference.webm" type="video/webm; codecs='vp8, vorbis'">
<source src="conference.ogv" type="video/ogg; codecs='theora, vorbis'">
<source src="conference.mpg">
Video player not available.
</video>

<!-- 嵌入音频 -->
<audio id="myAudio">
<source src="song.ogg" type="audio/ogg">
<source src="song.mp3" type="audio/mpeg">
Audio player not available.
</audio>
```

对媒体资源的解码支持，也提供了相应 API：

```js
if (audio.canPlayType("audio/mpeg")) {
  //进一步处理
}
```

## 二 自定义多媒体播放器示例

```js
    <div class="mediaplayer">
        <div class="video">
            <video id="player" src="movie.mov" poster="mymovie.jpg" width="300" height="200">
                Video player not available.
            </video>
        </div>
        <div class="controls">
            <input type="button" value="Play" id="video-btn">
            <span id="curtime">0</span>/<span id="duration">0</span>
        </div>
    </div>

    <script>

        //取得元素的引用
        var player = document.getElementById("player"),
            btn = document.getElementById("video-btn"),
            curtime = document.getElementById("curtime"),
            duration = document.getElementById("duration");
        //更新播放时间
        duration.innerHTML = player.duration;
        //为按钮添加事件处理程序
        EventUtil.addHandler(btn, "click", function (event) {
            if (player.paused) {
                player.play();
                btn.value = "Pause";
            } else {
                player.pause();
                btn.value = "Play";
            }
        });
        //定时更新当前时间
        setInterval(function () {
            curtime.innerHTML = player.currentTime;
        }, 250);
    </script>
```

## 一 访问设备

### 1.1 访问摄像头

```
新标准：部分浏览器不支持，支持的浏览器有：Fifox36+（需要moz前缀），Chrome47+（需要webkit前缀）
navigator.getUserMedia
旧标准：目前已废除，支持的浏览器有：Edg12+，Firefox17+，Chrome21+，AndroidBrowser56+，Chrome for Android57+，UC11.4+
navigator.mediaDevices.getUserMedia
```

案例代码：

```txt
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <title></title>
    <style>
      #canvas,
      #video {
        float: left;
        margin-right: 10px;
        background: #fff;
      }
      .box {
        overflow: hidden;
        margin-bottom: 10px;
      }
    </style>
  </head>
  <body>
    <div class="box">
      <video id="video" width="400" height="300"></video>
      <canvas id="canvas"></canvas>
    </div>
    <button id="live">直播</button>
    <button id="snap">截图</button>
    <script>
      let video = document.getElementById("video");
      let canvas = document.getElementById("canvas");
      let ctx = canvas.getContext("2d");
      let width = video.width;
      let height = video.height;
      canvas.width = width;
      canvas.height = height;
      function liveVideo() {
        let URL = window.URL || window.webkitURL; // 获取到window.URL对象
        navigator.getUserMedia(
          {
            video: true,
          },
          function (stream) {
            video.src = URL.createObjectURL(stream); // 将获取到的视频流对象转换为地址
            video.play(); // 播放
            //点击截图
            document
              .getElementById("snap")
              .addEventListener("click", function () {
                ctx.drawImage(video, 0, 0, width, height);
                let url = canvas.toDataURL("image/png");
                document.getElementById("download").href = url;
              });
          },
          function (error) {
            console.log(error.name || error);
          }
        );
      }
      document.getElementById("live").addEventListener("click", function () {
        liveVideo();
      });
    </script>
  </body>
</html>
```

### 5.2 传感器与摇一摇实战

现代手机都内置了传感器，通过传感器，可以感知手机的方向和位置变化。  
手机方向变化和位置变化的解释：

```
X轴：左右横贯手机方向，当手机绕X轴旋转时移动方向称为Beta
Y轴：上下纵贯手机方向，时手机绕Y轴旋转时移动方向称为Gamma
X轴：垂直手机平面方向，当手机绕Z轴旋转时移动方向称为Alpha
```

方向事件 deviceorientation：设备方向发生变化时触发

```javascript
window.addEventListener("deviceorientation", orientationHandler, true);
```

回调函数 orientationHandler 在注册后，会被定时调用，并会受到一个 DeviceOrientationEvent 类型的参数，通过该茶树获取设备的方向信息，如下所示：

```
absolute：如果方向数据跟地球坐标系和设备坐标系有差异，则为true，如果方向设备由设备本身的坐标系提供，则为false
alpha：设备在该方向上的旋转角度，范围是0-360
beta：设备在该方向上的旋转角度，范围是0-360
gamma：设备在该方向上的旋转角度，范围是0-360
```

移动事件 devicemotion：设备位置发生变化时触发：

```javascript
window.addEventListener("devicemotion", motionHandler, true);
```

同样的，回调函数 motionHandler 被注册后，也会被定时调用，并收到一个 DevicemotionEvnent 的参数，该参数可以访问设备的方向和位置信息，参数如下：

```
acceleration：设备在XYZ三个轴方向上移动的距离，已抵消重力加速
accelerationIncludingGravity：设备在XYZ三个轴的方向上移动的距离，包含重力加速
rotationRate：设备在Alpha，Beta，Gamma三个方向旋转的角度
interval：从设备获取数据的频率（单位毫秒）
```

摇一摇案例：摇一摇可以理解为手机不低于已定的速度在移动。  
实现的方法是监听 devicemotion 事件后，判断设备在 XYZ 三个方向上的移动距离与前一次移动的距离差，并除以两次事件触发的时间差，即为移动设备移动的速度

```txt
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta
      name="viewport"
      content="width=device-width, initial-scale=1.0, minimum-scale=1.0, maximum-scale=1.0,user-scalable=no"
    />
    <title>陀螺仪实现摇一摇</title>
  </head>

  <body>
    <div>用力摇一摇你的手机</div>
    <script type="text/javascript">
      let SHAKE_SPEED_THRESHOLD = 300; // 摇动速度阈值
      let lastTime = 0; // 上次变化的时间
      let x = (y = z = lastX = lastY = lastZ = 0); // 位置变量初始化

      function motionHandler(evt) {
        let acceleration = evt.accelerationIncludingGravity; // 取得包含重力加速的位置信息
        let curTime = Date.now(); // 取得当前时间
        if (curTime - lastTime > 120) {
          // 判断
          let diffTime = curTime - lastTime; // 两次变化时间差
          lastTime = curTime; // 保存此次变化的时间
          x = acceleration.x;
          y = acceleration.y;
          z = acceleration.z;
          let speed =
            (Math.abs(x + y + z - lastX - lastY - lastZ) / diffTime) * 1000; // 计算速度
          if (speed > SHAKE_SPEED_THRESHOLD) {
            // 速度是否大于预设速度
            alert("你摇动了手机");
          }
          lastX = x; // 保存此次变化的位置x
          lastY = y; // 保存此次变化的位置y
          lastZ = z; // 保存此次变化的位置z
        }
      }
      if (window.DeviceMotionEvent) {
        window.addEventListener("devicemotion", motionHandler, false);
      } else {
        alert("您的设备不支持位置感应");
      }
    </script>
  </body>
</html>
```
