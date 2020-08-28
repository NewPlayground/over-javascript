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

  // 格式化传入的数据为name=fox&age=18这样的格式
  var formatStr = "";
  for (let item in option.data) {
    formatStr += item; // 属性名
    formatStr += "="; //等号
    formatStr += option.data[item]; //属性值
    formatStr += "&"; //分隔符
  }
  // 去除最后一个&
  formatStr = formatStr.slice(0, -1);

  // open方法 如果是get方法,那么url之后需要添加数据
  if (option.method == "get") {
    option.url = option.url + "?" + formatStr;
    option.params = null;
  }

  xmlRequest.send(option.params);

  xmlRequest.onreadystatechange = function () {
    // 这步为判断服务器是否正确响应
    if (xhr.readyState == 4 && xhr.status == 200) {
      option.success(ajax获取的数据);
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
