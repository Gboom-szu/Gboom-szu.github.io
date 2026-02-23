---
title: 初识CUDA编程
date: 2023-03-13 15:16:05
tags: CUDA
category: CUDA入门
---

这个系列是CUDA编程：基础与实践的学习笔记。这是第一篇总结文章，对应书本的第2到4章，包括CUDA程序的基本结构、线程组织和错误检查三方面的内容。

<!--more-->

## hello word

```c++
#include <stdio.h>


__device__ void help(){
    int tid = blockIdx.x * blockDim.x + threadIdx.x;
    printf("hello from gpu in %d thread\n", tid);
}

__global__ void hello(){
    help();
}


int main(){
    
    hello<<<2,2>>>();
    cudaDeviceSynchronize();
    return 0;
}
```

cuda源文件后缀一般为**.cu**，通过`nvcc helloword.cu -o helloword`编译得到可执行文件，执行的结果为：

```
hello from gpu in 2 thread
hello from gpu in 3 thread
hello from gpu in 0 thread
hello from gpu in 1 thread
```

## CUDA程序基本结构

### 函数分类

从上述代码可以看到，我们通过f<<<1,2>>>()这样奇怪的形式调用cuda函数。这种函数成为核函数，在cuda代码中，函数分为三种类型：

- `__global__`核函数。核函数主要是在cpu端发起一个GPU计算任务，也可以在核函数中调用核函数。核函数的返回值必须是void，void与`__global__`的位置无关紧要。尖括号内的数字为线程数量。
- `__host__`主机函数。主机函数只可以在cpu上执行，一般上可以省略。
- `__device__`设备函数。设备函数只可以在GPU上执行，可以在核函数或者设备函数中调用。

### 线程组织

核函数前面的三个尖括号<<<>>>用于定义要创建多少线程执行该核函数，每一个线程都会独立的执行核函数。线程的数量为两个数字的乘积， 比如在`helloWord.cu中就创建了4个线程，所以有4行输出。**线程块最大为1024**个线程。

```c++
void __gloable__ kernel();
// 调用
<<<3,2>>>kernel(); 一共6个线程
// 三维调用
dim3 grid(1,2,3);
dim3 blcok(1,2,3);
<<<grid, block>>>kernel(); // 一共36个线程
// threadIdx 板块内线程号
// blockIdx 网格内板块号
// gridDim 网络维度（元素为板块）
// blockDim 板块维度（元素为线程）
```

### CUDA函数

cuda函数是cuda提供的库函数，包括了cudaDeviceSynchronize()和后面内存操作的函数。cudaDeviceSynchronize()具有同步gpu cpu执行的功能，因为核函数的调用是异步的，需要cudaDeviceSynchronize()进行同步。但是大部分的cuda函数都具有同步功能，因此无需显示调用cudaDeviceSynchronize()进行同步。

### 编译驱动

​	在编译cuda程序是可以指定程序的计算能力。`-arch=compute_XY`指定虚拟架构的计算能力，`-code=sm_ZW`指定真是架构的计算能力，真实的架构计算能力必须大于虚拟的计算能力。可以使用`-arch=sm_XY`同时指定真实和虚拟计算能力。这一块暂时还不太清楚，后续在理解。

## 内存操作

​	GPU是没有办法直接访问CPU的内存，同理CPU也不能直接访问GPU的内存。因此，需要在CPU与GPU之间交换数据。

- `cudaMalloc()` 在GPU上分配内存
- `cudafree()`释放内存
- `cudaMencpy()`复制内存，`cudaMencpyDeviceToHost` 设备到主机，`cudaMencpyHostToDevice`主机到设备，`cudaMencpyDeviceToDevice` 设备到设备，`cudaMencpyHostToHost`主机到主机

```c++
// add.cc
void __global__ add(const double *x, const double *y, double *z, int k)
{
    const int n = blockDim.x * blockIdx.x + threadIdx.x;
    for(int i = 0; i < k; i++)
    z[n] = x[n] + y[n];
}
```

## 错误检测

CUDA函数同LINUX的系统调用一样，会返回一个状态码，表示调用的状态。

```c++
cudaError_t CUDAFUNC();
```

如果调用成功则返回`cudaSuccess`，失败了就会返回对应的错误码，通过一下函数可以获得出错的原因

```c++
char* cudaGetErrorString(cudaError_t)
```

核函数是没有返回值的，因此不能通过上述方法获取调用的结果，但可以通过以下函数

```c++
cudaError_t cudaGetLastError();
```

### 运行时检查工具cuda-memcheck

cuda-memcheck可以检测运行时发生的内存错误，甚至可以报告出错的代码位置，非常好用。简单使用如下

```bash
cuda-memcheck ./a
```

