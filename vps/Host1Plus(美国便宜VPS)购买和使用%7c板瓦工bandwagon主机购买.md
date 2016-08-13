# Introducation 
本文原来介绍的是偶Hosts1Plus这个美国便宜的vps，使用半年以后，发现该款vps实际上是非常差的，而且算起来性价比并不高，速度也不快。现在改为有名的搬瓦工（bandwagon）vps的介绍说明。

# 优势说明
现在搬瓦工最便宜的也是19.99$一年了。合租的话性价比非常高。
```
Self-managed service
SSD: 10 GB
RAM: 512 MB
CPU: 1x Intel Xeon
BW: 1000 GB/mo
Link speed: 1 Gigabit

VPS technology: OpenVZ/KiwiVM
Linux OS: 32-bit and 64-bit Centos, Debian, Ubuntu, Fedora
Instant OS reload
1 Dedicated IPv4 address
Full root access
PPP and VPN support (tun/tap)
Instant RDNS update from control panel
No contract, anytime cancellation
Strictly self-managed, no support
```
通常有优惠码[adsense]，可以在付款的时候找优惠码，现在最大的优惠额度就是4.8%了，聊胜于无。
2-4人合租最合适，流量每月1000GB足够了。而且支持IPV6,如果教育网的话，可以搭ss翻墙，支持ipv6,速度非常快，看youtube 1080p的视频也不卡。
现在上面那款已经售磬，有一款性能稍微低一点的可以买。ram 256, BW 500GB，也足够了。
# 注册链接
##[搬瓦工注册购买链接，建议通过该链接注册，有时会自动加优惠码](https://bandwagonhost.com/aff.php?aff=4005)
#支持支付宝付款
# 技术支持
它的后台控制也很强大，而且如果设置系统是centos6,还可以一键安装vpn或者shadowsocks。

# 购买
![bandwagon][13]
通过上面的注册链接注册登录以后，点击`client area-->services -->order new services` 一般最上面那个就是默认推荐的配置，看下配置以及右边的价格，$19.99 USD Annually，年付19.99刀。
`out of stack`表示售磬。。
选中一款购买，购买流程很简单，不在赘述。
# 管理
购买完成后，在`Services-->My Services`中看到买的vps。点击`KiwiVM Control Panel`进入管理的控制台。
![](http://www.findspace.name/wp-content/uploads/2016/07/bandwagon_control_panel.png)
如图注释所示，其中一键搭起ss，需要vps设置的系统是centOS6才行。只需要在`install new os`里选择cent os 6即可。
## 安全问题
root密码建议不要动，就用随机生成的这个即可。同时，先在本地通过命令行ssh登录vps，进行一些安全防护。[服务器、VPS等安全防护教程](http://www.findspace.name/easycoding/1714)

# 登录
windows下载[Putty(点击即可下载)][8]
然后解压出来，打开
![][9]
在输入框填VPS的IP地址（如图），修改vps分配的ssh端口，搬瓦工默认ssh端口不是22。别的不用动，然后点击Open
弹出一个对话框，如果在自己的电脑上，选择yes，如果选择no，每次都会弹出这个提示。
![][10]
然后提示你log in:输入root，然后回车，输入密码（这里可以直接在网页里复制，单击鼠标右键自动粘贴，这里输入密码是不显示的，\*nix的风格）
登录后是这样的：
![][11]
输入
```
passwd
```
输入两次新的密码来修改用户密码，建议不要修改，因为默认的密码强度很高，而且一般不需要登录后台管理。

#配置Shadowsocks
见博客：
##[SHADOWSOCKS科学上网][12]

[8]: http://www.findspace.name/wp-content/uploads/2015/07/putty.zip
[9]: http://www.findspace.name/wp-content/uploads/2015/07/putty.png
[10]: http://www.findspace.name/wp-content/uploads/2015/07/puttynotify.png
[11]: http://www.findspace.name/wp-content/uploads/2015/07/puttylog.jpg
[12]: http://www.findspace.name/res/956 
[13]: http://www.findspace.name/wp-content/uploads/2016/03/bandwagon.jpg