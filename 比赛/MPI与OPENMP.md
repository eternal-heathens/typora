# openMP和MPI的区别

openmp： 线程级（并行粒度）、共享存储、隐式（数据分配方式）、可扩展性差。
MPI： 进程级、分布式存储、显式、可扩展性好。
openmp优点：

OpenMP相对于MPI而言更容易使用。
OpenMp对原串行代码改动较小，可以保护代码原貌。
代码更容易理解和维护
允许渐进式并行化
openmp缺点：

所有线程共享内存空间，硬件制约较大
目前主要针对循环并行化
适用于：SMP,DSM机器，不适合于集群。
MPI优点：

无论硬件是否共享内存空间，都可以使用。（但是线程间不共享内存空间）
与OpenMP相比，可以处理规模更大的问题
每个线程有自己的内存和变量，这样不用担心冲突问题
MPI缺点：

算法上经常有较大改动（建立communication等）
编程复杂度较大，调试复杂。
需要解决通信延迟大和负载不平衡两个主要问题。
MPI程序可靠性差，一个进程出问题，整个程序将错误。
适用于：各种机器设备



# openMP的原理

- 只需加上专用的pragma，编译器就可以自动将程序进行并行化。
- 当选择忽略这些pragma或不支持openMP，程序又可退化为通常的程序(一般为串行)。

### Fork-Join模型

![在这里插入图片描述](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202206161031448.png)

- 所有的OpenML程序都以一个单个进程——master thread开始，master threads按顺序执行知道遇到第一个并行区域。
- Fork：主线程创造一个并行线程组。
- Join：当线程组完成并行区域的语句时，它们同步、终止，仅留下主线程。



### 数据范围

- 由于OpenMP时是共享内存模型，默认情况下，在共享区域的大部分数据是被共享的
- 并行区域中的所有线程可以同时访问这个共享的数据
- 如果不需要默认的共享作用域，OpenMP为程序员提供一种“显示”指定数据作用域的方法

 编译器指令
并行区域构造：

```shell
#pragma omp parallel [clause ...] newline
                     if （scalar_expression）
                     private （list）
                     shared （list）
                     default（shared | none）
                     firstprivate （list）
                     reduction （operator：list）
                     copyin （list）
                     num_threads （integer-expression）

   structured_block

```

决定线程数的因素有多个，它们的优先级如下：

if语句的值
设置num_threads语句
使用的omp_set_num_threads() 库函数
设置的OMP_NUM_THREADS 环境变量
注意：生成的线程编号为0~N，其中0是主线程（master thread）的编号

https://blog.csdn.net/u012477435/article/details/108208624

```c
#include <omp.h>
 #define N 1000

 main(int argc, char *argv[]) {

 int i;
 float a[N], b[N], c[N], d[N];

 /* Some initializations */
 for (i=0; i < N; i++) {
   a[i] = i * 1.5;
   b[i] = i + 22.35;
   }

 #pragma omp parallel shared(a,b,c,d) private(i)
   {

   #pragma omp sections nowait
     {

     #pragma omp section
     for (i=0; i < N; i++)
       c[i] = a[i] + b[i];

     #pragma omp section
     for (i=0; i < N; i++)
       d[i] = a[i] * b[i];

     }  /* end of sections */

   }  /* end of parallel region */

 }

```

# FORTRAN

FORTRAN语言是Formula Translation的缩写，意为“公式翻译”。它是为科学、工程问题或企事业管理中的那些能够用数学公式表达的问题而设计的，其数值计算的功能较强。

