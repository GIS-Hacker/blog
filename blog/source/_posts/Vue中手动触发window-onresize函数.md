---
title: Vue 中手动触发 window.onresize() 函数
date: 2018-05-19 22:47:25
categories: 软件开发
tags: [Vue]
---
<p align="center" style="margin:0;"><img src="https://raw.githubusercontent.com/CS-Tao/github-content/master/contents/blog/image/others/05.png" width="30%" height="30%"></p>
平时在写网页的时候，经常会遇到需要手动触发 window.onresize() 函数的情况，这里记录一下在 Vue.js 中如何手动触发这个函数。

``` JavaScript
// 添加 Vue 静态方法, 在全局通过调用 Vue.triggerResize() 触发
Vue.triggerResize = () => {
  let e = document.createEvent('Event')
  e.initEvent('resize', true, true)
  window.dispatchEvent(e)
}

// 添加 Vue 实例方法, 在 Vue 实例中通过调用 this.$triggerResize() 触发
Vue.prototype.$triggerResize = Vue.triggerResize
```
