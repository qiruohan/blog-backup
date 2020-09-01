---
title: 「面试必会」中高级前端必会的手写面试题(二)
date: 2020-08-16 20:42:13
tags: 
  - JavaScript
categories: JavaScript
---

<img src="https://cdn.jsdelivr.net/gh/qiruohan/qiruohan.github.io/uploads/375312727.jpg" width="800" height="100%">
<!-- more -->

## 前言

在面试中，常常会问到一些“手写XXX”的面试题，如果我们只是停留在熟练使用这些 API，问到这种问题想必总是束手无策的。其实想要手写 API 的实现也并不难，更多的是需要我们训练自己通过使用方式来推倒实现的能力，千万不要死记硬背。最近我也在强化自己手写 API 的能力，并汇总了面试中高频的手写 API 面试题，希望对大家有所帮助～

> 前文回顾： [「面试必会」中高级前端必会的手写面试题(一)](https://juejin.im/post/6859026583533912072)

## 一、使用ES5实现类的继承

### 1. 构造函数继承

* 思路：

在子类的构造函数中执行父类的构造函数。并为其绑定子类的`this`，让父类的构造函数把成员的属性和方法都挂在`子类的this`这样能避免实例之间共享一个原型实例，又能向父类构造函数传参。

* 实现：

```js
// 父类
function Parent(){
  this.name = 'Cherry';
}
// 父类的原型方法
Parent.prototype.getName = function() {
  return this.name;
};
// 子类
function Child(){
  Parent.call(this);
  this.type = 'child';
};
const child = new Child();
console.log(child); // Child { name: 'Cherry', type: 'child' }
console.log(child.getName()); // 报错,找不到getName(), 构造函数继承的方式继承不到父类原型上的属性和方法
```

![](/uploads/api_1.png)

这么看使用构造函数继承的缺点已经很明显了：**继承不到父类原型上的属性和方法**，那么引出下面的方法。

### 2. 原型链继承

* 思路：

让子类的原型指向父类的实例，当子类实例找不到对用的属性和方法时，就会沿着原型链向上找，也就是去父类的实例上找，从而实现对父类属性和方法的继承。

* 实现：

```js
function Parent() {
  this.name = 'Cherry';
  this.play = [1, 2, 3];
}
Parent.prototype.getName = function() {
  return this.name;
}
function Child() {
  this.type = 'child';
}
// 子类的原型对象指向父类实例
Child.prototype = new Parent();
// 根据原型链的规则,顺便绑定一下constructor, 这一步不影响继承, 只是在用到constructor时会需要
Child.prototype.constructor = Child;

const child = new Child();
console.log(child);  // Parent { type: 'child' }
console.log(child.getName()); // Cherry
```
![](/uploads/api_2.png)

看似没有问题，父类的方法和属性都能够访问，但实际上有一个潜在的问题：

```js
const child1 = new Child();
const child2 = new Child();
child1.play.push(4);
console.log(child1.play, child2.play); // [ 1, 2, 3, 4 ] [ 1, 2, 3, 4 ]
```
![](/uploads/api_3.png)

在上面这个例子中，虽然我只改变了`child1`的`play`属性，但是`child2`的`play`属性也跟着变了。原因是因为**两个实例引用的是同一个原型对象。**

由此我们可以发现，使用原型链继承有以下两个缺点：

1. 由于所有Child实例原型都指向同一个Parent实例, 因此对某个Child实例的父类**引用类型**变量修改会影响所有的Child实例
2. 在创建子类实例时无法向父类构造传参, 即没有实现`super()`的功能

### 3. 组合式继承

既然原型链继承和构造函数继承各有互补的优缺点，那么我们为什么不组合起来使用呢，所以就有了综合二者的**组合式继承**。

```js
function Parent () {
  this.name = 'Cherry';
  this.play = [1, 2, 3];
}
Parent.prototype.getName = function() {
  return this.name;
}
function Child() {
  // 构造函数继承
  Parent.call(this);
  this.type = 'child';
}
//原型链继承
Child.prototype = new Parent();
// 如果不指定 Child.prototype.constructor 为 Child，根据原型链规则会默认向上查找，最后会指向 Parent
Child.prototype.constructor = Child;

const child1 = new Child();
const child2 = new Child();
console.log(child1); // Child { name: 'Cherry', play: [ 1, 2, 3 ], type: 'child' }
console.log(child1.getName()); // Cherry
child1.play.push(4);
console.log(child1.play, child2.play); // [ 1, 2, 3, 4 ] [ 1, 2, 3 ]
```

![](/uploads/api_4.png)

我们通过控制台的输出结果可以看到，之前的问题都得到了解决。但是这里又增加了一个新问题，那就是Parent的构造函数会多执行了一次`Child.prototype = new Parent();`虽然这并不影响父类的继承，但子类创建实例时，原型中会存在两份相同的属性和方法，这并不优雅。那么如何解决这个问题？

### 4. 寄生式组合继承

为了解决构造函数被执行两次的问题, 我们将`指向父类实例`改为`指向父类原型`, 减去一次构造函数的执行。

```js
function Parent () {
  this.name = 'Cherry';
  this.play = [1, 2, 3];
}

Parent.prototype.getName = function() {
  return this.name
}

function Child() {
  Parent.call(this);
  this.type = 'child';
}
// 将`指向父类实例`改为`指向父类原型`
Child.prototype = Parent.prototype;
Child.prototype.constructor = Child;

const child1 = new Child();
const child2 = new Child();
console.log(child1); // Child { name: 'Cherry', play: [ 1, 2, 3 ], type: 'child' }
console.log(child1.getName()); // Cherry
child1.play.push(4);
console.log(child1.play, child2.play); // [ 1, 2, 3, 4 ] [ 1, 2, 3 ]
```
![](/uploads/api_5.png)

但这种方式存在一个问题，由于子类原型和父类原型指向同一个对象，我们对子类原型的操作会影响到父类原型，例如给`Child.prototype`增加一个`getName()`方法，那么会使`Parent.prototype`上也增加或被覆盖一个`getName()`方法，为了解决这个问题，我们会给`Parent.prototype`做一个浅拷贝。

```js
function Parent () {
  this.name = 'Cherry';
  this.play = [1, 2, 3];
}

Parent.prototype.getName = function() {
  return this.name
}

function Child() {
  Parent.call(this);
  this.type = 'child';
}
// 给Parent.prototype做一个浅拷贝
Child.prototype = Object.create(Parent.prototype);
Child.prototype.constructor = Child;

const child1 = new Child();
const child2 = new Child();
console.log(child1); // Child { name: 'Cherry', play: [ 1, 2, 3 ], type: 'child' }
console.log(child1.getName()); // Cherry
child1.play.push(4);
console.log(child1.play, child2.play); // [ 1, 2, 3, 4 ] [ 1, 2, 3 ]
```
![](/uploads/api_6.png)

到这里我们就完成了ES5环境下的继承的实现，这种继承方式称为`寄生组合式继承`，是目前最成熟的继承方式，babel对ES6继承的转化也是使用了寄生组合式继承。

## 二、实现数组扁平化

* 概念：

将一个多维数组变为一维数组：

```js
[1, [2, 3, [4, 5]]]  ------>    [1, 2, 3, 4, 5]
```

### 1. ES6的flat()

```js
let arr = [1, [2, 3, [4, 5]]];
arr.flat(Infinity);
```

### 2. 序列化后正则

```js
let arr = [1, [2, 3, [4, 5]]];
let str = JSON.stringify(arr).replace(/(\[|\])/g, '');
str = '[' + str + ']';
JSON.parse(str);   // [1, 2, 3, 4, 5]
```

### 3. 递归处理

对于树状结构的数据，最直接的处理方式就是递归

```js
let arr = [1, [2, 3, [4, 5]]];

function flat(arr) {
  let result = [];
  for (const item of arr) {
    item instanceof Array ? result = result.concat(flat(item)) : result.push(item)
  }
  return result;
}

flat(arr);  // [1, 2, 3, 4, 5]
```

### 4. reduce

遍历数组每一项，若值为数组则递归遍历，否则直接累加。

```js
let arr = [1, [2, 3, [4, 5]]];

function flat(arr) {
  return arr.reduce((prev, current) => {
    return prev.concat(current instanceof Array ? flat(current) : current)
  }, [])
}

flat(arr);  // [1, 2, 3, 4, 5]
```

### 5. 迭代+扩展运算符

es6的扩展运算符能将二维数组变为一维

```js
// 每次while都会合并一层的元素，这里第一次合并结果为[1, 1, 2, 1, 2, 3, [4,4,4]]
// 然后arr.some判定数组中是否存在数组，因为存在[4,4,4]，继续进入第二次循环进行合并
let arr = [1, [1,2], [1,2,3,[4,4,4]]]

while (arr.some(Array.isArray)) {
  arr = [].concat(...arr);
}

console.log(arr); // [ 1, 1, 2, 1, 2, 3, 4, 4, 4 ]
```


## 三、实现数组去重

## 四、实现发布订阅模式



## 五、实现观察者模式

## 六、实现单例模式

## 七、实现Promise

重点难点，比较复杂，请参考我的另一篇文章:

[面试官：“你能手写一个 Promise 吗”](https://juejin.im/post/6850037281206566919)


## 八、实现深拷贝

重点难点，比较复杂，请参考我的另一篇文章:

[Javascript经典面试之深拷贝VS浅拷贝](https://juejin.im/post/6844904195758391310)

## 九、手写二进制转Base64

## 十、实现一个事件委托

## 十一、实现一个可以拖拽的DIV

## 十二、实现一个批量请求函数 multiRequest(urls, maxNum)

## 十三、实现一个 sleep 函数

## 十四、模拟实现一个 localStorage

## 十五、实现一个 normalize 函数，能将输入的特定的字符串转化为特定的结构化数据

## 参考

[2万字 | 前端基础拾遗90问](https://juejin.im/post/6844904116552990727)
[ES5实现继承的那些事](https://juejin.im/post/6844903872847151112)