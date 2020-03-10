---
title: 浅谈 this、call、apply 和 bind
date: 2020-01-19 14:41:18
tags:
categories: 前端
---

![image](/uploads/i2_1.jpg)
<!-- more -->

# 前言

这是一个前端面试特别容易考的考点，很多初学者在这个问题上都容易踩坑，包括我也是经常性蒙圈。所以这次我决定将他们梳理下来，加深自己的理解。如果有出错的地方，欢迎指正。

# this 是什么

this 关键字是 Javascript ES5 中最复杂的机制之一。[ES6](http://es6.ruanyifeng.com/#README) 中新增的箭头函数，很大程度上避免了使用 this 所产生的错误。但是在 ES5 中，有时候我们会错误的判断了 this 的指向。其实 this 的指向，始终坚持一个原理：**this 永远指向最后调用它的那个对象**，记住了这句话，this 的指向你已经了解一半了。

# this 的四种绑定方式
想要了解 this 的指向，我们首先要了解 this 的四种绑定方式：**隐式绑定、显示绑定、new 绑定、window 绑定**。

## 隐式绑定

执行绑定的第一个也是最常见的规则为 **隐式绑定**，它80% 的情况下它会告诉你 this 指向的对象是什么。

我们先来看一个简单的例子：
例1:
```
  const user = {
    name: 'Cherry',
    age: 27,
    getName() {
      alert(`Hello, my name is ${this.name}`)
    }
  }

  user.getName()
```

如果你要调用 **user** 对象上的  **getName** 方法，你会用到点```.```

这就是所谓隐式绑定，**函数被调用时先看一看点号左侧**。如果有“点”就查看“点”左侧的对象，这个对象就是 **this** 的引用。

在上面的例子中，**user** 在“点号左侧”意味着 **this** 引用了 **user** 对象。所以就好像 在 **getName** 方法的内部 **JavaScript** 解释器把 **this** 变成了 **user**。


例2:
```
const user = {
  name: 'Cherry',
  age: 27,
  getName() {
    alert(`Hello, my name is ${this.name}`)
  },
  mother: {
    name: 'Susan',
    getName() {
      alert(`Hello, my name is ${this.name}`)
    }
  }
}

user.getName()  // Cherry
user.mother.getName()    // Susan
```

如前所述，大约有 80% 的情况下在“点”的左侧都会有一个对象。这就是为什么在判断 this 指向时“查看点的左侧”是你要做的第一件事。但是，如果没有点呢？这就为我们引出了下一条规则：

## 显示绑定

例3:

```
function getName () {
  alert(`Hello, my name is ${this.name}`)
}

const user = {
  name: 'Tyler',
  age: 27,
}
```

现在引出了一个问题，我们怎样能让 **getName** 方法调用的时候将 **this** 指向 **user** 对象？我们不能再像之前那样简单的使用 **user.getName()**，因为 **user** 并没有 **getName** 方法。在 **JavaScript** 中，每个函数都包含了一个能让你恰好解决这个问题的方法，这个方法的名字叫做 **call、apply、bind**。

用下面的代码可以在调用 **getName** 时用 **user** 做上下文。

```
getName.call(user)
```

这就是第 2 条规则的基础（显示绑定），因为我们明确地（使用 .call）指定了 this 的引用。第六章节有对 call、apply、bind 详细的介绍，这里就不过多赘述了。


## new 绑定

第三条判断 **this** 引用的规则是 **new** 绑定。每当用 **new** 调用函数时，**JavaScript** 解释器都会在底层创建一个全新的对象并把这个对象当做 **this**。

> 这看起来就像创建了新的函数，但实际上 **JavaScript** 函数是重新创建的对象。

例4:
```
function User (name, age) {
  /*
    JavaScript 会在底层创建一个新对象 `this`，它会代理不在 User 原型链上的属性。
    如果一个函数用 new 关键字调用，this 就会指向解释器创建的新对象。
  */

  this.name = name
  this.age = age
}

const me = new User('Cherry', 27)
```

这就有要说另一个面试经典问题了：new 的过程。
这里就简单的来看一下 new 的过程吧：
伪代码表示：

```
var a = new User("Qi","Cherry");

new User{
  var object = {};
  object.__proto__ = User.prototype;
  var result = User.call(object,"Li","Cherry");
  return typeof result === 'object'? result : object;
}

```

**new** 的过程：
1.创建一个空对象 object;
2.将新创建的空对象的隐式原型指向其构造函数的显示原型；
3.使用 call 改变 this 的指向；
4.如果无返回值或者返回一个非对象值，则将 object 返回作为新对象；如果返回值是一个新对象的话那么直接直接返回该对象。

所以我们可以看到，在 new 的过程中，我们是使用 call 改变了 this 的指向。

## window 绑定

我们先来看下面这个例子：
例5:

```
function getAge () {
  console.log(`My age is ${this.age}`)
}

const user = {
  name: 'Cherry',
  age: 27,
}      
```

相信大家都知道为什么打印出来的是 **My name is undefined**，因为正如前面所说的，如果你想用 **user** 做上下文调用 **getAge**，你可以使用 **.call**、**.apply** 或 **.bind**。但如果我们没有用这些方法，而是直接和平时一样直接调用，**JavaScript** 会默认 **this** 指向 **window** 对象。但是 **window** 对象中并没有 **name** 属性，所以会打印 **“My age is undefined“**。

如果我们改成：

```
age = 27

function getAge () {
  console.log(`My age is ${this.age}`)
}

const user = {
  name: 'Cherry',
  // age: 27,
}
```

这就是第 4 条规则为什么是 **window** 绑定 的原因。如果其它规则都没满足，**JavaScript** 就会默认 **this** 指向 **window** 对象。

> 在 ES5 添加的 严格模式 中，JavaScript 不会默认 this 指向 window 对象，而会正确地把 this 保持为 undefined。

例6:
```
'use strict'

age = 27

function sayAge () {
  console.log(`My age is ${this.age}`)
}

sayAge() // TypeError: Cannot read property 'age' of undefined

```


# this 的指向
前面讲了关于 this 的四种绑定方式，我们对于 this 的指向应该也有了一些自己的理解，还记得我们之前说的吗？**this 永远指向最后调用它的那个对象，this 永远指向最后调用它的那个对象，this 永远指向最后调用它的那个对象。**重要的事情说三遍。所以我们记好这句话看下面的例子：

例7:

```
var name = "window";
function fn() {
  var name = "Cherry";

  console.log(this.name);          // window

  console.log("inner:" + this);    // inner: Window
}
fn();
console.log("outer:" + this)         // outer: Window
```
我们看最后调用 **fn** 的地方 `fn();`，前面没有“点”，**Javascript** 调用的对象默认指向了全局对象 **window**，这就相当于是 `window.fn()；`所以根据刚刚的那句话“**this 永远指向最后调用它的那个对象**”，**this** 指向的就是 **window**。
> 注意，这里我们没有使用严格模式，如果使用严格模式的话，全局对象就是 undefined，那么就会报错 Uncaught TypeError: Cannot read property 'name' of undefined。

例8:

```
var name = "window";
var user = {
  name: "Cherry",
  fn: function () {
      console.log(this.name);      // Cherry
  }
}
user.fn();
```

根据上文所说，我们看到函数 **fn** 左侧有“点”，“点”的左侧是 **user**，所以 **fn** 是对象 **user** 调用的。所以打印的值就是 **user** 中的 **name** 的值。绑定规则是隐式绑定，是不是有一点清晰了呢~

我们再看下面这个例子：

```
var name = "window";


function fnA(){
  var name = "Cherry";

  function fnB(){
    console.log(this.name);    // window 
  }

  //在A函数内部调用B函数
  fnB();
}

//调用A函数
fnA();
```
在函数执行环境中使用 this 时,如果函数没有明显的作为非 window 对象的属性，而只是定义了函数，不管这个函数是不是定义在另一个函数中，这个函数中的 this 仍然表示 window 对象。

例9:

```
var name = "window";
var user = {
  name: "Cherry",
  fn: function () {
    console.log(this.name);      // Cherry
  }
}
window.user.fn();
```
这里打印 Cherry 的原因也是因为刚刚那句话“this 永远指向最后调用它的那个对象”，最后调用它的对象仍然是对象 user。

我们改动一下：

例10:

```
var name = "window";
var user = {
  // name: "Cherry",
  fn: function () {
    console.log(this.name);      // undefined
  }
}
window.user.fn();
```

这是因为调用 **fn** 的是 **user** 对象，也就是说 **fn** 的内部的 **this** 是对象 **user**，而对象 **user** 中并没有对 **name** 进行定义，所以 **log** 的 **this.name** 的值是 **undefined**。

这个例子还是说明了：**this 永远指向最后调用它的那个对象**，因为最后调用 **fn** 的对象是 **user**，所以就算 **user** 中没有 **name** 这个属性，也不会继续向上一个对象寻找 **this.name**，而是直接输出 **undefined**。

我们再来看一个比较坑的例子：
例11:
```
var name = "window";
var user = {
  name : null,
  // name: "Cherry",
  fn : function () {
    console.log(this.name);      // window
  }
}

var f = user.fn;
f();
```

这里你可能会有疑问，为什么不是 Cherry？因为这里虽然将 **user** 对象的 `fn` 方法赋值给变量 `f` 了，但是**没有调用**，再接着跟我念这一句话：“**this 永远指向最后调用它的那个对象**”，由于刚刚的 `f` 并没有调用，所以 `fn()` 最后仍然是被 **window** 调用的。所以 **this** 指向的也就是 **window**。

由以上五个例子我们可以看出，this 的指向并不是在创建的时候就可以确定的，在 es5 中，**this永远指向最后调用它的那个对象。**

# 如何改变 this 的指向

改变 this 的指向我总结有以下几种方法：

* 使用 ES6 的箭头函数
* 在函数内部使用 _this = this
* 使用 apply、call、bind
* new 实例化一个对象

例12:

```
var name = "window";

var user = {
  name : "Cherry",

  func1: function() {
    console.log(this.name)     
  },

  func2: function() {
    setTimeout(function () {
      this.func1()
    },100);
  }
};

user.func2()     // this.func1 is not a function
```
我们逐一细说一下这个例子：`func2()`是被 **user**调用的，所以`func2`中的 **this** 应该指向 **user**。但是`func2`中又调用了 **window** 中的 **setTimeout** 方法。所以在 **setTimeout** 方法中的 **this** 指向的是后调用它的对象 **window**。但是在 **window** 中并没有 **func1** 函数。所以抛出错误：this.func1 is not a function。

如果我们想正确的调用 **user** 中的 `func1()`，应该怎么做呢？我们把这个例子作为 demo 进行改造。

## 箭头函数
众所周知，ES6 的箭头函数是可以避免 ES5 中使用 this 的坑的。箭头函数的 this 始终指向函数定义时的 this，而非执行时。箭头函数需要记着这句话：“**箭头函数中没有 this 绑定，必须通过查找作用域链来决定其值，如果箭头函数被非箭头函数包含，则 this 绑定的是最近一层非箭头函数的 this，否则，this 为 undefined**”。

例13:
```
var name = "window";

var user = {
  name : "Cherry",

  func1: function () {
    console.log(this.name)     
  },

  func2: function () {
    setTimeout( () => {
        this.func1()
    },100);
  }
};

user.func2()     // Cherry
```
## 在函数内部使用 _this = this
如果不使用 ES6，那么这种方式应该是最简单的不会出错的方式了，我们是先将调用这个函数的对象保存在变量 _this 中，然后在函数中都使用这个 _this，这样 _this 就不会改变了。

例14:
```
var name = "window";

var user = {

  name : "Cherry",

  func1: function () {
    console.log(this.name)     
  },

  func2: function () {
    var _this = this;
    setTimeout( function() {
      _this.func1()
    },100);
  }

};

user.func2()       // Cherry
```

这个例子中，在 func2 中，首先设置 var _this = this;，这里的 **this** 是调用 `func2` 的对象 **user**，为了防止在 `func2` 中的 **setTimeout** 被 **window** 调用而导致的在 **setTimeout** 中的 **this** 为 **window**。我们将 **this**(指向变量 user) 赋值给一个变量 **_this**，这样，在 `func2` 中我们使用 **_this** 就是指向对象 **user** 了。

## 使用 apply、call、bind
使用 apply、call、bind 函数也是可以改变 this 的指向的，原理稍后再讲，我们先来看一下是怎么实现的：

### 使用 apply()
例15:

```
var user = {
  name: "Cherry",

  func1: function() {
    console.log(this.name)
  },

  func2: function() {
    setTimeout(function () {
      this.func1()
    }.apply(user), 100);
  }
};

user.func2()            // Cherry
```

### 使用 call()

例16:

```
var user = {
  name: "Cherry",

  func1: function() {
    console.log(this.name)
  },

  func2: function() {
    setTimeout(function () {
      this.func1()
    }.call(user), 100);
  }
};

user.func2()            // Cherry
```

### 使用 bind()
例17:
```
var user = {
  name: "Cherry",

  func1: function() {
    console.log(this.name)
  },

  func2: function() {
    setTimeout(function () {
      this.func1()
    }.bind(user)(), 100);
  }
};

user.func2()            // Cherry
```

# apply、call、bind 的区别
刚刚我们已经介绍了 apply、call、bind 都是可以改变 this 的指向的，但是这三个函数稍有不同。

在 [MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/apply) 中定义 apply 如下；

> apply() 方法调用一个函数, 其具有一个指定的this值，以及作为一个数组（或类似数组的对象）提供的参数

语法：

> fun.apply(thisArg, [argsArray])

* thisArg：在 fun 函数运行时指定的 this 值。需要注意的是，指定的 this 值并不一定是该函数执行时真正的 this 值，如果这个函数处于非严格模式下，则指定为 null 或 undefined 时会自动指向全局对象（浏览器中就是window对象），同时值为原始值（数字，字符串，布尔值）的 this 会指向该原始值的自动包装对象。
* argsArray：一个数组或者类数组对象，其中的数组元素将作为单独的参数传给 fun 函数。如果该参数的值为null 或 undefined，则表示不需要传入任何参数。从ECMAScript 5 开始可以使用类数组对象。浏览器兼容性请参阅本文底部内容。

## apply 和 call 的区别

其实 apply 和 call 基本类似，他们的区别只是传入的参数不同。

call 的语法为: 

```
fun.call(thisArg[, arg1[, arg2[, ...]]])
```

所以 apply 和 call 的区别是 call 方法接受的是若干个参数列表，而 apply 接收的是一个包含多个参数的数组。

例18:

```
var user ={
  name: "Cherry",
  fn: function(a,b) {
    console.log(a + b)
  }
}

var newUser = user.fn;
newUser.apply(user,[1,2])     // 3
```

例19:

```
var user ={
  name: "Cherry",
  fn: function(a,b) {
    console.log(a + b)
  }
}

var newUser =user.fn;
newUser.call(user, 1, 2)       // 3
```
## bind 和 apply、call 区别
我们先来将刚刚的例子使用 bind 试一下

```
var user ={
  name: "Cherry",
  fn: function(a,b) {
    console.log(a + b)
  }
}

var newUser = user.fn;
nreUser.bind(user,1,2)
```

我们会发现并没有输出，这是为什么呢，我们来看一下 [MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/bind) 上的文档说明：

> bind()方法创建一个新的函数, 当被调用时，将其this关键字设置为提供的值，在调用新函数时，在任何提供之前提供一个给定的参数序列。

所以我们可以看出，bind 是创建一个新的函数，我们必须要手动去调用:

```
var user ={
  name: "Cherry",
  fn: function (a,b) {
    console.log( a + b)
  }
}

var newUser = user.fn;
newUser.bind(user,1,2)()           // 3
```

# 总结

每当在函数内部看到 this 关键字时，我们可以用以下步骤判断 this 的指向。

1.查看函数在哪被调用。
2.点左侧有没有对象？如果有，它就是 “this” 的引用。如果没有，继续第 3 步。
3.该函数是不是用 “call”、“apply” 或者 “bind” 调用的？如果是，它会显式地指明 “this” 的引用。如果不是，继续第 4 步。
4.该函数是不是用 “new” 调用的？如果是，“this” 指向的就是 JavaScript 解释器新创建的对象。如果不是，继续第 5 步。
5.是否在“严格模式”下？如果是，“this” 就是 undefined，如果不是，继续第 6 步。
6.JavaScript 很奇怪，“this” 会指向 “window” 对象。

另外切记：this**永远**指向最后调用它的那个对象。this永远指向**最后调用**它的那个对象。this永远指向最后调用它的**那个对象**。