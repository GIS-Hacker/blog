---
title: Electron 桌面端软件开发最佳实践
date: 2018-04-02 22:54:22
categories: 软件开发
tags: [Electron, Webpack, Vue, Element-ui]
toc: true
---
<img src="https://raw.githubusercontent.com/CS-Tao/github-content/master/contents/blog/image/others/02.png" width="40%" height="40%">
相比 Web 前端，我个人更偏爱于开发类 Wpf 的桌面端软件，直到我遇见了 Electron。<br/>
前端时间看到一篇推送，题目好像是《2018 年最值得关注的 JavaScript 发展趋势》，文章中提到了利用 Js 进行桌面端软件的开发，是的，使用的工具就是 Electron。但由于那之后一段时间一直忙于各种事情，也就只是枯燥地看完了它的官方文档，并没有什么机会深入地学习，不过令人欣慰的是最近学院举办了编程比赛，其中应用题的题目为开发一个 GIS 可视化系统，要求实现 Web 端、桌面端和移动端。由于种种原因，我不能以参赛选手的身份直接参加本次比赛，于是便"单纯地指导"了一个小组参加这次比赛，过程很艰辛，但最后很幸运地获得了一等奖，没错，都是 Electron 的功劳（当然还有 Vue 的功劳）。<br/>
在这个比赛中，我们网页端的框架使用 Webpack + Vue 搭建，使用 Element-ui 作为 UI 库，使用 ArcGIS Api for JavaScript、Echarts 进行数据可视化。桌面端是在 Web 端使用的工具的基础上利用 Electron 进行开发，也就是说，桌面端使用的工具主要是：Electron + Webpack + Vue + Element-ui + ArcGIS Api for JavaScript + Echarts。至于移动端，我们只是在 Web 端的基础上简单地使用了 PhoneGap + Android SDK + Eclipse 将最终生成的静态网页打包成移动端安装包。服务器使用的是阿里的 Ubuntu 云服务器，后台采用 Django 搭建，数据库使用的 PostgreSQL。数据来源是某个网站上全国 338 个地级市近 4 年的空气污染数据。

---
- 客户端源码：[CS-Tao/HC-Desktop](https://github.com/CS-Tao/HC-Desktop)
本仓库长期维护，有任何问题请移步 [Issues](https://github.com/CS-Tao/HC-Desktop/issues)

---
- 网页端源码仓库：[lsq210/HC-WebSite](https://github.com/lsq210/HC-WebSite)
本仓库不再维护，Web 端源码可以通过客户端源码直接打包生成

---
- 移动端源码仓库：[lsq210/HC-App](https://github.com/lsq210/HC-App)
本仓库不再维护，移动端源码可以通过客户端源码和 PhoneGap 直接生成

---
- 爬虫源码仓库：[sleepy-everyday/HC-Spider](https://github.com/sleepy-everyday/HC-Spider)
如果本仓库还未开源，说明我们负责后台的同学最近还没有时间对数据库的用户权限进行更改

---
- 后台源码仓库：[sleepy-everyday/HC-Server](https://github.com/sleepy-everyday/HC-Server)
如果本仓库还未开源，说明我们负责后台的同学最近还没有时间对数据库的用户权限进行更改

---
<!-- more -->

### 为什么选择这些工具？

#### Electron

官网传送门：<u>[https://electronjs.org/](https://electronjs.org/)</u>.

我们知道，借助浏览器强大的渲染功能，我们可以编写出很漂亮很炫酷的网页，这是传统的桌面端软件很难企及的。但 Electron 借助于 Chromium，可以很轻易地实现这些炫酷的效果，而且因为几乎所有的页面都保存在本地，Electron 应用程序的页面加载速度大大优于 Web 端。对 Electron 的一个错误的理解是 Electron 所做的只是将原本的浏览器窗口变成了一个应用程序窗口，把原本应该从互联网上下载的网页都放在了本地，从而实现了 Web 端和客户端的完美结合。其实不然，出于安全原因，浏览器访问网页时需要采用的沙箱机制，以防不安全代码对计算机原生资源的访问或破坏，而 Electron 虽然内置了 Chromium 内核，但它却可以利用 Node Api 访问计算机原生资源，我们可以把 Electron 看做一个拥有 Node.js 环境的本地网页浏览器，而事实上，这和传统的 Web 应用程序已经有本质区别了。<br/>
>&emsp;&emsp;Electron 是由 Github 开发，用 HTML，CSS 和 JavaScript 来构建跨平台桌面应用程序的一个开源库。Electron 通过将 Chromium 和 Node.js 合并到同一个运行时环境中，并将其打包为 Mac，Windows 和Linux 系统下的应用来实现这一目的。<br/>&emsp;&emsp;Electron 于 2013 年作为构建 Github 上可编程的文本编辑器 Atom 的框架而被开发出来。这两个项目在 2014 春季开源。

#### Webpack

官网传送门：<u>[https://www.webpackjs.com/](https://www.webpackjs.com/)</u>.

Webpack 是当下最好的前端资源加载、打包的工具，没有之一，它会根据模块之间的依赖关系，递归地构建一个依赖关系图，然后将这些模块打包为一个或多个 Js 文件。Webpack 有四个核心概念：

- 入口(entry)
- 出口(output)
- loader
- 插件(plugins)

这些内容都会在 Webpack 的配置文件中体现出来，但本文不会对这些概念进行详细的解释。

- 入口(entry)会告诉 Webpack 应该使用哪一个模块作为构建内部依赖关系图的开始，然后 Webpack 会根据这个入口(entry)模块，找到所有它直接或间接依赖的模块，构成依赖关系图。
    ```JavaScript
    module.exports = {
        entry: './path/to/my/entry/file.js'
    };
    ```

- 出口(output)会告诉 Webpack 应该在哪里输出打包好的文件。
    ```JavaScript
    const path = require('path');
    
    module.exports = {
        entry: './path/to/my/entry/file.js',
        output: {
            path: path.resolve(__dirname, 'dist'),
            filename: 'my-first-webpack.bundle.js'
        }
    };
    ```

- 由于 Webpack 本身只理解 JavaScript，对于其他文件，比如 CSS、TypeScript 等，Webpack 是不知道如何解析和加载它们的。loader 可以 让 Webpack 有能力去解析和加载这些非 JavaScript 文件。loader 有两个很重要的属性：

    - `test`：指定哪些文件需要被指定的 loader 处理，一般使用正则表达式。
    - `use`：指定一个或多个 loader 处理`test`指定的文件
    
```JavaScript
const path = require('path');

const config = {
    entry: './path/to/my/entry/file.js',
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'my-first-webpack.bundle.js'
    },
    module: {
        rules: [
            { test: /\.txt$/, use: 'raw-loader' }
            ]
        }
};
module.exports = config;
```

- 我们现在知道 loader 是用来解析和加载某些非 JavaScript 模块的，而插件(plugins)的应用范围比 loader 广泛得多，我们可以通过加载插件对 Webpack 的打包过程进行优化、压缩等等，当然它的功能远不止这些，通过加载不同的插件可以用来处理各种各样的任务。
    ```JavaScript
    const HtmlWebpackPlugin = require('html-webpack-plugin'); // 通过 npm 安装
    const webpack = require('webpack'); // 用于访问内置插件
    const path = require('path');
    
    const config = {
        entry: './path/to/my/entry/file.js',
        output: {
            path: path.resolve(__dirname, 'dist'),
            filename: 'my-first-webpack.bundle.js'
        },
        module: {
            rules: [
                { test: /\.txt$/, use: 'raw-loader' }
            ]
        },
        plugins: [
            new webpack.optimize.UglifyJsPlugin(),
            new HtmlWebpackPlugin({template: './src/index.html'})
        ]
    };
    
    module.exports = config;
    ```

#### Vue

官网传送门：<u>[https://cn.vuejs.org/](https://cn.vuejs.org/)</u>.

>&emsp;&emsp;Vue (读音 /vjuː/，类似于 view) 是一套用于构建用户界面的渐进式框架。与其它大型框架不同的是，Vue 被设计为可以自底向上逐层应用。Vue 的核心库只关注视图层，不仅易于上手，还便于与第三方库或既有项目整合。

不久前因为项目需要，我们使用 Vue 开发了一个执法信息管理系统，不得不说，Vue 是真的好用。在接触 Electron 之前，我个人比较喜欢用 Winform 或 Wpf 开发桌面端软件，虽然只能在 Windows 平台上运行，但毕竟自己只用 Windows，偶尔用用 Linux 也只使用命令行，所有也并没有开发跨平台软件的需求。用过 Wpf 的朋友应该知道 Wpf 的数据绑定特性和 MVVM 模式，这应该是我现在仍然使用 Wpf 最重要的原因了。而使用 Vue，我们可以实现类似的功能，通过一个 Vue 实例的 Data 选项和一个 DOM 元素的 v-bind 属性，可以将视图和数据进行绑定，而通过[单文件组件](https://cn.vuejs.org/v2/guide/single-file-components.html)结合 vue-loader，我们可以将各个组件分离、独立设计，从而实现组件内强耦合、组件间松耦合，每个组件只需要负责自己需要完成的任务。利用 Vue 的动态渲染功能，结合传入组件内的数据，我们可以很容易地实现 MVVM 模式。

#### Element-ui

官网传送门：<u>[http://element-cn.eleme.io/](http://element-cn.eleme.io/)</u>.

Element-ui 是由饿了么前端出品的一套 Vue.js 后台组件库，组件清爽美观，与 Vue 2.0 紧密集成。其设计原则是：<br/>
<img src="https://raw.githubusercontent.com/CS-Tao/github-content/master/contents/blog/image/others/03.png" width="85%" height="85%">

- 一致性 Consistency
    - 与现实生活一致：与现实生活的流程、逻辑保持一致，遵循用户习惯的语言和概念
    - 在界面中一致：所有的元素和结构需保持一致，比如：设计样式、图标和文本、元素的位置等
- 反馈 Feedback
    - 控制反馈：通过界面样式和交互动效让用户可以清晰的感知自己的操作
    - 页面反馈：操作后，通过页面元素的变化清晰地展现当前状态
- 效率 Efficiency
    - 简化流程：设计简洁直观的操作流程
    - 清晰明确：语言表达清晰且表意明确，让用户快速理解进而作出决策
    - 帮助用户识别：界面简单直白，让用户快速识别而非回忆，减少用户记忆负担
- 可控 Controllability
    - 用户决策：根据场景可给予用户操作建议或安全提示，但不能代替用户进行决策
    - 结果可控：用户可以自由的进行操作，包括撤销、回退和终止当前操作等

### 项目实践 - 客户端 [![Build Status](https://travis-ci.org/CS-Tao/HC-Desktop.svg?branch=master)](https://travis-ci.org/CS-Tao/HC-Desktop)

<iframe src="https://ghbtns.com/github-btn.html?user=CS-Tao&repo=HC-Desktop&type=watch&count=true&v=2" frameborder="0" scrolling="0" width="170px" height="20px"></iframe>
<iframe src="https://ghbtns.com/github-btn.html?user=CS-Tao&repo=HC-Desktop&type=star&count=true" frameborder="0" scrolling="0" width="170px" height="20px"></iframe>
<iframe src="https://ghbtns.com/github-btn.html?user=CS-Tao&repo=HC-Desktop&type=fork&count=true" frameborder="0" scrolling="0" width="170px" height="20px"></iframe>

这个比赛从开始到答辩只持续了一周多的时间，由于一些不可抗力，前三天我没有任何时间去考虑如何架构这个系统，所以我"指导"的这个队伍前三天也没有开始任何设计或编码的工作，只是大概确定了一下了我们应该会做一个空气质量可视化系统。

现在想想全校有近 90 个队伍，其中有一半多的队伍参加的应用组比赛，像我们这样后知后觉也能拿到一等奖，的确得感谢全世界为开源事业做出贡献的人了，所以决赛答辩完我们也很快地将我们的成果以 Apache 2.0 协议开源了，之后会改为 MIT 协议的，待填坑...

#### 选择框架

当然在比赛开始前我们就已经决定使用 Electron + Vue 进行开发了，然后为了提高开发效率，我们使用了 [vue-element-admin](https://github.com/PanJiaChen/vue-element-admin) 作为基础的 UI 框架。

#### 修改框架

- 移除 vue-element-admin 的无关组件
- 将 Dashboard 页面改为 Github 的代码提交量和成员能力的可视化图表
- 添加登录用户

#### 添加路由

使用 vue-router 组件添加路由，按需添加了：
- mapview (地图可视化)
    - arcgismap (ArcGIS Map)
    - arcgisscene (ArcGIS Scene)
- charts (图表可视化)
    - singlecity (单个城市数据统计)
    - multicity (多个城市数据对比)

#### 添加可视化组件

- 添加 ArcGIS API for JavaScript 组件

这应该是本次开发中遇见的最大的问题了，ArcGIS API 并没有像其他组件一样提供一个可供直接导入源码的仓库，而是鼓励开发者使用 [esri-loader](https://github.com/Esri/esri-loader) 异步导入 Js 文件，当然我们也做了测试，因为该 Js 文件较大，如果直接利用 Webpack 导入该文件的确会使 Webpack 卡死。这个异步导入的过程需要从 Esri 官网加载 Js 文件，而国内的 GFW 和某学院院办楼里的网关，一个对 ArcGIS 官网限速，一个直接屏蔽了该网站，为了调试程序，我们小组开热点不知道花了多少流量...

- 添加 Echarts 组件

这一步操作应该还算顺利，之前参加美赛的时候用过 Echarts，负责前端的两位同学也都使用过它，加上十分详细的官方文档，这部分的开发可以说是十分流畅了。<br>`一点遗憾`：原本准备再添加三个 3D 图表的([#16](https://github.com/sleepy-everyday/HC-WebSite/issues/16))，但是由于时间和技术的限制，最后也没能实现，再后来想着看比赛结束后有没有时间能做一下，才发现我们数据的维度根本不够，罢了罢了，就这样平平淡淡吧。

#### 实现数据接入

- 撰写接口文档

因为有过一定的开发经验，我们小组只是商量然后撰写了需要的接口的形式和数据返回的格式，就各自按照接口文档的描述开始负责自己的模块了，后台采用 Django 提供数据接口服务，前端采用 Axios 异步加载数据，提供 Vue 的数据绑定功能，实现了从加载后台数据到更新前端视图的高度耦合。

- 按城市和日期选择加载数据

对于地图可视化，我们只需要根据日期加载数据即可，但对于图表可视化，我们则需要按城市和日期双重限制加载数据。这两个功能的实现自然不难，只需要从服务器端读取数据然后绑定到一个 el-select 控件即可。对我们来说最困难的是这两个控件应该如何安放，特别是如何在 ArcGIS 控件上安放 el-select 控件，但机智的我们还是强行地把这个问题解决了。好吧，也是待填坑...

#### 项目完善

- 更改 ArcGIS 控件布局
- 修改原 vue-element-admin 项目的测试代码
- 添加云端持续集成

### 后记

>&emsp;&emsp;有时候我们参加比赛并不是为了赢得奖品或荣誉，也不是因为这个东西可以在我们简历上增加多少项目履历，我们只是抱着对未知事物最原始的渴望以不断地尝试，才能不断地进步。

---
** 感谢为这个项目做出贡献的所有人。**
[@ GCCCCG](https://github.com/GCCCCG)
[@ lsq210](https://github.com/lsq210)
[@ HaoKunT](https://github.com/HaoKunT)
[@ HPDell](https://github.com/HPDell)
[@ CS-Tao](https://github.com/CS-Tao)
