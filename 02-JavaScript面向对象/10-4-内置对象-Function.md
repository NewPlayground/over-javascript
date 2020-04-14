## 一 函数对象

创建函数一般有两种方式：
```js
//方式一：直接量声明
function fn1(){
    console.log("函数1");
}

//方式二：函数表达式
var fn2 = function(){
    console.log("函数3");
}
```

函数声明与函数表达式是有区别的：解析器会先读取函数声明，使其在执行任何代码之前可用（可以访问）；至于函数表达式，则必须等到解析器执行到它所在的代码行，才会真正被解释执行。
```js
// 可以正常执行，但是使用函数表达式则不会正常执行
sum(2,3);
function sum(num1, num2){
    return num1 + num2;
}
```

## 二 JS的函数没有重载

JS中同时创建两个相同函数，参数不同，并不会发生重载现象：
```js
function addSomeNumber(num){
    return num + 100;
}

function addSomeNumber(num) {
    return num + 200;
}

var result = addSomeNumber(100); //300
```

这是因为后面的函数覆盖了前面的函数，和下面的代码道理一致：
```js
var addSomeNumber = function (num){
    return num + 100;
};

addSomeNumber = function (num) {
    return num + 200;
};

var result = addSomeNumber(100); //300
```

### 三 函数的内部对象

在函数内部有三个特殊对象：arguments、this、caller。

### 3.1 arguments

arguments对象用于保存参数，该对象存在一个 callee属性指针，指向拥有该arguments对象的函数（即原函数本身）

示例：
```js
// 阶乘函数示例：该写法造成了内部函数与函数本身的耦合，且若修改了函数名，还要去内部修改一次递归调用明
function factorial(num){
    if (num <=1) {
        return 1;
    } else {
        return num * factorial(num-1)
    }
}

// 替换写法
function factorial(num){
    if (num <=1) {
        return 1;
    } else {
        return num * arguments.callee(num-1)
    }
}
```

### 3.2 this

this对象是函数的执行环境对象，也可以称为具体某个实例。

### 3.3 caller

caller对象中保存着调用当前函数的函数的引用，如果是在全局作用域中调用当前函数，其值为null：
```js
// 下列代码会导致警告框中显示 outer()函数的源代码，因为 outer()调用了 inter()，所以inner.caller 就指向 outer()。
function outer(){
    inner();
}

function inner(){
    alert(inner.caller);
}

outer();

// 使用 caller 实现松耦合
function outer(){
    inner();
}

function inner(){
    alert(arguments.callee.caller);
}

outer();
```

## 四 函数属性

函数也是对象，因此也有属性与方法。  

每个函数都包含两个属性：
- length：表示函数命名参数的个数
- prototype：原型属性，每个函数都有原型，用于实现继承。要注意的是prototype属性不可枚举（即不能通过for in 发现）

## 五 apply() call()

每个函数也都包含两个非继承而获得的方法：apply()、call()，都可以用来调用当前函数。  

示例：
```js
var a = 20;
var obj = {
    a: 40
}

function fn(){
    console.log(this.a);
}

fn();       // 输出20，因为这里的this是全局的window

// 修改this指向
fn.call(obj);       // 40
fn.apply(obj);      // 40
```

apply与call都修改了函数内部this的指向，this指向第一个参数，如果这个参数为null，则this指向window。  

apply与call的区别是：第二个参数及其后续参数传递形式不同。
- apply：第二个参数为数组，将函数执行需要的参数组装为数组再传入
- call：第二个及其之后的参数是函数执行需要的参数

但是其参数是不同的：
- apply用于函数的形参个数不确定的情况：函数名.apply(绑定对象,函数参数列表数组);
- call用于确定了函数的形参有多少个的时候使用：函数名.call(绑定对象,函数参数1,参数2,参数3....);
- apply和call的第一个参数都为null时，表示为函数调用模式，即this指向window


使用案例一：求数组最大值
```javascript
var arr = [9,1,4,10,7];
var max1 = Math.max(9,1,4,10,7);
var max2 = Math.max.apply(null,arr);
console.log(max1);      //输出10
console.log(max2);      //输出10
```

使用案例二：伪数组
```javascript
//obj是个伪数组，无法使用obj.0获取属性，也无法像数组那样用obj[0]获取
var obj = {
    0: "a",
    1: "b",
    2: "c",
    length: 3
};
// [].concat(1,2,3)会产生数组[1,2,3]
var arr = [].concat.apply([], obj);
console.log(arr);                       //输出['a','b','c']
```

## 六  bind

ES5增加了bind函数，也用来改变函数内部的this指向。但是bind函数与call/apply不同的是，返回的是一个新的函数，这个心函数与原函数有共同的函数体，但是并非原函数，所以也不会立即执行：
```js
function fn(num1, num2){
    return this.a + num1 + num1;
}

var a = 20;
var obj = {
    a: 40
}

var _fn = fn.bind(obj, 1, 2);

console.log(_fn === fn);            // false
_fn();                              // 43
_fn(1, 4);                          // 43，因为参数绑定，重新传入的参数是无效的
```