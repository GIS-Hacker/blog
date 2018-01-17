---
title: '解决C#发送电子邮件失败的问题'
date: 2017-10-18 08:21:15
categories: 技术相关
tags: [C#]
---
** 解决C#-WPF桌面软件发送电子邮件失败的问题** <Excerpt in index | 首页摘要>
    C#发送邮件的方法在网上搜一下可以找到很多，几个小时过去了还是没能实现，对比了很多人写的博客，笔者最终找到了问题所在，并在此记录。
<!-- more -->

# 实现过程

- 配置App.Config文件
    - 在项目中添加`System.Configuration`程序集的引用
    - 在App.Config文件中添加键值，如下（只需要关注appSettings标签内的内容）

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <configSections>
    <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=*************" requirePermission="false" />
    <!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
  </configSections>
  <startup>
    <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
  </startup>
  <appSettings>
  <!--邮箱-->
    <add key="MANAGER_MAIL_NUM" value="123456789@qq.com" />
    <!--邮箱密码-->
    <add key="MANAGER_MAIL_PASSWORD" value="邮箱密码。QQ邮箱需要许可码" />
    <!--邮件显示名-->
    <add key="MANAGER_MAIL_NAME" value="发送邮件使用的用户名" />
    <!--QQ邮箱对应的SMTP服务器-->
    <add key="SmtpClient_HOST" value="smtp.qq.com"/>
  </appSettings>
</configuration>
```

- EmailHelper.cs文件内容

```C#
using System;
using System.Text;
using System.Net.Mail;
using static System.Configuration.ConfigurationManager;

namespace View_Spot_of_City.UIControls.Helper
{
    public static class EmailHelper
    {
        public static bool SendEmail(string mail, string title, string content)
        {
            MailMessage message = new MailMessage();
            {
                message.To.Add(mail);
                message.From = new MailAddress(AppSettings["MANAGER_MAIL_NUM"], AppSettings["MANAGER_MAIL_NAME"], Encoding.UTF8);
                message.Subject =title;
                message.SubjectEncoding = Encoding.UTF8;
                message.Body = content;
                message.BodyEncoding = Encoding.UTF8;
                message.IsBodyHtml = false;
                message.Priority = MailPriority.Normal;
            }

            SmtpClient smtp = new SmtpClient();
            {
                smtp.Host = AppSettings["SmtpClient_HOST"];
                smtp.EnableSsl = true;
                smtp.UseDefaultCredentials = false;
                smtp.Credentials = new System.Net.NetworkCredential(AppSettings["MANAGER_MAIL_NUM"], AppSettings["MANAGER_MAIL_PASSWORD"]);
            }
            object userState = message;
            try
            {
                smtp.SendAsync(message, userState);
                return true;
            }
            catch(Exception ex)
            {
                Console.Write(ex.Message);
                return false;
            }
        }
    }
}
```

# 注意

若使用QQ邮箱，输入密码为许可码，需要在QQ邮箱中打开SMTP服务

- 打开QQ邮箱网页版
- 点击左上角“设置”，并在导航栏中点击账户标签
- 开启SMTP服务并获得许可码
    ![开启SMTP服务](https://raw.githubusercontent.com/CS-Tao/github-content/master/contents/blog/image/WpfSendMail.png)