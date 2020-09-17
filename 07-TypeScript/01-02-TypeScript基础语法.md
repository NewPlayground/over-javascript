## 二 数据类型

### 2.1 九种类型

```
布尔类型：	boolean
数字类型：	number
字符串类型：string
数组类型：	array
元组类型：	tuple
枚举类型：	enum
任意类型：	any
void类型：	void
never类型： 包括null和undefined
```

### 2.2 类型校验

TypeScript 引入了类型校验，生命一个变量必须同时声明变量的类型：

```ts
var flag: boolean = true;
flag = 1; //已经报错
```

### 2.3 数组 array

在 typescript 中可以直接使用 es5 的定义语法，也可以指定数组中元素类型：

```ts
var arr1 = [1, 2, "str"]; //ES5原生数组
var arr2: number[] = [1, 2, 3]; //数组元素只能是数字
var arr3: Array<number> = [1, 2, 3]; //数组元素只能是数字
```

### 2.4 元组类型 tuple

大多数语言的数组内所有元素的类型都是相同的，我们也推荐在 js 中这样使用，元组也是一种数组，可以在数组内添加不同类型的元素。

```ts
let arr1: [number, string] = [1, "aaa"];
let arr2: [number, string] = [1, 2, "aaa"]; //报错 二个元素应该是字符串且长度不对
```

### 2.5 枚举 enum

```ts
enum Flag {
  success = "成功",
  error = "失败",
}
let f: Flag = Flag.success;
console.log(f); //成功
```

案例：

```ts
enum Color {
  blue,
  red,
  green = 3,
  "红色",
}
let a: Color = Color.blue;
let b: Color = Color.red;
let c: Color = Color.green;
let d: Color = Color.红色;
console.log(a); //输出0 如果标识符没有赋值，它的值就是下标
console.log(b); //输出1
console.log(c); //输出3
console.log(d); //输出4
```

### 2.6 任意类型 any

在前端开发中通过 getElementById()获取到的元素是个 object 类型，而 ts 没有该类型，直接赋值就会报错，可以使用 any 类型。

```ts
let num: any = 123;
console.log(typeof num);
num = false;
console.log(typeof num);
num = "str";
console.log(typeof num);
```

### 2.7 其他类型 never

never 类型除了 never 修饰外，undefined 和 null 也是 never 类型的子类型。

```ts
var num1: number;
console.log(num1); //声明没有赋值报错

var num2: undefined;
console.log(num2); //undefined类型直接输出不会报错

var num3: number | undefined;
console.log(num3); //不会报错

var num: null;
num = null; //正确
num = 123; //报错，定义一个变量为null时，变量的值只能是null
```

never 类型是任何类型的子类型，可以赋值给任何类型；
然而，除了 never 本身之外，没有类型可以赋值给 never 类型（包括 any）。
通常表现为抛出异常或无法执行到终止点（例如无线循环）。

```ts
let x: never;

// 运行正确，never 类型可以赋值给 never类型
x = (() => {
  throw new Error("exception");
})();

// 返回值为 never 的函数可以是抛出异常的情况
function error(message: string): never {
  throw new Error(message);
}

// 返回值为 never 的函数可以是无限循环这种无法被执行到的终止点的情况
function loop(): never {
  while (true) {}
}
```

## 三 函数

```ts
//声明
function run(name: string, age: number): boolean {
  return false;
}

/*
ts 也支持匿名函数
void 类型表示没有返回值
*/

//参数可以设置默认参数
function test(a: string, b: string = "hi") {
  console.log(a);
  console.log(b);
}

//ES5中，方法的实参和形参可以不一样，但是ts中必须一样，如果要求二者不一样，ts中可以配置可选参数。
function test(a: string, b?: string, c: string = "hi") {
  console.log(a);
  console.log(b);
  console.log(c);
}

//剩余参数
function fn(a: any, ...args: any) {
  args.forEach((arg: any) => {
    console.log(arg);
  });
}

fn(1, 2, 3); //输出2 3

//无需书写小括号的场景：字符串模板作为参数
fn`test ${name}`;
```

函数重载：ts 中的函数重载与 Java 中的重载不太一样。

```ts
function getInfo(name: string): string;
function getInfo(age: number): string;

function getInfo(str: any): any {
  if (typeof str === "string") {
    return "我叫：" + str;
  } else {
    return "我的年龄是：" + str;
  }
}
console.log(getInfo("张三"));
console.log(getInfo(12));
```
