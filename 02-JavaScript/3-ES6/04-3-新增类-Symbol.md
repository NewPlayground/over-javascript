## 一 Symbol

Symbol 是 ES6 新增的数据类型！

```js
let s1 = Symbol();
let s2 = Symbol();

console.log(typeof s1); // symbol
console.log(typeof s2); // symbol
console.log(s1 == s2); // false

// Symbol类型不是对象，不能像对象那样使用
s1.name = "zs";
console.log(s1.name); // undefined
```

为了区分不同的 Symbol 变量，可以在构造时加入描述：

```js
let s1 = Symbol("我是s1");
let s2 = Symbol("我是s2");

// 也可以使用 for方法
let s3 = Symbol.for("我是s3");
let s4 = Symbol.for("我是s4");
console.log(s3 == s4); // 这里是相同的，因为使用for函数会在内存中查找是否有定义
```

## 二 Symbol 的使用场景

```js
let users = {
  lisi: "30岁，前端工程师",
  lisi: "24岁，设计师",
};

// 这2个人的名字一样
console.log(users.lisi); // 24岁，设计师
```

解决：

```js
let user1 = {
  name: "lisi",
  key: Symbol(),
};

let user2 = {
  name: "lisi",
  key: Symbol(),
};

let userInfo = {
  [user1.key]: "30岁，前端工程师",
  [user2.key]: "24岁，设计师",
};
```

## 三 Symbol 成员的获取

```js
let s = Symbol();

let p = {
  name: "lisi",
  [s]: "隐藏属性",
};

// 使用 for in 或者 for of 都无法遍历到 [s] 成员
// for (const key in p){}
// for (const key of Object.keys(p)){}

// 只能遍历到 Symbol类型属性
// for(const key of Object.getOwnPropertySymbols(p)){}

// 遍历所有属性
for (const key of Reflect.ownKeys(p)) {
  console.log(key); // name  Symbol()
}
```
