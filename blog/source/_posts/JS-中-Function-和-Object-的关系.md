---
title: JS 中 Function 和 Object 的关系
date: 2019-04-04 19:22:54
categories: 技术相关
tags: [JacaScript]
reward: true
---
这段时间我一直在复习前端基础知识，遇到了下面这道面试题：
```js
var F = function(){}
var f = new F()
Object.prototype.a = function(){}
Function.prototype.b = function(){}

console.log(f.a)
console.log(f.b)
```
这段代码会输出什么？
打开浏览器控制台，将上面的代码复制进去，回车，我们可以得到它的答案：
```js
ƒ (){}
undefined
```
细看我们可以发现这道题并不难，对于`f.a`，JavaScript 解释器会安装原型链的规则去找 f 的 a 属性：
1. 在 f 本身查找是否有 a 属性，显然没有，接下来将会进入 f 的原型链查找
1. 查找`f.__proto__`是否有 a 属性，而`f.__proto__ === F.prototype`，显然也没有
1. 继续查找`f.__proto__.__proto__`，即`F.prototype.__proto__`，而`F.prototype.__proto__ === Object.prototype`，可以发现`Object.prototype` 中有 a 属性

对于`f.b`，解释器会进行和上面一样的操作，但并不会在原型链中找到 b 属性， 因为`f.__proto__.__proto__.__proto__ === Object.prototype.__proto__ === null`，结束查找

那么代码中的`Function.prototype.b = function(){}`能有什么作用呢？其实不难发现：
```js
console.log(F.b) // ƒ (){}
```
看起来 F 是 Function 的一个实例？的确是这样的，所有的函数都是 Function 的实例，这么说 Function 是一个很基础的工具咯？那它和另一个基础的工具 Object 有什么关系呢？

我们可以做以下测试：
```js
F instanceof Object // true
F instanceof Function // true
Object instanceof Function // true
Function instanceof Object // true
Object == Function // false
```
完全懵了...

为什么会出现这么怪异的结果，Object 和 Function 谁更底层一点？它们为什么会"互为实例"？

通过对 JavaScript 各个概念出现先后顺序的分析，我得到了如下的一张图：

![Js_Function_Object.png](https://raw.githubusercontent.com/CS-Tao/github-content/master/contents/blog/image/others/Js_Function_Object.png)

JavaScript 是我学过的最糟糕的语言...

结合该图和 JavaScript 的继承规则，我们可以解释上面的结果：
```js
F instanceof Object // true, F.__proto__.__proto__ === '函数原型'.__proto__ === '对象原型' === Object.prototype
F instanceof Function // true, F.__proto__ === '函数原型' === Function.prototype
Object instanceof Function // true, Object.__proto__ === '函数原型' === Function.prototype
Function instanceof Object // true, Function.__proto__.__proto__ === '函数原型'.__proto__ === '对象原型' === Object.prototype
```
我们还可以从上图发现其它一些有趣的信息：
```js
Function.prototype === Function.__proto__ // true
Object.prototype === Object.__proto__.__proto__
```
好了到此为止，我不该入门前端的...
