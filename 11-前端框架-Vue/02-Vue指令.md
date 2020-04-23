## 一 指令概念

类似 `v-for` 这样在Vue中具备特殊作用的标识即为Vue指令，其本质是自定义属性。

## 二 常见指令

### 2.1 v-cloak 解决闪烁问题

插值表达式具有闪烁问题：当网速较慢时，`{{}}`的原始内容会被显示在浏览器上，过一段很小的内容才会被替换为真实的data内的数据。这样用户体验就会变得很差。  

`v-cloak`指令可以解决上述问题：
```js
// css
<style>
    [v-cloak]{
        display: none;
    }
</style>

// html
<div v-cloak>{{msg}}</div>
```

当vue没有请求过来的时候，标签上的 v-cloak 属性对应的css样式起作用（隐藏掉了）；vue请求过来之后，这个属性就移出了，就可以显示对应的内容，不会出现闪烁了。

### 2.2 v-text v-html v-pre 数据绑定

`v-text`，`v-html`，`v-pre`的作用与`{{}}`作用一样，都可以用来插入数据，且没有闪烁问题：
```html
    <div class="div" v-text="msg">
    </div>

    <script>
        new Vue({
            el: '.div',
            data: {
                msg: "hello "
            }
        }) 
    </script>
```

注意：
- `v-text`填充文本，不会出现闪烁问题
- `v-html`填充html文本，能够额外将数据中的标签文本解析出来，所以很容易引起安全问题（XSS攻击）。
- `v-pre`填充原始数据，作用是跳过编译，直接显示原始文本。比如要在界面中显示 `{{}}` 这2个括号，就需要该指令

### 2.3 v-model 双向数据绑定

```js
    <!-- 单向绑定：无法修改界面 -->
    <h4>{{msg}}</h4>

    <!-- 单向绑定：修改input内容不会修改data内容 -->
    <input type="text" :value="msg">

    <!-- 双向绑定：修改input内容不会修改data内容 -->
    <input type="text" v-model="msg">
```

注意：
- v-model一般用于表单元素中
- v-once作用：显示内容后不再具有数据响应功能


### 2.4 v-bind 绑定属性

```html
<input type="button" v-bind:title="mytitle">

<script>
    new Vue({
        el: "#app",
        data: {
            mytitle: "自定义title"
        }
    })
</script>
```

注意：
- v-bind可以省略，直接写冒号即可 `:title="mytitle"`
- v-bind中可以使用表达式： `:title="mytitle + '123'"`

### 2.5 v-on 绑定事件

```html
<input type="button" v-on:click="show">

<script>
    new Vue({
        el: "#app",
        data: {
            mytitle: "自定义title"
        },
        methods: {                          // 用于定义方法
            show: function(){
                alert(this.mytitle);        // this代表new出来的vm实例
            }
        }
    })
</script>
```

v-on可以缩写为@，绑定的事件函数可以写函数名，也可以书写函数调用：
```
@click='do'                         // 该方式默认会携带事件对象，do函数的第一个参数就是事件对象
@click='do("hello", $event)'        // 最后一个参为事件对象，并且只有显式传递才能获取到
```

开发者可以在事件处理函数中对事件进行阻止冒泡、阻止默认事件等操作，但是vue也提供了更简便的工具-事件修饰符：
- stop：阻止冒泡，所有冒泡行为都被阻止
- self：阻止冒泡，只阻止了自己身上的冒泡行为
- prevent：阻止默认事件
- capture：添加事件侦听器时使用事件捕获模式
- self：只有当事件在该元素本身（非子元素）触发时触发回调
- once：事件只触发一次

一些特殊标签也拥有自身专属的事件修饰符，如按键修饰符：`@keyup.enter=''`等。  

事件修饰符是可以串联写的：如：`@click.prevent.once="clickHandler"`。  

### 2.6 v-for  循环列表

当在组件中使用v-for时，key现在是必须的，每次for循环的时候，通过指定的key来标识唯一身份：
```js
<div id="app">
    <h2 v-for="item in arr">{{item}}</h2>
    <h2 v-for="item in 5">{{item}}</h2>         <!-- 从1开始 -->
    <h5 v-for="(item, i) in arr">{{i}}</h5>
</div>

//数据为： arr: ["a","b","c"]
```

注意：2.20版本以上的vue中，key是必须的

### 1.7 v-if与v-show 条件渲染

v-if 每次都会重新创建或移除元素，切换性能消耗高。v-show只是切换display:none的样式，初始渲染消耗高。  

```html

     <!-- 每次都会重新创建或移除元素，切换性能消耗高 -->
    <h2 v-if="flag">test1</h2>

    <!-- 只是切换display:none的样式，初始渲染消耗高  -->
    <h2 v-show="flag">test1</h2>    

    <button @click="flag=!flag">点击</button>
```

**如果元素涉及到频繁的切换推荐使用v-show；**  

**如果元素可能永远也不会被显示出来被用户看到推荐使用v-if；**