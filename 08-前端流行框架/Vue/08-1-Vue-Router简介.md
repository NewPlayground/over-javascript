## 一 vue-router 简介

VueRouter 是 vue 可以渐进引入的路由模块，使用 Vue-Router 的项目已经具备了工程化的概念，推荐直接采用 vue-cli 构建工具初始化项目。

前端路由即根据 url 分配对应的处理程序，VueRouter 是前端路由的基础，在 vue-router 中，通过管理 url，实现组件与 url 的对应，通过 url 进行组件之间的切换。

vue-router 的安装：

```
# vue-cli创建项目时若安装了vue-router，则无需安装。
npm i vue-router -D
```

## 二 vue-router 使用示例

### 2.1 router.js

由于一个项目路由众多，推荐对路由进行集中管理。创建路由管理文件 `src/router/router.js`：

```js
import Vue from "vue";
import VueRouter from "vue-router";

import Home from "@/views/Home.vue";

// 启用路由
Vue.use(VueRouter);

const routes = [
  {
    path: "/",
    name: "Home",
    component: Home,
  },
  {
    path: "/about",
    name: "About",
    component: () => import("../views/About.vue"),
  },
];

const router = new VueRouter({
  mode: "history",
  routes,
});

export default router;
```

Home 与 About 是自定义的两个组件。

### 2.2 main.js

创建完路由映射晚间后，需要在 main.js 中使用该路由文件：

```js
import Vue from "vue";
import App from "./App.vue";
import router from "./router/router";

Vue.config.productionTip = false;

new Vue({
  router,
  store,
  render: (h) => h(App),
}).$mount("#app");
```

### 2.2 App.vue

App.vue 是整个项目的根组件，界面的内容在此显示，引入路由后，需要利用标签 `<router-view>` 进行显示：

```js
<template>
  <div id="app">
    <p>{{msg}}</p>
    <router-link to="/">首页</router-link>
    <router-link to="/about">关于</router-link>
    <router-view></router-view>
  </div>
</template>

<script>

export default {
  data(){
    return {
      msg: "hello world"
    }
  }
}
</script>

<style>
</style>
```

`router-view` 是 vue-router 提供的页面显示区域，`router-link`是 vue-router 提供的链接导航。

### 2.3 访问项目

在 `npm run serve`后就可以访问项目了，默认地址为：

```
http://localhost:8080/home
```
