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
        let vm = new Vue({
            el: "#app"
        });
    </script>
```

## 二 组件切换

除了可以使用自定义true/false方式来切换组件外，vue本身也提供了组件切换机制：在component 里展示对应名称的组件

```html
    <div id="app">
        <span @click="comName='com1'">显示组件com1</span>
        <span @click="comName='com2'">显示组件com2</span>
        <component :is="'comName'"></component>
    </div>

    <script>
        Vue.component('com1', {
            template: '<h3>111</h3>'
        });

        Vue.component('com2', {
            template: '<h3>222</h3>'
        });

        let vm = new Vue({
            el: "#app"
            data:{
                comName:'com1'
            }
        });
    </script>
```


## 三 组件内的数据存储

组件内也可以有data属性，但是该属性是个函数，且必须返回一个对象：
```html
    <div id="app">
        <my-com4></my-com4>                     
    </div>

    <script>
        new Vue({
            el: "#app",
            components: {
                myCom4: {
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

## 四 组件间传值

### 4.1 父组件向子组件传值

**2.3.1 传值的步骤**

1、父组件，可以在引用子组件的时候，通过属性绑定（v-bind:）的形式，把需要传递给子组件的数据，以属性绑定的形式，传递到子组件内部，供子组件使用。

2、把父组件传递过来的属性，先在props数组中定义一下才能使用这个属性值（传递过来的数据）

```html
    <div id="app">
        <!-- 先通过 v-bind 绑定数据 -->
        <son v-bind:parent-msg="msg"></son>     
    </div>

    <script>
        new Vue({
            el: "#app",
            data: {
                msg: "你好..."
            },
            components: {
                son: {
                    props: [    // 父组件传递过来的数据需要在这里再次定义
                        'parentMsg'    // 下滑线对应大写     
                    ],
                    template: '<h4>子组件得到的父组件数据:{{parentMsg}}</h4>'
                }
            }
        });
    </script>
```

data与props对比：
- data中一般放置请求到的数据，props里面放置的是传递过来的数据。
- data中的数据都是可读写的，而props中的数据是只读的

### 4.2 父组件向子组件传递方法

```html
    <div id="app">
        <son @sonfunc="show"></son>
    </div>

    <script>

        let vm = new Vue({
            el: "#app",
            data: {
                msg: "father..."
            },
            methods: {
                show: function(){
                    console.log("show...")
                }
            },
            components: {
                son: {
                    props: [            // 父组件通过属性传递过来的数据需要在这里再次定义才能使用
                        'parentMsg'     // 下滑线对应大写     
                    ],
                    methods: {
                        myclick: function(){
                            this.$emit('sonfunc');
                        }
                    },
                    template: '<button @click="myclick">点击触发父组件传递过来的事件</button>'
                }
            }
        });

    </script>
```

父组件向子组件传递方法，使用的是事件绑定机制（v-on）；自定义了一个事件属性sonfunc，父组件的方法传递给此属性。

### 4.3 子组件向父组件传数据

```html
<div id="app">
    <son @sonfunc="show"></son>
</div>

<script>

    let vm = new Vue({
        el: "#app",
        data: {
            arr: ""
        },
        methods: {
            show: function(arg){
                console.log(arg);
                this.arr = arg;
            }
        },
        components: {
            son: {
                data(){
                    return {
                        sonArr:[
                            {
                                id:1,
                                name:'aa'
                            },
                            {
                                id:2,
                                name:'bb'
                            }
                        ]
                    }
                }
                template: '<button @click="myclick">点击触发父组件传递过来的事件</button>'

                methods: {
                    myclick: function(){
                        this.$emit('sonfunc',this.sonArr);
                    }
                },
            }
        }
    });

</script>
```
