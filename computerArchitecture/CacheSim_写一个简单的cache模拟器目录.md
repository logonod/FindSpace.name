# Introduction
这个专辑记录了写一个简单的cache模拟器的过程。
项目地址：
[CacheSim- Github](https://github.com/indHao/CacheSim)
这个项目是加州大学圣迭戈分校（University of California, San Diego，简称为UCSD，又常译为加州大学圣地亚哥分校）的[计算机体系结构原理这门课程的其中一个实验](http://cseweb.ucsd.edu/classes/fa07/cse240a/project1.html)，感兴趣的话，可以在github上搜课程号cse240a可以看到其他同学的作业代码。
由于我做的项目的原因，需要手写一个简单的cache模拟器，因此我在[某位同学代码的基础]((http://bbs.pediy.com/showthread.php?t=160003))上，进行了纠错和完善，主要是将代码面向对象化，可以方便的嵌入自己的项目中。
本项目的编写在[B站上进行了全程直播](http://live.bilibili.com/493492)，包括阅读代码，大概三天的时间，代码也不长。下面的内容是对整个项目的总结。
# 1. Cache背景知识简介
本文简单介绍了Cache的相关背景知识，包括cache的来源以及结构，说明了组相联的地址映射方式和地址划分。并解释了模拟器中地址字段位数的设定。
# 2. Cache模拟器类的设计
本文说明了模拟Cache需要完成的功能，以及大体框架设计。介绍了头文件的内容
# 3. Cache模拟器详细实现
主要介绍了读入trace文件、分析指令、构造函数、检查命中函数。
# 3. Cache模拟器详细实现2
主要说明了获得可用cache line函数。
# 4. 实验数据
本文通过python脚本对实验数据进行了统计，以图表形式进行了说明。

