---
title: (未完成)面试题目汇总
date: 2020-07-20 17:12:03
tags: [面试]
categories: 前端
---

<img src="https://cdn.jsdelivr.net/gh/qiruohan/qiruohan.github.io/uploads/353632561.jpg" width="800" height="100%">
<!-- more -->

## CSS
### 1.说说CSS选择器以及这些选择器的优先级

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

### 2. 伪类和伪元素的区别
**伪元素**：主要是用来创建一些不存在原有dom结构树种的元素，例如：用::before和::after在一些存在的元素前后添加文字样式等，这些被添加的内容会以具体的UI显示出来，被用户所看到的，这些内容不会改变文档的内容，不会出现在DOM中，不可复制，仅仅是在CSS渲染层加入。CSS3中建议使用::表示伪元素，如：div::before。

**::before和::after这两个伪类下有特有的属性content，必须有这个属性。**

**伪类**：表示已存在的某个元素处于某种状态，但是通过dom树又无法表示这种状态，就可以通过伪类来为其添加样式。例如a元素的:hover, :active等。CSS3中建议使用:表示伪元素，如：a:hover。

### 3. 你知道什么是BFC么？
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

### 4.如何实现居中
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
### 5.盒模型
页面渲染时，dom 元素所采用的 布局模型。可通过`box-sizing`进行设置。根据计算宽高的区域可分为：

* `content-box` (W3C 标准盒模型)
* `border-box` (IE 盒模型)
* `padding-box` (FireFox 曾经支持)
* `margin-box` (浏览器未实现)

![image](https://cdn.jsdelivr.net/gh/qiruohan/qiruohan.github.io/uploads/450.png)

注：理论上是有上面 4 种盒子，但现在 w3c 与 mdn 规范中均只支持 content-box 与 border-box；


### 6.绝对定位、固定定位和 z-index

### 7.如何实现左侧宽度固定，右侧宽度自适应的布局

### 8.层叠上下文

### 9.

通常，CSS 并不是重点的考察领域，但这其实是由于现在国内业界对 CSS 的专注不够导致的，真正精通并专注于 CSS 的团队和人才并不多。因此如果能在 CSS 领域有自己的见解和经验，反而会为相当的加分和脱颖而出。