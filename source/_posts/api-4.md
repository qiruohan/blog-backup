---
title: 「面试必会」中高级前端必会的手写面试题(二)
date: 2020-08-16 20:42:13
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
// 每次while都会合并一层的元素，然后arr.some判定数组中是否存在数组，如果存在，继续进入第二次循环进行合并
let arr = [1, [1,2], [1,2,3,[4,4,4]]]

while (arr.some(Array.isArray)) {
  arr = [].concat(...arr);
}

console.log(arr); // [ 1, 1, 2, 1, 2, 3, 4, 4, 4 ]
```


## 三、实现数组去重

### 1. 使用 filter 方法

filter 方法可以过滤掉不符合条件的元素，并返回一个新数组，任何不符合条件的数组都将不在过滤后的数组中。

```js
let arr = ["banana", "apple", "orange", "lemon", "apple", "lemon"];

function removeDuplicates(data) {
  return data.filter((value, index) => data.indexOf(value) === index);
}

console.log(removeDuplicates(arr)); // [ 'banana', 'apple', 'orange', 'lemon' ]
```

我们还可以通过简单的调整，使用filter方法从数据中检索出重复值

```js
let arr = ["banana", "apple", "orange", "lemon", "apple", "lemon"];

function removeDuplicates(data) {
  return data.filter((value, index) => data.indexOf(value) !== index);
}

console.log(removeDuplicates(arr)); // [ 'apple', 'lemon' ]
```
### 2.使用 ES6 的 Set

Set 是 ES6 中的新对象类型，用于创建唯一key的集合。

```js
let arr = ["banana", "apple", "orange", "lemon", "apple", "lemon"];

function removeDuplicates(data) {
  return [...new Set(data)];
}

console.log(removeDuplicates(arr)); // [ 'banana', 'apple', 'orange', 'lemon' ]
```

### 3. 使用 forEach 方法

forEach 方法可以遍历数组中的元素，如果该元素不在数组中，就将该元素push到数组中。

```js
let arr = ["banana", "apple", "orange", "lemon", "apple", "lemon"];

function removeDuplicates(data) {
  let unique = [];
  data.forEach(element => {
    if (!unique.includes(element)) {
      unique.push(element);
    }
  });
  return unique;
}

console.log(removeDuplicates(arr)); // [ 'banana', 'apple', 'orange', 'lemon' ]
```

### 4.使用 reduce 方法

```js
let arr = ["banana", "apple", "orange", "lemon", "apple", "lemon"];

function removeDuplicates(data) {
  let unique = data.reduce(function (a, b) {
    if (a.indexOf(b) < 0) a.push(b);
    return a;
  }, []);
  return unique;
}

console.log(removeDuplicates(arr)); // [ 'banana', 'apple', 'orange', 'lemon' ]
```

或者：

```js
let arr = ["banana", "apple", "orange", "lemon", "apple", "lemon"];

function removeDuplicates(data) {
  return data.reduce((acc, curr) => acc.includes(curr) ? acc : [...acc, curr], []);
}

console.log(removeDuplicates(arr)); // [ 'banana', 'apple', 'orange', 'lemon' ]
```

### 5.在数组原型上添加去重方法

```js
let arr = ["banana", "apple", "orange", "lemon", "apple", "lemon"];

Array.prototype.unique = function () {
  let unique = [];
  for (let i = 0; i < this.length; i++) {
    const current = this[i];
    if (unique.indexOf(current) < 0) unique.push(current);
  }
  return unique;
}

console.log(arr.unique()); // [ 'banana', 'apple', 'orange', 'lemon' ]
```

### 6. Array.from + ES6 Set

```js
let arr = ["banana", "apple", "orange", "lemon", "apple", "lemon"];

function removeDuplicates(data) {
  return Array.from(new Set(arr))
}

console.log(removeDuplicates(arr)); // [ 'banana', 'apple', 'orange', 'lemon' ]
```

### 7.从对象数组中删除重复的对象

有时，我们需要通过属性的名称从对象数据中删除重复的对象，我们可以使用 Map 来实现：

```js
let users = [
  { id: 1, name: 'susan', age: 25 },
  { id: 2, name: 'cherry', age: 28 },
  { id: 3, name: 'cindy', age: 27 },
  { id: 2, name: 'cherry', age: 28 },
  { id: 1, name: 'susan', age: 25 },
]

function uniqueByKey(data, key) {
  return [
    ...new Map(
      data.map(x => [key(x), x])
    ).values()
  ]
}

console.log(uniqueByKey(users, item => item.id));

// [ { id: 1, name: 'susan', age: 25 },
//   { id: 2, name: 'cherry', age: 28 },
//   { id: 3, name: 'cindy', age: 27 } ]
```

或者用reduce实现：

```js
let users = [
  { id: 1, name: 'susan', age: 25 },
  { id: 2, name: 'cherry', age: 28 },
  { id: 3, name: 'cindy', age: 27 },
  { id: 2, name: 'cherry', age: 28 },
  { id: 1, name: 'susan', age: 25 },
]

function uniqueByKey(data, key) {
  const object = {};
  data = data.reduce((prev, next) => {
    // eslint-disable-next-line no-unused-expressions
    object[next[key]]
      ? ''
      : (object[next[key]] = true && prev.push(next));
    return prev;
  }, []);
  return data;
}

console.log(uniqueByKey(users, "id"));

// [ { id: 1, name: 'susan', age: 25 },
//   { id: 2, name: 'cherry', age: 28 },
//   { id: 3, name: 'cindy', age: 27 } ]
```
## 四、实现数组的取交集，并集，差集

### 1. 取交集

**Array.prototype.includes**

```js
let a = [1, 2, 3];
let b = [2, 4, 5];

let intersection = a.filter(v => b.includes(v));

console.log(intersection); // [ 2 ]
```

**Array.from**

```js
let a = [1, 2, 3];
let b = [2, 4, 5];
let aSet = new Set(a);
let bSet = new Set(b);
let intersection = Array.from(new Set(a.filter(v => bSet.has(v))));

console.log(intersection); // [ 2 ]
```

**Array.prototype.indexOf**

```js
let a = [1, 2, 3];
let b = [2, 4, 5];
let intersection = a.filter((v) => b.indexOf(v) > -1);

console.log(intersection); // [ 2 ]
```

### 2. 取并集


**Array.prototype.includes**

```js
let a = [1, 2, 3];
let b = [2, 4, 5];

let union = a.concat(b.filter(v => !a.includes(v)));

console.log(union); // [ 1, 2, 3, 4, 5 ]
```

**Array.from**

```js
let a = [1, 2, 3];
let b = [2, 4, 5];
let aSet = new Set(a);
let bSet = new Set(b);
let union = Array.from(new Set(a.concat(b)));

console.log(union); // [ 1, 2, 3, 4, 5 ]
```

**Array.prototype.indexOf**

```js
let a = [1, 2, 3];
let b = [2, 4, 5];
let union = a.concat(b.filter((v) => a.indexOf(v) === -1));

console.log(union); // [ 1, 2, 3, 4, 5 ]
```

### 3. 取差集


**Array.prototype.includes**

```js
let a = [1, 2, 3];
let b = [2, 4, 5];

let difference = a.concat(b).filter(v => !a.includes(v) || !b.includes(v));

console.log(difference); // [ 1, 3, 4, 5 ]
```

**Array.from**

```js
let a = [1, 2, 3];
let b = [2, 4, 5];
let aSet = new Set(a);
let bSet = new Set(b);
let difference = Array.from(new Set(a.concat(b).filter(v => !aSet.has(v) || !bSet.has(v))));

console.log(difference); // [ 1, 3, 4, 5 ]
```

**Array.prototype.indexOf**

```js
let a = [1, 2, 3];
let b = [2, 4, 5];
let difference = a.filter((v) => b.indexOf(v) === -1).concat(b.filter((v) => a.indexOf(v) === -1));

console.log(difference); // [ 1, 3, 4, 5 ]
```

## 五、实现发布订阅模式

> **发布订阅模式** 一共分为两个部分：`on`、`emit`。**发布和订阅之间没有依赖关系，发布者告诉第三方（事件频道）发生了改变，第三方再通知订阅者发生了改变。**

* on：就是把一些函数维护到数组中

* emit：让数组中的方法依次执行

```js
let fs = require("fs");

let event = {
  arr: [],
  on(fn) {
    this.arr.push(fn);
  },
  emit() {
    this.arr.forEach(fn => fn());
  }
}

event.on(function () {
  console.log("读取了一个");
})

event.on(function () {
  if (Object.keys(school).length === 2) {
    console.log("读取完毕");
  }
})

let school = {};
fs.readFile('./name.txt', 'utf8', function (err, data) {
  school.name = data;
  event.emit();
});

fs.readFile('./age.txt', 'utf8', function (err, data) {
  school.age = data;
  event.emit();
});
```

## 六、实现观察者模式

> **观察者模式** 是基于发布订阅模式的，分为`观察者`和`被观察者`两部分，需要被观察者先收集观察者，当被观察者的状态改变时通知观察者。**观察者和被观察者之间存在关系，被观察者数据发生变化时直接通知观察者改变。**

比如：现在有一家之口，爸爸、妈妈和小宝宝，爸爸妈妈告诉小宝宝你有任何状态变化都要通知我们，当小宝宝饿了的时候，就会通知爸爸妈妈过来处理。这里的小宝宝就是被观察者`Subject`，爸爸妈妈就是观察者`Observer`，小宝宝维护了一个观察者队列，当自己有任何状态改变的时候都直接通知队列中的观察者。


```js
class Subject {  // 被观察者：小宝宝
  constructor(name)  {
    this.name = name;
    this.state = "开心的";
    this.observer = [];
  }

  attach(o) {
    this.observer.push(o);
  }

  setState(newState) {
    this.state = newState;
    this.observer.forEach(o => o.update(this));
  }

}

class Observer { // 观察者：爸爸 妈妈
  constructor(name) {
    this.name = name;
  }

  update(baby) {
    console.log("当前"+this.name+"被通知了，当前小宝宝的状态是："+baby.state);
  }
}

// 爸爸妈妈需要观察小宝宝的心理变化
let baby = new Subject("小宝宝");
let father = new Observer("爸爸");
let mother = new Observer("妈妈");

baby.attach(father);
baby.attach(mother);
baby.setState("我饿了");

```

所以，我用下图表示这两个模式最重要的区别：

![image](/uploads/t_1.png)

## 七、实现单例模式

> 单例模式是创建型设计模式的一种。确保全局中有且仅有一个对象实例，并提供一个访问它的全局访问点，如线程池、全局缓存、window 对象等。


### 1.常规实现：

```js
// 单例构造函数
function CreateSingleton (name) {
    this.name = name;
    this.getName();
};

// 获取实例的名字
CreateSingleton.prototype.getName = function() {
    console.log(this.name)
};
// 单例对象
var Singleton = (function(){
    var instance;
    return function (name) {
        if(!instance) {
            instance = new CreateSingleton(name);
        }
        return instance;
    }
})();

// 创建实例对象1
var a = new Singleton('a');
// 创建实例对象2
var b = new Singleton('b');

console.log(a === b);
```

### 2. 用闭包和Proxy属性拦截实现

```js
const singletonify = (className) => {
  return new Proxy(className.prototype.constructor, {
    instance: null,
    construct: (target, argumentsList) => {
      if (!this.instance)
        this.instance = new target(...argumentsList);
      return this.instance;
    }
  });
}

class MyClass {
  constructor(msg) {
    this.msg = msg;
  }

  printMsg() {
    console.log(this.msg);
  }
}

MySingletonClass = singletonify(MyClass);

const myObj = new MySingletonClass('first');
myObj.printMsg();           // 'first'
const myObj2 = new MySingletonClass('second');
myObj2.printMsg();     // 'first'
```
## 八、实现Promise

重点难点，面试高频考点，具体请参考我的另一篇文章:

[面试官：“你能手写一个 Promise 吗”](https://juejin.im/post/6850037281206566919)


## 九、实现深拷贝

也是面试高频考点，具体请参考我的另一篇文章:

[Javascript经典面试之深拷贝VS浅拷贝](https://juejin.im/post/6844904195758391310)

## 十、手写字符串转二进制

```js
function charToBinary(text) {
  let code = "";
  for (let i of text) {
    // 字符编码
    let number = i.charCodeAt().toString(2);
    // 1 bytes = 8bit，将 number 不足8位的0补上
    for (let a = 0; a <= 8 - number.length; a++) {
       number = 0 + number;
    }
    code += number;
  }
  return code;
}

```

## 十一、手写二进制转Base64

```js
// 将二进制数据每 6bit 位替换成一个 base64 字符
function binaryTobase64(code) {
  let base64Code = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/";
  let res = '';
  // 1 bytes = 8bit，6bit 位替换成一个 base64 字符
  // 所以每 3 bytes 的数据，能成功替换成 4 个 base64 字符
    
  // 对不足 24 bit (也就是 3 bytes) 的情况进行特殊处理
  if (code.length % 24 === 8) {
    code += '0000';
    res += '=='
  }
  if (code.length % 24 === 16) {
    code += '00';
    res += '='
  }

  let encode = '';
  // code 按 6bit 一组，转换为
  for (let i = 0; i < code.length; i += 6) {
    let item = code.slice(i, i + 6);
    encode += base64Code[parseInt(item, 2)];
  }
  return encode + res;
}
```

## 十二、手写字符转Base64

```js
function base64encode(text) {
  let base64Code = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/=";
  let res = '';
  let i = 0;
  while (i < text.length) {
    let char1, char2, char3, enc1, enc2, enc3, enc4;
    
    // 三个字符一组，转二进制
    char1 = text.charCodeAt(i++); 
    char2 = text.charCodeAt(i++);
    char3 = text.charCodeAt(i++);

    enc1 = char1 >> 2; // 取第 1 字节的前 6 位
    
    // 三个一组处理
    if (isNaN(char2)) {
      // 只有 1 字节的时候
      enc2 = ((char1 & 3) << 4) | (0 >> 4);
      // 第65个字符用来代替补位的 = 号
      enc3 = enc4 = 64;
    } else if (isNaN(char3)) {
      // 只有 2 字节的时候
      enc2 = ((char1 & 3) << 4) | (char2 >> 4);
      enc3 = ((char2 & 15) << 2) | (0 >> 6);
      enc4 = 64;
    } else {
      enc2 = ((char1 & 3) << 4) | (char2 >> 4); // 取第 1 个字节的后 2 位(3 = 11 << 4 = 110000) + 第 2 个字节的前 4 位
      enc3 = ((char2 & 15) << 2) | (char3 >> 6); // 取第 2 个字节的后 4 位 (15 = 1111 << 2 = 111100) + 第 3 个字节的前 2 位
      enc4 = char3 & 63; // 取最后一个字节的最后 6 位 (63 = 111111)
    }
    
    // 转base64
    res += base64Code.charAt(enc1) + base64Code.charAt(enc2) + base64Code.charAt(enc3) + base64Code.charAt(enc4)
  }

  return res;
}
```

最优解：

```js
let encodedData = window.btoa("this is a example");
console.log(encodedData); // dGhpcyBpcyBhIGV4YW1wbGU=


let decodeData = window.atob(encodedData);
console.log(decodeData); // this is a example
```

## 十三、实现一个可以拖拽的DIV

思路：

* 有一个DIV层，设定position属性为absolute或fixed，通过更改其left，top来更改层的相对位置。

* 在DIV层上绑定mousedown事件，设置一个拖动开始的标志为true，拖动结束的标志为false，本例为isMouseDown。

* 拖动时的细节优化，如：

  * 鼠标于DIV的相对位置
  
  * 拖动时防止文字被选中
  
  * 限定DIV的移动范围，拖动到边界处的处理

  * 当鼠标移出窗口时失去焦点的处理

  * 当鼠标移动到iframe上的处理

```js
let injectedHTML = document.createElement("DIV");
    injectedHTML.innerHTML = '<dragBox id="dragBox" class="drag-box">\
  <dragBoxBar id="dragBoxBar" class="no-select"></dragBoxBar>\
  <injectedBox id="injectedBox">CONTENT</injectedBox>\
  </dragBox>';

document.body.appendChild(injectedHTML);

let isMouseDown,
  initX,
  initY,
  height = injectedBox.offsetHeight,
  width = injectedBox.offsetWidth,
  dragBoxBar = document.getElementById('dragBoxBar');


dragBoxBar.addEventListener('mousedown', function(e) {
  isMouseDown = true;
  document.body.classList.add('no-select');
  injectedBox.classList.add('pointer-events');
  initX = e.offsetX;
  initY = e.offsetY;
  dragBox.style.opacity = 0.5;
})

dragBoxBar.addEventListener('mouseup', function(e) {
  mouseupHandler();
})

document.addEventListener('mousemove', function(e) {
  if (isMouseDown) {
    let cx = e.clientX - initX,
        cy = e.clientY - initY;
    if (cx < 0) {
        cx = 0;
    }
    if (cy < 0) {
        cy = 0;
    }
    if (window.innerWidth - e.clientX + initX < width + 16) {
        cx = window.innerWidth - width;
    }
    if (e.clientY > window.innerHeight - height - dragBoxBar.offsetHeight + initY) {
        cy = window.innerHeight - dragBoxBar.offsetHeight - height;
    }
    dragBox.style.left = cx + 'px';
    dragBox.style.top = cy + 'px';
  }
})

document.addEventListener('mouseup', function(e) {
  if (e.clientY > window.innerWidth || e.clientY < 0 || e.clientX < 0 || e.clientX > window.innerHeight) {
    mouseupHandler();
  }
});

function mouseupHandler() {
  isMouseDown = false;
  document.body.classList.remove('no-select');
  injectedBox.classList.remove('pointer-events');
  dragBox.style.opacity = 1;
}
```

```css
* {
  margin: 0;
  padding: 0;
  border: none
}

body,
html {
  height: 100%;
  width: 100%;
}

.drag-box {
  user-select: none;
  background: #f0f0f0;
  z-index: 2147483647;
  position: fixed;
  left: 0;
  top: 0;
  width: 200px;
}


#dragBoxBar {
  align-items: center;
  display: flex;
  justify-content: space-between;
  background: #ccc;
  width: 100%;
  height: 40px;
  cursor: move;
  user-select: none;
}

.no-select {
  user-select: none;
}

.pointer-events {
  pointer-events: none;
}


.no-border {
  border: none;
}


#injectedBox {
  height: 160px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 2rem;
  background: #eee;
}
```

## 十四、实现一个批量请求函数 multiRequest(urls, maxNum)
```js
function loadImg(url) {
  return new Promise((resolve, reject) => {
    const img = new Image();
    img.onload = function() {
      console.log(url, "加载完成");
      resolve(img);
    };
    img.onerror = function() {
      reject(new Error('Error at:' + url));
    };
    img.src = url;
  })
}

function multiRequest(urls, maxNum) {
  const firstMaxNum = urls.splice(0, maxNum);
  let promises = firstMaxNum.map((url, index)=>{
    return loadImg(url).then(()=>{
      return index
    })
  })
  return urls.reduce((res, cur)=>{
    return res.then(()=>{
      return Promise.race(promises)
    }).then((idx)=>{
      promises[idx] = loadImg(cur).then(()=>{
        return idx
      })
    })
  }, Promise.resolve()).then(()=>{
    return Promise.all(promises)
  })  
}

multiRequest(urls, 4).then(()=>{
  console.log('finish')
})
```

## 十五、实现一个 sleep 函数

思路：比如 sleep(1000) 意味着等待1000毫秒，可从 Promise、Generator、Async/Await 等角度实现。

```js
//Promise
const sleep = time => {
  return new Promise(resolve => setTimeout(resolve,time))
}
sleep(1000).then(()=>{
  console.log(1)
})

//Generator
function* sleepGenerator(time) {
  yield new Promise(function(resolve,reject){
    setTimeout(resolve,time);
  })
}
sleepGenerator(1000).next().value.then(()=>{console.log(1)})

//async
function sleep(time) {
  return new Promise(resolve => setTimeout(resolve,time))
}
async function output() {
  let out = await sleep(1000);
  console.log(1);
  return out;
}
output();

//ES5
function sleep(callback,time) {
  if(typeof callback === 'function')
    setTimeout(callback,time)
}

function output(){
  console.log(1);
}
sleep(output,1000);
```

## 十六、模拟实现一个 localStorage

```js
'use strict'
const valuesMap = new Map()

class LocalStorage {
  getItem (key) {
    const stringKey = String(key)
    if (valuesMap.has(key)) {
      return String(valuesMap.get(stringKey))
    }
    return null
  }

  setItem (key, val) {
    valuesMap.set(String(key), String(val))
  }

  removeItem (key) {
    valuesMap.delete(key)
  }

  clear () {
    valuesMap.clear()
  }

  key (i) {
    if (arguments.length === 0) {
      throw new TypeError("Failed to execute 'key' on 'Storage': 1 argument required, but only 0 present.") // this is a TypeError implemented on Chrome, Firefox throws Not enough arguments to Storage.key.
    }
    let arr = Array.from(valuesMap.keys())
    return arr[i]
  }

  get length () {
    return valuesMap.size
  }
}
const instance = new LocalStorage()

global.localStorage = new Proxy(instance, {
  set: function (obj, prop, value) {
    if (LocalStorage.prototype.hasOwnProperty(prop)) {
      instance[prop] = value
    } else {
      instance.setItem(prop, value)
    }
    return true
  },
  get: function (target, name) {
    if (LocalStorage.prototype.hasOwnProperty(name)) {
      return instance[name]
    }
    if (valuesMap.has(name)) {
      return instance.getItem(name)
    }
  }
})
```

## 参考

[2万字 | 前端基础拾遗90问](https://juejin.im/post/6844904116552990727)
[可拖动DIV层的实现方法](https://blog.csdn.net/twoByte/article/details/73269653)
[高级前端面试题汇总](https://github.com/Advanced-Frontend/Daily-Interview-Question)
[7 ways to remove duplicates from an array in JavaScript](https://levelup.gitconnected.com/7-ways-to-remove-duplicates-from-array-in-javascript-cea4144caf31)
[How can I implement a singleton in JavaScript](https://www.30secondsofcode.org/blog/s/javascript-singleton-proxy)