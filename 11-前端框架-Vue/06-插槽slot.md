## 一 插槽 slot 的作用

假设定义了一个组件 comp，在视图中还要额外添加信息：
```js
<comp>
    <div>新增内容<div>
</comp>
```
此时添加的div信息是不显示的，因为Vue在new的时候，去查找comp的template内容，从而直接对视图中的 `comp` 标签进行了替换。  

现在有这样的需求：comp组件内部需要依据数据列表循环显示多个数据，slot是最为快捷的实现方式：
```html
    <div id="app">

        <comp>
            <list></list>
        </comp>

    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    
    <script>

        Vue.component("comp",{
            template: `
                <div>
                    <p>comp内容：</>
                    <slot></slot>
                </div>`
        });

        Vue.component("list", {
            template: `
                <ul>
                    <li>1</li>
                    <li>2</li>
                    <li>3</li>
                </ul>`
        })

        new Vue({
            el: "#app",
            data:{
            }
        });
    </script>
```

