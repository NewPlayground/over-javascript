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
