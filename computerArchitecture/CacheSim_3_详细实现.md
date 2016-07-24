# Introduction
[CacheSim 简单的Cache模拟器 专辑目录](http://www.findspace.name/easycoding/1717)
本文详细说明`load_trace`、`check_cache_hit`等函数。

# 1.程序入口
`main.cpp`主要针对一个测试文件，配置了不同cache line大小，不同组相联路数，不同的替换策略。默认使用写回法。默认cache大小32KB（0x8000 Bytes）。
在每次循环里，首先初始化cache的配置，然后设置替换策略，最后读入trace文件并开始模拟内存读写过程。
# 2.初始化
在类CacheSim的构造函数里，主要根据上一章一些变量的意义进行了初始化，主要还是注意shifts的两个变量，使用库函数`log2()`算出位数。
cache line实际是存储在caches变量中，`memset`填充0，则cache line的flag默认也是填充的0。
注意记得析构函数中对caches和cache_buf的释放。
# 3.load_trace加载trace文件函数
读取trace文件的指令，trace文件的格式在前面已经介绍过，有关trace的生成等[请看英文原文](http://cseweb.ucsd.edu/classes/fa07/cse240a/project1.html),trace文件是通过intel的 pin工具生成的虚拟地址。
通过`fgets`函数读文件到buf字符数组里，然后`sscanf`从buf里格式化读入一行指令，注意，原文件里是`s 0x1fffff50 1`的格式，最后一个参数我们先不考虑，因此只读入了指令类型（store or read）和内存地址。
通过判断`style`来决定是读还是写，每读取（执行）一条，我们的`tick_count`就++，同样用来记录是读写的计数器也相应++。
`rcount/wcount`是来统计读写指令的数量，而`cache_r_count/cache_w_count`是统计内存和cache进行数据读写的次数。在cache miss找到替换line时，如果找到的line中是脏数据，需要将其写回到内存中，此时就产生了数据通信，同样将数据从内存中加载到这个替换line中也有数据通信。
trace文件处理完成后，就是打印结果。目前主要关注的三个方面：

+ 指令计数
+ miss率/hit率
+ 读写数据通信

# 4.check_cache_hit检查是否cache命中
`do_cache_op`分析一条指令
```cpp
int CacheSim::check_cache_hit(_u32 set_base, _u32 addr) {
    /**循环查找当前set的所有way（line），通过tag匹配，查看当前地址是否在cache中*/
    _u32 i;
    for (i = 0; i < cache_mapping_ways; ++i) {
        if ((caches[set_base + i].flag & CACHE_FLAG_VAILD) && (caches[set_base + i].tag == ((addr >> (cache_set_shifts + cache_line_shifts))))) {
            return set_base + i;
        }
    }
    return -1;
}
```
传入参数是地址和读写的标记。对于load和store指令而言，只有两种情况：hit or miss。则中间主要涉及三个部分：检查是否命中、命中之后的操作、miss之后的操作。
```cpp
int CacheSim::check_cache_hit(_u32 set_base, _u32 addr) 
```
检查命中的传参有两个，一个是set的基址，一个是当前trace中的内存地址。
```cpp
set = (addr >>cache_line_shifts) % cache_set_size;
set_base = set * cache_mapping_ways;
```
根据第一章中我们对地址的区域划分，来获取当前地址映射到了cache的哪一个set中去了。而cache在模拟器中是作为一维数组对待的，所以还需要获取这个set的首地址。命中与否的判定则是根据tag的匹配情况，如果当前地址addr的tag和我们cache中映射set中的某一个line tag相同(`caches[set_base + i].tag == ((addr >> (cache_set_shifts + cache_line_shifts)))`)，且这个line是有效的(`caches[set_base + i].flag & CACHE_FLAG_VAILD`)，那么返回这个line的在这个一维数组cache的index。如果当前set中没有找到，说明这个addr中的数据并没有加载到cache上，返回-1。
# 5.命中之后的操作
```cpp
if (index >= 0) {
    cache_hit_count++;
    //只有在LRU的时候才更新时间戳，第一次设置时间戳是在被放入数据的时候。所以符合FIFO
    if (CACHE_SWAP_LRU == swap_style)
        caches[index].lru_count = tick_count;
    //直接默认配置为写回法，即要替换或者数据脏了的时候才写回。
    //命中了，如果是改数据，不直接写回，而是等下次，即没有命中，但是恰好匹配到了当前line的时候，这时的标记就起作用了，将数据写回内存
    if (!is_read)
        caches[index].flag |= CACHE_FLAG_DIRTY;
}
```
如果命中了，皆大欢喜，命中计数++，如果替换算法是LRU，则更新一下时间戳（有关时间戳的事情后面会详细说明）。
如果是一个写内存的操作，则需要把命中的这个cache line标志设置为脏，因为指令的意思是往这个内存地址写数据，而cache中原来有一份数据，由于我们的默认cache写的方式是写回法（写回法，write back，即写cache时不写入主存，而当cache数据被替换出去时才写回主存），因此这里只是先标记为脏数据，等该line要被替换出去的时候，再把该line中的数据写回到内存。


# Reference
[Getting physical address in pin tool](https://stackoverflow.com/questions/26114260/getting-physical-address-in-pin-tool)
[百度百科-写回法](http://baike.baidu.com/view/178536.htm)

















