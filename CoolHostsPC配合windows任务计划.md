本文介绍了新发布的非常简陋的coolhostsPC版，并说明了发布
# 1. 下载CoolHosts PC版
[百度网盘分享地址](https://pan.baidu.com/s/1eS2W7qq)
或者从[github的release下载](https://github.com/FindHao/CoolHostsPC/releases)
# 2.运行测试
双击运行CoolHostsPC，查看hosts是否更新。
在CoolHostsPC所在的目录下，会生成一个timestamp的文件。
如果hosts文件已经成功更新，请继续步骤3，否则请检查hosts文件权限。
![](http://www.findspace.name/wp-content/uploads/2016/08/4.png)
# 3.添加任务计划

从开始菜单的windows附件中找到任务计划并打开，“创建基本任务”
名称描述随便写。触发器设置为每天一次。操作选择“启动程序”。
![](http://www.findspace.name/wp-content/uploads/2016/08/1.png)
![](http://www.findspace.name/wp-content/uploads/2016/08/2.png)
![](http://www.findspace.name/wp-content/uploads/2016/08/3.png)
![](http://www.findspace.name/wp-content/uploads/2016/08/5.png)
通过“浏览”按钮，找到coolhostspc的位置(添加任务以后，如果将coolhostspc位置移动，任务将失效)。完成基本任务计划的添加
![](http://www.findspace.name/wp-content/uploads/2016/08/6.png)

# 4. 设置为每天每小时运行一次
注意，由于FindSpace博客源防火墙设置问题，不建议更新频率高于1小时/次。
在任务计划中选择自己添加的任务，右键属性打开属性设置，找到“触发器”标签页，选中设置的触发器，“编辑”，在高级设置部分，勾选任务间隔，并设置为1小时，持续时间1天。确定后生效。
![](http://www.findspace.name/wp-content/uploads/2016/08/7.png)
![](http://www.findspace.name/wp-content/uploads/2016/08/8.png)

