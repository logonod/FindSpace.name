# Introduction
[CacheSim 简单的Cache模拟器 专辑目录](http://www.findspace.name/easycoding/1717)
本文主要介绍cache miss以后的操作。
接上一章的cache hit以后的操作，如果miss了
```cpp
index = get_cache_free_line(set_base);
set_cache_line((_u32)index, addr);
if (is_read) {
    cache_r_count++;
} else {
    cache_w_count++;
}
cache_miss_count++;
```
先获得可用的line（可能是当前set恰好不满，有空闲，也可能是通过替换算法获得可以替换的line），然后将addr内存地址中的数据写入到cache line里，相应的读写通信次数也要++，`cache_r_count`已经在第3章中的`load_trace`函数部分介绍过了。
# get_cache_free_line获得当前可用的line函数
`_u32 CacheSim::get_cache_free_line(_u32 set_base) `当然是从当前映射的set里找可用的line，传入在“一维数组”的cache里映射set的index。
```cpp
for (i = 0; i < cache_mapping_ways; ++i) {
    if (!(caches[set_base + i].flag & CACHE_FLAG_VAILD)) {
        if (cache_free_num > 0)
            cache_free_num--;
        return set_base + i;
    }
}
```
循环当前set一遍，判断标志位中的有效位，如果有无效的cache line，那么就意味这个line是“空闲”的。直接返回它的index即可。
循环完都没有找到，说名当前的cache set是满的，那么执行替换算法：
```cpp
free_index = 0;
if(swap_style == CACHE_SWAP_RAND){
    free_index = rand() % cache_mapping_ways;
}else{
    min_count = caches[set_base].count;
    for (j = 1; j < cache_mapping_ways; ++j) {
        if(caches[set_base + j].count < min_count ){
            min_count = caches[set_base + j].count;
            free_index = j;
        }
    }
}
```
随机替换算法的种子在`CacheSim`的构造函数里进行过初始化了。
对于FIFO(First in, first out)先进先出替换算法，其cache line的时间戳记录的是这个line被填充数据时的时间，即进入“使用队列”时的时间，因此FIFO是找到最先进入这个队列的line，将其替换出去。
LRU(Least Recently Used)最近最少使用替换算法
![](http://my.csdn.net/uploads/201205/24/1337859321_3597.png)
则当line被访问的时候，就需要更新其时间戳，因此在`do_cache_op`函数里，当cache 命中时，
```cpp
if (CACHE_SWAP_LRU == swap_style)
    caches[index].lru_count = tick_count;
```
需要更新其时间戳。
`min_count`在FIFO中获得是最早进入队列的line，而在LRU中，则是这个队列中已经很长时间没有访问的line。
如果通过替换算法获得的这个line中原有的数据是脏数据，那么标记其脏位
```cpp
if (caches[free_index].flag & CACHE_FLAG_DIRTY) {
    caches[free_index].flag &= ~CACHE_FLAG_DIRTY;
    cache_w_count++;
}
```
# set_cache_line将cache line中的数据写回到内存函数
```cpp
void CacheSim::set_cache_line(_u32 index, _u32 addr) {
    Cache_Line *line = caches + index;
    // 这里每个line的buf和整个cache类的buf是重复的而且并没有填充内容。
    line->buf = cache_buf + cache_line_size * index;
    // 更新这个line的tag位
    line->tag = addr >> (cache_set_shifts + cache_line_shifts );
    line->flag = (_u8)~CACHE_FLAG_MASK;
    line->flag |= CACHE_FLAG_VAILD;
    line->count = tick_count;
}
```
传入的是“一维数组”cache中要写回的line的index以及要写入line的内存地址。
这里的buf并没有什么作用，只是表示将内存里的数据写入了当前这个line，而在我们的cache模拟器里，并没有真正的数据流动。
更新tag则根据第一章中地址划分的内容，对addr进行移位操作即可。
flag先清空，`CACHE_FLAG_MASK = 0xff`则`~CACHE_FLAG_MASK = 0x00`，然后将最后一位有效位置为有效，并更新时间戳。





















