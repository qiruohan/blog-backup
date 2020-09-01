---
title: 「面试必会」中高级前端必会的手写面试题(一)
date: 2020-08-04 13:40:57
tags: 
  - JavaScript
categories: JavaScript
---

<img src="https://cdn.jsdelivr.net/gh/qiruohan/qiruohan.github.io/uploads/375538043.jpg" width="800" height="100%">
<!-- more -->

## 前言
在面试中，常常会问到一些“手写XXX”的面试题，如果我们只是停留在熟练使用这些 API，问到这种问题想必总是束手无策的。其实想要手写 API 的实现也并不难，更多的是需要我们训练自己**通过使用方式来推倒实现**的能力，千万不要死记硬背。最近我也在强化自己手写 API 的能力，并汇总了面试中高频的手写 API 面试题，希望对大家有一丢丢帮助～

### 一、实现call/apply

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

  let result = eval('context.fn('+args+')');
  delete context.fn;
  return result;
}
```
### 二、实现bind方法

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

  fn.prototype = this.prototype;
  fBound.prototype = new Fn();
  return fBound;
}
```

### 三、实现new关键字

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
function mockNew() {
  // Constructor => animal，剩余的 arguments 就是其他的参数
  let Constructor = [].shift.call(arguments);
  let obj = {};  //返回的结果
  obj.__proto__ = Constructor.prototype;
  Constructor.apply(obj, arguments);
  return r instanceof Object ? r : obj;
}  
```

### 四、用ES5实现数组的map方法

* 特点：

1. 循环遍历数组，并返回一个新数组
2. 回调函数一共接收3个参数，分别是：「正在处理的当前元素的值、正在处理的当前元素的索引、正在遍历的集合对象」

* 用法：
```js
let array = [1, 2, 3].map((item) => {
  return item * 2;
})

console.log(array);  // [2, 4, 6]
```

* 实现：

```js
Array.prototype.map = function(fn) {
  let arr = [];
  for(let i = 0; i < this.length; i++) {
    arr.push(fn(this[i], i, this));
  }
  return arr;
}
```

### 五、用ES5实现数组的filter方法

* 特点：

1. 该方法返回一个由通过测试的元素组成的新数组，如果没有通过测试的元素，则返回一个空数组
2. 回调函数一共接收3个参数，同 map 方法一样。分别是：「正在处理的当前元素的值、正在处理的当前元素的索引、正在遍历的集合对象」

* 用法：

```js
let array = [1, 2, 3].filter((item) => {
  return item > 2;
})

console.log(array); // [3]
```

* 实现：

```js
Array.prototype.filter = function(fn) {
  let arr = [];
  for(let i = 0; i < this.length; i++) {
    fn(this[i]) && arr.push(fn(this[i], i, this));
  }
  return arr;
}
```

### 六、用ES5实现数组的some方法

* 特点：

1. 在数组中查找元素，如果找到一个符合条件的元素就返回true，如果所有元素都不符合条件就返回 false；
2. 回调函数一共接收3个参数，同 map 方法一样。分别是：「正在处理的当前元素的值、正在处理的当前元素的索引、正在遍历的集合对象」。

* 用法：

```js
let flag = [1, 2, 3].some((item) => {
  return item > 1;
})

console.log(flag); // true
```

* 实现：

```js
Array.prototype.some = function(fn) {
  for(let i = 0; i < this.length; i++) {
    if (fn(this[i])) {
      return true;
    }
  }
  return false;
}
```

### 七、用ES5实现数组的every方法

* 特点：

1. 检测一个数组中的元素是否都能符合条件，都符合条件返回true，有一个不符合则返回 false
2. 如果收到一个空数组，此方法在任何情况下都会返回 true
3. 回调函数一共接收3个参数，同 map 方法一样。分别是：「正在处理的当前元素的值、正在处理的当前元素的索引、正在遍历的集合对象」

* 用法：

```js
let flag = [1, 2, 3].every((item) => {
  return item > 1;
})

console.log(flag); // false
```

* 实现：

```js
Array.prototype.every = function(fn) {
  for(let i = 0; i < this.length; i++) {
    if(!fn(this[i])) {
      return false
    }
  }
  return true;
}
```

### 八、用ES5实现数组的find方法

* 特点：

1. 在数组中查找元素，如果找到符合条件的元素就返回这个元素，如果没有符合条件的元素就返回 undefined，且找到后不会继续查找
2. 回调函数一共接收3个参数，同 map 方法一样。分别是：「正在处理的当前元素的值、正在处理的当前元素的索引、正在遍历的集合对象」

* 用法：

```js
let item = [1, 2, 3].find((item) => {
  return item > 1;
})

console.log(item); // 2
```

* 实现：

```js
Array.prototype.find = function(fn) {
  for(let i = 0; i < this.length; i++) {
    if (fn(this[i])) return this[i];
  }
}
```

### 九、用ES5实现数组的forEach方法
* 特点：

1. 循环遍历数组，该方法没有返回值
2. 回调函数一共接收3个参数，同 map 方法一样。分别是：「正在处理的当前元素的值、正在处理的当前元素的索引、正在遍历的集合对象」

* 用法：

```js
[1, 2, 3].forEach((item, index, array) => {
  // 1 0 [1, 2, 3]
  // 2 1 [1, 2, 3]
  // 3 2 [1, 2, 3]
  console.log(item, index, array)  
})
```

* 实现：

```js
Array.prototype.forEach = function(fn) {
  for(let i = 0; i < this.length; i++) {
    fn(this[i], i, this);
  }
}
```

### 十、用ES5实现数组的reduce方法

* 特点：

1. 初始值不传时的特殊处理：会默认用数组中的第一个元素
2. 函数的返回结果会作为下一次循环的 prev
3. 回调函数一共接收4个参数，分别是「上一次调用回调时返回的值、正在处理的元素、正在处理的元素的索引，正在遍历的集合对象」

* 用法：

```js
let total = [1, 2, 3].reduce((prev, next, currentIndex, array) => {
  return prev + next;
}, 0)

console.log(total); // 6
```

* 实现：

```js
Array.prototype.reduce = function(fn, prev) {
  for(let i = 0; i < this.length; i++) {
    // 初始值不传时的处理
    if (typeof prev === 'undefined') {
      // 明确回调函数的参数都有哪些
      prev = fn(this[i], this[i+1], i+1, this);
      ++i;
    } else {
      prev = fn(prev, this[i], i, this)
    }
  }
  // 函数的返回结果会作为下一次循环的 prev
  return prev;
}
```

### 十一、实现instanceof方法

* 特点：

沿着原型链的向上查找，直到找到原型的最顶端，也就是`Object.prototype`。查找构造函数的 prototype 属性是否出现在某个实例对象的原型链上，如果找到了返回 true，没找到返回 false。

* 用法：

```js
console.log([] instanceof Array); // true
console.log([] instanceof Object); // true

// 相当于：
console.log([].__proto__ === Array.prototype); // true
console.log([].__proto__.__proto__ === Object.prototype); // true
```

* 实现：

```js
function myInstanceof(left, right) {
  left = left.__proto__;
  while(true) {
    if (left === null) {
      return false;
    }
    if (left === right.prototype) {
      return true;
    }
    left = left.__proto__;
  }
}

class A{};

const a = new A();
console.log(myInstanceof(a, A)); // true
console.log(myInstanceof(a, Object)); // true 
console.log(myInstanceof(a, Array)); // false
```

### 十二、实现Object.create方法(经常考)
* 特点：

创建一个新对象，使用现有的对象来提供新创建的对象的__proto__

* 用法：

```js
let demo = {
    c : '123'
}
let cc = Object.create(demo)
console.log(cc);
```

* 实现：

```js
function create(proto) {
    function Fn() {};
    // 将Fn的原型指向传入的 proto
    Fn.prototype = proto;
    Fn.prototype.constructor = Fn;
    return new Fn();
}
```

### 十三、实现一个通用的柯里化函数

* 特点：
柯里化就是将一个函数的功能细化，把接受「多个参数」的函数变换成接受一个「单一参数」的函数，并且返回接受「余下参数」返回结果的一种应用。

1. 判断传递的参数是否达到执行函数的fn个数
2. 没有达到的话，继续返回新的函数，将fn函数继续返回并将剩余参数累加
3. 达到fn参数个数时，将累加后的参数传给fn执行

* 用法：

```js
function sum(a, b, c, d, e) {
  return a+b+c+d+e;
}

let a = curring(sum)(1,2)(3,4)(5);  // 15
```

* 实现：

```js
const curring = (fn, arr = []) => {
  let len = fn.length;
  return function (...args) {
    arr = [...arr, ...args];
    if (arr.length < len) {
      return curring(fn, arr);
    } else {
      return fn(...arr);
    }
  };
};
```

### 十四、实现一个反柯里化函数 

* 特点：
使用`call`、`apply`可以让非数组借用一些其他类型的函数，比如，`Array.prototype.push.call`, `Array.prototype.slice.call`， `uncrrying`把这些方法泛化出来，不在只单单的用于数组，更好的语义化。

* 用法：

```js
// 利用反柯里化创建检测数据类型的函数
let checkType = uncurring(Object.prototype.toString);

checkType(1); // [object Number]
checkType("hello"); // [object String]
checkType(true); // [object Boolean]
```

* 实现：

```js
Function.prototype.uncurring = function () {
  var self = this;
  return function () {
    return Function.prototype.call.apply(self, arguments);
  }
}
```

### 十五、实现一个简单的节流函数(throttle)

* 特点：

规定在一个单位时间内，只能触发一次函数。如果这个单位时间内触发多次函数，只有一次生效。

节流重在加锁`flag = false`

* 应用场景：

  * scroll滚动事件，每隔特定描述执行回调函数
  * input输入框，每个特定时间发送请求或是展开下拉列表，（防抖也可以）

* 用法：

```js
const throttleFn = throttle(fn, 300);
```

* 实现：

```js
const throttle = (fn, delay = 500) => {
  let flag = true;
  return (...args) => {
    if (!flag) return;
    flag = false;
    setTimeout(() => {
      fn.apply(this, args);
      flag = true;
    }, delay);
  };
};
```

### 十六、实现一个简单的防抖函数(debounce)

* 特点：

在事件被触发n秒后再执行回调，如果在这n秒内又被触发，则重新计时

防抖重在清零`clearTimeout(timer)`

* 应用场景：

  * 浏览器窗口大小resize避免次数过于频繁
  * 登录，发短信等按钮避免发送多次请求
  * 文本编辑器实时保存

* 用法：

```js
const debounceFn = debounce(fn, 300);
```

* 实现：

```js
const debounce = (fn, delay) => {
  let timer = null;
  return (...args) => {
    clearTimeout(timer);
    timer = setTimeout(() => {
      fn.apply(this, args);
    }, delay);
  };
};
```

`lodash`、`underscore`等库中的节流防抖功能还提供了更多的配置参数，这里我们只是实现了最基本的节流防抖，感兴趣的同学可以看看`lodash`、`underscore`的源码。

### 十七、实现一个 Compose (组合)

* 特点：

将需要嵌套执行的函数平铺，嵌套执行就是一个函数的返回值将作为另一个函数的参数。该函数调用的方向是从右至左的（先执行 sum，再执行 toUpper，再执行 add）

* 用法：

```js
function sum(a, b) {
  return a+b;
}

function toUpper(str) {
  return str.toUpperCase();
}

function add(str) {
  return '==='+str+'==='
}

// 使用 compose 之前：
console.log(add(toUpper(sum('cherry', '27')))); // ===CHERRY27===
// 使用 compose 之后：
console.log(compose(add, toUpper, sum)('cherry', '27')); // ===CHERRY27===
```

* 实现：

```js
// 使用 ES5- reduceRight 实现
function compose(...fns) {
  return function (...args) {
    let lastFn = fns.pop();
    return fns.reduceRight((a, b) => {
      return b(a);
    }, lastFn(...args));
  };
}

// 使用 ES6 - reduceRight 实现
const compose = (...fns) => (...args) => {
  let lastFn = fns.pop();
  return fns.reduceRight((a, b) => b(a), lastFn(...args));
};

// 使用 ES6 - reduce 一行代码实现：
const compose = (...fns) => fns.reduce((a, b) => (...args) => a(b(...args)));
```

### 十八、实现一个 Pipe （管道）

* 特点：

pipe函数跟compose函数的作用是一样的，也是将参数平铺，只不过他的顺序是从左往右。（先执行 splitString，再执行 count）

* 用法：

```js
function splitString(str) {
  return str.split(' ');
}

function count(array) {
  return array.length;
}

// 使用 pipe 之前：
console.log(count(splitString('hello cherry'))); // 2
// 使用 pipe 之后：
console.log(pipe(splitString, count)('hello cherry')); // 2
```

* 实现：

```js
const pipe = function(){
  const args = [].slice.apply(arguments);
  return function(x) {
    return args.reduce((res, cb) => cb(res), x);
  }
}

// 使用 ES5- reduceRight 实现
function pipe(...fns) {
  return function (...args) {
    let lastFn = fns.shift();
    return fns.reduceRight((a, b) => {
      return b(a);
    }, lastFn(...args));
  };
}

// 使用 ES6 - reduceRight 实现
const pipe = (...fns) => (...args) => {
  let lastFn = fns.shift();
  return fns.reduceRight((a, b) => b(a), lastFn(...args));
};

// 使用 ES6 - reduce 一行代码实现：（redux源码）
const pipe = (...fns) => (...args) => fns.reduce((a, b) => b(a), ...args);
```

### 十九、实现一个模版引擎

* 特点：with语法 + 字符串拼接 + new Function来实现

1. 先将字符串中的 `<%=%>`替换掉，拼出一个结果的字符串；
2. 再采用`new Function`的方式执行该字符串，并且使用`with`解决作用域的问题。

* 用法：

```js
const ejs = require('ejs');
const path = require('path');

ejs.renderFile(path.resolve(__dirname, 'template.html'),{name: 'Cherry', age: 27, arr: [1, 2, 3]}, function(err, data) {
  console.log(data);
})

```

```html
// ===== template.html =====
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>
  <%=name%>  <%=age%>
  <%arr.forEach(item =>{%>
      <li><%=item%></li>
  <%})%>
</body>
</html>
```

* 实现：

我们用`{ {} }`替换`<%=%>`标签来模拟实现一个模版引擎，实现原理是一样的，重点看实现原理哈。

```html
// ===== my-template.html =====
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>
  {{name}}  {{age}}
  {%arr.forEach(item => {%}
      <li>{{item}}</li>
  {%})%}
</body>
</html>
```

```js
const fs = require('fs');
const path = require('path');

const renderFile = (filePath, obj, cb) => {
  fs.readFile(filePath, 'utf8', function(err, html) {
    if(err) {
      return cb(err, html);
    }

    html = html.replace(/\{\{([^}]+)\}\}/g, function() {
      console.log(arguments[1], arguments[2]);
      let key = arguments[1].trim();
      return '${' + key + '}';
    });

    let head = `let str = '';\r\n with(obj){\r\n`;
    head += 'str+=`';
    html = html.replace(/\{\%([^%]+)\%\}/g, function() {
      return '`\r\n' + arguments[1] + '\r\nstr+=`\r\n';
    });
    let tail = '`}\r\n return str;';
    let fn = new Function('obj', head + html + tail);
    cb(err, fn(obj));
  });
};

renderFile(path.resolve(__dirname, 'my-template.html'),{name: 'Cherry', age: 27, arr: [1, 2, 3]}, function(err, data) {
  console.log(data);
});
```

## 小结

计划输出：
中高级前端工程师必会的手写API（二）
