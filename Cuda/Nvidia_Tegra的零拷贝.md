# Introduction
本文系翻译，原文地址：
[Zero Copy on Tegra K1](http://arrayfire.com/zero-copy-on-tegra-k1/)
之前写过的关于cuda 零拷贝的文章：
[CUDA零复制内存](http://www.findspace.name/easycoding/1448)
[CUDA锁页内存和零复制](http://www.findspace.name/easycoding/1349)
以下是NVIDIA论坛和Google Groups里关于tegra板子零拷贝的讨论，给出的观点也是零拷贝在unified memory上是没有发生copy行为的，强烈推荐看他们的讨论，可能需要翻墙。
[Jetson TK1 latency too high](https://devtalk.nvidia.com/default/topic/781450/jetson-tk1-latency-too-high/)
[Zero Copy vs Managed Memory in Tegra K/X](https://groups.google.com/forum/#!topic/arrayfire-users/2Cm0VrmG7wc)

零拷贝（Zero Copy）已经加入CUDA Toolkit很长时间了。但是很少有应用使用这个特性，因为随着GPU的发展，显存容量已经很大了。现有的使用零拷贝的应用主要是数据库处理的应用，因为它有及其严格的内存要求。
零拷贝是一个映射主机内存到GPU上，使得CUDA内核可以直接通过[PCIe](https://www.wikiwand.com/zh-cn/PCI_Express#/.E5.8F.83.E8.A6.8B)访问，而不用明确的内存传输。使用零拷贝后，读数据是从PCIe而不是从全局显存了，所以现在读取的速度是限制于PCIe的速度(最多16GB/s)而不是全局内存的速度(大约200GB/s)了。因此，零拷贝实际上对大多数应用没有真实的性能提升。
然而，随着Jetson TK1板子的面世，零拷贝变得非常有用。TK1板子有2GB的物理内存，ARM CPU和 NVIDIA GPU共享。如果执行一个`cudaMemcpy`命令，从Host拷贝到GPU，在TK1板子上，数据只不过是从内存的一个地方拷贝到了同一个内存的另外一个地方。在这种情况下，零拷贝就变得很必要了。
# 标准的CUDA Pipeline
```cpp
// Host Arrays
float* h_in  = new float[sizeIn];
float* h_out = new float[sizeOut];
 
//Process h_in
 
// Device arrays
float *d_out, *d_in;
 
// Allocate memory on the device
cudaMalloc((void **) &d_in,  sizeIn ));
cudaMalloc((void **) &d_out, sizeOut));
 
// Copy array contents of input from the host (CPU) to the device (GPU)
cudaMemcpy(d_in, h_in, sizeX * sizeY * sizeof(float), cudaMemcpyHostToDevice);
 
// Launch the GPU kernel
kernel<<<blocks, threads>>>(d_out, d_in);
 
// Copy result back
cudaMemcpy(h_out, d_out, sizeOut, cudaMemcpyDeviceToHost);
 
// Continue processing on host using h_out
```
# 零拷贝的CUDA pipeline
```cpp
// Set flag to enable zero copy access
cudaSetDeviceFlags(cudaDeviceMapHost);
 
// Host Arrays
float* h_in  = NULL;
float* h_out = NULL;
 
// Process h_in
 
// Allocate host memory using CUDA allocation calls
cudaHostAlloc((void **)&h_in,  sizeIn,  cudaHostAllocMapped);
cudaHostAlloc((void **)&h_out, sizeOut, cudaHostAllocMapped);
 
// Device arrays
float *d_out, *d_in;
// Get device pointer from host memory. No allocation or memcpy
cudaHostGetDevicePointer((void **)&d_in,  (void *) h_in , 0);
cudaHostGetDevicePointer((void **)&d_out, (void *) h_out, 0);
 
// Launch the GPU kernel
kernel<<<blocks, threads>>>(d_out, d_in);
 
// No need to copy d_out back
// Continue processing on host using h_out

```
在Tegra上用零拷贝看起来代码简单一点。kernel函数的代码不变。You can also allocate host memory using the cudaHostAlloc call without using zero copy access. This allows the fast pinned memory transfer when doing a cudaMemcpy.
# 实验结果
用零拷贝和标准的pipeline运行矩阵转置，矩阵大小4096×4096，实验结果如下：

|Pipeline|Bandwidth (GB/s)|Time (ms)|
|--------|----------------|---------|
|StandardPipeline|3.0|45|
|Zero Copy Pipeline|5.8|23|

设备到设备的拷贝速度在Tegra上大约是6.6GB/s。
实验结果表明，使用零拷贝应该比标准的pipeline更快。但是也并非一定要好，这需要测试过你的程序才能知道。然而，从上面代码的对比就可以看出，修改代码来用上零拷贝是很简单的，所以你可以修改代码来测试下是否对你的项目有提升效果。
# 应用
用过以上的测试结果，我们相信Tegra K1对流媒体程序是友好的。大多数流媒体应用都是图片或者信号处理算法，大概每秒30-60帧。在桌面PC级别，尽管这些kernel函数运行良好，符合时间要求，但是其中内存传输也占了很大一部分。
这就是为什么Tegra K1很棒。通过零拷贝，我们节省了100%的内存传输时间（从GPU到CPU等）。这就允许Tegre K1可以在性能限制下运行那些流媒体应用，即使它的计算能力和桌面GPU相比看起来很差。
当然，流媒体应用不是唯一适合用零拷贝的程序，还有很多。如果你测试后，零拷贝对你的程序产生了很棒的加速效果，请留言给我，我们很高兴可以收到你的回复。
[文中测试的代码下载](http://arrayfire.com/wp-content/uploads/2014/10/transpose.zip)