---
title: WPF自定义消息框
date: 2017-10-18 08:19:17
categories: 软件开发
tags: [Wpf]
reward: true
---
** 一个更改按钮显示语言的的WPF消息框** <Excerpt in index | 首页摘要>
消息框采用Material风格，支持中英切换，支持的返回值有Ok、Cancel、Yes、No，代码已托管并发布至[Github](https://github.com/CS-Tao/MyMessageBox/releases/tag/v1.0)
<!-- more -->
<The rest of contents | 余下全文>

# 效果预览

- 带OK按钮的消息框

![带OK按钮的消息框](https://raw.githubusercontent.com/CS-Tao/github-content/master/contents/blog/image/MyMessagebox/MyMessageBox_OK1.png)
![带OK按钮的消息框](https://raw.githubusercontent.com/CS-Tao/github-content/master/contents/blog/image/MyMessagebox/MyMessageBox_OK2.png)

- 带OK和取Cancel按钮的消息框

![带OK和取Cancel按钮的消息框](https://raw.githubusercontent.com/CS-Tao/github-content/master/contents/blog/image/MyMessagebox/MyMessageBox_OKCancel1.png)
![带OK和取Cancel按钮的消息框](https://raw.githubusercontent.com/CS-Tao/github-content/master/contents/blog/image/MyMessagebox/MyMessageBox_OKCancel2.png)

- 带Yes和No按钮的消息框

![带Yes和No按钮的消息框](https://raw.githubusercontent.com/CS-Tao/github-content/master/contents/blog/image/MyMessagebox/MyMessageBox_YesNo1.png)
![带Yes和No按钮的消息框](https://raw.githubusercontent.com/CS-Tao/github-content/master/contents/blog/image/MyMessagebox/MyMessageBox_YesNo2.png)

- 带Yes、No和Cancel按钮的消息框

![带Yes、No和Cancel按钮的消息框](https://raw.githubusercontent.com/CS-Tao/github-content/master/contents/blog/image/MyMessagebox/MyMessageBox_YesNoCancel1.png)
![带Yes、No和Cancel按钮的消息框](https://raw.githubusercontent.com/CS-Tao/github-content/master/contents/blog/image/MyMessagebox/MyMessageBox_YesNoCancel2.png)

# 使用方法

## 首先

- 引用本消息框所在程序集。
- 在App.xaml文件中添加：

```xml
<ResourceDictionary Source="pack://application:,,,/CSTao.MessageBox;component/Resources.xaml"/>
```

如下：

![添加资源字典](https://raw.githubusercontent.com/CS-Tao/github-content/master/contents/blog/image/MyMessagebox/MessageBoxResoureCode.png)

## 使用MessageBox

- 在程序中需要使用的地方添加命名空间：

```C#
using CSTao.MessageBox;
```

- 调用MessageboxMaster.Show()函数，该函数有多个重载，请按您的需求使用。

## 使用语言切换

- 静态修改

将`MyMessageBox\MyMessagebox\CSTao.MessageBox\Languages\LanguagesDictionary.xaml`中的`Language.CN.xaml`改为`Language.EN.xaml`即可。

- 动态修改

在您定义的修改语言的响应函数中添加代码

```C#
string requestedCulture = string.Format(@"pack://application:,,,/CSTao.MessageBox;component/Languages/Language.{0}.xaml", languageDictionary[0或1]);
ResourceDictionary resourceDictionary = Application.Current.Resources.MergedDictionaries.FirstOrDefault((x) =>
{
    return (x.Source == null) ? false : (x.Source.OriginalString.Contains("CSTao.MessageBox;component/Languages"));
});
if (resourceDictionary != null)
{
    Application.Current.Resources.MergedDictionaries.Remove(resourceDictionary);
    ResourceDictionary requestDictionary = new ResourceDictionary()
    {
        Source = new Uri(requestedCulture)
    };
    Application.Current.Resources.MergedDictionaries.Add(requestDictionary);
}
```

`温馨提醒:`本方法是笔者用于全局改变软件语言的代码，不仅针对本消息框，慎用

## 修改主题颜色

修改`MyMessageBox\MyMessagebox\CSTao.MessageBox\Resources.xaml`中`PrimaryHueBrush`键的值即可

# 特别感谢

感谢[师兄](https://hpdell.github.io/)提供的代码参考。