---
title: 浅谈 this、call、apply 和 bind
date: 2020-01-19 14:41:18
---

![image](https://cdn.jsdelivr.net/gh/qiruohan/qiruohan.github.io/uploads/i2_1.jpg)
<!-- more -->

## 前言

这是一个前端面试经常考的基础考点，很多初学者在这个问题上都容易踩坑，包括我也是经常性蒙圈。所以这次决定将他们梳理下来，加深自己的理解。如果有出错的地方，欢迎指正。

## this 是什么

this 关键字是 Javascript ES5 中最复杂的机制之一。[ES6](http://es6.ruanyifeng.com/#README) 中新增的箭头函数，很大程度上避免了使用 this 所产生的错误。但是在 ES5 中，有时候我们会错误的判断了 this 的指向。其实关于 this 的指向，始终坚持一个原理：**this 永远指向最后调用它的那个对象**，记住了这句话，this 的指向你已经了解一半！

想要了解 this 的指向，我们首先要了解 this 的四种绑定方式：**隐式绑定、显示绑定、window 绑定、new 绑定**。

## this 的四种绑定方式

### 隐式绑定

执行绑定的第一个也是最常见的规则为 **隐式绑定**，它 80% 的情况下会告诉你 this 指向的对象是什么。

我们先来看一个简单的例子：

```
  const user = {
    name: 'Cherry',
    age: 27,
    getName() {
      console.log(`Hello, my name is ${this.name}`)
    }
  }

  user.getName()  // Hello, my name is Cherry
```
当我们执行 `user.getName()` 时，会打印出`Hello, my name is Cherry`。

如果你要调用 **user** 对象上的  **getName** 方法，你会用到点```.```

这就是所谓隐式绑定，**函数被调用时先看一看点号左侧**。如果有“点”就查看“点”左侧的对象，这个对象就是 **this** 的引用。

在上面的例子中，**user** 在“点号左侧”意味着 **this** 引用了 **user** 对象。所以就好像 在 **getName** 方法的内部 **JavaScript** 解释器把 **this** 变成了 **user**。

所以，你可以得出这样的结论：**使用对象来调用其内部的一个方法，该方法的 this 是指向对象本身的**。这就是所谓隐式绑定，你也可以这样认为：JavaScript解释器在执行 `user.getName()`时，将其转化为了：

```
user.getName.call(user);
```

我们将代码增加一层调用：

```
const user = {
  name: 'Cherry',
  age: 27,
  getName() {
    console.log(`Hello, my name is ${this.name}`)
  },
  mother: {
    name: 'Susan',
    getName() {
      console.log(`Hello, my name is ${this.name}`)
    }
  }
}

user.getName()  // Hello, my name is Cherry
user.mother.getName()    // Hello, my name is Susan
```
正如刚才所说：**this 永远指向最后调用它的那个对象**，那么“点”左侧的对象即为后调用该方法的对象，this 指向该对象。但是，如果没有点呢？这就为我们引出了下一条规则：

### 显示绑定

关于显示绑定，我们可以通过 call 来设置函数执行上下文的 this 指向，比如下面这段代码：

```
function getName () {
  console.log(`Hello, my name is ${this.myName}`)
}

let user = {
  myName: 'Cherry',
  age: 27,
}

getName.call(user)  // Hello, my name is Cherry
```

执行这段代码，然后观察输出结果，你会发现 getName 函数内部的 this 已经指向了 user 对象。

其实除了 call 方法，我们还可以使用 bind 和 apply 方法来设置函数执行上下文中的 this，它们在使用上有一些区别，文章的第六小节会对 call、apply、bind 进行详细的介绍，这里我就不过多赘述了。


### window 绑定

我们在刚才的例子的基础上修改一下：

```
function getName () {
  console.log(`Hello, my name is ${this.myName}`)
}

let user = {
  myName: 'Cherry',
  age: 27,
}

getName();

```

相信大家都知道为什么打印出来的是 **My name is undefined**，因为正如前面所说的，如果你想用 **user** 做上下文调用 **getName**，你可以使用 **.call**、**.apply** 或 **.bind**。但如果我们没有用这些方法，而是直接和平时一样直接调用，**JavaScript** 会默认 **this** 指向 **window** 对象。但是 **window** 对象中并没有 **myName** 属性，所以会打印 **“My name is undefined“**。

> 在 ES5 添加的 严格模式 中，JavaScript 不会默认 this 指向 window 对象，而会正确地把 this 保持为 undefined。

例如：
```
'use strict'

age = 27

function sayAge () {
  console.log(`Hello, my age is ${this.age}`)
}

sayAge() // TypeError: Cannot read property 'age' of undefined

```

### new 绑定

第四条判断 **this** 引用的规则是 **new** 绑定。每当用 **new** 调用函数时，**JavaScript** 解释器都会在底层创建一个全新的对象并把这个对象当做 **this**。

> 这看起来就像创建了新的函数，但实际上 **JavaScript** 函数是重新创建的对象。

例如：
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

伪代码表示：

```
var me = new User("Cherry","27");

new User{
  var object = {};
  object.__proto__ = User.prototype;
  var result = User.call(object,"Cherry","27");
  return typeof result === 'object'? result : object;
}

```

**new** 的过程：
1.创建一个空对象 object;
2.将新创建的空对象的隐式原型指向其构造函数的显示原型；
3.使用 call 改变 this 的指向；
4.如果无返回值或者返回一个非对象值，则将 object 返回作为新对象；如果返回值是一个新对象的话那么直接直接返回该对象。

所以我们可以看到，在 new 的过程中，其实是使用 call 改变了 this 的指向。

## this 的指向
前面讲了关于 this 的四种绑定方式，我们对于 this 的指向应该也有了一些自己的理解，还记得我们之前说的吗？**this 永远指向最后调用它的那个对象**，我们记好这句话来练习下下面的例子：

例1:
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
我们看最后调用 **fn** 的地方 `fn();`，前面没有“点”，**Javascript** 调用的对象默认指向了全局对象 **window**，这就相当于是 `window.fn()；`所以根据刚刚的那句话“**this 永远指向最后调用它的那个对象**”，**this** 指向的就是 **window**。绑定规则是Window绑定。

> 注意，这里我们没有使用严格模式，如果使用严格模式的话，全局对象就是 undefined，那么就会报错 Uncaught TypeError: Cannot read property 'name' of undefined。

例2:
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

根据上文所说，我们看到函数 **fn** 左侧有“点”，“点”的左侧是 **user**，所以 **fn** 是对象 **user** 调用的。所以打印的值就是 **user** 中的 **name** 的值。绑定规则是隐式绑定。

例3：

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

**嵌套函数中的 this 不会从外层函数中继承**。在函数执行环境中使用 this 时,如果函数没有明显的作为非 window 对象的属性，而只是定义了函数，这个函数中的 this 仍然默认指向 window 对象。

例4:

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

例5：（这个例子稍稍有点坑）

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

## 如何改变 this 的指向

改变 this 的指向我总结有以下几种方法：

* 使用 ES6 的箭头函数
* 在函数内部使用 _this = this
* 使用 apply、call、bind
* new 实例化一个对象

我们看下面的例子:

```
var name = "window";

var user = {
  name : "Cherry",

  fn1: function() {
    console.log(this.name)     
  },

  fn2: function() {
    setTimeout(function () {
      this.fn1()
    },100);
  }
};

user.fn2()     // this.fn1 is not a function
```
我们逐一细说一下这个例子：`fn2()`是被 **user**调用的，所以`fn2`中的 **this** 应该指向 **user**。但是`fn2`中又调用了 **window** 中的 **setTimeout** 方法。所以在 **setTimeout** 方法中的 **this** 指向的是后调用它的对象 **window**。但是在 **window** 中并没有 **fn1** 函数。所以抛出错误：this.fn1 is not a function。

如果我们想正确的调用 **user** 中的 `fn1()`，应该怎么做呢？我们把这个例子作为 demo 进行改造。

### 箭头函数
众所周知，ES6 的箭头函数是可以避免 ES5 中使用 this 的坑的。“所有的箭头函数都没有自己的this，都指向外层。”--这句话就是箭头函数的精髓。箭头函数的this，总是指向定义时所在的对象，而不是运行时所在的对象。这句话说的太模糊了，最好改成：**总是指向所在函数运行时的this**。

上面例子我们使用**箭头函数**改变this的指向如下:
```
var name = "window";

var user = {
  name : "Cherry",

  fn1: function () {
    console.log(this.name)     
  },

  fn2: function () {
    setTimeout( () => {
        this.fn1()
    },100);
  }
};

user.fn2()     // Cherry
```
### 在函数内部使用 _this = this
如果不使用 ES6，那么这种方式应该是最简单的不会出错的方式了，我们是先将调用这个函数的对象保存在变量 _this 中，然后在函数中都使用这个 _this，这样 _this 就不会改变了。

```
var name = "window";

var user = {

  name : "Cherry",

  fn1: function () {
    console.log(this.name)     
  },

  fn2: function () {
    var _this = this;
    setTimeout( function() {
      _this.fn1()
    },100);
  }

};

user.fn2()       // Cherry
```

这个例子中，在 fn2 中，首先设置 var _this = this;，这里的 **this** 是调用 `fn2` 的对象 **user**，为了防止在 `fn2` 中的 **setTimeout** 被 **window** 调用而导致的在 **setTimeout** 中的 **this** 为 **window**。我们将 **this**(指向变量 user) 赋值给一个变量 **_this**，这样，在 `fn2` 中我们使用 **_this** 就是指向对象 **user** 了。

### 使用 apply、call、bind
使用 apply、call、bind 函数也是可以改变 this 的指向的，成为显示绑定，我们先来看一下是怎么实现的：

#### 使用 apply()
```
var user = {
  name: "Cherry",

  fn1: function() {
    console.log(this.name)
  },

  fn2: function() {
    setTimeout(function () {
      this.fn1()
    }.apply(user), 100);
  }
};

user.fn2()            // Cherry
```

#### 使用 call()
```
var user = {
  name: "Cherry",

  fn1: function() {
    console.log(this.name)
  },

  fn2: function() {
    setTimeout(function () {
      this.fn1()
    }.call(user), 100);
  }
};

user.fn2()            // Cherry
```

#### 使用 bind()
```
var user = {
  name: "Cherry",

  fn1: function() {
    console.log(this.name)
  },

  fn2: function() {
    setTimeout(function () {
      this.fn1()
    }.bind(user)(), 100);
  }
};

user.fn2()            // Cherry
```

## apply、call、bind 的区别
刚刚我们已经介绍了 apply、call、bind 都是可以改变 this 的指向的，但是这三个函数稍有不同。

在 [MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/apply) 中定义 apply 如下；

> apply() 方法调用一个函数, 其具有一个指定的this值，以及作为一个数组（或类似数组的对象）提供的参数

### apply 和 call 的区别

其实 apply 和 call 基本类似，他们的区别只是传入的参数不同。

call 的语法为: 

```
fun.call(thisArg[, arg1[, arg2[, ...]]])
```

所以 apply 和 call 的区别是 call 方法接受的是若干个参数列表，而 apply 接收的是一个包含多个参数的数组。

apply()的使用方法：
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

call()的使用方法：

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

但凡事都有例外：
若将null、undefined等值作为call、apply的第一个参数，那么实际调用时会被忽略，从而应用到Window绑定规则，即绑定到window上，有些时候我们不关心上下文，只关心参数时，可以这样做。

但这样其实存在这一些潜在的风险，绑定到window很可能无意中添加或修改了全局变量，造成一些隐蔽的bug。所以为了防止这种情况出现，可以将第一个参数绑定为一个空对象。当然具体还是看需求，这只是建议。

### bind 和 apply、call 区别
我们先使用 bind 试一下刚刚的例子：
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

所以我们可以看出，**bind 是创建一个新的函数**，我们必须要手动去调用:

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

以上就是三种显示绑定的方法，但有三点需要注意：

1. call和apply是立即执行，bind则是返回一个绑定了this的新函数，只有你调用了这个新函数才真的调用了目标函数
2. bind函数存在多次绑定的问题，如果多次绑定this，则以第一次为准。
3. bind函数实际上是显示绑定（call、apply）的一个变种，称为**硬绑定**。由于硬绑定是一种非常常用的模式，所以在 ES5 中提供了内置的方法`Function.prototype.bind`

为什么多次使用bind绑定this，以第一次为准呢？我们看下面的例子：

```
function foo() {
  console.log( this.name );
} 

var obj1 = {
  name: 'obj1'
}; 

var obj2 = {
  name: 'obj2'
}

var fn = foo.bind(obj1).bind(obj2)
fn() // => 'obj1'
fn.call(obj2) // => 'obj1'
```
也就是说bind函数只能绑定一次，多次绑定是没有用的，绑定后的函数this无法改变，即使call/apply也不行，所以才称作硬绑定。

但凡事总有例外，且看new绑定。

## 绑定的优先级

如果显示绑定和new绑定同时存在，或者更宽泛的说：**在某个调用位置多条绑定规则同时存在怎么办呢**？为了解决这个问题就必须给这些规则设定优先级，这就是我们接下来要介绍的内容。

毫无疑问，Window绑定的优先级是最低的，显式绑定和隐式绑定的优先级，通过上面的例子也可以证明，显式大于隐式。所以目前顺序是：`显式 > 隐式 > Window`

那我们来测试下显示绑定和new绑定的优先级顺序。由于call/apply无法和new一起使用，我们可以使用bind（硬绑定）来验证。

```
function foo() {
  this.name = 'Cherry';
} 
var obj = {
  name: 'obj'
}; 

var fn = foo.bind(obj)
var result = new fn()
console.log(obj.name) // => 'obj'
console.log(result.name) // => 'Cherry'
```
显而易见的，new的优先级，大于显示绑定。最终顺序为：`new > 显式 > 隐式 > Window`。

于是我们判断this，就有了一个顺序：

1. 函数是否在new中调用？
2. 是否通过call、apply、bind等调用？
3. 是否在某个上下文对象中调用？
4. 都不是则是Window绑定。且严格模式下绑定到undefined。

## 总结

判断this主要有以下步骤：

1. 函数是否在new中调用？
2. 是否通过call、apply、bind等调用？
3. 是否在某个上下文对象中调用？
4. 都不是则是默认绑定。且严格模式下绑定到undefined。

另外还要注意箭头函数的特殊性以及undefined和null会被忽略这一特性。还有这一句：this永远指向最后调用它的那个对象。