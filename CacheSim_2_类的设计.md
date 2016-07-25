# Introduction
本文说明了模拟的Cache需要完成的功能，以及大体框架设计，通过头文件内容的说明，使读者获得整体的认识。
# 1.模拟Cache需要完成的功能
## 1.1 指令读取
由于主存访问trace以文件形式给出，所以需要从文件中读取访存trace。其中trace的格式如下：
```
s 0x1fffff50 1
```
每行的第一个字符是表示该条指令的类型，s为写，r为读。中间的十六进制数为内存地址，最后一个整数是指存储器访问指令之间的间隔指令数。例如第5条指令和第10条指令为存储器访问指令，且中间没有其他存储器访问指令，则间隔指令数为4。
## 1.2 对一条指令进行分析
在从trace文件中读取一条指令之后，对指令进行分析，对地址进行变换，查看地址里的数据是否在cache中，获得命中与否的结果。
## 1.3 检查是否命中
通过对地址进行操作，检查是否命中
## 1.4 查找空闲line
如果命中，则只需要更新时间戳，如果是写操作，则标记脏位。
如果之前没有命中，则需要从当前Cache中找到可以使用的块，优先当前是invaild状态的line，否则执行替换算法，找到需要被替换的块，同时如果要替换的块中原来的数据是脏的，写回到内存中。
## 1.5 将数据加载到cache line中
如果miss，通过1.4找到可以放置数据的cache line，下一步则就是将数据从主存中加载到cache line上。
# 2 头文件编写
代码地址
[Github](https://github.com/FindHao/CacheSim/blob/master/CacheSim.h)
替换算法实现了FIFO（先进先出）、LRU（最近最常访问）、RAND（随机替换）算法。
## 2.1 Cache_Line类
Cache_Line单独做一个类，tag采用无符号32位，一个巧妙的地方在count的实现上，
```cpp
 union {
        _u32 count;
        _u32 lru_count;
        _u32 fifo_count;
    };
```
采用union数据结构，后面在更新count的时候，只需要针对不同的替换算法，进行特殊赋值即可，而且不会导致一直使用count出现的思路混乱。
buf本意是存储内存里的数据，参看上一章中结构说明的bank。
## 2.2 Cache_Sim类
命名规则说明：
cache\_\*\*\_shifts是指某个元素大小的对数，以2为底。比如`cache_line_shifts`，一个cache line是32bytes，则在内存地址中，最后5位(2^5=32)是表示的块内地址（此处划分参见第一章）。
着重注意的是`cache_tag_mask`变量，其赋值方式为：
```cpp
// 获取用来提取tag位的mask，比如0b1011 & 0b1100(0xB) = 0b(1000)
this->cache_tag_mask =0xffffffff << (this->cache_set_shifts + this->cache_line_shifts + this->cache_mapping_ways_shifts);
```
通过`0xffffffff`左移补0,移的位数与地址的划分设计有关，在使用的时候，
```cpp
if ((tag & CACHE_FLAG_VAILD) && ((tag & this->cache_tag_mask) == (addr & this->cache_tag_mask))) {
// 还有更新这个line的tag位时
line->tag = addr & ~CACHE_FLAG_MASK;
```
一开始都是通过`memset`初始化为0了，所以最开始加载数据（强制缺失）的时候，基本都是设置cache line，`addr & ~CACHE_FLAG_MASK`则就是



















# Reference
[C、C++中union用法总结](http://www.jellythink.com/archives/468)
[左移和右移运算符（>> 和 <<）](https://msdn.microsoft.com/zh-cn/library/336xbhcz.aspx)