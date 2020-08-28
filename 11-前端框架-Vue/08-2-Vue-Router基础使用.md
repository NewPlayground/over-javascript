## 一 路由常见配置

### 1.1 路由模式

路由模式有两种：

- hash 模式：默认的 vue 路由模式，地址为需要有#号："#/home"
- history 模式

如下所示：

```js
let router = new VueRouter({
  mode: "history", // 默认是hash，此时设置为history模式
  routes: [
    {
      path: "/home",
      component: Home,
    },
  ],
});
```

### 1.2 路由别名

路由中的 name 属性是个可选项，作用是给路由起个名字。

name 的使用场景一：

```js
let router = new VueRouter({
  routes: [
    {
      path: "/home",
      component: Home,
      name: "Home", // 可选项
      alias: "/index", // 别名：访问/index渲染Home
    },
    {
      path: "*",
      redirect: { name: "Home" },
    },
  ],
});
```

name 的使用场景二：

```html
<router-link :to="{name:'路由的名字'}"></router-link>
```

### 1.3 路由重定向

重定向设置：

```js
let router = new VueRouter({
  routes: [
    {
      path: "/home",
      component: Home,
      name: "Home",
      alias: "/index",
    },
    {
      path: "*", // 配置在最后，当所有路由都未被匹配到则如何处理
      // component: Error,          // 可以直接调转到错误提示页

      // redirect会替换掉浏览器中的地址，alias不会
      // redirect: '/index'         // 也可以配置重定向
      // redirect: {path: '/home'} // 重定向方式二
      // redirect: {name: 'Home'}   // 重定向方式三，name是路由的名字
      redirect: (to) => {
        // 重定向方式四,动态设置重定向的目标；
        //     console.log(to);       // to目标路由对象，就是访问的路径的路由信息
        //     return '/home'         // return值也可以写为  {path:} 或 {name:}

        //除了可以直接return重定向的路由外，还可以通过 path\hash\query 等判断，动态设置重定向的目标路由：

        if (to.path === "/123") {
          return "/home";
        } else if (to.path === "/456") {
          return { path: "/document" };
        } else {
          return { name: "about" };
        }
      },
    },
  ],
});
```

## 二 router-link

采用 history 模式会带来新的问题：a 连接会引起页面刷新。vue 为了解决这个问题，提供了 router-link 标签。

```js
<div id="app">
  <router-link to="/home">显示home</router-link>
  <router-view></router-view>
</div>
```

使用 router-link 替代 a 标签的好处：

- router-link 仍然会被解析为 a 链接，但是其默认行为(刷新页面)被阻止了。
- hash 模式下，使用 router-link 也无需修改跳转地址为 "#/home"。

router-link 的其他设置：

- to 的书写支持多种形式：`:to="home"`，`:to="{path: '/home'}"`
- router-link 默认生成的是 a 标签，也可以生成 div、p 等标签，添加属性：`tag="div"`，此时 div 会自动拥有监听点击事件的功能
- 默认的触发组件事件是点击事件，也可以修改为别的事件：添加属性：`event="mouseover"`
- 添加`exact`属性会让样式渲染变为不包含形式（精确匹配），`<router-link to="about" exact tag="li"> </router-link>`  


**router-link 改成制定的标签：**很多导航中，使用导航标签既包含图标又包含文字，router-link 可以这样配置：

```js
<router-link :to="home" tag="li">
    <i><img src=""></i>
    <span>首页</span>
</router-link>
```

**router-link 配置当前激活状态的 class 名**

router-link 生成的 li 元素，被点击的 li 上会带有 router-link-active 的 class 属性，用来配置激活状态的样式。这个 class 名字很长可以自己配置新名字：

```js
// 第一种：全局配置方式：在router里面修改，linkActiveClass:“” ， 所有生成的元素的router-link-active都被修改
let router = new VueRouter({
    linkActiveClass: 'isActive',            // 此时class名为isActive
})

// 第二种：局部修改：在router-link标签上添加属性 active-class=“” ，只支持当前组件
<router-link to="/home" active-class="isActive">显示home</router-link>
```

## 三 router-view

router-view 是组件渲染的地方。

**同时给子组件添加相同的类名**：在 router-view 标签添加 class="center"，那么所有子组件渲染的时候，外层根节点标签就会自动添加 class="center"；

```js
<router-view class="center"></router-view>      <!-- 配置样式 -->
```
