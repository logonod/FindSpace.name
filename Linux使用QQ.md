# Introduction
本文主要介绍了如何在Linux上完美运行deepin团队维护的wine下的一些软件。比如QQ等。这个方法运行的QQ基本没有bug，而且比广泛流行的qq国际版（实际上也是deepin团队的作品）版本更新，且功能更多，比longene社区的QQ（bug实在太多了）要稳定很多。
本文主要内容转载自[其它linux发行版完美运行deepin上的wine软件包(ubuntu QQ也完美)](http://www.cnblogs.com/xuelongqy/p/5437015.html)，已获授权，并进行了简单的补充。
# 1. Linux运行QQ
我们知道在linux上面，deepin做了很多wine的应用程序，但是在其他的linux发行版上却没有这种待遇，下面介绍一下，如何在继续debian的linux发行版上运行deepin的wine应用程序。
测试通过的平台：
Mint 17.3（crossover14），Ubuntu 16.04（crossover 15），Debian8（crossover 14 && 15）
注:此文章所提及的内容纯属个人兴趣，可以一起交流但不得用于非法途径，使用过程中出现的问题与本人无关
deepin的wine应用是基于crossover运行的，但是我试了移植到playOnLinux上基本没多少问题，但是考虑到安装比较麻烦还是使用了crossover。
( Linux)将CrossOver的wine程序移植到PlayOnLinux: http://www.cnblogs.com/xuelongqy/p/5438405.html
如果支持crossover的朋友请购买正版。
下文皆以Debian8 Gnome3 x64为例
## 1.1 安装crossover
[crossover百度网盘下载](http://pan.baidu.com/s/1gflTQd9)
注：资源文件夹里面有多个版本的crossover，后缀加了free你们懂的，但是存在部分功能性问题，不影响deepinwine的软件包使用。没有free的没有功能性问题，但是可能出现deepinwine软件兼容性问题，试用15天的，如果支持crossover的朋友请购买正版。建议大家来回覆盖安装这两个版本使用，基本可以互补。如果有兴趣的朋友可以和我一起讨论研究解决问题。
对于什么是来回覆盖安装，举个例子：free版是不能创建容器的，那么我们先安装不是free的版本也就是试用版，把容器创建好了以后，在覆盖安装free版就可以使用了。所以两个版本功能是互补的，虽然麻烦，但还是不影响正常使用的。
### 1.1.1 添加32位库的支持
如果是64位系统，先添加对32位库的支持：
```
sudo dpkg --add-architecture i386
sudo apt-get update
# 可能需要添加下列32位库
sudo apt-get install lib32z1 lib32ncurses5
```
### 1.1.2 安装crossover
如果要安装14版本，
从上面的分享地址里下载`crossover_14.1.11-1_all.deb` `crossover_14.1.11-1_all-free.deb` `deepin-crossover_0.5.14_all.deb`三个文件。依次安装
如果安装15，
`crossover-15_15.0.3-1_all.deb crossover-15_15.0.3-1_all-free.deb deepin-crossover-helper_1.0deepin0_all.deb` 并依次安装。
### 1.1.3 可能出现的依赖问题

+ debian 8 可能会出现依赖问题，需要安装`libjpeg8`但是库里没有，这是因为这个包在testing分支里，stable是没有的，但是可以直接[下载deb包安装](http://antix.daveserver.info/jessie/pool/main/libj/libjpeg8/)。
+ 如果crossover不能使用(不能创建容器)，安装libp11-kit-gnome-keyring_3.18.3-0ubuntu2_i386.deb，还是不能的话的试试64位版的
+ 其他依赖问题：
	+ 如果安装过程中缺少依赖不能安装，可以参照后面移植的方法去掉相关依赖，影响不大
	+ 如果可以安装libgnutls-deb0-28的话就安装，不能安装也没多大影响。
	+ 安装libgif4，安装不了的，可以安装libgif7，比如ubuntu16
	+ 以上依赖包可以通过新立得安装，也可以使用"sudo apt-get install 软件名 "安装

## 1.2 安装deepin的wine程序
[QQ 8.x，需要Crossover 15](http://packages.deepin.com/deepin/pool/non-free/a/apps.com.qq.im/)
[QQ 7.x 支持Crossover 14](http://packages.deepin.com/deepin/pool/non-free/d/deepinwine-qq/)
[其他的deepin wine程序下载，实际上这就是deepin的源](http://packages.deepin.com/deepin/pool/non-free/d/)

都是deb包，可以直接下载安装。

# 2. 简单移植deepin的wine软件包道其他Linux发行版
对于无法解决的依赖，可以直接对软件包进行修改，去掉这些依赖进行安装，但由于依赖问题，所以部分软件并不能完美运行。
## 2.1 安装dpkg
默认已经安装
```
sudo apt-get install dpkg

```
## 2.2 修改deepin的wine软件包
下载deepin的wine软件包，例如wine.deb，
### 2.2.1 解压wine软件包
```
# 先创建软件包目录
mkdir -p extract/DEBIAN
# 用dpkg解压
dpkg-deb -x wine.deb extract/
dpkg-deb -e wine.deb extract/DEBIAN
```
### 2.2.2 去掉依赖
用文本编辑器打开`extract/DEBIAN/control`，找到`Depends`行，去掉安装时，提示的不能通过的依赖，一般只留下crossover即可。
### 2.2.3 重新打包
```
# 建立软件包生成目录
mkdir build
# 重新打包
dpkg-deb -b extract/ build/
```
在build目录下会看到新生成的wine软件包，安装即可。然后在crossover中会看到这个容器，运行里面的软件即可

