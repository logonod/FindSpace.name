​原来用过rime，那时候只觉得配置很强大，很细致，但是用了一段时间，发现词库并不丰富，还不如用fcitx-googlepinyin + fcitx-yunpinyin好得还有个云候选词，但是使用时间长了，感觉这毕竟还是个权宜之计。

今天看论坛突然又看到了相关信息，搜索了下，发现可以把搜狗的词库转换成rime词库。这下就基本解决了​词库问题。

## 1. 安装rime
我使用的是fcitx平台的，只需要安装fcitx-rime即可。（ibus框架也收录了rime）
## 2. 下载词库
[从搜狗词库下载自己需要的](pinyin.sogou.com/dict/)
默认搜狗词库的后缀都是`scel`，如果下载文件格式有问题，自己重命名即可。
## 3. 使用深蓝词库转换工具
从github上下载开源的[深蓝词库转换工具](https://github.com/studyzy/imewlconverter)，注意需要安装.net3库，win10在你运行程序的时候会自动给你安装。

1. 选择下载的细胞词库，可以按住<kbd>Ctrl</kbd>来多选。
2. 转换的源设置为搜狗细胞词库，同时也可以发现这个工具支持非常多的词库格式
3. 输出选择Rime中州韵，随后会让你选择编码类型和运行的平台

![](http://www.findspace.name/wp-content/uploads/2016/08/shenlancikuzhuanhuan.png)
完成后点击转换等待即可。
转换完成会询问文件保存地址。
## 4. 给rime添加转换好的词库
以我的debian， fcitx-rime为例：
配置文件都在`~/.config/fcitx/rime`下，新建一个`luna_pinyin.extended.dict.yaml`文件：
```
---
name: luna_pinyin.extended
version: "2015.12.02"
sort: by_weight
use_preset_vocabulary: true
import_tables:
  - luna_pinyin
  - luna_pinyin.sogou
...
```
将你转换好的词库文件复制到这里，并重命名为`luna_pinyin.sogou.dict.yaml`，当然自己添加的词库也仿照这个格式，比如新添加了`luna_pinyin.test.dict.yaml`，则需要在`luna_pinyin.extended.dict.yaml`文件里`- luna_pinyin.sogou`后面添加类似的`- luna_pinyin.test`。

我的词库分享，包含的搜狗词库有：
```
成语俗语【官方推荐
计算机专业词库
古诗词名句【官方推荐】
日常用语大词库
计算机词汇
软件专业
计算机词汇大全【官方推荐】
诗词名句大全
计算机名词
网络流行新词【官方推荐】
```
[转换好的文件下载](https://pan.baidu.com/s/1hrWy9jm)


## 5. 设置rime的配置文件同步
修改`installation.yaml`文件，添加内容如下：
```
sync_dir: '/home/find/mega/RimeSync'
installation_id: 'lab_debian'
```
`sync_dir`表示配置文件同步的放置文件夹，可以设置为dropbox或者自己使用的文件同步工具的目录。
`installation_id`则是文件夹名字，可以用来区分自己不同机器上的同步配置。

重启rime。输入一些常用短语进行测试查看。
# Reference
[〔新手推荐敎程〕关于导入词库及「深蓝词库转换」的正确操作-百度贴吧](http://tieba.baidu.com/p/2757690418)
