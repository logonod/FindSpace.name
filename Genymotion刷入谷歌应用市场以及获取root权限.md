# Introduction
现在genymotion新建的镜像中，都去掉了对root权限的管理，但是在命令行进入之后，仍然是具有root权限的，本文介绍了刷入supersu权限管理工具和谷歌应用包。

# 说明
genymotion 是一个非常流行的安卓模拟工具，网络上现在也流传着很多在genymotion里安装谷歌服务（Google play store等等）的教程(包括我之前翻译的一篇[Genymotion简单教程及问题解决](http://www.findspace.name/res/1221))，但是大多数都已经没法用了。根据那些教程安装上的google play store，经常会弹出“已停止服务”的窗口。
其实最关键的点是，要刷对google apps package版本。现在常用的[OpenGapps](http://opengapps.org)在他们的zip包里有特殊的脚本，但是它不适合genymotion。要使用**BaNkS Dynamic GApps**。
同样，supersu root权限管理工具也是，必须用正确的（老的）版本。
本文针对genymotion中的andoird 6.0镜像进行设置。

>论坛里有人说可以直接用opengapps的x86架构（非x86_64）的包，我还没测试。

# 文件准备

+ Genymotion Arm Translator [百度网盘下载地址](http://pan.baidu.com/s/1qWNl5WK)
+ [BaNkS Dynamic GApps](http://download.dirtyunicorns.com/files/gapps/banks_gapps/)
+ [SuperSU v2.46(不能用新的版本)](https://download.chainfire.eu/696/supersu/)

# 步骤梗概

1. 在genymotion中添加andorid 6.0的镜像
2. 刷入genymotion arm translator，重启镜像
3. 刷入gapps，重启
4. 刷入supersu，重启
5. 通过应用市场更新supersu，在supersu里更新二进制文件，重启（可选）

# 详细步骤
## 1.在genymotion中添加android6.0镜像
由于提供的gapps是针对6.0版本的，之前的安卓版本可能不支持。如有测试在5.0上可以用的gapps，请在该文章下留言。
## 2. 刷入genymotion arm transltor
非常重要的步骤，以便让x86架构的genymotion 镜像里支持arm架构的app。
直接拖动下载的genymotion arm translator到开启的android 镜像里，会提示你是否刷入，选择是，然后等待提示刷入成功。
## 3. 刷入gapps，重启
拖动下载的gapps，也是选择刷入，然后重启（在debian下，adb reboot命令始终不能正常运行，我是叉掉窗口然后重新从genymotion里打开镜像）。
## 4. 刷入supersu，重启
类似上述步骤刷入即可。
注意，也可以通过shell刷入，首先把文件传入到android 6.0以后，通过
```
adb shell flash-archive.sh /sdcard/Download/UPDATE-SuperSU-v2.65-20151226141550.zip
```
`flash-archive.sh`是genymotion提供的一个刷入zip文件的脚本，在`/system/bin/`下，后面的路径是你选择的要输入的文件路径，在虚拟机里。
## 5.更新supersu
不再赘述。

# Reference
本文内容系翻译[xda论坛](http://forum.xda-developers.com/android/software/guide-genymotion-play-store-supersu-t3396840)，文章里也说明了xposed的安装，本文并未添加该部分内容。
[找到的另外一个方法，只是包的不同](https://z3ntu.github.io/2015/12/10/play-services-with-genymotion.html)