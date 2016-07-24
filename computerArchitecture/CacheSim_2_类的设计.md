# Introduction
[CacheSim 简单的Cache模拟器 专辑目录](http://www.findspace.name/easycoding/1717)
本文说明了模拟的Cache需要完成的功能，以及大体框架设计，通过头文件内容的说明，使读者获得整体的认识。
# 1.模拟Cache需要完成的功能
## 1.1 指令读取
由于主存访问trace以文件形式给出，
[trace文件下载](https://pan.baidu.com/s/1ge9yCcN)
所以需要从文件中读取访存trace。其中trace的格式如下：
```
s 0x1fffff50 1
```
每行的第一个字符是表示该条指令的类型，s为写（store），r为读（read）。中间的十六进制数为内存地址，最后一个整数是指存储器访问指令之间的间隔指令数。例如第5条指令和第10条指令为存储器访问指令，且中间没有其他存储器访问指令，则间隔指令数为4。
我们写的模拟器中暂不考虑最后这个参数的使用。
## 1.2 对一条指令进行分析
在从trace文件中读取一条指令之后，对指令进行分析，对地址进行变换，查看地址里的数据是否在cache中，获得命中与否的结果。
## 1.3 检查是否命中
通过对地址进行操作，检查是否命中
## 1.4 查找空闲line
如果命中，则只需要更新时间戳，如果是写操作，则标记脏位。
如果之前没有命中，则需要从当前Cache中找到可以使用的块，优先当前是invaild状态的line，否则执行替换算法，找到需要被替换的块，同时如果要替换的块中原来的数据是脏的，写回到内存中。
## 1.5 将数据加载到cache line中
如果miss，通过1.4找到可以放置数据的cache line，下一步则就是将数据从主存中加载到cache line上。
# 2.头文件编写
代码地址
[Github](https://github.com/FindHao/CacheSim/blob/master/CacheSim.h)
替换算法实现了FIFO（先进先出）、LRU（最近最常访问）、RAND（随机替换）算法。
## 2.1 Cache_Line类
Cache_Line单独做一个类，tag采用无符号32位，
```cpp
class Cache_Line {
public:
    _u32 tag;
    /**计数，FIFO里记录最一开始的访问时间，LRU里记录上一次访问的时间*/
    union {
        _u32 count;
        _u32 lru_count;
        _u32 fifo_count;
    };
    _u8 flag;
    _u8 *buf;
};
```
count用来记录访问的时间，采用union数据结构，后面在更新count的时候，只需要针对不同的替换算法，进行特殊赋值即可，而且不会导致一直使用count出现的思路混乱。这个值的变化后面讲替换算法重点说明。
buf本意是存储内存里的数据，参看上一章中结构说明的bank。
**flag**则是一些标记位的存储：

|位数|7~3|2|1|0|
|-|-|-|-|
|作用|保留|Locking位|脏位|有效位|

在上一章中的地址划分小节，我们了解到内存地址的划分，但是要做主存和cache的映射，就需要有一个映射关系。如上一章的图所示，Cache中只需要保存好tag即可，cache的组织结构隐含了和主存的划分关系，而flag标志位则保存了一些cache line的状态。

## 2.2 Cache_Sim类
### 2.2.1 变量

cache\_\*\*\_shifts是指某个元素大小的对数，以2为底。比如`cache_line_shifts`，假设一个cache line是32bytes，则在内存地址中，最后5位(2^5=32)是表示的块内地址（此处划分参见第一章）。

需要记录的元素有：

|变量|意义|
|-|-|
|cache_size|cache总大小，单位byte|
|cache_line_size|一个cache line的大小，单位byte|
|cache_line_num|cache一共有多少个line|
|cache_mapping_ways|几路组相联|
|cache_set_size|整个cache有多少组(set)|
|cache_set_shifts|在内存地址划分中占的位数，log2(cache_set_size)|
|cache_line_shifts|在内存地址划分中占的位数，log2(cache_line_num)|
|caches|真正的cache数组|
|tick_count|指令计数器|
|cache_buf|cache bank，存内存数据的地方|
|cache_free_num|当前空闲cache line的数量|
|cache_r_count, cache_w_count|cache读写内存总数据量|
|cache_hit_count, cache_miss_count|hit和miss计数|
|swap_style|替换算法|

这些变量在CacheSim类的构造函数里都进行了初始化。对于指针类型的通过malloc进行了内存分配。
### 2.2.2 函数
对应着 1.提出的应完成的功能，定义的函数如下

|函数|功能|
|-|-|
|`check_cache_hit`|检查是否命中|
|`get_cache_free_line`|获得当前空闲的cache line，如果没有空闲的则执行替换算法，返回一个可以替换的块|
|`set_cache_line`|将内存中的数据加载到通过`get_cache_free_line`获得的cache line上去|
|`do_cache_op`|对一条指令进行分析|
|`load_trace`|加载trace文件，开始进行分析|
|`set_swap_style`|设置本次的缓存替换算法|

## 2.3 常量
```cpp
const unsigned char CACHE_FLAG_VAILD = 0x01;
const unsigned char CACHE_FLAG_DIRTY = 0x02;
const unsigned char CACHE_FLAG_LOCK = 0x04;
const unsigned char CACHE_FLAG_MASK = 0xff;
```
对应本文2.1中的表格，lock位是留着后面做cache locking用的。后续会修改项目实现这个功能。`CACHE_FLAG_MASK`主要是在写入cache line时进行初始化flag用到。其他则是通过与cache line中的flag进行`&`操作，判断有效性或者脏位与否。












# Reference
[C、C++中union用法总结](http://www.jellythink.com/archives/468)
[左移和右移运算符（>> 和 <<）](https://msdn.microsoft.com/zh-cn/library/336xbhcz.aspx)