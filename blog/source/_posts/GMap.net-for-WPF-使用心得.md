---
title: GMap.net for WPF 使用心得
date: 2017-08-19 16:42:23
categories: 软件开发
tags: [Wpf, GMap.net]
---
** GMap.net for WPF** <Excerpt in index | 首页摘要>
    利用GMap.net for WPF绘制点线面的方式以及未指定长宽的要素与其他要素间相对定位的方式
<!-- more -->
<The rest of contents | 余下全文>

## GMap.net概述

[GMap.net](https://greatmaps.codeplex.com/ "进入GMap.NET的项目地址")是一个强大、免费、跨平台、开源的.NET控件，它在WinForm和WPF环境中能够通过Google, Yahoo!, Bing, OpenStreetMap, ArcGIS, Pergo, SigPac等实现寻找路径、地理编码以及地图展示功能，并支持缓存和运行在Mobile环境中。<br>GMap.NET是一个开源的GEO地图定位和跟踪程序。就像谷歌地图、雅虎地图一样，可以自动计算两地的距离，定位经纬度，与Google地图不同的是，该项目是建立在WinForm框架或WPF框架基础上的。可以对地图放大缩小，进行城市标记等。

## GMap.net for WPF 绘制要素

因为现在正在利用GMap.net for WPF写一个项目，所以我对WPF版本的GMap.net更加熟悉，如有错误或不当之处，还望指出，共同进步！

* 不同于Winform版本，WPF版本没有图层的概念，但用于显示要素的对象GMapMarker提供了Zindex属性，该属性值大的会遮盖属性值小的。所以大家可以利用Zindex对地理要素建立逻辑上的图层关联。

* WinForm版本绘图可以直接在显示对象上设置图形的属性，如：
    ```C#
    GMapPolygon polygon = new GMapPolygon(pointList, "Polygon")
    {
        IsHitTestVisible = true;
        Fill = new SolidBrush(Color.FromArgb(50, Color.Red));
        Stroke = new Pen(Color.Blue, 2);
    }
    overlay.Polygons.Add(polygon);
    ```
    对于wpf版本的点对象，可以直接指定显示用户控件，如：<br>
    ```C#
    GMapMarker marker = new GMapMarker(pointLatLng);
    {
        MyUserControl myUserControl = new MyUserControl()
        marker.Shape = myUserControl;
        marker.ZIndex = (int)LayerIndex.Point;
        marker.Offset = new Point(-myUserControl.ActualWidth / 2, -myUserControl.ActualHeight / 2);
    }
    mapControl.Markers.Add(marker);
    ```
    其中MyUserControl可以重载自UserControl，并自定义显示内容。LayerIndex为自定义的枚举类型。mapControl重载自GMapControl。
    但是wpf版本的线的属性设置需要重载GMapControl的CreateRoutePath方法，面的属性设置需要重载CreatePolygonPath方法。为了不影响原函数的内容，我们可以参考GMapControl的源代码[*GMapControl.cs*](https://greatmaps.codeplex.com/SourceControl/latest#GMap.NET.WindowsPresentation/GMap.NET.WindowsPresentation/GMapControl.cs "查看源码文件")文件。重载CreateRoutePath方法和CreatePolygonPath后的内容如下，只做了少量修改：

    ```C#
    /// <summary>
    /// creates path from list of points, for performance set addBlurEffect to false
    /// </summary>
    /// <param name="pl"></param>
    /// <returns></returns>
    public override Path CreateRoutePath(List<Point> localPath, bool addBlurEffect)
    {
        // Create a StreamGeometry to use to specify myPath.
        StreamGeometry geometry = new StreamGeometry();

        using (StreamGeometryContext ctx = geometry.Open())
        {
            ctx.BeginFigure(localPath[0], false, false);

            // Draw a line to the next specified point.
            ctx.PolyLineTo(localPath, true, true);
        }

        // Freeze the geometry (make it unmodifiable)
        // for additional performance benefits.
        geometry.Freeze();

        // Create a path to draw a geometry with.
        Path myPath = new Path();
        {
            // Specify the shape of the Path using the StreamGeometry.
            myPath.Data = geometry;

            if (addBlurEffect)
            {
                BlurEffect ef = new BlurEffect();
                {
                    ef.KernelType = KernelType.Gaussian;
                    ef.Radius = 0.0;
                    ef.RenderingBias = RenderingBias.Performance;
                }

                myPath.Effect = ef;
            }

            myPath.Stroke = lineBrush;
            myPath.StrokeThickness = lineWidth;
            myPath.StrokeLineJoin = PenLineJoin.Round;
            myPath.StrokeStartLineCap = PenLineCap.Triangle;
            myPath.StrokeEndLineCap = PenLineCap.Round;

            myPath.Opacity = lineOpacity;
            myPath.IsHitTestVisible = false;
        }
        return myPath;
    }
    ```
    `注意:`代码中lineBrush、lineWidth、lineOpacity为重载GMapControl时新添的公共字段。

    ```C#
    /// <summary>
    /// creates path from list of points, for performance set addBlurEffect to false
    /// </summary>
    /// <param name="pl"></param>
    /// <returns></returns>
    public override Path CreatePolygonPath(List<Point> localPath, bool addBlurEffect)
    {
        // Create a StreamGeometry to use to specify myPath.
        StreamGeometry geometry = new StreamGeometry();

        using (StreamGeometryContext ctx = geometry.Open())
        {
            ctx.BeginFigure(localPath[0], true, true);

            // Draw a line to the next specified point.
            ctx.PolyLineTo(localPath, true, true);
        }

        // Freeze the geometry (make it unmodifiable)
        // for additional performance benefits.
        geometry.Freeze();

        // Create a path to draw a geometry with.
        Path myPath = new Path();
        {
            // Specify the shape of the Path using the StreamGeometry.
            myPath.Data = geometry;

            if (addBlurEffect)
            {
                BlurEffect ef = new BlurEffect();
                {
                    ef.KernelType = KernelType.Gaussian;
                    ef.Radius = 0.0;
                    ef.RenderingBias = RenderingBias.Performance;
                }

                myPath.Effect = ef;
            }

            myPath.Stroke = polygonStrokeBrush;
            myPath.StrokeThickness = polygonThickness;
            myPath.StrokeLineJoin = PenLineJoin.Miter;
            myPath.StrokeStartLineCap = PenLineCap.Triangle;
            myPath.StrokeEndLineCap = PenLineCap.Square;

            myPath.Fill = polygonFillBush;

            myPath.Opacity = polygonOpacity;
            myPath.IsHitTestVisible = false;
        }
        return myPath;
    }
    ```
    `注意:`代码中polygonStrokeBrush、polygonThickness、polygonFillBush、polygonOpacity为重载GMapControl时新添的公共字段。
* wpf版本只能绘制Point、PolyLine、Polygon三种图形，绘制圆则需要借助多边形的绘制。示例如下：
    ```C#
    public void DrawCircle(PointLatLng center, double R)
    {
        double cartesianCenterX = double.MaxValue;
        double cartesianCenterY = double.MaxValue;
        double cartesianCenterZ = double.MaxValue;

        mapControl.MapProvider.Projection.FromGeodeticToCartesian(center.Lat, center.Lng, 0, out cartesianCenterX, out cartesianCenterY, out cartesianCenterZ);//将圆心投影到笛卡尔坐标系

        int pointCount = 200;//用于拟合圆的多边形顶点个数

        List<PointLatLng> polygonPointList = new List<PointLatLng>(pointCount);//用于存放多边形顶点

        double interval = 2 * Math.PI / pointCount;
        for (double degree = 0; degree < 2 * Math.PI; degree += interval)
        {
            double tempX = cartesianCenterX + R * Math.Cos(degree);
            double tempY = cartesianCenterY + R * Math.Sin(degree);
            double tempLng = double.MaxValue;
            double tempLat = double.MaxValue;
            mapControl.MapProvider.Projection.FromCartesianTGeodetic(tempX, tempY, cartesianCenterZ, out tempLat, out tempLng);//投影到WGS84坐标系
            polygonPointList.Add(new PointLatLng(tempLat, tempLng));
        }

        GMapPolygon circle = new GMapPolygon(polygonPointList);
        {
            circle.ZIndex = (int)LayerIndex.Polygon;
        }
        mapControl.Markers.Add(circle);//添加到地图
    }
    ```
    效果如下：
    ![软件截屏](https://raw.githubusercontent.com/CS-Tao/github-content/master/contents/blog/image/GMap-Wpf-Draw-Circle.png)<br>
    因为投影问题，说好的圆变为了椭圆，如果想生成正圆，可以在程序中使用一些WebAPI服务替换GMap的投影服务，我们项目使用的是搭建在自己服务器上的的GeoServer服务。效果如下：
    ![软件截屏](https://raw.githubusercontent.com/CS-Tao/github-content/master/contents/blog/image/FGIS-Damage-Circle.png)<br>
    `备注：`两张图片截图自不同的程序。

## 未指定长宽的要素与其他要素间相对定位的方式

在使用GMap添加要素的时候，遇到需要对要素添加Tooltip，但不能指定Tooltip的长宽，且该要素与Tooltip需要水平中心对其，试过很多办法都不能成功，因为wpf控件的ActualWidth和ActualHeight属性必须加载过一次才能有正确的属性值，也就是说如果根据长宽计算GMapMarker的偏移量，Tolltip在第一次显示的时候无法正确定位，经过探索，最终利用wpf控件的SizeChanged响应函数实现了该效果，如果您有其他方法实现，希望能在评论中指出。效果如下：<br>
![软件截屏](https://raw.githubusercontent.com/CS-Tao/github-content/master/contents/blog/image/GMap-Tooltip.png)

```C#
public void AddIconWithTooltip(PointLatLng pll, Uri iconUri, string tooltip)
{
    Guid id = Guid.NewGuid();

    //添加tooltip显示窗口
    GMapMarker tooltipViewer = new GMapMarker(pll);
    {
        tooltipViewer.ZIndex = (int)LayerIndex.Point;
        tooltipViewer.Tag = id;
        TooltipForMap content = new TooltipForMap(tooltip, tooltipViewer);
        tooltipViewer.Shape = content;
        tooltipViewer.Shape.Visibility = Visibility.Hidden;
    }
    mapControl.Markers.Add(tooltipViewer);

    UIElement shape = new MyIcon(new BitmapImage(iconUri), tooltipViewer);//构造函数：MyIcon(ImageSource image, GMapMarker iconTooltipViewer, double width = 22, double height = 22, bool showTipAlways = false)

    GMapMarker iconMarker = new GMapMarker(pll);
    {
        iconMarker.ZIndex = (int)layerIndex;
        iconMarker.Offset = new Point(-11, -11);
        iconMarker.Tag = id;
        iconMarker.Shape = shape;
    }
    mapControl.Markers.Add(iconMarker);
}
```

`注意：`代码中id的作用是用于GMapMarker间的逻辑关联，方便同时从MapControl中移除。<br>
关键代码：

```C#
TooltipForMap content = new TooltipForMap(tooltip, tooltipViewer);
```

其中TooltipForMap类的SizeChanged函数如下：

```C#
private void TooltipForMap_SizeChanged(object sender, SizeChangedEventArgs e)
{
    _TooltipViewer.Offset = new Point(-ActualWidth / 2, -ActualHeight - 22);
}
```

`注意：`_TooltipViewer和传入构造函数的tooltipViewer为同一实例。