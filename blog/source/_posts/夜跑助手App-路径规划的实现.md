---
title: 夜跑助手App-路径规划的实现
date: 2017-09-03 16:34:55
categories: 软件开发
tags: [Android, WebAPI]
---
** [夜跑助手App](https://github.com/CS-Tao/Route-NightRun)路径规划的实现 ** <Excerpt in index | 首页摘要>
    夜跑助手App是笔者参加某数据生产公司的地图制图大赛的成果之一，这里对其中的夜跑路径规划的方式进行记录。
<!-- more -->
<The rest of contents | 余下全文>

# 按站点进行路径规划

## 原理和方法

按站点进行路径规划的方式主要是利用[GraphHopper](https://www.graphhopper.com/)提供的WebAPI进行路径规划，通过对Rest接口发送Get请求获取json数据，如[示例](https://graphhopper.com/api/1/route?point=49.932707,11.588051&point=50.3404,11.64705&vehicle=car&debug=false&key=f8821850-c1f8-4f8f-befb-f976c887ebfb&optimize=true)。

## 具体实现

- 通过用户在手机屏幕上的双击操作获得用户希望经过的站点并进行标记
- 在用户指定的站点链表的首位加上用户位置
- 将上一步产生的站点列表投影为地理坐标
- 生成http请求
- 得到返回的json数据
- 解析数据，此时便可得到路径信息
- 将路径显示到屏幕上
- 同时计算路径附近两百米形成的地理坐标框，发送http请求到我们自己的服务器
- 得到饮品店信息并进行显示

流程图如下：

![流程图](https://raw.githubusercontent.com/CS-Tao/github-content/master/contents/blog/image/%E6%8C%89%E7%AB%99%E7%82%B9%E7%9A%84%E8%B7%AF%E5%BE%84%E8%A7%84%E5%88%92.png)

效果图如下：

![App截屏](https://raw.githubusercontent.com/CS-Tao/github-content/master/contents/blog/image/%E6%8C%89%E7%AB%99%E7%82%B9%E8%A7%84%E5%88%92%E8%B7%AF%E5%BE%841.jpg)

![App截屏](https://raw.githubusercontent.com/CS-Tao/github-content/master/contents/blog/image/%E6%8C%89%E7%AB%99%E7%82%B9%E8%A7%84%E5%88%92%E8%B7%AF%E5%BE%842.jpg)

# 按路径长度进行路径规划

## 原理和方法

按路径长度进行路径规划相比于按站点的路径规划显得更加复杂，需要预定若干站点，并对符合预定要求的站点依次利用GraphHopper的Rest接口计算最短距离，接着得到与指定路径长度的一半最接近的距离和其对应的站点，最后通过GraphHopper得到最短路径。

## 具体实现

`注意：`夜跑区域的站点数据，由App维护人员通过其他软件采集并上传到数据库，软件已托管至[Github](https://github.com/CS-Tao/DataAcquisitionForNightRunning)。

- 用户输入路径长度，设为a
- 得到以用户为中心周围a/4到a/2区域内的所有站点
- 依次利用GraphHopper的最短路径接口发送http请求，并获得用户位置到上一步所有站点的路径距离
- 将这些距离值与与a/2比较，得到与a/2最接近的距离，并记录其对应的站点
- 通过发送http请求获得用户位置到该站点的最短路径
- 解析数据并显示路径
- 显示附近的饮品店，和之前的方式一样，在此不详述

流程图如下：

![流程图](https://raw.githubusercontent.com/CS-Tao/github-content/master/contents/blog/image/%E6%8C%89%E8%B7%AF%E5%BE%84%E9%95%BF%E5%BA%A6%E8%BF%9B%E8%A1%8C%E8%B7%AF%E5%BE%84%E8%A7%84%E5%88%92.png)

效果图如下：

![App截屏](https://raw.githubusercontent.com/CS-Tao/github-content/master/contents/blog/image/%E6%8C%89%E8%B7%AF%E5%BE%84%E9%95%BF%E5%BA%A6%E8%BF%9B%E8%A1%8C%E8%B7%AF%E5%BE%84%E8%A7%84%E5%88%921.png)

![App截屏](https://raw.githubusercontent.com/CS-Tao/github-content/master/contents/blog/image/%E6%8C%89%E8%B7%AF%E5%BE%84%E9%95%BF%E5%BA%A6%E8%BF%9B%E8%A1%8C%E8%B7%AF%E5%BE%84%E8%A7%84%E5%88%922.png)

# 结束语

按指定站点进行路径规划得到的是环形回路，按指定路径长度得到的是一条供往返的线路。笔者思考了很久，最终采用了如上文叙述的方法进行按路径长度规划路径，如果您有更好的方法，无论是算法还是工具，希望您能在评论中指出，共同进步，非常感谢。