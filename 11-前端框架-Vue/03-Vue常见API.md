## 一 常见选项属性

### 1.1 计算属性 computed

计算属性可以简化直接书写表达式带来的代码繁杂性。比如在实际开发中，后端返回的数据并不符合前端展示的需要，需要对这些数据进行处理：

```html
<div>{{msg.toUpperCase()}}</div>
```

在 Vue 中，为了简化、美观上述操作，给出了计算属性，用来做数据的处理：

```js
<div>{{ getMsg }}</div>; // 这里使用 getMsg，而不再是直接使用 msg与表达式

new Vue({
  el: "#app",
  data: {
    msg: "hello",
  },
  computed: {
    getMsg() {
      return this.msg.toUpperCase();
    },
  },
});
```

我们也可以通过方法来实现上述操作：

```js
    <div>{{getMsg()}}</div>               // 方法必须加 () 执行调用

    new Vue({
        el: "#app",
        data: {
            msg: "hello"
        },
        methods: {                      // 这里是方法，而不是计算属性
            getMsg(){
                return this.msg.toUpperCase()
            }
        }
    })
```

注意：计算属性依赖的状态（数据）改变了，也会重新计算一遍，所以也会重新渲染新的数据。但是 methods 方法也可以做到，为什么还需要计算属性？

```js
// 视图中多个地方都需要同样的数据时，使用计算属性性能更高，如下所示两处地方，计算属性只会计算一次！方法个地方都会调用一次！！
// 即：计算属性会将计算的结果保存在内存缓存中，视图需要的数据直接从缓存中获取，只有依赖的数据改变才会重新计算一次
<div>{{getMsg}}</div>
<div>{{getMsg}}</div>
```

贴士：计算属性还可以写在视图的指令中，如`<li v-for="data in getArr">`

### 1.5 侦听器 watch

侦听器用来监听数据，数据一旦发生变化，就会触发侦听器绑定的方法。侦听器一般用于处理一些异步、开销较大的操作。

```js
new Vue({
  el: "#app",
  data: {
    msg: "hello",
  },
  watch: {
    ageChange: function (val) {},
  },
});
```

### 1.3 过滤器 Vue.fliter()

过滤器可以对传递过来的值进行过滤：

```html
<div id="app">{{count | myFormat(3,1) }}</div>

<script>
  Vue.filter("myFormat", function (count, num1, num2) {
    return count + num1 - num2;
  });

  new Vue({
    el: "#app",
    data: {
      count: 2,
    },
  });
</script>
```

如上所示，过滤器由管道符 `|` 表示，也支持多次调用：`{{count | myFormat(3,1) | test }} `

过滤器也有自己的作用域，使用 `Vue.filter()` 创建的是全局过滤器，Vue 构造函数中也可以传入私有过滤器属性 filter：

```js
// 私有过滤器
new Vue({
    filters:{
        myFormat: funtion(){}
    }
});
```

如果出现了同名过滤器，将会依据就近原则调用（私有为准）

### 1.4 自定义指令

directive() 用来定义全局的指令。

示例：定义一个全局指令：v-focus，用于获取焦点

```js
<input v-focus>

Vue.directive('focus', {
    bind: function(el){           //每当指令绑定到元素上的时候，会立即执行bind函数，且只执行一次，一般绑定样式相关操作
        // el.focus()                // 此时元素还未插入，focus没有作用
    },
    inserted: function(el){      // 元素插入DOM时，会执行该函数，只会触发一次
        el.focus();
    },
    updated: function(el){}       // 当VNode更新时，执行，可能会触发多次
});
```

定义一个私有指令：

```js
new Vue({
  directives: {
    focus: {},
  },
});
```

也可以直接简写为：

```js
directives: {
    'focus': function(el, bingding){}       // 相当于同时绑定到了bind和inserted
}
```

### 1.5 事件修饰符

按键修饰符使用方式：

```js
<input type="text" @keyup.enter="add">
```

按键修饰符有：

```
.enter
.tab
.delete
.esc
.space
.up
.down
.left
.right
```

按键修饰符也可以自定义，比如 113 键（F2）按下去的时候执行 add 方法：

```js
<input type="text" @keyup.113="add">

// 但是键盘码不方便记忆，可以自定义：
<input type="text" @keyup.f2="add">
Vue.config.keyCode.f2 = 250; // 自定义按键修饰符
```

表单域修饰符：

```
v-model.number：转化为数值
v-model.trim：去掉首尾空格
v-model.lazy：将input事件切换为change事件
```
