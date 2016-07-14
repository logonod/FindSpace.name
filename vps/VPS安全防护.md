# Introduction
在[从搬瓦工（Bandwagon）购买VPS](http://www.findspace.name/res/1417)之后，自信的在vps里使用了debian搭建ss等等。
过了一个多月，就收到搬瓦工邮件，说
>We have detected a large number of outgoing SMTP connections originating from this server. This usually means that the server is sending out spam.
>我们检测到你的服务器上有大量SMTP连接，换句话说，你的服务器在发送垃圾邮件。

就是说vps被黑了。被人用来滥发邮件。
经过一番努力之后，终于在两三个月里，被黑了三次。达到了他们的容忍上限（每个用户的每个vps有三次机会，超过三次就会被停止使用），发tickt也不给面子，就是不让用了。只能新买服务。。
于是新买了个vps，这里记录下使用vps需要注意的安全问题。
# 1.认识问题严重性
先在你机器（包括你平时用的linux的机器）上跑几个命令看看吧。
## 1.1查看尝试暴力破解机器密码的人
```bash
sudo grep "Failed password for root" /var/log/auth.log | awk '{print $11}' | sort | uniq -c | sort -nr | more
```
害怕吗？
## 1.2查看暴力猜用户名的人
```bash
sudo grep "Failed password for invalid user" /var/log/auth.log | awk '{print $13}' | sort | uniq -c | sort -nr | more
```
在我实验室的机器上，看到的结果如下：

|次数|ip|
|-|-|
|207|202.201.34.104|
|108|114.80.253.81|
|8|222.186.58.244|
|8|222.186.56.120|
|3|198.55.103.144|
|2|一堆ip|
|1|一堆ip|

**感受到寒意了吗？**

# 2应对策略
## 2.1 SSH相关
### 2.1.1 不使用默认的22端口
ssh登陆默认的端口是22，而搬瓦工一般都默认是一个比较大的随机端口，**不要为了方便，改回22**。
修改`/etc/ssh/ssdh_config`文件，将其中的`Port 22`改为随意的端口比如`Port 47832`，port的取值范围是 0 - 65535(即2的16次方)，0到1024是众所周知的端口（知名端口，常用于系统服务等，例如http服务的端口号是80)。
### 2.1.2 不要使用简单密码
搬瓦工默认生成的root密码是随机的，但是不要改成你自己的密码，你可以将密码记在手机上，但是一定不要改成你自己的有规律的密码。
### 2.1.3 禁止使用密码登陆，使用RSA私钥登陆
**这条是最重要最有效的。**
跟之前写的[debian ssh 连接android 通过termux](http://www.findspace.name/easycoding/1652)里登陆部分是一样的。rsa的原理也不再赘述，[wiki上的条目](https://zh.wikipedia.org/wiki/RSA%E5%8A%A0%E5%AF%86%E6%BC%94%E7%AE%97%E6%B3%95)描述的很清楚。
先通过`ssh-keygen -t rsa`生成你的客户端的密钥，包括一个私钥和公钥，用`scp id_rsa.pub root@XX.XX.XX.XX:~/`把公钥拷贝到服务器上，注意，生成私钥的时候，文件名是可以自定义的，且可以再加一层密码，所以建议文件名取自己能识别出哪台机器的名字。然后在服务器上，你的用户目录下，新建`.ssh`文件夹，并将该文件夹的权限设为600，`chmod 600 .ssh`，并新建一个`authorized_keys`，这是默认允许的key存储的文件。如果已经存在，则只需要将上传的`id_rsa.pub`文件内容追加进去即可：`cat id_rsa.pub >> authorized_keys`，如果不存在则新建并改权限为400即可。
然后编辑ssh的配置文件：
```bash
# vi /etc/ssh/sshd_config
RSAAuthentication yes #RSA认证
PubkeyAuthentication yes #开启公钥验证
AuthorizedKeysFile .ssh/authorized_keys #验证文件路径
PasswordAuthentication no #禁止密码认证
PermitEmptyPasswords no #禁止空密码
# 最后保存，重启
# /etc/init.d/sshd restart
```
### 2.1.4 禁止root用户登录
你可以新建一个用户来管理，而非直接使用root用户，防止密码被破解。
还是修改`/etc/ssh/sshd_config`
```bash
PermitRootLogin no
```
##2.2 使用denyhosts
>Denyhosts是一个Linux系统下阻止暴力破解SSH密码的软件，它的原理与DDoS Deflate类似，可以自动拒绝过多次数尝试SSH登录的IP地址，防止互联网上某些机器常年破解密码的行为，也可以防止黑客对SSH密码进行穷举。

建议不要从软件仓库里安装，而是从[denyhosts的github地址](https://github.com/denyhosts/denyhosts)下载，解压，安装。
### 2.2.1 配置文件
复制默认的配置文件到/etc下
```bash
cp denyhosts.conf /etc
sudo vim /etc/denyhosts.conf
```
配置文件里基本不用配置，可以查看下都有什么选项。
### 2.2.2 守护程序

```bash
cp daemon-control-dist daemon-control
sudo vim ./daemon-control
sudo chown root daemon-control
sudo chmod 700 daemon-control
```
主要修改执行文件所在的文件夹，改成你下载denyhosts的所在目录+denyhosts.py
```bash
DENYHOSTS_BIN   = "/usr/bin/denyhosts.py"
```
同时修改这个守护程序的所属用户和权限。
### 2.2.3 运行
添加开机自动运行，编辑`/etc/rc.local`，在最后的exit之前添加
```
# 你的daemon-control在哪里就改为哪里
/usr/share/denyhosts/daemon-control start
```
在当前目录下通过`daemon-control start`来运行。
## 2.3 终极杀手锏 iptables
>iptables，一个运行在用户空间的应用软件，通过控制Linux内核netfilter模块，来管理网络数据包的流动与转送。在大部分的Linux系统上面，iptables是使用/usr/sbin/iptables来操作，文件则放置在手册页（Man page[2]）底下，可以通过 man iptables 指令获取。通常iptables都需要内核层级的模块来配合运作，Xtables是主要在内核层级里面iptables API运作功能的模块。因相关动作上的需要，iptables的操作需要用到超级用户的权限。

```
# 清除已有iptables规则
iptables -F
# 允许本地回环接口(即运行本机访问本机)
iptables -A INPUT -i lo -j ACCEPT
# 允许已建立的或相关连的通行
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
#允许所有本机向外的访问
iptables -A OUTPUT -j ACCEPT
# 允许访问22端口，以下几条相同，分别是22,80,443端口的访问
iptables -A INPUT -p tcp --dport 22 -j ACCEPT
iptables -A INPUT -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -p tcp --dport 443 -j ACCEPT
#如果有其他端口的话，规则也类似，稍微修改上述语句就行
#允许ping
iptables -A INPUT -p icmp -m icmp --icmp-type 8 -j ACCEPT
#禁止其他未允许的规则访问（注意：如果22端口未加入允许规则，SSH链接会直接断开。）
iptables -A INPUT -j REJECT 
iptables -A FORWARD -j REJECT
```
开机启动项的设置可以参考Reference里关于iptables的条目

# 总结
安全问题实在太重要，即使设置了以上防护，也有可能被攻克，重要的是提高安全意识。
等我遇到了新的问题，再更新文章。。
# Reference
[VPS 防止 SSH 暴力登录尝试攻击](http://www.lovelucy.info/vps-anti-ssh-login-attempts-attack.html)
[linux默认端口范围是多少](http://bbs.csdn.net/topics/340204446)
[10 分钟服务器安全设置，Ubuntu安全设置入门](http://blog.jobbole.com/103344/)
[Linux下的iptables详解及配置](http://tanxin.blog.51cto.com/6114226/1261245)
[Linux上iptables防火墙的基本应用教程](http://www.vpser.net/security/linux-iptables.html)
[iptables wiki](https://zh.wikipedia.org/wiki/Iptables)
[iptables archwiki](https://wiki.archlinux.org/index.php/Iptables_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))