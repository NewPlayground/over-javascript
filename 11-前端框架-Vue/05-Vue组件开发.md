## 一 Vue组件创建

### 1.0 Vue的组件化

Vue的组件化是指从UI界面角度出发，合理重用UI组件。  

使用 `Vue.conponent()`函数可以创建全局组件，使用 `components`属性可以创建私有组件。  

这里有两个注意点：
- 模板中的元素必须只有一个根元素（外围包裹元素）
- 如果组件名称没有使用驼峰式命名，则该名称可以直接使用。若使用了驼峰命名，在引用组件的时候，需要把大写的驼峰改为小写的字母，同时两个单词之间使用‘-’连接。  

### 1.1 创建全局组件

```html
    <div id="app">
        <my-com1></my-com1>              <!-- 驼峰需要使用-连接 -->
        <my-com2></my-com2>              <!-- 驼峰需要使用-连接 -->
    </div>

    <script>

        // 创建全局组件方式一
        Vue.component('myCom1', Vue.extend({
            template: '<h3>全局组件1</h3>'        
        }));

        // 创建全局组件方式二
        Vue.component('myCom2', {
            template: '<h3>全局组件2</h3>'        
        });                                      

        new Vue({
            el: "#app"
        });
    </script>
```

### 1.2 创建私有组件

```html
    <div id="app">
        <my-com3></my-com3>                   
    </div>

    <script>
        new Vue({
            el: "#app",
            components: {
                myCom3: {
                    template: '<h3>私有组件</h3>'
                }
            }
        });
    </script>
```

### 1.3 引用template

template 属性也可以直接引用其他已经定义好的template。
```html
    <div id="app">
        <my-com3></my-com3>                     
    </div>

    <template id="tmp1">
        <h3>组件</h3>
    </template>

    <script>
        Vue.component('myCom3', {
            template: '#tmp1'
        })

        new Vue({
            el: "#app"
        });
    </script>
```

## 二 组件切换-动态组件

除了可以使用自定义true/false方式来切换组件外，vue本身也提供了组件切换机制：在component 里展示对应名称的组件

```html
    <div id="app">
        <span @click="who='com1'">显示组件1</span>
        <span @click="who='com2'">显示组件2</span>
        <component :is="who"></component>
    </div>

    <script>
        Vue.component('com1', {
            template: '<h3>111</h3>'
        });

        Vue.component('com2', {
            template: '<h3>222</h3>'
        });

        let vm = new Vue({
            el: "#app",
            data:{
                who:'com1'
            }
        });
    </script>
```

## 三 组件内的数据存储

组件内也可以有data属性，但是该属性是个函数，且必须返回一个对象：
```html
    <div id="app">
        <my-com></my-com>                     
    </div>

    <script>
        new Vue({
            el: "#app",
            components: {
                myCom: {
                    template: '<h3>{{msg}}</h3>',
                    data: function(){
                        return {
                            msg: '组件内部数据'
                        }
                    }
                }
            }
        });
    </script>
```

组件之间数据是不能直接共享的，比如 data、method、computed等，必须通过一系列的机制来进行传递。  

## 四 父子组件通信

### 4.1 父组件向子组件传递数据

传递数据操作步骤：
- 第一步：父组件中使用 v-bind 绑定要传递的数据
- 第二步：子组件在 props 属性中定义传递过来的数据

```html
    <div id="app">

        <father></father>

    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    
    <script>

        // 定义父组件：将父组件数据 fatherMsg 绑定到了子组件属性 sonMsg 上，将父组件方法 fatherShow 绑定到子组件 sonClick上
        Vue.component("father", {
            // 父组件将数据绑定在子组件属性上
            template: `                 
                <div>
                    <p>父组件</p>
                    <son :sonMsg="fatherMsg"></son>                     
                </div>`,
            data: function(){
                return {
                    "fatherMsg": "姓氏为李"                                 
                }
            }
        })

        // 定义子组件：在props中接收父组件数据，在template中使用
        Vue.component("son", {
            data: function(){
                return {
                    name: "xxx"
                }
            },
            props: ["sonMsg"],                                          
            template: `<div>子组件，接收到的数据为：{{sonMsg}}</div>`, 
        })

        new Vue({
            el: "#app",
            data:{}
        })

    </script>
```

data与props对比：
- data中一般放置请求到的数据，props里面放置的是传递过来的数据。
- data中的数据都是可读写的，而props中的数据是只读的

### 4.2 子组件向父组件传数据

子组件要传递数据给父组件，需要利用函数传递。也可以变相的理解为父组件将自己的方法传递给了子组件，子组件在调用方法时，将自身的数据传递给了父组件。  

传递方法操作步骤：
- 第一步：父组件中使用 v-on 绑定要传递的方法
- 第二步：子组件在 methods 中使用 $emit 来触发父组件方法

```html
    <div id="app">

        <father></father>

    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    
    <script>

        // 定义父组件：将父组件数据 fatherMsg 绑定到了子组件属性 sonMsg 上，将父组件方法 fatherShow 绑定到子组件 sonClick上
        Vue.component("father", {
            // 父组件将数据绑定在子组件属性上
            template: `                 
                <div>
                    <p>父组件</p>
                    <son @sonClick="fatherShow"></son>                     
                </div>`,
            data: function(){
                return {
                    "fatherMsg": "姓氏为李"                                 
                }
            },
            methods: {
                fatherShow: function(arg){
                    alert(arg);
                }
            }    
        })

        // 定义子组件：在props中接收父组件数据，在template中使用
        Vue.component("son", {
            data: function(){
                return {
                    name: "xxx"
                }
            },
            props: ["sonMsg"],                                          
            template: `<div @click="sonShow">子组件</div>`, 
            methods: {
                sonShow: function(){
                    this.$emit('sonClick', this.name);
                }
            } 
        })

        new Vue({
            el: "#app",
            data:{}
        })

    </script>
```

### 4.3 数据类型校验

数据在传递时还可以提前进行数据类型校验，以提升应用的安全性：
```js
props: {
    sonMsg: String,
    siMan: Boolean
}, 
```

### 4.4 ref实现父子通信


ref 可以获取到原生节点：
```html
    <div id="app">

        <input type="text" ref="mytext">
        <button @click="add">点击</button>

    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    
    <script>
        new Vue({
            el: "#app",
            data:{},
            methods: {
                add(){
                    alert(this.$refs.mytext.value);
                }
            }
        })
    </script>
```

ref如果放置在组件上，则获取到的是组件对象，这时候就可以实现组件通信!!  

ref通信方式需要在视图结构上添加ref，不太推荐。  

## 五 非父子关系组件间通信

组件之间一般都存在着联系，一般都可以通过祖先组件等进行互相通信，但是该方式由于需要层层跨越，实现起来比较麻烦。  

Vue为非父子关系组件提供了事件总线机制来实现通信。事件总线独立于所有组件之外，其本质就是一个空的Vue实例:
```html
    <div id="app">

        <publish></publish>
        <subcribe></subcribe>

    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    
    <script>

        let bus = new Vue();            // 新建中央事件总线

        Vue.component("publish", {
            template: `
                <div>
                    <input type="text" />
                    <button @click="handleClick()">发布</button>    
                </div>`,
            methods: {
                handleClick(){
                    bus.$emit("msg", "helloworld");
                }
            }
        });

        Vue.component("subcribe", {
            template: `
                <div>
                    数据为： 
                </div>`,
            mounted(){      // 这里让组件尽早的订阅总线消息
                bus.$on("msg", (data)=>{
                    alert(data);
                });
            }
        });

        let app = new Vue({
            el: "#app",
            data:{}
        })

    </script>
```

## 六 动态组件

