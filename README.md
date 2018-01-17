### 个人主页

 采用ejs模板引擎渲染生成html主页，demo：[http://home.cs-tao.cc/](http://home.cs-tao.cc/)。

#### 依赖工具

- [Node.js](https://nodejs.org/zh-cn/)

    进入官网下载安装即可。

- [ejs-on-command](https://github.com/shennan/ejs-on-command)

    这是一个ejs渲染工具。安装完Node.js后，在本地命令行中执行以下命令安装ejs-on-command：
    ```bash
    npm install -g ejs-on-command
    ```
    此步骤为非必要步骤：之后的操作会自动安装本工具，不一定需要在这里单独安装。

#### 使用方法

1. Fork本仓库，仓库地址为[https://github.com/GIS-Hacker/GIS-Hacker.github.io](https://github.com/GIS-Hacker/GIS-Hacker.github.io)

1. 进入你fork后的仓库的Settings页面，将"Repository name"改为如下格式：
    ```bash
    <你的账户名>.github.io
    ```
1. 修改CNAME文件：如果你有已经解析好的域名，就直接修改文件中的内容，如果没有，就删掉它（重点）。
1. 在本地打开git命令行窗口，输入下面的命令（下载源码）：
    ```bash
    git clone https://github.com/<你的账户名>/<你的账户名>.github.io.git
    ```
1. 输入以下命令（进如源码文件夹、安装必要的工具-ejs-on-command）
    ```bash
    cd GIS-Hacker.github.io
    npm install
    ```
1. 修改配置文件"config.json"内容，后续详述。

1. 启动渲染工具：
    ```bash
    npm run render
    ```
1. 提交更改并发布到远程仓库（git基本操作，不详述）：
    ```bash
    git add --a
    git commit -m "修改配置文件、重新渲染"
    git push origin master
    ```
#### 配置文件"config.json"样板和注释（千万不要在配置文件中加注释）：
```json
{
    "Title": "CS-Tao · HomePage", //主页标题

    "Title_leave": "点我点我", //当主页未获得焦点时的标题

    "Title_return": "就很开心", //当主页重新获得焦点时的标题，仅保持两秒

    "Favicon": "favicon.png", //网站logo

    "Avatar": "https://raw.githubusercontent.com/CS-Tao/CS-Tao.github.io/master/img/avatar.png", //头像url，可以是网络资源，也可以是本地文件，如果是本地文件，输入相对路径即可

    "Avatar_href": "http://blog.cs-tao.cc/", //点击头像进入的超链接，将在本窗口中打开

    "Author" : "CS-Tao", //你的名字

    "Author_href": "http://blog.cs-tao.cc/", //点击你的名字进入的超链接，将在本窗口中打开

    "Text_for_show": "也许，某天，当我们转过头，发现一切已在手里。<br>也许，某天，当我们偶一回眸，看到那人还在灯火阑珊中。", //主页下方显示的文字

    "Copyright_time": "2018", //版权的起始日期

    "Music_URL": "assets/music.mp3", //音乐url，可以是网络资源，也可以是本地文件，如果是本地文件，输入相对路径即可（这里使用的本地资源）

    "Music_name": "告白", //音乐名，会显示在页脚处

    //社交链接
    //现在支持的Name的取值有：twitter、facebook、mail、home、share、qq、weibo、segmentfault、jianshu、acfun、tumblr、rss、github、film、weixin、qzone、douban、tuding、zhihu、linkedin、google、bilibili、psn。
    //URL为点击对应图标进入的超链接，将在新窗口中打开
    "Social": [
        {
            "Name": "mail",
            "URL": "http://mail.qq.com/cgi-bin/qm_share?t=qm_mailme&amp;email=whucstao@qq.com"
        },
        {
            "Name": "github",
            "URL": "https://github.com/CS-Tao"
        },
        {
            "Name": "weibo",
            "URL": "http://weibo.com/tcs990296951"
        },
        {
            "Name": "zhihu",
            "URL": "https://www.zhihu.com/people/tao-chun-sheng-21/"
        },
        {
            "Name": "douban",
            "URL": "https://www.douban.com/people/CS-Tao/"
        },
        {
            "Name": "google",
            "URL": "https://plus.google.com/115901576244995527191"
        },
        {
            "Name": "twitter",
            "URL": "https://twitter.com/TaoChengSheng"
        },
        {
            "Name": "linkedin",
            "URL": "https://www.linkedin.com/in/春生-陶-9917b3148/"
        },
        {
            "Name": "rss",
            "URL": "http://blog.cs-tao.cc/atom.xml"
        }
    ]
}
```

© CS-Tao since 2017. Inspired by [Github Pages](https://github.com/mystic-cg/mystic-cg.github.io).
