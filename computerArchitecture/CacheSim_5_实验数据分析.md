# Introduction
[CacheSim 简单的Cache模拟器 专辑目录](http://www.findspace.name/easycoding/1717)
本文统计了组相联路数、cache_line_size,cache_size以及替换策略对miss率和读写通信数据量的影响，并以图表的形式展现出来。
# 1. 组相联路数的影响
不变条件：cache_line_size 32bytes，LRU，Cache_size 32KB

|组相联路数|miss率 |
|-|-|
|1|1.735562|
|2|1.338225|
|4|1.305841|
|8|1.289746|
|12|1.282765|
|16|1.283347|
|32|1.279468|

不用怀疑12,我的PC就是12路组相联。。
![](http://www.findspace.name/wp-content/uploads/2016/07/mapping_ways.png)
在1路组相联（直接映射）时，miss率最高，随着路数的增加，miss率稳定在一个数值。
读写通信量也符合上图的走势。

# 2. cache_line_size的影响
不变条件：cache_size 32KB，LRU，mapping_ways 8

|cache_line_size(Byte)|miss率|
|--|-------|
|8|4.238069|
|16|2.309364|
|32|1.289746|
|64|0.765974|
|128|0.491969|
|256|0.369801|
|512|0.351379|

随着cache_line_size的增大，miss率逐渐降低，降低的幅度逐渐减小，最后稳定在一个数值
![](http://www.findspace.name/wp-content/uploads/2016/07/cache_line_size.png)
读写通信量（单位Byte）
![](http://www.findspace.name/wp-content/uploads/2016/07/cache_line_size_RWtraffice.png)
这个读写通信量的计算是`cache_r_count*cache_line_size`，只有在miss的时候，才会发生读写通信，其中读写的次数尽管随着miss率的下降而减少，但是miss率下降得越来越少，而cache_line_size在上图中却一直是两倍增长，因此造成了随着cache_line_size增加，反而读写通信量在上涨的趋势。
为什么写的通信量比读的大很多？
读的计次仅在miss之后，找到替换块，并**将数据从内存加载到cache**中时发生，而写比读多的地方在于，miss之后，找替换块的时候，**如果替换块是脏数据，也要将其写入回内存**。

# 3. 替换策略的影响
miss率比较：

|cache大小(KB)|FIFO|LRU|RAND|
|-|-|-|-|
|2|4.142467|3.415276|4.298765|
|4|2.490871|2.025081|2.684983|
|8|1.844156|1.588185|1.955853|
|16|1.535827|1.396207|1.609710|
|32|1.365762|1.289746|1.409393|
|64|1.270354|1.230019|1.284898|

![](http://www.findspace.name/wp-content/uploads/2016/07/swap2.png)
查阅到的一个实验报告说

>在 Cache 容量较小的情况下，随机(RANDOM)策略相对较好，而随着 Cache 容量的增加，最近最少使用(LRU)和先进先出(FIFO)策略的效果较好。所以一般的计算机较多的采用 LRU 替换策略。

然而在我们的cachesim里，并没有出现这样的情况，使用别的测试数据时，出现过一两个点rand比FIFO低的情况，但应该跟rand的数有关，总体上，miss率LRU<FIFO<RAND

# 4. Cache大小的影响
不变条件： cache_line_size 32bytes，LRU，mapping_ways 8
![](http://www.findspace.name/wp-content/uploads/2016/07/cache_size.png)
cache越大，能加载的数据越多，能在cache中找到数据的可能性越大。
![](http://www.findspace.name/wp-content/uploads/2016/07/cache_size_RWtraffic.png)

# 5. 实验总结
cache的miss率可以通过增大cache_line_size、增加组相联路数、使用较优的替换策略、增大cache大小来降低，读写通信量也是如此，除了增大cache_line_size造成的通信量增加以外。
实际数据还跟测试的序列有关。本文采用的是`gcc.trace`。
