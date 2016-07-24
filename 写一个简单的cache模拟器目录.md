# Introduction
项目地址：
[CacheSim- Github](https://github.com/FindHao/CacheSim)<!-- Place this tag where you want the button to render. -->
<a class="github-button" href="https://github.com/FindHao/CacheSim" data-style="mega" data-count-href="/FindHao/CacheSim/stargazers" data-count-api="/repos/FindHao/CacheSim#stargazers_count" data-count-aria-label="# stargazers on GitHub" aria-label="Star FindHao/CacheSim on GitHub">Star</a>
这个专辑记录了写一个非常简单的cache模拟器的过程。
主要是因为我要做的研究里，使用gem5来跑程序太慢了，所以只能自己在项目里实现一个简易的cache模拟器，获得一些实验数据。
正好加州大学圣迭戈分校（University of California, San Diego，简称为UCSD，又常译为加州大学圣地亚哥分校）有一门[Principles of Computer Archictecture](http://cseweb.ucsd.edu/classes/fa07/cse240a/)的课程，其中有个cache 模拟器的实验，本专辑最一开始参考的代码即是[某位同学的代码](http://bbs.pediy.com/showthread.php?t=160003)，并在他的基础上，进行了错误纠正和优化完善。
本项目的整个代码以及文章编写都在B站上进行了直播，地址：
[FindHao的直播间](http://live.bilibili.com/493492)

本专辑正在编写，大概7.24完成。行文仓促，如有纰漏，恳请指正。

# [1. Cache背景知识简介](http://www.findspace.name/easycoding/1719)
本文简单介绍了Cache的相关背景知识，包括cache的来源以及结构，说明了组相联的地址映射方式和地址划分。并解释了模拟器中地址字段位数的设定。
# [2. Cache模拟器类的设计](http://www.findspace.name/easycoding/1720)
本文说明了模拟Cache需要完成的功能，以及大体框架设计。介绍了头文件的内容
# [3. Cache模拟器详细实现](http://www.findspace.name/easycoding/1721)
主要介绍了读入trace文件、分析指令、构造函数、检查命中函数等
# [4. Cache模拟器详细实现2](http://www.findspace.name/easycoding/1722)
主要说明了cache miss以后的操作，包括`set_cache_line`和`get_cache_free_line`函数。
# 5. 实验数据总结
本文通过python脚本对实验数据进行了统计，以图表形式进行了说明。

