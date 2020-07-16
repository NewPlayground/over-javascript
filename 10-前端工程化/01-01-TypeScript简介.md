## 一 JavaScript弱类型

JavaScript是一种弱类型的，动态类型检查的语言：
- 弱类型：在定义变量的时候，可以为变量赋值任何数据，变量的数据类型不是固定死的。而强类型则一旦给变量赋值，那么变量的数据类型就已经确定，之后如果要给该变量赋值其他类型的数据，需要进行强制数据类型转换。
- 动态类型检查：动态类型的类型检查会在代码运行的时候进行，而静态类型的类型检查则是在编译时进行。

动态类型很容易造成一个现象：代码错误不易被捕捉到。
```js
function add(num){
    return num + 1;
}
```
上述JS函数add()，随着参数如果我们传递对象、字符串、undefined等都会产生不同的结果，使用该函数时不会产生这些错误，只有运行时候才能发现。而静态类型语言在函数定义时就会要求num必须是整数或者小数等，在书写代码阶段（其实就是编译：开发工具帮助提前编译），一旦出现非法参数，直接报错，无法通过编译。  

此外脚本语言在开发工具中也不能得到很好的代码提示。  

弱类型为JavaScript带来了大型工程化项目开发的困难，经典的一句话来形容脚本语言开发大型项目：动态一时爽，重构火葬场。  

为了解决该问题，JS涌现了多数类型检查机制，常见的有：
- Flow：Facebook推出的JavaScript静态类型检查工具，已渐渐式微
- TypeScript：微软开发的JavaScript的超集，是一门编程语言，当前热度如日中天。 

## 二 TypeScript初识

TypeScript是JavaScript的超集，遵循ES6与ES5规范，扩展了原生JS语法，适合开发大型企业项目。环境配置：
```
npm install -g typescript
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

该代码不能在浏览器、Node环境中直接运行，需要编译：
```
//编译后产生的 hello.js 可以直接运行
tsc hello.ts
```
每次书写完的ts文件需要编译是很痛苦的，通过配置可以实时编译，（webstorme支持自动编译），VSCode配置步骤如下：
第一步：tsc --init 生成配置文件，可以生成一个tsconfig.json文件；
第二步：点击 菜单-任务-运行任务-tsc:监视-tsconfig.json。
