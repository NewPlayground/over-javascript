## 四 类 class

### 4.1 类的定义

```ts
class Person {
  name: string;
  age: number;

  constructor(a: string, b: number) {
    this.name = a;
    this.age = b;
  }

  info(): void {
    console.log(this.name + "的年龄是：" + this.age);
  }
}

let p = new Person("张三", 40);
p.info();
```

注意：TS 一样支持 ES6 中的 extends static 关键字

### 4.2 属性修饰符

```
public：		默认的修饰符，可以不写，在类里面、子类、类外面都可以访问
private：	    在类里面可以访问，子类、类外部都没法访问
protected：	    在类里面、子类里面可以访问，类外部无法访问
```

注意：如果没有修饰符，则默认都是 public，但是在构造函数中，有所区别。
如果构造函数的参数没有修饰符，那么在构造函数外部是无法使用这个参数的；
如果构造函数的参数使用了 public 修饰符，那么在构造函数外部可以使用该参数

### 4.3 多态

### 4.4 抽象类

抽象类和抽象方法用来定义标准。抽象方法不包含具体的实现。
注意：抽象类不能实例化，只有其子类才能实例化。

```ts
abstract class Animal {
  abstract eat(): any; //抽象方法只能出现在抽象类中
}
```

## 五 接口

### 5.1 接口简介

抽象类和抽象方法虽然提供了标准，但是只针对类中的某个函数设立了规范。
接口对类本身设立了规范。

### 5.2 属性接口

属性接口更多是对 json 对象提供了约束。

```ts
interface Person {
  name: string;
  age: number;
}

class Student {
  //构造函数支持接口
  constructor(public config: Person) {}
}

//在创建对象时，必须满足接口定义的属性
let s1 = new Student({
  name: "lisi",
  age: 18,
});
```

### 5.3 类实现接口

```ts
interface Person {
  name: string;
  age: number;
  run(): void;
}
class Student implements Person {
  name: string = "";
  age: number = 0;
  constructor() {}
  //实现接口方法
  run() {
    console.log(this.name + " run....");
  }
}
let s1 = new Student();
s1.name = "lisi";
s1.age = 15;
s1.run();
```

### 5.4 函数接口

```ts
interface encrypt {
  (key: string, val: string): string;
}

let md5: encrypt = function (key: string, val: string): string {
  return key + val;
};

console.log("name", "zs");
```

### 5.5 可索引接口

可索引接口可以用来对数组的索引进行限制，如果数组中的索引传入了其他类型，那么报错：

```ts
interface UserArr {
  [index: number]: string;
}
let arr: UserArr = ["aaa", "bbb"];
console.log(arr[0]);
```

可索引接口也可以对对象进行限制，如果 json 中多写了字段，则报错：

```ts
interface UserObj {
  [index: string]: string;
}
let u: UserObj = {
  name: "lisi",
};
```

### 5.6 接口可以被继承

```ts
interface Animal {
  eat(): void;
}

interface Person extends Animal {
  work(): void;
}
```

## 六 泛型

### 6.1 泛型简介

在软件工程中，我们不仅要创建一致的 API，也要考虑可重用性，组件不仅能够支持当前的数据类型，同事也支持未来的数据类型。
通俗的理解：泛型就是提升 类 接口 方法的复用性，以支持不特定的数据类型

### 6.2 应用场景

```ts
function f1(value: string): string {
  return value;
}

function f2(value: number): number {
  return value;
}

//上述的明显函数的创建显得累赘，通过泛型解决
function fn<T>(value: T): T {
  return value;
}
console.log(fn<number>(123));
console.log(fn<string>("test"));
```

### 6.3 泛型应用于类

```ts
class Hello<T> {
  public list: T[] = [];
  add(val: T): void {
    this.list.push(val);
  }
}
var h = new Hello<number>(); //让类型为number
```

同理，泛型可以应用于类，函数

### 七 注解

注解为程序的元素（类、方法、变量）加上更直观更明了的说明，这些说明信息与程序的业务逻辑无关，而是供指定的工具或框架使用。

### 八 类型定义文件

类型定义文件后缀为 .d.ts 。
类型定义文件用来帮助开发者在 TS 中使用已有的 JS 工具包，如：jQuery。
这个文件其实是一个 typescript 文件模块，提供给开发者直接使用，比如引入 jQuery 的类型定义文件后，就可以使用\$与 jQuery 的方法。
