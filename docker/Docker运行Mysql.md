# Introduction
更多关于Docker内容请查看[Docker学习笔记目录](http://www.findspace.name/easycoding/1698)
本章主要说明了如何在docker中的linux中启用mysql服务。
我的需求是在宿主机上写代码，适用mysql数据库，不想在宿主机上安装，把数据库直接放到docker里。
由于我的docker 拉取ubuntu:latest时16.04尚未发布，所以本文的基准镜像是ubuntu 14.04,其他linux版本理论上也适用。
```bash
REPOSITORY TAG   IMAGE ID      CREATED       SIZE
ubuntu    latest b72889fa879c  6 weeks ago   188 MB
```
通过 `docker run -it -p 3306:3306 ubuntu /bin/bash`进入容器，此处的命令含义可以在[Dcoker初体验](http://www.findspace.name/easycoding/1700)中了解。
# 安装配置mysql
在容器中的操作：
```bash
apt-get update && apt-get install mysql-server -y
```
配置mysql，使得可以远程登陆。
首先修改帮顶地址
```bash
vi /etc/mysql/my.cnf
```
将`bind-address = 127.0.0.1`改为
>bind-address = 0.0.0.0

保存后，从终端进入mysql:
```
service mysql start
mysql -u root -p -A
```
`-u`是指定用户，`-p`是密码登陆的意思，输入密码后登陆，(`-A`没太理解，但是后面从宿主机登陆的时候，好像没加出问题了，欢迎留言写下你的观点)。进入后通过`create database a_new_sql`来创建新的数据库，`use a_new_sql`来使用这个数据库。
进入数据库后，执行以下命令赋予权限：
```
grant all on * to 'root'@'%' identified by 'mysecret';
```
表示将当前数据库(`on *`代表的含义)的所有权限(`all`)赋予（`grant`）给用户root（`'root'`），允许它从任意地点登陆（`@'%'`），它的登陆密码是`mysecret`（identified by 'mysecret'）
`exit`退出mysql的命令行，回到容器的命令行。
在宿主机连接：
```
mysql -u root -p -h 127.0.0.1
```
输入密码后登陆成功。在mysql-workbench等可视化工具里也是一样的登陆方法。
# Reference
[Docker 安裝與操作](http://jamyy.us.to/blog/2014/06/6500.html)
[ linux下mysql的远程连接 ](http://blog.chinaunix.net/uid-28458801-id-3445261.html)