# Introduction
本文主要介绍了在Linux上不需要安卓虚拟机的方式运行安卓App的方法。主要内容转载自[在Linux直接运行安卓程序](http://www.cnblogs.com/xuelongqy/p/5438461.html)，并进行了chrome上运行安卓程序的内容的添加。
# ARChon项目

[App Runtime for Chrome (ARC)](https://developer.chrome.com/apps/getstarted_arc)项目是谷歌推出的，开发者可以通过一个 Chrome 浏览器在任意操作系统运行 Android 应用，当然现在还很不完善，大部分app即使经过官方发布的[ARC Welder](https://chrome.google.com/webstore/detail/arc-welder/emfinbmielocnlhgmfkkmkngdoccbadn/related)进行apk到chrome application的转换，也不能正常运行。
民间的[ARChon项目主页](https://archon-runtime.github.io/)则移植了ARC到Linux和win上。国内的deepin团队也对其进行了自己的适配，以便适合自己的deepin操作系统，而且在此基础上，deepin又在维护很多国内的app。

# 一、使用deepin团队维护的作品[推荐]
## 运行打包好的deb
[包下载地址百度云分享](http://pan.baidu.com/s/1slqZi3r)
从环境包里下载`archon-integration_0.0~20150528_amd64.deb deepin-archon_1.0.1_amd64.deb`并安装，依赖noidejs，debian直接`apt-get -f install`解决依赖即可。
[deepin维护的app](http://packages.deepin.com/deepin/pool/non-free/a/)
我目前只下载测试了贴吧，微博，正常使用，但是cajviewer(知网的阅读器，用来阅读知网自己格式的论文)崩溃，无法使用。
## 直接运行apk [可以尝试自己下载apk]
下载安卓apk程序，尽量下载pad版本，解压apk：
```bash
# xxx.apk为被解压的apk文件名
/usr/lib/node_modules/chromeos-apk/chromeos-apk --archon -t --scale --name "Android App" xxx.apk
```
运行
```bash
/lastore/framework/myarchon/archonrun --silent-launch --load-and-launch-app=被解压apk程序的完整路径
```
# 二、使用谷歌官方的插件
你也可以自己使用谷歌的插件进行尝试运行android app。
##　安装谷歌ARC Welder应用
直接从谷歌应用商店下载[ARC Welder](https://chrome.google.com/webstore/detail/arc-welder/emfinbmielocnlhgmfkkmkngdoccbadn/related)即可（上不去谷歌请查看博客主页置顶文章）
## 安装配置android app
首先要开启chrome设置里的硬件加速选项，可选的操作是在`chrome://flags/`里开启第一个
>覆盖软件渲染列表 Mac, Windows, Linux, Chrome OS, Android
覆盖内置的软件渲染列表，并对不支持的系统配置启用 GPU 加速。 

下载自己想要测试的app的apk安装包，从chrome的应用起动器或者访问` chrome://apps`打开ARC Welder，首次打开ARC Welder需要点击Choose设置一个目录用于存储资料。然后点击Add your APK添加你想要运行的安卓应用程序
![](http://www.findspace.name/wp-content/uploads/2016/08/arcwelder.png)
主要设置项如下：

|设置|选项|
|-|-|
|Orientation 屏幕方向|Landscape 横向 / Portail 竖向|
|Form Factor 设备类型|Tablet 平板电脑 / Phone 手机 / Maximized 最大化|
|Clipboard Access 访问剪切板|Yes / No|

我的debian 8 gnome3选项没有显示，但是点击相应位置可以设置。。
设置完成后点击TEST运行app，weiboHD版登录的时候屏幕方向有问题，但是登录完成以后就正常的横屏了。
## 同时运行多个app
ARC Welder 一次只允许启动一个app，可以点击Download ZIP 将app转换成chrome应用，将获得的zip解压到任意文件夹，前往`chrome://extensions` 打开「扩展工具」，勾上右上角「开发者模式」，然后点击「加载正在开发的扩展程序..」并选择该目录，将这个 Chrome 应用导入到浏览器去。
![](http://img.iplaysoft.com/wp-content/uploads/2015/arc-welder/chrome_extension.png)
添加完成后，就可以在chrome应用启动器的应用列表里看到新添加的android 应用。点击图标即可运行。


# Reference 
[ARC Welder 谷歌官方安卓模拟器 ](http://www.iplaysoft.com/arc-welder.html)
[在Linux直接运行安卓程序](http://www.cnblogs.com/xuelongqy/p/5438461.html)
[在Ubuntu下开启Chrome的WebGL功能（开启GPU加速）](http://my.oschina.net/noahxiao/blog/146018)
