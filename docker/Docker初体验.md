#Introduction
更多关于Docker内容请查看[Docker学习笔记目录](http://www.findspace.name/easycoding/1698)

简单记录下自己用docker的一些经历和经验。并不对docker进行介绍，可自行查阅下面的链接。
先发一些阅读的的东西。
推荐这个简短的小教程：
[Docker 从入门到实践](http://dockerpool.com/static/books/docker_practice/index.html)
这是官方的文档：
[Docker Documents](https://docs.docker.com/mac/)
后面的一些安装也是按照官方文档里说的进行。
[利用Docker构建开发环境](http://tech.uc.cn/?p=2726)

#安装
我的运行环境：
Debian 8 Jessie x86_64
[参考官方文档](https://docs.docker.com/linux/step_one/)
```bash
 wget -qO- https://get.docker.com/ | sh
```
系统会要求你输入sudo的密码，然后会安装docker和它的依赖包。
注意在安装完成的时候，终端里会有个提示，大概意思就是如果你想让普通用户也可以运行docker命令，需要把你的用户添加到docker group里，
```bash
sudo usermod -aG docker username
```
#获取镜像
先从[官网的hub](https://hub.docker.com/explore/)查看都有哪些镜像可以用
比如点到debian的详细信息，可以看到支持很多版本：8.3，8,7,等等。右边有提示命令：
```bash
docker pull debian
```
默认都是latest版本，想要下载指定版本可以通过在后面添加`:version`：
```bash
docker pull debian:stretch
```
等待下载完成即可
#管理镜像
```bash
docker images
```
在列出信息中，可以看到几个字段信息

+ 来自于哪个仓库，比如 ubuntu
+ 镜像的标记，比如 14.04
+ 它的 ID 号（唯一）
+ 创建时间
+ 镜像大小

其中镜像的 ID 唯一标识了镜像，注意到 ubuntu:14.04 和 ubuntu:trusty 具有相同的镜像 ID，说明它们实际上是同一镜像。

TAG 信息用来标记来自同一个仓库的不同镜像。例如 ubuntu 仓库中有多个镜像，通过 TAG 信息来区分发行版本，例如 10.04、12.04、12.10、13.04、14.04 等。
#进入镜像
下面的命令指定使用镜像debian:stretch来启动一个容器。
```bash
docker run -t -i debian:stretch /bin/bash
```
如果不指定具体的标记，则默认使用 latest 标记信息。`-t`是指明tag，`-i`是绑定tty到当前的命令行终端。不然的话，就直接运行一下就结束了，不能产生交互效果。
默认里面啥也没有，所以先apt update，然后装自己需要的就行。因为默认的镜像源很慢，所以跟修改普通的一个系统一样，修改sourcelist，再重新update。
# 进入容器
可以通过`docker ps -a`获取当前所有的容器，包括已经退出的。获得容器的ID之后，可以通过
```bash
docker start -i contain_id
```

# 后台运行

```bash
# docker run -d ubuntu /bin/sh -c "while true; do echo hello world; sleep 2; done"
ae60c4b642058fefcc61ada85a610914bed9f5df0e2aa147100eab85cea785dc
```
它将直接把启动的container挂起放在后台运行，并且会输出一个CONTAINER ID，通过docker ps可以看到这个容器的信息，可在container外面查看它的输出docker logs ae60c4b64205，也可以通过docker attach ae60c4b64205连接到这个正在运行的终端，此时在<kbd>Ctrl</kbd>+<kbd>C</kbd>退出container就消失了，按<kbd>Ctrl</kbd>+<kbd>P</kbd> 然后<kbd>Ctrl</kbd>+<kbd>Q</kbd>可以退出到宿主机，而保持container仍然在运行
另外，如果-d启动但后面的命令执行完就结束了，如`/bin/bash`、`echo test`，则container做完该做的时候依然会终止。而且`-d`不能与`--rm`同时使用
可以通过这种方式来运行memcached、apache等。



#卸载
官网文档给的很详细，
To uninstall the Docker package:
```bash
sudo apt-get purge docker-engine
```
To uninstall the Docker package and dependencies that are no longer needed:
```bash
sudo apt-get autoremove --purge docker-engine
```
The above commands will not remove images, containers, volumes, or user created configuration files on your host. If you wish to delete all images, containers, and volumes run the following command:
```bash
rm -rf /var/lib/docker
```
You must delete the user created configuration files manually.
# 与宿主机共享数据
[官网挂载宿主机文件夹到docker的说明](https://docs.docker.com/engine/userguide/dockervolumes/#mount-a-host-directory-as-a-data-volume)
简单解释：
```bash
docker run -v /Users/<path>:/<container path>
```
符合from to的参数规则，先写from路径，冒号，to路径，注意每个路径后面没有`/`，如果路径不存在，docker会自动创建。注意，这个参数最好写在前面，起码是在`-ti`参数前面。
默认挂载是rw模式，可读可写。

# 端口绑定
我们使用`-P`标记创建一个容器，将容器的内部端口随机映射到主机的高端口49000到49900。这时我们可以使用`docker ps`来看到端口5000绑定主机端口49155。
```bash
$ sudo docker ps nostalgic_morse
CONTAINER ID  IMAGE                   COMMAND       CREATED        STATUS        PORTS                    NAMES
bc533791f3f5  training/webapp:latest  python app.py 5 seconds ago  Up 2 seconds  0.0.0.0:49155->5000/tcp  nostalgic_morse
```
我们也可以使用`-p`标识来指定容器端口绑定到主机端口,`:`前是宿主机端口，后是容器端口
```bash
docker run -it -p 5000:5000 ubuntu /bin/bash
```
# 删除容器或者镜像
删除任何容器之前，要保证它已经停止了，通过`docker stop contain_id`，否则会提示你未停止，不能删除。
删除容器：
```bash
docker rm container_id
```
删除任何镜像之前，要保证没有任何容器是基于它构建的。
删除镜像：
```bash
docker rmi image_id_or_name
```


# Reference
[docker专题(2)：docker常用管理命令（上）](https://segmentfault.com/a/1190000000751601)
[连接容器](http://www.widuu.com/docker/userguide/dockerlinks.html)