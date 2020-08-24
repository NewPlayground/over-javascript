## 一 JavaScript弱类型

JavaScript是一种弱类型的动态语言：
```
弱类型与强类型：
    弱类型：变量的数据类型不固定，可以为其赋值任何类型的值。
    强类型：变量的类型是严格固定的，给变量赋值其他类型的数据会引发错误，必须先进行合法的数据类型转换。

动态类型与静态类型：
    针对的是代码类型的检查方式。
    动态类型的类型检查会在代码运行的时候进行
    静态类型的类型检查则是在编译时进行。（静态语言一般都需要先编译、再运行！）
```

动态类型很容易造成一个现象，即代码错误不易被捕捉到：
```js
// 该函数参数数据类型并未限制，可以任意传递 对象、字符串、undefined，产生的结果也会千奇百怪
function add(num){
    return num + 1;
}
```
静态类型的语言在函数定义时会要求num必须是整数或者小数等，在书写代码阶段（其实就是编译：开发工具帮助提前编译），一旦出现非法参数，直接报错，无法通过编译，可以大大降低低级错误，而且动态类型语言在开发工具中也不能获得很好的代码提示。
```c
// 这是C语言中的一个函数
int add(int num) {
    return num + 1
}
```  

所以弱类型的语言往往不适合大型项目的开发与协作，有这样一句经典的话来形容：动态一时爽，重构火葬场。  

为了解决该问题，JavaScript涌现了许多类型检查机制，常见的有：
- Flow：Facebook推出的JavaScript静态类型检查工具，已渐渐式微
- TypeScript：微软推出的编程语言，是JavaScript的超集，当前热度如日中天。 

## 二 TypeScript初识

TypeScript是JavaScript的超集，遵循ES6与ES5规范，扩展了原生JS语法，适合开发大型企业项目。  

环境配置：
```
# 安装typescript
npm install -g typescript

# 测试环境：tsc是在安装typescript时默认安装的TS编译器
tsc --version
```

helloworld 案例：
```ts
//新建一个 hello.ts
export class Hello {
    run() {
        console.log("hello world!");
    }
}
(new Hello()).run();
```




## 三 

