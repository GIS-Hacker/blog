# 博客相册

本仓库用于给博客的相册提供照片源，包括原图和缩略图。demo：[http://home.cs-tao.cc/blog/gallery](http://home.cs-tao.cc/blog/gallery)

# 目录说明

- scripts：存放python脚本文件，用于生成缩略图和json描述文件
- photos：存放高分辨率图片，即源图片。注意，如果原图的长宽不相等，会被截取成长宽相等的图片，所以请将原图备份到其他位置
- min_photos：存放缩略图

# 图片以及描述文件命名格式

## 图片命名格式

年-月-日_文件默认描述[简短].图片后缀

**注意：月和日小于10的应补足两位，如'2017-08-02_图片.jpg'**

## 描述文件命名格式

图片全名.txt

如'2017-08-02_图片.jpg.txt'

## 其他说明

如果没有指定描述文件，图片的描述信息默认为图片名的后面一部分，如'2017-08-02_图片.jpg'的描述信息为'图片'
