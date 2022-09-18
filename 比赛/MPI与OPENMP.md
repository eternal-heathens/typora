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

在所有的预处理指令中，#Pragma 指令可能是最复杂的了，它的作用是设定编译器的状态或者是指示编译器完成一些特定的动作。#pragma指令对每个编译器给出了一个方法，在保持与C和C++语言完全兼容的情况下，给出主机或操作系统专有的特征。依据定义，编译指示是机器或操作系统专有的，且对于每个编译器都是不同的。其格式一般为: #pragma Para。其中Para 为参数，下面来看一些常用的参数：


![image-20220730163454052](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207301634090.png)

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



# 历史

* 如果有不相关的任务对数据集的不同元素进行**相同的操作**，我们称该数据相关图展现了数据并行性
* 如果有不相关的任务对数据集的不同元素进行**不同的操作**，我们称该数据相关图展现了功能并行性

### 数据聚类

### 并行编程

1. 扩展现有的编译器以便将串行程序转化为并行程序

	* eg：fortram ， 对于面向函数的语言 ，若还要并发变成，开发成本过高，因此如何将这部分转移到编译器是个长远的命题
2. 扩展现有语言，增加新的操作以允许用户表达并行性

	* 没有像单纯的编译器执行的支持全面，出现问题在人为的概率上变大，对开发人员的要求较高
3. 在现有串行语言上增加一个并行语言层

	* 适用于多进程和分布式系统
4. 定义全新的并行语言和编译系统
	* 将并行语句添加到现存的程序语言中，或是创造一个全新的语言都需要开发新的编译器

* 完全的并行化编译器和高层及的并行编程还在优化，目前流行的仍是第二的在现有的串行语言上，通过增加用函数调用或者编译指导语句来表示低层次语句。使用C语言、MPI和OPENMP就是采用这种方法的例子。在前面语言和编译器较为成熟的基础上，能具有较高的性能和好的移植性，但在完全基于并行的语言和编译器支持下，在编程方便性和调试方面肯定有所不及

# 并行体系结构

## 互连网络

### 共享机制与开关介质

* 共享介质：某一时刻仅仅一个消息被发送
* 开关介质：支持出理解对之间的点对点的消息互传（支持多消息并发传输，支持互连网络的扩展）

![image-20220711233316604](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207112333645.png)

### 开关网络的拓扑结构

* 直径：一个网络的直径是两个开关节点之间的最大距离，决定了需要在随机节点对之间进行通信的并行算法复杂度的下界
* 对分带宽：为了将网络划分为两半而必须被删除的边数的最小值，越大越好（大量数据移动算法重，并行算法复杂度的下界为数据集的大小除以对分带宽）
* 每个开关节点的边数：最佳情况是一个与网络大小无关的常数
* 固定的边长：最佳的情况为网络的节点和边能够不至于三维空间，使得最大边长是一个与网络大小无关常数

### 二维网格形网络

* 开关分布在一个二维的格子中，通信只能在相邻的开关间进行

![image-20220712000033800](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207120000983.png)

### 二叉树形网络

* 一个有着2n-1个开关的二叉树支撑着n=2^d个处理器节点间的通信

![image-20220713142743293](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207131427411.png)

### 超树形网络

### ![image-20220713142857185](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207131428343.png) 蝶形网络

* 一个通过n（logn +1）个开关节点连接n = 2^d个处理器节点的间接拓扑。开关节点被划分为logn+1行或阶（rank），其中每行或均含有n个节点。列序号从0到logn，尽管有时候第0阶和第d阶联合在一起，使得每个开关与别的4个开关节点相连

![image-20220713142957020](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207131429236.png)





### 超立方体网络

每行开关节点都收缩为单节点的蝶形网络，一个二叉n立方体由n = 2^d个处理器节点和相同数目的开关节点所组成，是一个直接拓扑，两开关的标注仅有一个一个二进制位存在差异，则它们是相邻的，具有n=2^d个开关节点的超立方体的直径为logn，对分带宽为n/2，每个开关节点的边数为logn

![image-20220713144119419](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207131441573.png)



### 混洗-交换网路

* 有着n=2^个处理器/开关对的直接拓扑，开关有两种连接，分别称为混洗和交换。

> 交换连接：将序号差异存在于最低位上的开关联系起来
>
> 混洗连接：将开关i与开关j联系起来，其中j是将i向左轮转一个位置后的结果

![image-20220713150715329](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207131507529.png)

### 网络对比

![image-20220713150920052](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207131509126.png)



