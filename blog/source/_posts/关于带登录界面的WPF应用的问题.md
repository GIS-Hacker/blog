---
title: 关于带登录界面的WPF应用的问题
date: 2017-10-11 23:24:18
categories: 软件开发
tags: [Wpf]
---
** 解决WPF关闭登录窗口后主窗口无法打开的问题** <Excerpt in index | 首页摘要>
    笔者最近在开发一款[WPF应用程序](https://github.com/CS-Tao/View-Spot-of-City)的时候，发现在登录窗口关闭之后，主窗口无法打开，遂上网搜索了一下，找到了问题所在
<!-- more -->
<The rest of contents | 余下全文>

# 问题描述

笔者通过重载App类OnStartup()函数，在其中添加了启动登录框的代码：

```C#
protected override void OnStartup(StartupEventArgs e)
{
    //验证License
    if (!RegisterMaster.CanStart())
    {
        Environment.Exit(0);
    }

    //登录
    bool? loginDlgResult = (new LoginDlg()).ShowDialog();
    if (!loginDlgResult.HasValue || !loginDlgResult.Value)
        Environment.Exit(0);

    base.OnStartup(e);
}
```

却发现登录窗口关闭后不能启动主窗口（也就是WPF自动生成的MainWindow类对应的窗口）。

# 解决方法

将App的ShutdownMode属性改为OnExplicitShutdown即可：

```C#
protected override void OnStartup(StartupEventArgs e)
{
    //应用程序关闭时，才System.Windows.Application.Shutdown调用
    this.ShutdownMode = ShutdownMode.OnExplicitShutdown;

    //验证License
    if (!RegisterMaster.CanStart())
    {
        Environment.Exit(0);
    }

    //登录
    bool? loginDlgResult = (new LoginDlg()).ShowDialog();
    if (!loginDlgResult.HasValue || !loginDlgResult.Value)
        Environment.Exit(0);

    base.OnStartup(e);
}
```

`注意：`需要关闭应用程序时需要显示调用System.Windows.Application.Shutdown()函数，或其他退出程序的函数。

# 出现问题的原因

由上我们可以看出问题的根源就是App的ShutdownMode属性，那么这个属性有什么意义呢？<br>
我们可以很容易地知道ShutdownMode是一个枚举属性，其可能得取值有三个，分别是OnLastWindowClose、OnMainWindowClose、OnExplicitShutdown。<br>进一步探索笔者发现ShutdownMode属性的默认值为OnLastWindowClose，也就是WPF会在最后一个窗口关闭时隐式调用Application的Shutdown()函数，对此MSDN中有提到：https://msdn.microsoft.com/zh-cn/subscriptions/system.windows.application.shutdownmode(v=vs.100).aspx<br>
>如果将 ShutdownMode 设置为 OnLastWindowClose，则 Windows Presentation Foundation (WPF) 会在应用程序中的最后一个窗口关闭时隐式调用 Shutdown，即使任何当前已经实例化的窗口被设置为主窗口也是如此。请参见 https://msdn.microsoft.com/zh-cn/subscriptions/system.windows.application.mainwindow(v=vs.100).aspx

WPF会把第一个在AppDomain中实例化的第一个Window对象的引用，自动设置为应用程序的主窗口，也就是说当登录框实例化的时候，就被设置为主窗口了。且当登录窗口关闭时，没有任何其他的窗口处于显示状态，满足`OnLastWindowClose`的退出条件，WPF会隐式调用ShutDown()，以至于真正的主窗口无法显示。