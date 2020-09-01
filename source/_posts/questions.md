---
title: (未完成)面试题目汇总
date: 2020-07-20 17:12:03
tags: 
  - 面试
categories: 前端
---

<img src="https://cdn.jsdelivr.net/gh/qiruohan/qiruohan.github.io/uploads/353632561.jpg" width="800" height="100%">
<!-- more -->

# CSS
## 1.说说CSS选择器以及这些选择器的优先级

> `!important` > 内联样式 > ID 选择器 > 类选择器 = 属性选择器 = 伪类选择器 > 标签选择器 = 伪元素选择器 > 通配符选择器 > 继承 > 浏览器默认属性

选择器的优先级：
* `!important`
* 内联样式(`style='xxx'`)（1000）
* ID选择器(`#example`)（0100）
* 类选择器(`.example`)/属性选择器(`[type="radio"]`)/伪类选择器(`:hover`)（0010）
* 标签选择器(`h1`)/伪元素选择器(`::before`)(0001)
* 通配符选择器(`*`)/关系选择器(`+`,`>`,`~`,`' '`,`||`)/否定伪类（`:not()`）(0000)

**注**：通配选择器(`*`)、组合选择器(`+`,`>`,`~`,`' '`,`||`)和否定伪类（`:not()`）不会影响优先级（但是，`:not()`内部声明的选择器会影响优先级）。

优先级是由`A`、`B`、`C`、`D`的值来决定的，其中它们的值计算规则如下：

1. 如果存在内联样式，那么`A = 1,`否则`A = 0`;
2. `B` 的值等于 `ID选择器` 出现的次数;
3. `C` 的值等于 `类选择器` 和 `属性选择器` 和 `伪类` 出现的总次数;
4. `D` 的值等于 `标签选择器` 和 `伪元素` 出现的总次数 。

这么很抽象，那么来个例子你就懂了：

```js
#nav-global > ul > li > a.nav-link
```

套用上面的算法，依次求出 `A` `B` `C` `D` 的值：

1. 因为没有内联样式 ，所以 `A = 0;`
2. ID选择器总共出现了1次， `B = 1`;
类选择器出现了1次， 属性选择器出现了0次，伪类选择器出现0次，所以 `C = (1 + 0 + 0) = 1`；
标签选择器出现了3次， 伪元素出现了0次，所以 `D = (3 + 0) = 3`;

上面算出的`A`、 `B`、`C`、`D` 可以简记作：`(0, 1, 1, 3)`。


访问[MDN](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Specificity)来了解更多关于优先级的详细信息。

## 2. 伪类和伪元素的区别
**伪元素**：主要是用来创建一些不存在原有dom结构树种的元素，例如：用::before和::after在一些存在的元素前后添加文字样式等，这些被添加的内容会以具体的UI显示出来，被用户所看到的，这些内容不会改变文档的内容，不会出现在DOM中，不可复制，仅仅是在CSS渲染层加入。CSS3中建议使用::表示伪元素，如：div::before。

**::before和::after这两个伪类下有特有的属性content，必须有这个属性。**

**伪类**：表示已存在的某个元素处于某种状态，但是通过dom树又无法表示这种状态，就可以通过伪类来为其添加样式。例如a元素的:hover, :active等。CSS3中建议使用:表示伪元素，如：a:hover。

## 3. 你知道什么是BFC么？
3-1 什么是BFC
**块级格式化上下文**，是一个独立的渲染区域，让处于 BFC 内部的元素与外部的元素相互隔离，使内外元素的定位不会相互影响。
> IE下为 Layout，可通过 zoom:1 触发
3-2 触发BFC的条件
* 根元素
* `float`元素
* `position: absolute/fixed`
* `display: inline-block / table`
* ovevflow !== visible
* display: flow-root
* column-span: all

3-3 BFC的约束规则
* 属于同一个 BFC 的两个相邻 Box 垂直排列（可以看作BFC中有一个的常规流）
* 属于同一个 BFC 的两个相邻 Box 的 margin 会发生重叠
* BFC 中子元素的 margin box 的左边，与包含块 (BFC) border box的左边相接触 (子元素 absolute 除外)
* BFC 的区域不会与 float 的元素区域重叠
* 计算 BFC 的高度时，考虑BFC所包含的所有元素，连浮动元素也参与计算
* 文字层不会被浮动层覆盖，环绕于周围

3-4 BFC可以解决的问题
* 阻止`margin`重叠
* 可以包含浮动元素 —— 清除内部浮动(清除浮动的原理是两个div都位于同一个 BFC 区域之中)
* 自适用两列布局（float + overflow）
* 可以阻止元素被浮动元素覆盖

## 4.如何实现居中
1. 绝对定位 + margin
优缺点：需要父级有具体宽高，且要知道宽高的具体值
```js
.parent {
  position: relative;
  height: 400px;
  width: 100%;
  border: 1px solid #000;
}
.child {
  position: absolute;
  top: 50%;
  left: 50%;
  width: 100px;
  height: 100px;
  margin-left: -50px;
  margin-top: -50px;
  background-color: aquamarine;
}
```

2. transform
优缺点：不需要父级有具体宽高，但是兼容性不是特别好

```js
.parent {
  position: relative;
  height: 400px;
  width: 100%;
  border: 1px solid #000;
}
.child {
  position: absolute;
  top: 50%;
  left: 50%;
  width: 100px;
  height: 100px;
  transform: translate(-50%, -50%);
  background-color: aquamarine;
}
```

3. 绝对定位
优缺点：需要父级有具体宽高，但是不需要知道宽高的具体值

```js
.parent {
  position: relative;
  height: 400px;
  width: 100%;
  border: 1px solid #000;
}
.child {
  position: absolute;
  top: 0;
  left: 0;
  bottom: 0;
  right: 0;
  margin: auto;
  width: 100px;
  height: 100px;
  background-color: aquamarine;
}
```

4.flex
优缺点：更简单了，但是也是兼容性不是特别好

```js
.parent {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 400px;
  width: 100%;
  border: 1px solid #000;
}

.child {
  width: 100px;
  height: 100px;
  background-color: aquamarine;
}
```

5.table-cell
优缺点：要求父级有固定宽高，且不能使用百分比

```js
.parent {
  display: table-cell;
  vertical-align: middle;
  text-align: center;
  height: 400px;
  width: 400px;
  border: 1px solid #000;
}

.child {
  display: inline-block;
  width: 100px;
  height: 100px;
  background-color: aquamarine;
}
```

6.javascript

```js
<script>
  let html = document.documentElement
  winW = html.clientWidth
  winH = html.clientHeight
  boxW = box.offsetWidth
  boxH = box.offsetHeight
  box.style.position = 'absolute'
  box.style.left = (winW - boxW) / 2 + 'px'
  box.style.top = (winH - boxH) / 2 + 'px'
</script>
```
## 5.盒模型
页面渲染时，dom 元素所采用的 布局模型。可通过`box-sizing`进行设置。根据计算宽高的区域可分为：

* `content-box` (W3C 标准盒模型)
* `border-box` (IE 盒模型)
* `padding-box` (FireFox 曾经支持)
* `margin-box` (浏览器未实现)

![image](https://cdn.jsdelivr.net/gh/qiruohan/qiruohan.github.io/uploads/450.png)

注：理论上是有上面 4 种盒子，但现在 w3c 与 mdn 规范中均只支持 content-box 与 border-box；


## 6.绝对定位、固定定位和 z-index

## 7.如何实现左侧宽度固定，右侧宽度自适应的布局

## 8.层叠上下文

## 小结
通常，CSS 并不是重点的考察领域，但这其实是由于现在国内业界对 CSS 的专注不够导致的，真正精通并专注于 CSS 的团队和人才并不多。因此如果能在 CSS 领域有自己的见解和经验，反而会为相当的加分和脱颖而出。

# Javascript

## 1. JS 的数据类型及存储方式的区别

在 ECMAScript 规范中，共定义了 8 种数据类型，分为**基本数据类型**和**引用数据类型**（引用数据类型又称复杂数据类型）两大类：

> 基本数据类型：String、Number、Boolean、Null、Undefined、Symbol（ES6 新增）、BigInt（ES10 新增）
> 引用数据类型：Object（Function、Array、Date、RegExp、Math...都是Object类型的实例对象）

储存方式的区别：

> 基本数据类型：变量名和值都储存在栈内存中；
> 引用数据类型：变量名储存在**栈内存**中，值储存在**堆内存**中，堆内存中会提供一个引用地址指向堆内存中的值，而这个引用地址是储存在栈内存中的。

## 2.常用的判断 JS 数据类型的四种方法

2-1. typeof

缺点：不能判断 Object 类型，也不能判断 Null 类型。

> `type null` 返回结果为 `object` 是因为 JavaScript 是用 32 位比特来存储值的，且是通过值的低1位或3位来识别类型的，object 的前三位表示是 000，null 为机器码空指针，32位表示全是0，它们俩的前三位一样，所以 `typeof null` 也会打印出 `object`。

```js
console.log(typeof "")  // string
console.log(typeof 1)  // number
console.log(typeof true)  // boolean
console.log(typeof Symbol())  // symbol
console.log(typeof undefined)  // undefined
console.log(typeof null)  // object
console.log(typeof [])  // object
console.log(typeof {})  // object

```

2-2. constructor

`constructor`可以找到这个变量是通过谁构造出来的。

缺点： 
[1]. 不能判断 null 和 undefined，因为 null 和 undefined 是无效的对象，因此是不会有 constructor 存在，这两种类型的数据需要通过其他方式来判断。
[2]. 函数的 constructor 是不稳定的，这个主要体现在自定义对象上，当开发者重写 prototype 后，原有的 constructor 引用会丢失，constructor 会默认为 Object

```js
console.log(("").constructor == String)  // true
console.log((1).constructor == Number)  // true
console.log((true).constructor == Boolean);  // true
console.log([].constructor == Array)  // true
console.log(({}).constructor == Object) // true
```

2-3. instanceof

`instanceof`用来判断A是否是B的实例，在这里需要特别注意的是：**instanceof 检测的是原型**。

缺点：instanceof 只能用来判断两个对象是否属于实例关系， 而不能判断一个对象实例具体属于哪种类型。

```js
console.log([] instanceof Array)  // true
console.log({} instanceof Object)  // true
console.log([] instanceof Object)  // true
console.log(new Date() instanceof Date)  // true
```

2-4. Object.prototype.toString.call()

通过 **.call** 将传进来的对象作为 Object 原型上的 this，然后通过 toString 方法可以看到具体的类型。

优缺点：不能细分谁是谁的实例，但是判断这个变量的类型还是非常方便和靠谱的。

```js
console.log(Object.prototype.toString.call())  // [object Undefined]
console.log(Object.prototype.toString.call("")) // [object String]
console.log(Object.prototype.toString.call(1))  // [object Number]
console.log(Object.prototype.toString.call(true))  // [object Boolean]
console.log(Object.prototype.toString.call(Symbol()))  // [object Symbol]
console.log(Object.prototype.toString.call(null))  // [object Null]
console.log(Object.prototype.toString.call(new Function()))  // [object Function]
console.log(Object.prototype.toString.call(new Date()))  // [object Date]
console.log(Object.prototype.toString.call([]))  // [object Array]
console.log(Object.prototype.toString.call(new RegExp()))  // [object RegExp]
console.log(Object.prototype.toString.call(new Error()))  // [object Error]
console.log(Object.prototype.toString.call(document))  // [object HTMLDocument]
console.log(Object.prototype.toString.call(window))  // [object Window] 
```

## 3. 作用域、作用域链、执行上下文（EC）、执行上下文栈（ECS）

### 作用域

函数在定义时会产生两种`作用域`：分为 全局作用域 和 局部作用域，所以 JS 的作用域是`静态作用域`。


### 执行上下文（EC）

当 JS 在执行一段代码的时候，会产生`执行上下文(EC)`；

「执行上下文」一共有三种类型：全局执行上下文、函数执行上下文、`eval`执行上下文。

「执行上下文」包含三个部分：变量对象(VO)、作用域链(词法作用域)、`this`指向。

> 活动对象 (AO): 当变量对象所处的上下文为 active EC 时，称为活动对象。

### 执行上下文栈（ECS）

在「执行上下文」的形成过程中，会使用「执行上下文栈」来管理执行上下文。

代码执行的过程：
1. 创建 全局上下文 (global EC)
2. 全局执行上下文 (caller) 逐行 自上而下 执行。遇到函数时，函数执行上下文 (callee) 被push到执行栈顶层
3. 函数执行上下文被激活，成为 active EC, 开始执行函数中的代码，caller 被挂起
4. 函数执行完后，callee 被pop移除出执行栈，控制权交还全局上下文 (caller)，继续执行

举个例子：

```js
function a() {
  b();
}

function b() {
  c();
}

function c() {
  console.log("welcome");
}

/**

ECS = [
  globalContext
]
ECS.push(functionAContext);
ECS.push(functionBContext);
ECS.push(functionCContext);
ECS.pop();
ECS.pop();
ECS.pop();
**/
```

### 作用域链

函数内部会保留一个`[[scope]]`属性，它会保存所有的父级的变量对象。而且在函数执行的时候，会把自身的`AO`对象加进去，所以执行的时候会先找自己的`AO`属性，找不到的话会向上查找，这就是作用域链。

「作用域链」有两部分组成：

[1]. `[[scope]]`属性: 指向父级变量对象和作用域链，也就是包含了父级的`[[scope]]`和`AO`。
[2]. `AO`: 自身活动对象

如此 `[[scope]]`包含`[[scope]]`，便自上而下形成一条「作用域链」。

举个例子：

```js
function a() {
  function b() {
    function c() {
      
    }
  }
}

/*
函数声明时：

a.[[scope]] = [
  globalContext.VO
]

b.[[scope]] = [
  aContext.AO,
  globalContext.VO
]

c.[[scope]] = [
  bContext.AO,
  aContext.AO,
  globalContext.VO
]
*/

var a = 1;
function sum() {
  var b = 2;
  return a+b;
}

sum();

/*
sum.[[scope]] = {
  globalContext.VO
}

// 编译阶段： 
sumContext = {
  A0:{
    arguments: {
      length: 0
    },
    b: undefined
  },
  Scope: [A0, sum.[[scope]]]
}

// 执行阶段：
ESC = [
  globalContext,
  sumContext
]

A0:{
    arguments: {
      length: 0
    },
    b: 2
  },

// 执行完：
ECS.pop()
*/
```

## 4. 原型与原型链

* 原型：`prototype`，每一个函数都有一个 `prototype` 属性；

* 原型链：`__proto__`，每一个对象都有一个__proto__属性;

* 构造函数：可以通过`new`来**新建一个对象**的函数。

* 实例：通过构造函数和`new`创建出来的对象，便是实例。**实例通过`__proto__`指向原型，通过`constructor`指向构造函数。**

举个例子：
```js
function Animal() {
  this.type = "哺乳类"
}

Animal.prototype.type = "哺乳"

// 实例
let animal = new Animal();
console.log(animal.__proto__.__proto__ === Object.prototype); //true
console.log(Animal.prototype.constructor == Animal); // true
console.log(Object.prototype.__proto__); // null
```
这么说可能比较抽象，我画了一张图帮大家彻底理解他们之间的关系：

![image](/uploads/i5_1.png)

特殊的情况：**Function 可以充当对象，也可以充当函数**

```js
console.log(Function.__proto__ === Function.prototype);
console.log(Object.__proto__ === Function.prototype);
console.log(Object.__proto__ === Function.__proto__ );
```
## 5. 谈谈你对闭包的理解

闭包属于一种特殊的作用域。它的定义可以理解为: 父函数被销毁 的情况下，返回出的子函数的`[[scope]]`中仍然保留着父级的单变量对象和作用域链，因此可以继续访问到父级的变量对象，这样的函数称为闭包。

* 闭包会产生一个很经典的问题:
多个子函数的`[[scope]]`都是同时指向父级，是完全共享的。因此当父级的变量对象被修改时，所有子函数都受到影响。

* 解决:
1. 变量可以通过 `函数参数的形式` 传入，避免使用默认的`[[scope]]`向上查找
2. 使用`setTimeout`包裹，通过第三个参数传入
3. 使用 `块级作用域`，让变量成为自己上下文的属性，避免共享

* 手写一个简单的闭包

下面例子中的 closure 就是一个闭包：

```js
function func(){
  var a = 1,b = 2;
  
  function closure(){
    return a+b;
  }
  return closure;
}
```

## 6. 手写 call/apply()

* 特点：

1. 可以改变当前函数 this 的指向
2. 让当前函数执行

* 用法：

```js
function f1() {
  console.log(1);
}

function f2() {
  console.log(2);
}

// 让 f1 的 this 指向 f2，并且让 f1 执行
f1.call(f2);  // 1

// 如果多个 call，会让 call 方法执行，并把 call 中的 this 指向改变成 fn2
f1.call.call.call(f2);
```

* 实现：

```js
Function.prototype.call = function (context) {
  // 如果 context 存在，使用 context，如果 context 不存在，使用 window；如果 context 是普通类型，转成对象。
  context = context ? Object(context) : window;
  context.fn = this;    // this指向调用call的对象,即我们要改变this指向的函数
  let args = [];
  for(let i = 1; i < arguments.length; i++) {
    args.push('arguments['+i+']');
  }

  let result = eval('context.fn('+args+')');   // 字符串拼接参数让 fn 执行 
  delete context.fn;     // 删除我们声明的fn属性
  return result;    // 返回函数执行结果
}

Function.prototype.apply = function (context, args) {
  // 如果 context 存在，使用 context，如果 context 不存在，使用 window；如果 context 是普通类型，转成对象。
  context = context ? Object(context) : window;
  context.fn = this;

  if(!args){
    return context.fn();
  }

  let r = eval('context.fn('+args+')');
  delete context.fn;
  return r;
}
```

## 7. 手写 bind()

* 特点：

1. bind 方法可以绑定 this 指向
2. bind 方法返回一个绑定后的函数
3. 如果绑定的函数被 new，当前函数的 this 就是当前的实例
4. new 出来的实例要保证原函数的原型对象上的属性不能丢失

* 用法：

```js
// 用法一:
let person = {
  name: "Cherry",
}

function fn(name, age) {
  console.log(this.name+ '养了一只'+ name + '今年' + age + '了'); // Cherry养了一只猫今年2了
}

let bindFn = fn.bind(person, '猫');

bindFn(2);

// 用法二：
let person = {
  name: "Cherry",
}

function fn(name, age) {
  this.say = '说话'
  console.log(this);  // fn {say: "说话"}
}

let bindFn = fn.bind(person, '猫');
let instance = new bindFn(9);

// 用法三：
let person = {
  name: "Cherry",
}

function fn(name, age) {
  this.say = '说话'
}

fn.prototype.flag = '哺乳类';
let bindFn = fn.bind(person, '猫');
let instance = new bindFn(9);
console.log(instance.flag);
```

* 实现：

```js
Funcition.protoType.bind = function (context) {
  // this表示调用bind的函数
  let that = this;
  let bindArgs = Array.prototype.slice.call(arguments, 1);  //["猫"]
  function Fn() {}
  function fBound() {
    let args = Array.prototype.slice.call(arguments);  //[9] 
    //this instanceof fBound为true表示构造函数的情况。如new bindFn(9);
    return that.apply(this instanceof fBound ? this : context, bindArgs.concat(args));
  }

  // 继承原型上的属性和方法
  fn.prototype = this.prototype;
  fBound.prototype = new Fn();
  return fBound;
}
```

## 9. 模拟实现 new


* 特点： 

1. 创建一个全新的对象，这个对象的__proto__要指向构造函数的原型对象
2. 执行构造函数
3. 返回值为object类型则作为new方法的返回值返回，否则返回上述全新对象

* 用法：

```js
function Animal(type) {
  this.type = type;   // 实例上的属性
  // 如果当前构造函数返回的是一个引用类型，需要直接返回这个对象
  return {name: 'dog'}
}

Animal.prototype.say = function () {
  console.log('say');
}

let animal = new Animal('哺乳类');

console.log(animal.type); // 哺乳类
animal.say(); // say

```

* 实现：

```js
// new是关键字,这里我们用函数mockNew来模拟
function mockNew() {
  // Constructor => animal，剩余的 arguments 就是其他的参数
  let Constructor = [].shift.call(arguments);
  let obj = {};  //返回的结果
  obj.__proto__ = Constructor.prototype;
  Constructor.apply(obj, arguments);
  return r instanceof Object ? r : obj;
}  
```
## 9. 数组扁平化

对于`[1, [1,2], [1,2,3]]`这样多层嵌套的数组，我们如何将其扁平化为`[1, 1, 2, 1, 2, 3]`这样的一维数组呢：

1. ES6的flat()

```js
const arr = [1, [1,2], [1,2,3]]
arr.flat(Infinity)  // [1, 1, 2, 1, 2, 3]
```

2. 序列化后正则

```js
const arr = [1, [1,2], [1,2,3]]
const str = `[${JSON.stringify(arr).replace(/(\[|\])/g, '')}]`
JSON.parse(str)   // [1, 1, 2, 1, 2, 3]
```

3. 递归

对于树状结构的数据，最直接的处理方式就是递归

```js
const arr = [1, [1,2], [1,2,3]]
function flat(arr) {
  let result = []
  for (const item of arr) {
    item instanceof Array ? result = result.concat(flat(item)) : result.push(item)
  }
  return result
}

flat(arr) // [1, 1, 2, 1, 2, 3]
```

4. reduce()递归

```js
const arr = [1, [1,2], [1,2,3]]
function flat(arr) {
  return arr.reduce((prev, cur) => {
    return prev.concat(cur instanceof Array ? flat(cur) : cur)
  }, [])
}

flat(arr)  // [1, 1, 2, 1, 2, 3]
```

5. 迭代+展开运算符

```js
// 每次while都会合并一层的元素，这里第一次合并结果为[1, 1, 2, 1, 2, 3, [4,4,4]]
// 然后arr.some判定数组中是否存在数组，因为存在[4,4,4]，继续进入第二次循环进行合并
let arr = [1, [1,2], [1,2,3,[4,4,4]]]
while (arr.some(Array.isArray)) {
  arr = [].concat(...arr);
}
```

## 10. ES5如何实现继承

说到继承，最容易想到的是ES6的`extends`，当然如果只回答这个肯定不合格，我们要从函数和原型链的角度上实现继承，下面我们一步步地、递进地实现一个合格的继承

一、原型链继承

原型链继承的原理很简单，直接让子类的原型对象指向父类实例，当子类实例找不到对应的属性和方法时，就会往它的原型对象，也就是父类实例上找，从而实现对父类的属性和方法的继承

```js
// 父类
function Parent() {
    this.name = 'Cherry'
}
// 父类的原型方法
Parent.prototype.getName = function() {
    return this.name
}
// 子类
function Child() {}

// 让子类的原型对象指向父类实例, 这样一来在Child实例中找不到的属性和方法就会到原型对象(父类实例)上寻找
Child.prototype = new Parent()
Child.prototype.constructor = Child // 根据原型链的规则,顺便绑定一下constructor, 这一步不影响继承, 只是在用到constructor时会需要

// 然后Child实例就能访问到父类及其原型上的name属性和getName()方法
const child = new Child()
child.name          // 'Cherry'
child.getName()     // 'Cherry'
```

原型继承的缺点:

1. 由于所有Child实例原型都指向同一个Parent实例, 因此对某个Child实例的父类引用类型变量修改会影响所有的Child实例
2. 在创建子类实例时无法向父类构造传参, 即没有实现super()的功能

```js
// 示例:
function Parent() {
    this.name = ['Cherry'] 
}
Parent.prototype.getName = function() {
    return this.name
}
function Child() {}

Child.prototype = new Parent()
Child.prototype.constructor = Child 

// 测试
const child1 = new Child()
const child2 = new Child()
child1.name[0] = 'foo'
console.log(child1.name)          // ['foo']
console.log(child2.name)          // ['foo'] (预期是['Cherry'], 对child1.name的修改引起了所有child实例的变化)
```

二、构造函数继承

构造函数继承，即在子类的构造函数中执行父类的构造函数，并为其绑定子类的this，让父类的构造函数把成员属性和方法都挂到子类的this上去，这样既能避免实例之间共享一个原型实例，又能向父类构造方法传参

```js
function Parent(name) {
    this.name = [name]
}
Parent.prototype.getName = function() {
    return this.name
}
function Child() {
    Parent.call(this, 'Cherry')   // 执行父类构造方法并绑定子类的this, 使得父类中的属性能够赋到子类的this上
}

//测试
const child1 = new Child()
const child2 = new Child()
child1.name[0] = 'foo'
console.log(child1.name)          // ['foo']
console.log(child2.name)          // ['Cherry']
child2.getName()                  // 报错,找不到getName(), 构造函数继承的方式继承不到父类原型上的属性和方法
```

构造函数继承的缺点:

1. 继承不到父类原型上的属性和方法

三、组合式继承

既然原型链继承和构造函数继承各有互补的优缺点, 那么我们为什么不组合起来使用呢, 所以就有了综合二者的组合式继承

```js
function Parent(name) {
    this.name = [name]
}
Parent.prototype.getName = function() {
    return this.name
}
function Child() {
    // 构造函数继承
    Parent.call(this, 'Cherry') 
}
//原型链继承
Child.prototype = new Parent()
Child.prototype.constructor = Child

//测试
const child1 = new Child()
const child2 = new Child()
child1.name[0] = 'foo'
console.log(child1.name)          // ['foo']
console.log(child2.name)          // ['Cherry']
child2.getName()                  // ['Cherry']
```

组合式继承的缺点:

1. 每次创建子类实例都执行了两次构造函数(Parent.call()和new Parent())，虽然这并不影响对父类的继承，但子类创建实例时，原型中会存在两份相同的属性和方法，这并不优雅

四、寄生式组合继承：

为了解决构造函数被执行两次的问题, 我们将指向父类实例改为指向父类原型, 减去一次构造函数的执行

```js
function Parent(name) {
    this.name = [name]
}
Parent.prototype.getName = function() {
    return this.name
}
function Child() {
    // 构造函数继承
    Parent.call(this, 'Cherry') 
}
//原型链继承
// Child.prototype = new Parent()
Child.prototype = Parent.prototype  //将`指向父类实例`改为`指向父类原型`
Child.prototype.constructor = Child

//测试
const child1 = new Child()
const child2 = new Child()
child1.name[0] = 'foo'
console.log(child1.name)          // ['foo']
console.log(child2.name)          // ['Cherry']
child2.getName()                  // ['Cherry']
```

但这种方式存在一个问题，由于子类原型和父类原型指向同一个对象，我们对子类原型的操作会影响到父类原型，例如给Child.prototype增加一个getName()方法，那么会导致Parent.prototype也增加或被覆盖一个getName()方法，为了解决这个问题，我们给Parent.prototype做一个浅拷贝

```js
function Parent(name) {
    this.name = [name]
}
Parent.prototype.getName = function() {
    return this.name
}
function Child() {
    // 构造函数继承
    Parent.call(this, 'Cherry') 
}
//原型链继承
// Child.prototype = new Parent()
Child.prototype = Object.create(Parent.prototype)  //将`指向父类实例`改为`指向父类原型`
Child.prototype.constructor = Child

//测试
const child = new Child()
const parent = new Parent()
child.getName()                  // ['Cherry']
parent.getName()                 // 报错, 找不到getName()
```
到这里我们就完成了ES5环境下的继承的实现，这种继承方式称为`寄生组合式继承`，是目前最成熟的继承方式，babel对ES6继承的转化也是使用了寄生组合式继承
我们回顾一下实现过程：
一开始最容易想到的是`原型链继承`，通过把子类实例的原型指向父类实例来继承父类的属性和方法，但原型链继承的缺陷在于`对子类实例继承的引用类型的修改会影响到所有的实例对象以及无法向父类的构造方法传参`。
因此我们引入了`构造函数继承`, 通过在子类构造函数中调用父类构造函数并传入子类this来获取父类的属性和方法，但构造函数继承也存在缺陷，构造函数继承`不能继承到父类原型链上的属性和方法`。
所以我们综合了两种继承的优点，提出了`组合式继承`，但组合式继承也引入了新的问题，它`每次创建子类实例都执行了两次父类构造方法`，我们通过将子类原型指向父类实例改为子类原型指向父类原型的浅拷贝来解决这一问题，也就是最终实现 —— `寄生组合式继承`


# React

# Vue

# Webpack

# Node

# NPM

# 算法

## 1. 冒泡排序

* 思路：拿当前项和后一项比较，符合条件的两者交换位置。最多比较`arr.length-1`轮。**时间复杂度**：O(n^2)

* 实现：

```js
function swap(arr, i, j) {
  let temp = arr[i];
  arr[i] = arr[j];
  arr[j] = temp;
  return arr;
}

Array.prototype.bubble = function bubble() {
  // 外层循环 i 控制比较的轮数
  for (let i = 0; i < this.length - 1; i++) {
    // 里层循环 j 控制每一轮比较的次数
    for (let j = 0; j < this.length - 1 - i; j++) {
      if (this[j] > this[j + 1]) {
        // 当前项大于后一项，交换位置
        swap(this, j, j+1);
      }   
    }  
  }
  return this;  
}

let array = [12, 8, 24, 16, 1];
array.bubble();
console.log(array);
```

**如何优化一个冒泡排序：**

冒泡排序总会执行(N-1)+(N-2)+(N-3)+...+2+1轮，但如果运行到当中某一轮时排序已经完成，或者输入时就是一个有序数组，那么后面的比较就会是多余的，为了比较这种情况，我们可以增加一个flag，判断数组是否在排序途中已经有序，也就是**判断是否有元素交换**。

```js
function swap(arr, i, j) {
  let temp = arr[i];
  arr[i] = arr[j];
  arr[j] = temp;
  return arr;
};

Array.prototype.bubble = function bubble() {
  for (let i = 0; i < this.length - 1; i++) {
    // 优化：
    let flag = true;
    for (let j = 0; j < this.length - 1 - i; j++) {
      if (this[j] > this[j + 1]) {
        swap(this, j, j+1);
        // 优化：
        flag = false;
      } 
    } 
    // 优化：如果`某次循环`中没有交换过元素，那么意味着排序已经完成
    if (flag) break; 
  }
  return this;  
};

let array = [12, 8, 24, 16, 1];
array.bubble();
console.log(array); // [ 1, 8, 12, 16, 24 ]
```

## 2. 选择排序

* 思路：遍历自身后面的元素和自身比较，让比自身小的元素跟自己调换位置，最多比较`arr.length-1`轮。**时间复杂度**：O(n^2)

* 实现：

```js
function swap(arr, i, j) {
  let temp = arr[i];
  arr[i] = arr[j];
  arr[j] = temp;
  return arr;
};

Array.prototype.select = function select() {
  for (let j = 0; j < this.length - 1; j++) {
    let min = j;
    // 找到比当前项还小的这一项索引
    for (let i = min + 1; i < this.length; i++){
      if (this[i] < this[min]) {
        min = i;
      }  
    }
    // 让最小的项和当前首位交换位置
    swap(this, min, j);
  }
  return this;
};

let array = [12, 8, 24, 16, 1];
array.select();
console.log(array); // [ 1, 8, 12, 16, 24 ]
```

## 3. 插入排序

* 思路：将元素插入到已排序好的数组中，类似于玩扑克(抓一张牌，跟手里的牌依次比较，顺序将其插入到合适的位置)。**时间复杂度**：O(n^2)

* 实现：

```js
Array.prototype.insert = function insert() {
  // 1.准备一个新数组，用来存储抓到手里的牌，开始先抓一张牌进来
  let handle = [];
  handle.push(this[0]);
  

  // 2.从第二项开始依次抓牌，一直到把台面上的牌抓光
  for (let i = 1; i < this.length; i++) {
    // A是新抓的牌
    let A = this[i];
    // 和handle手里的牌依次比较（从后向前比）
    for (let j = handle.length - 1; j >= 0; j--) {
      // 每一次要比较的手里的牌
      let B = handle[j];
      // 如果当前新牌A比要比较的牌B大了，把A放到B的后面
      if (A > B) {
        handle.splice(j+1, 0, A);
        break;
      }
      if (j === 0) {
        // 已经比到第一项，我们把新牌放到手中最前面即可
        handle.unshift(A);
      }
    }
  }
  return handle;
}

array = [12, 8, 24, 16, 1].insert();
console.log(array); // [ 1, 8, 12, 16, 24 ]
```

## 4. 希尔排序

* 思路：先算出一个间隔数（数组总长度/2），然后从第一项开始依次跟间隔数后的那一项进行比较。然后再让间隔数=上次间隔数/2，从第一次项开始依次跟新的间隔数后的那一项进行比较。**时间复杂度**：O(n*logn)

* 实现：

```js
function swap(arr, i, j) {
  let temp = arr[i];
  arr[i] = arr[j];
  arr[j] = temp;
  return arr;
};

Array.prototype.shell = function shell() {
  let gap = Math.floor(this.length / 2);
  while (gap >= 1) {
    for (let i = gap; i < this.length; i++) {
      while (i - gap >= 0 && this[i] < this[i - gap]) {
        swap(this, i, i - gap);
        i = i - gap;
      }
    }
    gap = Math.floor(gap / 2);
  }
};

let array = [58, 23, 67, 36, 40, 46, 35, 28, 20, 10];
array.shell();
console.log(array); // [ 10, 20, 23, 28, 35, 36, 40, 46, 58, 67 ]
```


## 5. 快速排序

* 思路：
1. 选取基准元素：数组的中间项，并将基准元素在数组中移除
2. 循环遍历数组，比基准元素小的元素放在左右，大的放右边
3. 在左右子数组中重复步骤一二，直到数组只剩下一个元素
4. 向上逐级合并数组，拼接左边+中间+右边成最后的结果

* **时间复杂度**：O(n*logn)

* 实现：
```js
function quick(arr) {
  // 4.结束递归（当数组中小于等于一项，则不用处理）
  if(arr.length <= 1) {
    return arr;
  }

  //  1.找到数组的中间项，在原有的数组中把它移除
  let middleIndex = Math.floor(arr.length / 2);
  let middleValue = arr.splice(middleIndex, 1)[0];

  // 2.准备左右两个数组，循环剩下数组中的每一项，比当前项小的放在左边数组中，比当前项大的放在右边数组中
  let arrLeft = [],arrRight = [];
  for(let i = 0; i < arr.length; i++) {
    const item = arr[i];
    item < middleValue ? arrLeft.push(item) : arrRight.push(item);
  }
  return quick(arrLeft).concat(middleValue,quick(arrRight));
}

console.log(quick([12, 8, 15, 16, 1, 24])); // [ 1, 8, 12, 15, 16, 24 ]
```

## 6. 堆排序

* 思路：

1. 初始化大(小)根堆，此时根节点为最大(小)值，将根节点与最后一个节点(数组最后一个元素)交换
2. 除开最后一个节点，重新调整大(小)根堆，使根节点为最大(小)值
3. 重复步骤二，直到堆中元素剩一个，排序完成

* 实现：
```js
function swap(arr, i, j) {
  let temp = arr[i];
  arr[i] = arr[j];
  arr[j] = temp;
  return arr;
};

function heap(arr) {
  // 我们用数组来储存这个大根堆,数组就是堆本身
  // 初始化大顶堆，从第一个非叶子结点开始
  for (let i = Math.floor(arr.length / 2 - 1); i >= 0; i--) {
    heapify(arr, i, arr.length);
  }
  // 排序，每一次 for 循环找出一个当前最大值，数组长度减一
  for (let i = Math.floor(arr.length - 1); i > 0; i--) {
    // 根节点与最后一个节点交换
    swap(arr, 0, i);
    // 从根节点开始调整，并且最后一个结点已经为当前最大值，不需要再参与比较，所以第三个参数为 i，即比较到最后一个结点前一个即可
    heapify(arr, 0, i);
  }
  return arr;
};

// 将 i 结点以下的堆整理为大顶堆，注意这一步实现的基础实际上是：
// 假设结点 i 以下的子堆已经是一个大顶堆，heapify 函数实现的
// 功能是实际上是：找到 结点 i 在包括结点 i 的堆中的正确位置。
// 后面将写一个 for 循环，从第一个非叶子结点开始，对每一个非叶子结点
// 都执行 heapify 操作，所以就满足了结点 i 以下的子堆已经是一大顶堆
function heapify(array, i, length) {
  let temp = array[i]; // 当前父节点
  // j < length 的目的是对结点 i 以下的结点全部做顺序调整
  for (let j = 2 * i + 1; j < length; j = 2 * j + 1) {
    temp = array[i]; // 将 array[i] 取出，整个过程相当于找到 array[i] 应处于的位置
    if (j + 1 < length && array[j] < array[j + 1]) {
      j++; // 找到两个孩子中较大的一个，再与父节点比较
    }
    if (temp < array[j]) {
      swap(array, i, j); // 如果父节点小于子节点:交换；否则跳出
      i = j; // 交换后，temp 的下标变为 j
    } else {
      break;
    }
  }
}
```

## 7. 归并排序

* 思路：归并排序和快排的思路类似，都是递归分治，区别在于快排边分区边排序，而归并在分区完成后才会排序

* 实现：

```js
function mergeSort(arr) {
  if(arr.length <= 1) return arr		//数组元素被划分到剩1个时，递归终止
  const midIndex = arr.length/2 | 0
  const leftArr = arr.slice(0, midIndex)
  const rightArr = arr.slice(midIndex, arr.length)
  return merge(mergeSort(leftArr), mergeSort(rightArr))	//先划分，后合并
}

//合并
function merge(leftArr, rightArr) {
  const result = []
  while(leftArr.length && rightArr.length) {
    leftArr[0] <= rightArr[0] ? result.push(leftArr.shift()) : result.push(rightArr.shift())
  }
  while(leftArr.length) result.push(leftArr.shift())
  while(rightArr.length) result.push(rightArr.shift())
  return result
}
```

## 总结

口诀: 插冒归基稳定，快选堆希不稳定

| 排序算法 | 平均时间复杂度 | 最坏时间复杂度 | 空间复杂度 | 是否稳定 |
| -----  | :-:  | :-: | :-:  | ---: |
| 冒泡排序    | O(n^2)  |  O(n^2) |  O(1) | 是 |
| 选择排序    | O(n^2)  |  O(n^2) |  O(1) | 不是 |
| 插入排序    | O(n^2)  |  O(n^2) |  O(1) | 是 |
| 快速排序    | O(nlogn)  |  O(n^2) |  O(logn) | 不是 |
| 希尔排序    | O(nlogn)  |  O(n^s) |  O(1) | 不是 |
| 堆排序    | O(nlogn)  |  O(nlogn) |  O(1) | 不是 |
| 归并排序    | O(nlogn)  |  O(nlogn) |  O(n) | 是 |

稳定性： 同大小情况下是否可能会被交换位置, 虚拟dom的diff，不稳定性会导致重新渲染；

## 8.二叉树和二叉查找树

* 树：树是 n 个节点的有限集，有且仅有一个特定的称为根的节点。当 n>1 时，其余节点可分为 m 个互不相交的有限集，每一个集合本身又是一个树，并称为根的子树。

* 二叉树：二叉树是树的一种特殊形式，每一个节点最多有两个孩子节点。

  * 满二叉树：一个二叉树的所有非叶子节点都存在左右孩子，并且所有叶子节点都在同一层级上。

  ![](/uploads/q_1.png)

  

  * 完全二叉树：最后一个节点之前的节点都齐全即可

  ![](/uploads/q_2.png)

* 二叉树可以用哪些物理存储结构来表述？

  * 链表：

    1. 存储数据的data变量
    2. 指向左孩子的left指针
    3. 指向左孩子的right指针

  ![](/uploads/q_3.png)

  * 数组：

  使用数组存储时，会按照层级顺序把二叉树的节点放到数组中对应的位置上。 如果某一个节点的左孩子或右孩子空缺，则数组的相应位置也空出来。

  为什么这样设计呢？因为这样可以更方便地在数组中定位二叉树的孩子节点和父节点。

  ![](/uploads/q_4.png)

* 二叉树最主要的应用在**查找操作**和**维持相对顺序**两个方面。

* 二叉查找树：

  下图就是一个标准的二叉查找树。

  ![](/uploads/q_5.png)

  * 二叉查找树在二叉树的基础上增加了以下几个条件：

    1. 如果左子树不为空，则左子树上所有节点的值均小于根节点的值
    2. 如果右子树不为空，则右子树上所有节点的值均大于根节点的值
    3. 左、右子树也都是二叉查找树

  * 遍历二叉查找树
    * 深度优先遍历：
      * **前序遍历**：输出顺序是根节点、左子树、右子树。
        ![](/uploads/q_6.png)
      * **中序遍历**：输出顺序是左子树、根节点、右子树。
        ![](/uploads/q_7.png)
      * **后序遍历**：输出顺序是左子树、右子树、根节点。
        ![](/uploads/q_8.png)
    * 广度优先遍历：
      * **层序遍历**：树按照从根节点到叶子节点的层次关系，一层 一层横向遍历各个节点。
        ![](/uploads/q_8.png)

  * 实现二叉查找树

```js
class TreeNode {
  constructor(data) {
    this.data = data;
    this.left = null;
    this.right = null;
  }
}

class BinarySearchTree {
  constructor() {
    this.root = null
  }

  // 向树中插入一个节点，判断大小决定插入左侧还是右侧
  insert(data) {
    let newNode = new TreeNode(data);
    let insertNode = function (root, newNode) {
      if (newNode.data < root.data) {
        if (root.left === null) {
          root.left = newNode
        } else {
          insertNode(root.left, newNode)
        }
      } else {
        if (root.right === null) {
          root.right = newNode
        } else {
          insertNode(root.right, newNode)
        }
      }
    }
    if (this.root === null) {
      this.root = newNode;
    } else {
      insertNode(this.root, newNode)
    }
  }

  // 在树中查找一个节点
  find(data) {
    let findNode = function (node, key) {
      if (node === null) return null;
      if (key < node.data) {
        return findNode(node.left, key)
      } else if (key > node.data) {
        return findNode(node.right, key)
      } else {
        return node
      }
    }
    return findNode(this.root, data)
  }

  // 最小节点
  min(node = this.root) {
    let minNode = function (node) {
      if (node === null) return null;
      while (node && node.left !== null) {
        node = node.left
      }
      return node
    }
    return minNode(node)
  }

  // 最大节点
  max(node = this.root) {
    let maxNode = function (node) {
      if (node === null) return null;
      while (node && node.right !== null) {
        node = node.right
      }
      return node
    }
    return maxNode(node)
  }

  // 前序遍历
  preOrderTraveral(callback) {
    let preOrderTraveralNode = function(node, callback) {
      if (node !== null) {
        callback(node.data)
        preOrderTraveralNode(node.left, callback)
        preOrderTraveralNode(node.right, callback)
      }
    }
    preOrderTraveralNode(this.root, callback)
  }

  // 中序遍历
  inOrderTraveral(callback) {
    let inOrderTraveralNode = function(node, callback) {
      if (node !== null) {
        inOrderTraveralNode(node.left, callback)
        callback(node.data)
        inOrderTraveralNode(node.right, callback)
      }
    }
    inOrderTraveralNode(this.root, callback)
  }

  // 后序遍历
  postOrderTraveral(callback) {
    let postOrderTraveralNode = function(node, callback) {
      if (node !== null) {
        postOrderTraveralNode(node.left, callback)
        postOrderTraveralNode(node.right, callback)
        callback(node.data)
      }
    }
    postOrderTraveralNode(this.root, callback)
  }

  // 从树中移除一个节点
  remove(data) {
    let removeNode = function(node, key) {
      if (node === null) return null;
      if (key < node.data) {
        node.left = removeNode(node.left, key)
        return node
      } else if (key > node.data) {
        node.right = removeNode(node.right, key)
        return node
      } else {
        // 情况1：没有孩子节点
        if (node.left === null && node.right === null) {
          node = null
          return node
        }
        // 情况2：只有一个孩子节点
        if (node.left === null) {
          node = node.right
          return node
        } else if (node.right === null) {
          node = node.left
          return node
        }
        // 情况3：左孩子和右孩子都存在
        let temp = this.min(node.left)
        node.data = temp.data
        node.right = removeNode(node.right, temp.data)

        return node
      }
    }
    this.root = removeNode(this.root, data)
  }
  
}

function output(data) {
  console.log(data)
}

let btree = new BinarySearchTree();

btree.insert(3)
btree.insert(4)
btree.insert(2)
btree.insert(7)
btree.insert(9)

console.log('前序遍历：')
btree.preOrderTraveral(output) // 3 2 4 7 9

console.log('中序遍历：')
btree.inOrderTraveral(output) // 2 3 4 7 9

console.log('后序遍历：')
btree.postOrderTraveral(output) // 2 9 7 4 3

btree.remove(7)
```

* 二叉堆：特殊的完全二叉树，分为最大堆和最小堆
  * 最大堆：任何一个父节点的值都大于两个子节点
  * 最小堆：任何一个父节点的值都小于两个子节点

* 优先队列：最大优先队列和最小优先队列
  * 在最大优先队列中，无论入队顺序如何，当前最大的元素都会优先出队
  * 在最小优先队列中，无论入队顺序如何，当前最小的元素都会优先出队


注：图片来源于《算法漫画》侵删

# 设计模式

# 计算机网络

## 1. 说说浏览器的缓存策略

### 浏览器缓存位置和优先级

1. Service Worker
2. Memory Cache（内存缓存）
3. Disk Cache（硬盘缓存）
4. Push Cache（推送缓存）
5. 以上缓存都没命中就会进行网络请求

### 不同缓存位置的差别

> Service Worker

和Web Worker类似，是独立的线程，我们可以在这个线程中缓存文件，在主线程需要的时候读取这里的文件，Service Worker使我们可以自由选择缓存哪些文件以及文件的匹配、读取规则，并且缓存是持续性的。

> Memory Cache

即内存缓存，内存缓存不是持续性的，缓存会随着进程释放而释放。

> Disk Cache

即硬盘缓存，相较于内存缓存，硬盘缓存的持续性和容量更优，它会根据HTTP header的字段判断哪些资源需要缓存。

> Push Cache

即推送缓存，是HTTP/2的内容，目前应用较少。

### 浏览器缓存策略

缓存分为**强缓存**和**协商缓存**。设置为强缓存，之后的请求都不访问服务器，直接从缓存中找，默认返回状态码是200，且默认强制缓存不缓存首页资源；设置为协商缓存后，每次请求仍然要向服务器询问缓存是否过期，返回状态码是304。**两种缓存机制同时存在时，强缓存高于协商缓存。**

> 强缓存(不要向服务器询问的缓存)

**设置Expires(HTTP1.0)**：

* 即过期时间，表示缓存会在这个时间后失效，这个过期日期是绝对日期，如果修改了本地日期，或者本地日期与服务器日期不一致，那么将导致缓存过期时间错误。

  ```js
  res.setHeader('Expires', new Date(Date.now()+3600*1000).toGMTString());
  ```

**设置Cache-Control(HTTP/1.1)**：

* HTTP/1.1新增字段，Cache-Control可以通过`max-age`字段来设置过期时间，除此之外`Cache-Control`还有很多属性，不同的属性代表不同的含义:

  * private：客户端可以缓存
  * public：客户端和代理服务器都可以缓存
  * max-age=t：缓存内容将在t秒后失效
  * **no-cache**：**默认值**，需要使用协商缓存来验证缓存数据，意思不是不缓存，而是**请求且缓存**。
  * **no-store**：真正不缓存，意思是**请求但不缓存**。

  ```js
  res.setHeader('Cache-Control', 'max-age=3600');
  res.setHeader('Cache-Control', 'no-cache');
  ```

请注意no-cache指令很多人误以为是不缓存，这是不准确的，no-cache的意思是可以缓存，但每次都会向服务器验证缓存是否可用。no-store才是不缓存。当在首部字段 Cache-Control 有指定 max-age 指令时，**Cache-Control的 max-age 优先级高于 Expires。**命中强缓存的表现形式：Firefox浏览器表现为一个灰色的200状态码。Chrome浏览器状态码表现为200 (from disk cache)或是200 OK (from memory cache)。


> 协商缓存(当缓存已经过期时，使用协商缓存）

* **Last-Modified & if-Modified-Since(HTTP/1.0)**：对比修改时间

**Last-Modified**：即最后修改时间，浏览器第一次请求资源时，服务器会在响应头上加上`Last-Modified`，告诉浏览器资源的最后修改时间。

**if-Modified-Since**：当浏览器再次请求该资源时，浏览器会在请求头中带上`If-Modified-Since`字段，字段的值就是之前服务器返回的最后修改时间，服务器对比这两个时间，若相同，浏览器直接从缓存中获取数据信息。返回状态码304；若不同则返回新资源，返回状态码200，并更新`Last-Modified`。

**缺点**：

  1. 内容没变时间变化了，也会重新读取内容；

  2. 时间不精准，时间的最小粒度只到`s`，`s`以内的改动无法检测到。

* **Etag & If-None-Match**：对比唯一标识，这种比较方式比较精准，但是默认不会根据完整内容生成唯一标识；为了保证精确度，我们一般会用内容的一部分+文件的总大小来生成唯一标识。**Etag 的优先级高于 Last-Modified**。

**Etag(HTTP/1.1)**：服务器响应请求时，通过此字段告诉浏览器当前资源在服务器生成的唯一标识（生成规则由服务器决定）。

**If-None-Match**：当浏览器再次请求该资源时，浏览器会在请求头中带上`If-None-Match`字段，字段的值就是在缓存中获取的唯一标识。服务器对比两次请求的标识，若相同说明资源没有被修改，浏览器直接从缓存中获取数据信息。返回状态码304；若不同则说明资源被改动过，响应整个资源内容，返回状态码200。

**缺点**：

  1. 文件内容越大，越耗性能

### 缓存场景

对于大部分的场景都可以使用强缓存配合协商缓存解决，但是在一些特殊的地方可能需要选择特殊的缓存策略

* 对于某些不需要缓存的资源，可以使用 Cache-control: no-store ，表示该资源不需要缓存
* 对于频繁变动的资源，可以使用 Cache-Control: no-cache 并配合 ETag 使用，表示该资源已被缓存，但是每次都会发送请求询问资源是否更新
* 对于代码文件来说，通常使用 Cache-Control: max-age=31536000 并配合策略缓存使用，然后对文件进行指纹处理，一旦文件名变动就会立刻下载新的文件

## 2.  HTTP/1.0和HTTP/1.1有什么区别

* **持久连接**： HTTP/1.1 支持持久连接和请求的流水线，在一个TCP连接上可以传送多个HTTP请求，只要浏览器或者服务器没有明确断开连接，那么该 TCP 连接会一直保持，这样的好处是避免了因为多次建立TCP连接的时间消耗，减少了服务器额外的负担，并提升整体 HTTP 的请求时长。目前浏览器中对于同一个域名，**默认允许同时建立 6 个 TCP 持久连接。**
* **提供虚拟主机的支持**： 在 HTTP/1.0 中认为每台服务器都有唯一的 IP 地址，但随着虚拟主机技术的发展，多个主机共享一个 IP 地址愈发普遍。因此，HTTP/1.1 的请求头中增加了 Host 字段，用来表示当前的域名地址，这样服务器就可以根据不同的 Host 值做不同的处理。
* **缓存处理**： HTTP/1.1 引入Entity tag，If-Unmodified-Since, If-Match, If-None-Match等新的请求头来控制缓存。
* **对动态生成的内容提供了完美支持**： 随着服务器端的技术发展，很多页面的内容都是动态生成的，因此在传输数据之前并不知道最终的数据大小，这就导致了浏览器不知道何时会接收完所有的文件数据。HTTP/1.1 通过引入 Chunk transfer 机制来解决这个问题，服务器会将数据分割成若干个任意大小的数据块，每个数据块发送时会附上上个数据块的长度，最后使用一个零长度的块作为发送数据完成的标志。这样就提供了对动态内容的支持。
* **客户端 Cookie、安全机制**：HTTP1.1 引入了 cookie 安全机制。
* **带宽优化及网络连接的使用**： HTTP1.1 则在请求头引入了 range 头域，支持断点续传功能。

## 3. 介绍一下HTTP/2.0新特性

* **多路复用**： 一个域名都通过一个 TCP 连接并发地完成。主要是为了规避 TCP 的慢启动，TCP 连接之间的竞争问题和队头阻塞问题。
* **服务端推送**： 服务端能够主动把资源推送给客户端。
* **可以设置请求的优先级**：在发送请求时，标上该请求的优先级，这样服务器接收到请求之后，会优先处理优先级高的请求。
* **新的二进制格式**： HTTP/2采用二进制格式传输数据，相比于HTTP/1.1的文本格式，二进制格式具有更好的解析性和拓展性。
* **头部压缩**： HTTP/2压缩消息头，减少了传输数据的大小。

## 4. 说说HTTP/3.0
尽管HTTP/2解决了很多1.1的问题，但HTTP/2仍然存在一些缺陷，这些缺陷并不是来自于HTTP/2协议本身，而是来源于底层的TCP协议，我们知道TCP链接是可靠的连接，如果出现了丢包，那么整个连接都要等待重传，HTTP/1.1可以同时使用6个TCP连接，一个阻塞另外五个还能工作，但HTTP/2只有一个TCP连接，阻塞的问题便被放大了。

由于TCP协议已经被广泛使用，我们很难直接修改TCP协议，基于此，HTTP/3选择了一个折衷的方法——UDP协议，HTTP/2在UDP的基础上实现多路复用、0-RTT、TLS加密、流量控制、丢包重传等功能。

## 5. 常见HTTP状态码有哪些

# 前端安全

在 Web 安全领域中，XSS 和 CSRF 是最常见的攻击方式。章将会简单介绍 XSS 和 CSRF 的攻防问题。

## 1. 什么是CSRF攻击

> CSRF即Cross-site request forgery(跨站请求伪造)，是一种挟制用户在当前已登录的Web应用程序上执行非本意的操作的攻击方法。

假如黑客在自己的站点上放置了其他网站的外链，例如`"www.weibo.com/api`，默认情况下，浏览器会带着`weibo.com`的cookie访问这个网址，如果用户已登录过该网站且网站没有对CSRF攻击进行防御，那么服务器就会认为是用户本人在调用此接口并执行相关操作，致使账号被劫持。

## 2. CSRF特点

* CSRF（通常）发生在第三方域名；

* CSRF攻击者（通常）不能获取到Cookie等信息，只是使用；

## 3. 如何防御CSRF攻击

* 验证`Token`：浏览器请求服务器时，服务器返回一个token，然后放在页面中，页面提交请求的时候，带上这个Token。服务端把Token从Session中拿出，与请求中的Token进行比对验证。

* 验证`Referer`：通过验证请求头的Referer来验证来源站点，但请求头很容易伪造

* 设置`SameSite`：设置cookie的SameSite，可以让cookie不随跨域请求发出，但浏览器兼容不一

* 验证码：CSRF 攻击往往是在用户不知情的情况下构造了网络请求。而验证码会强制用户必须与应用进行交互，才能完成最终请求。因此验证码被认为是对抗 CSRF 攻击最简洁而有效的防御方法。但验证码并不是万能的，因为出于用户考虑，不能给网站所有的操作都加上验证码。所以验证码只能作为防御 CSRF 的一种辅助手段，而不能作为最主要的解决方案。

## 4. 什么是XSS攻击

> XSS即Cross Site Scripting（跨站脚本攻击），指的是通过利用网页开发时留下的漏洞，注入恶意指令代码到网页，使用户加载并执行攻击者恶意制造的网页程序。常见的例如在评论区植入JS代码，用户进入评论页时代码被执行，进而窃取隐私数据比如cookie、session，或者重定向到不好的网站等。

## 5. XSS攻击有哪些类型

* **存储型**：即攻击被存储在服务端，常见的是在评论区插入攻击脚本，如果脚本被储存到服务端，那么所有看见对应评论的用户都会受到攻击。

* **反射型**：攻击者将脚本混在URL里，服务端接收到URL将恶意代码当做参数取出并拼接在HTML里返回，浏览器解析此HTML后即执行恶意代码。例如在url上添加脚本类的参数：`https://www.baidu.com?jarttoTest=<script>alert(document.cookie)</script>`。

* **DOM型**：将攻击脚本写在URL中，诱导用户点击该URL，如果URL被解析，那么攻击脚本就会被运行。和前两者的差别主要在于DOM型攻击不经过服务端

## 6. 如何防御XSS攻击

* 输入检查：**不要相信用户的任何输入**，对输入内容中的`<script><iframe>`等标签进行转义或者过滤。

* 输出检查：**不要完全信任服务端**，对服务端输出内容有规则的过滤后再输出到页面中。

* **设置httpOnly**：很多XSS攻击目标都是窃取用户cookie伪造身份认证，设置此属性可防止JS获取cookie。

* **开启CSP**：即开启白名单，可阻止白名单以外的资源加载和运行。

* 需要转义的字符有：

| 字符 | 转义后字符 |
| ----- | ---: |
| &	   | `&amp;` |
| <	   | `&lt;` |
| >	   | `&gt;` |
| "	   | `&quot;` |
| '	   | `&#x27;` |
| /	   | `&#x2F;` |


参考文章：https://github.com/dwqs/blog/issues/68