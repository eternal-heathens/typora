 

文档地址：

[Get Started | Intel® DevCloud](https://devcloud.intel.com/oneapi/get_started/)   https://jupyter.oneapi.devcloud.intel.com/user/u162556/lab/tree/

参考资料

CPU与GPU有什么区别？xPU又是什么鬼？ [(54条消息) 深度解析：CPU与GPU有什么区别？xPU又是什么鬼？_张巧龙的博客-CSDN博客](https://blog.csdn.net/best_xiaolong/article/details/103231543)



## 1

### CPU

64 GBHBM的带宽是ddr5的3-4倍，都是8通道的话，HBM应该时钟频率是DDR5的3-4倍,对于CPU的高速计算的话，内存刷新最重要的刷洗而不是容量

小于32核一块芯片，56核分成4个芯片

![image-20220723213753645](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207232137932.png)

指令集增加-sapphire Rapids  直接支持AMX运算（矩阵计算）

加速器更新-》加速量化模型的推理

加速量化模型



### 第一代GPU Ponte Vecchio

![image-20220723213623667](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207232136967.png)

多层芯片嵌套



![image-20220723214025874](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207232140191.png)



### 傲腾持久内存



### 文件系统

专门支持高性能计算（需要持久内存支持）

![image-20220723214138835](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207232141207.png)



## ONEAPI&HPC

高性能计算软件集

![image-20220723214635455](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207232146652.png)

### oneAPI

基于oneAPi规范实现的语言开发和支持（比赛必须使用）

![image-20220723214717040](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207232147223.png)

### oneAPi tools 

![image-20220723214849223](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207232148403.png)

oneAPi tools用对应的 DPC++等进行编写后通过one API tools给开发者调用（像cpc比赛提供的接口？），给我们开发MPI等并行程序使用



### DPC++兼容性工具

![image-20220723215118914](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207232151015.png)



### 编译器选择

![image-20220723215136021](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207232151128.png)

![image-20220723215500197](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207232155305.png)

重点编译器icx ，优化选项等

## 可能用到的依赖



![image-20220723215853584](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207232158681.png)

### oneMKL(重要)

上面的ONEAPI包括的一个依赖

![image-20220723215729214](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207232157385.png)

### intel MPI Library(重要)

![image-20220723215756228](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207232157402.png)

### Intel软件社区

https：// community.intel.com



### 比赛可能会涉及的

![image-20220723220204342](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207232202519.png)



# 2

硬件的增多导致硬件接口的增多，如果希望直接通过语言进行编写，需要等语言提供支持与编译器提供支持（如DPC++），opeAPi 将硬件接口的调用封装成一个库供多个语言使用

![image-20220724113304486](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207241133661.png)

### 全部套件

![image-20220724190247683](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207241902924.png)



### DPC++



![image-20220724185655500](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207241856689.png)

### 聚合通信库

![image-20220724185828474](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207241858656.png)

### Vtune分析器

![image-20220724185918635](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207241859870.png)

### Advisor

![image-20220724190008652](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207241900852.png)

## 举例架构

![image-20220724213842534](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207242138842.png)

## DPC++ 程序结构

32：17

 ![image-20220724190946873](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207241909027.png)

**异构性**：指不同的指令集架构Instruction-Ser Architectures(ISA)

**异构计算**:使用一种以上处理器或核心的系统

	1. 通过添加不同的协处理器
	1. 集成专门的处理功能来处理特定任务

### SYCL类

SYCL是OpenCL的高级编程模型（**C++语言实现的基于OPENCL的一个框架**），作为基于纯C ++ 11（用于SYCL 1.2.1）和C ++ 14(用于SYCL 2.2)的单源特定于域的嵌入式语言(DSEL), 用于提高编程效率。 

> Khronos Group团队成立于 2000 年 1 月，由包括 [3Dlabs](https://baike.baidu.com/item/3Dlabs/10141380), [ATI](https://baike.baidu.com/item/ATI), [Discreet](https://baike.baidu.com/item/Discreet/5177372), Evans & Sutherland, [Intel](https://baike.baidu.com/item/Intel/125450), [Nvidia](https://baike.baidu.com/item/Nvidia), [SGI](https://baike.baidu.com/item/SGI/9841082) 和 [Sun Microsystems](https://baike.baidu.com/item/Sun Microsystems) 在内的多家国际知名[多媒体](https://baike.baidu.com/item/多媒体)行业领导者创立，致力于发展开放标准的[应用程序接口](https://baike.baidu.com/item/应用程序接口/10418844) [API](https://baike.baidu.com/item/API/10154) ，以实现在多种平台和[终端设备](https://baike.baidu.com/item/终端设备/643738)上的[富媒体](https://baike.baidu.com/item/富媒体/3331198)创作、加速和回放。

SYCL是一个免版税的跨平台抽象层，基于OpenCL的基本概念，可移植性和效率，使得异构处理器的代码可以使用完全标准的“单一来源”风格编写C ++。

**单源：**主机代码和异构加速器内核可以混合在相同的源文件中

#### 设备

![image-20220724191610753](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207241916912.png)

#### 设备选择器

![image-20220724191741950](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207241917128.png)

#### 队列

![image-20220724191811329](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207241918472.png)

![image-20220724191830401](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207241918568.png)

#### 内核

![image-20220724191908042](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207241919219.png)

### DPC++ 语言和运行时

包含一系列C++类、模板和库

**应用范围和命令组范围**

* 在主机上执行的代码
* 应用和命令组范围内均支持C++的所有功能

**内核范围**

* 在设备上执行的代码
* 内核范围支持的C++有部分限制

#### 并行内核

![image-20220724192430701](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207241924892.png)

#### 基础并行内核

![image-20220724192512408](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207241925589.png)

#### ND-Range内核

![image-20220724213310139](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207242133366.png)

![image-20220724214106102](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207242141271.png)

#### 缓冲区内存模式

![image-20220724214444264](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207242144421.png)

### 异步执行

SYCl分为两部分

1. 主机代码

2. 内核执行图

除了同步操作，这些执行都是相互独立的

* 主机代码提交工作以构建graph图（并且可以自己执行计算工作）
* 内核执行和数据移动的graph图由SYCL运行时管理与主机代码异步执行
* **DPC++异步执行时会自动帮你排序数据和控制依赖性**

### 同步

#### 主存访问器 Host Accessors

* 使用主机缓冲区访问目标的访问器
* 在命令组范围外创建
* 允许访问的数据将在主机上可用
* 用于通过构建主机访问对象将数据同步回主机

![image-20220725003854310](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207250038534.png)

#### 缓冲区销毁 Buffer Destruction

![image-20220725234535525](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207252345717.png)

### USM、Sub-Groups

![image-20220725234910274](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207252349462.png)

## oneMKL（数学核心库）

支持异构计算

![image-20220725235418367](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207252354540.png)

#### 哪里支持

![image-20220725235805793](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207252358972.png)

#### 支持功能

![image-20220725235835648](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207252358805.png)

### linux 编译

![image-20220726001547607](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207260015749.png)

![image-20220726001601481](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207260016635.png)

### 基本步骤

step1： observe the definition of an asynchronous exception handler

step2： create a device object

step3：create a queue object

step4：create a sycl event and allocate USM

step5：Execute an oneApi operation



## 资源

![image-20220727223613575](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207272236733.png)

# 3

## Linux:Setting the Enviroment

### **Linux: Setting the Environment**

Bash source script: Latest version

source /opt/intel/oneapi/setvars.sh -

Bash source script: Previous versions

source /opt/intel/oneapi/<component>/<version>/env/vars.sh Would need to source each component

\-

### Other shells

https://community.intel.com/t5/Intel-Fortran-Compiler/How-to-use-zsh-csh-or-tcsh-with-oneAPI-setvars-sh-vars-sh-or/td-p/1227040

### Command Line:

·https://software.intel.com/content/www/us/en/develop/documentation/oneapi-dpcpp-cpp-compiler-dev-guide-and-reference/top/compiler-setup/using-the-command-line.html 



### Visual Studio:

·https://software.intel.com/content/www/us/en/develop/documentation/oneapi-dpcpp-cpp-compiler-dev-guide-and-reference/top/compiler-setup/using-microsoft-visual-studio.html 



## Compiling the test code

**·For DPC++ source files**

·dpcpp [options] file1 [file2...] 

**For C/C++source files**

·{icx|icpx} [options] file1 [file2..]

```shell
	dpcpp 				-ipo -		mprefer-vector-width=512 		 test.cpp 				-o test
Compiler driver	ICX options 		LLVM options				Source code			Executable	file 
```



**Common Compiler Options**

![image-20220727225159596](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207272251724.png)

-O3 会采用激进的优化策略，适合循环体、浮点计算、处理很大的数据集的运算（会更激进，但是有可能在缺乏上述类似的结构时，o2更快）

-ipo 编译器会扩大优化识别边界，不止识别某一个函数，还会识别出参入参等进行优化

> IPO(IPA+LTO)支持单文件编译和多文件编译两种编译模型，单文件编译使用[Q] ip编译选项，并为每个要编译的源文件生成一个真实的目标文件。在单文件编译期间，编译器对调用当前源文件中定义的过程执行内联函数扩展。编译器在O2默认优化级别上执行一些单文件过程间优化。另外，编译器可能会为O1优化级别执行一些内联。多文件编译使用[Q] ipo选项，并生成一个或多个模拟目标文件，而不是普通目标文件。此外，编译器还会从构成程序的各个源文件中收集信息。使用此信息，编译器可以跨不同源文件中的函数和过程执行优化。
>
> 过程间分析（inter-procedural analysis）是一个多步骤的过程GCC中的IPA包含的操作有： increase alignment、devirtualization、constant propagation、inline、pure/const analysis等。可使用-fdump-ipa-all选项打印出ipa阶段的信息，如下：
>
> gcc -flto -O3 ..... -fdump-ipa-all t.c //可使用任意优化选项，此处使用flto和O3

-fiopenmp (CPU/GPU)  用到openMP

可以使用-Q --help =optimizers来获取每个优化标识所启用的优化选项。





1.-O，-O1：

这两个命令的效果是一样的，目的都是在不影响编译速度的前提下，尽量采用一些优化算法降低代码大小和可执行代码的运行速度。并开启如下的优化选项：

```text
-fauto-inc-dec 
-fbranch-count-reg 
-fcombine-stack-adjustments 
-fcompare-elim 
-fcprop-registers 
-fdce 
-fdefer-pop 
-fdelayed-branch 
-fdse 
-fforward-propagate 
-fguess-branch-probability 
-fif-conversion2 
-fif-conversion 
-finline-functions-called-once 
-fipa-pure-const 
-fipa-profile 
-fipa-reference 
-fmerge-constants 
-fmove-loop-invariants 
-freorder-blocks 
-fshrink-wrap 
-fshrink-wrap-separate 
-fsplit-wide-types 
-fssa-backprop 
-fssa-phiopt 
-fstore-merging 
-ftree-bit-ccp 
-ftree-ccp 
-ftree-ch 
-ftree-coalesce-vars 
-ftree-copy-prop 
-ftree-dce 
-ftree-dominator-opts 
-ftree-dse 
-ftree-forwprop 
-ftree-fre 
-ftree-phiprop 
-ftree-sink 
-ftree-slsr 
-ftree-sra 
-ftree-pta 
-ftree-ter 
-funit-at-a-time
```

\2. -O2

该优化选项会牺牲部分编译速度，除了执行-O1所执行的所有优化之外，还会采用几乎所有的目标配置支持的优化算法，用以提高目标代码的运行速度。

```text
-fthread-jumps 
-falign-functions  -falign-jumps 
-falign-loops  -falign-labels 
-fcaller-saves 
-fcrossjumping 
-fcse-follow-jumps  -fcse-skip-blocks 
-fdelete-null-pointer-checks 
-fdevirtualize -fdevirtualize-speculatively 
-fexpensive-optimizations 
-fgcse  -fgcse-lm  
-fhoist-adjacent-loads 
-finline-small-functions 
-findirect-inlining 
-fipa-cp 
-fipa-cp-alignment 
-fipa-bit-cp 
-fipa-sra 
-fipa-icf 
-fisolate-erroneous-paths-dereference 
-flra-remat 
-foptimize-sibling-calls 
-foptimize-strlen 
-fpartial-inlining 
-fpeephole2 
-freorder-blocks-algorithm=stc 
-freorder-blocks-and-partition -freorder-functions 
-frerun-cse-after-loop  
-fsched-interblock  -fsched-spec 
-fschedule-insns  -fschedule-insns2 
-fstrict-aliasing -fstrict-overflow 
-ftree-builtin-call-dce 
-ftree-switch-conversion -ftree-tail-merge 
-fcode-hoisting 
-ftree-pre 
-ftree-vrp 
-fipa-ra
```

\3. -O3

该选项除了执行-O2所有的优化选项之外，一般都是采取很多向量化算法，提高代码的并行执行程度，利用现代CPU中的流水线，Cache等。

```bash
-finline-functions      // 采用一些启发式算法对函数进行内联
-funswitch-loops        // 执行循环unswitch变换
-fpredictive-commoning  // 
-fgcse-after-reload     //执行全局的共同子表达式消除
-ftree-loop-vectorize　  // 
-ftree-loop-distribute-patterns
-fsplit-paths 
-ftree-slp-vectorize
-fvect-cost-model
-ftree-partial-pre
-fpeel-loops 
-fipa-cp-clone options
```

这个选项会提高执行代码的大小，当然会降低目标代码的执行时间。

４. -Os

这个优化标识和-O3有异曲同工之妙，当然两者的目标不一样，-O3的目标是宁愿增加目标代码的大小，也要拼命的提高运行速度，但是这个选项是在-O2的基础之上，尽量的降低目标代码的大小，这对于存储容量很小的设备来说非常重要。

为了降低目标代码大小，会禁用下列优化选项，一般就是压缩内存中的对齐空白(alignment padding)

```bash
-falign-functions  
-falign-jumps  
-falign-loops 
-falign-labels
-freorder-blocks  
-freorder-blocks-algorithm=stc 
-freorder-blocks-and-partition  
-fprefetch-loop-arrays
```

5. -Ofast:

该选项将不会严格遵循语言标准，除了启用所有的-O3优化选项之外，也会针对某些语言启用部分优化。如：-ffast-math ，对于Fortran语言，还会启用下列选项：

```text
-fno-protect-parens 
-fstack-arrays
```

6.-Og:

该标识会精心挑选部分与-g选项不冲突的优化选项，当然就能提供合理的优化水平，同时产生较好的可调试信息和对语言标准的遵循程度。 

## 手册

Useful Links
Getting Started with DPC++Compiler
https://www.intel.com/content/www/us/en/develop/documentation/get-started-with-dpcpp-compiler/top.html
Getting Started with OpenMP*Offload to GPU
v-bind可简写成：
https://www.intel.com/content/www/us/en/velop/documentation/get-started-with-cpp-fortran-compiler-openmp/top.html
Intel oneAPI DPC++/C++Compiler Developer Guide and Reference
·Compiler Setup
Optimization and Programming
https://www.intel.com/content/www/us/en/develop/documentation/oneapi-dpcpp-cpp-compiler-dev-guide-and-reference/top.html

## 常用命令

sycl -ls 查看所有机器

## DPC++

DPC++本质上是C++的扩展，**增加了对SYCL的支持（在C++语言层面支持SYCL，而不是简单的框架，Intel自己做的，在sycl的规范上加了很多自家产品为基础的规范）**。

**什么是数据并行C++(Data Parallel C++)?**
oneAPI Implementation of SYCL = C++和SYCL*标准 扩展

### 特性

基于现代C++
C++效率优势和熟悉的构造
基于标准的跨架构语言
·集成了SYCL标准，支持数据并行性和异构编程

### 优点

DPC++扩展了SYCL标准
增强效率
避繁就简
减少冗余(reduce verbosity),降低编程人员的工作负担
提升性能
·让编程人员控制程序的执行
启用特定于硬件的特性
DPC++:快速发展的开放式协作，及时为融入SYCL标准提供反馈
**以upstream LLVM为目标的开源实现**

> 它最初的编写者，是一位叫做Chris Lattner([Chris Lattner's Homepage](https://link.zhihu.com/?target=http%3A//www.nondot.org/sabre/))的大神，硕博期间研究内容就是关于编译器优化的东西，发表了很多论文，博士论文是提出一套在编译时、链接时、运行时甚至是闲置时的优化策略，与此同时，LLVM的基本思想也就被确定了，这也让他在毕业前就在编译器圈子小有名气。
>
> 而在这之前，`Apple`公司一直使用`gcc`作为编译器，后来GCC对`Objective-C`的语言特性支持一直不够，Apple自己开发的GCC模块又很难得到GCC委员会的合并，所以老乔不开心。等到Chris Lattner毕业时，Apple就把他招入靡下，去开发自己的编译器，所以LLVM最初受到了Apple的大力支持。
>
> LLVM最初设计时，因为只想着做优化方面的研究，所以只是想搭建一套虚拟机，所以当时这个的全称叫`Low Level Virtual machine`，后来因为要变成编译器了么，然后官方就放弃了这个称呼，不过LLVM的简称还是保留下来了。
>
> 因为LLVM只是一个编译器框架，所以还需要一个前端来支撑整个系统，所以Apple又拨款拨人一起研发了Clang，作为整个编译器的前端，Clang用来编译C、C++和Objective-C。所以当我接触Apple编译器时，当时的帖子里都说使用Clang/LLVM来和gcc做对比，当然是在代码优化、速度和敏捷性上比gcc强不少。这里我有两个文章，一个是gcc评价它在代码诊断方面和Clang的比较([ClangDiagnosticsComparison - GCC Wiki](https://link.zhihu.com/?target=https%3A//gcc.gnu.org/wiki/ClangDiagnosticsComparison))，另一个是Clang评价它和gcc（以及其他几个开源编译器）的优缺点([http://clang.llvm.org/comparison.html](https://link.zhihu.com/?target=http%3A//clang.llvm.org/comparison.html))，还是很客观的。相比来说，Clang/LLVM的完整性还不够，毕竟还在发展中。
>
> 对了，Clang的发音是`/ˈklæŋ/`，这是官方确认过的，我现在还在纠正发音过程中（后续：半个月后已纠正完毕，无缝衔接）。
>
> 后来Apple推出了Swift，也是基于LLVM作为编译器框架的。
>
> 至于Chris Lattner，17年的时候跳到了特斯拉，再后来去了Google，在TensorFlow团队，还是挺厉害的。而LLVM开源之后由LLVM委员会负责维护，当然发展势头也很猛。
>
> LLVM是一个编译器框架。LLVM作为编译器框架，是需要各种功能模块支撑起来的，你可以将clang和lld都看做是LLVM的组成部分，框架的意思是，你可以基于LLVM提供的功能开发自己的模块，并集成在LLVM系统上，增加它的功能，或者就单纯自己开发软件工具，而利用LLVM来支撑底层实现。LLVM由一些库和工具组成，正因为它的这种设计思想，使它可以很容易和IDE集成（因为IDE软件可以直接调用库来实现一些如静态检查这些功能），也很容易构建生成各种功能的工具（因为新的工具只需要调用需要的库就行）。
>
> 也就是一个针对C++ 语言特性不同支持程度的编译器

DPC++扩展，旨在成为核心SYCL或Khronos扩展*

ONP API则是基于这个新的DPC++又封装了很多常用基础库

### SYCL2020规范的新特性：

统一共享内存(Unified Shared Memory)(USM）
子组(Sub-Group)
简化归约(Simplified Reduction)
主要目标是通过公开硬件特性以简化编程并实现性能。

eg：

![image-20220730170413619](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207301704801.png)

## 统一共享内存

开发人员可以使用统一共享内存在主机(host)和设备(device)代码中引用相同的内存对象（主从核级别的共享内存）

统一共享内存统一共享内存的设置方法如下：

int *data malloc_shared<int>(N,q); 

您还可以使用更熟悉的C++/C风格

malloc:int *data static_cast<int*>(malloc_shared(N sizeof(int),q));

![image-20220730171712868](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207301717010.png)

![image-20220730171755922](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207301717163.png)

### 为何使用统一共享内存？

SYCL*标准提供了缓冲(Buffer)内存抽象概念
非常强大而简洁地表达了数据依赖关系
然而…在C++程序中用buffer替换所有指针(pointer))和数组(array)仍会给程序员带来
负担
USM在DPC++中提供了一种基于指针的替代方案
简化对加速器的移植
为程序员提供所需的控制级别
与缓冲(ouffer)互补 

### 分配方式

![image-20220730172415574](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207301724700.png)

如果限定在某一种需要频繁切换访问的话，性能就会下降，到那时如果设置成分配可以在两种的话，就会以相较于共享的实现（可能是同一地址编码，再转换实际编码地址访问）直接访问（对应设备挂起再去寻找地址）提供一种更快的速度

#### 显示数据传输

![image-20220730172542902](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207301725117.png)

#### 隐式数据传输

![image-20220730172619052](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207301726241.png)

### USM-任务中的数据依赖关系

在多个内核任务中使用统一共享内存时，必须使用事件(event)指定操作之间的依赖关系(dependences)。
编程人员可以显式(explicitly)等待事件(event)对象，或使用命令组（command group)中的depends on方法指定在任务开始之前必须完成的事件列表。

#### wait

![image-20220730174407725](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207301744906.png)

#### in_order

![image-20220730174431269](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207301744447.png)

#### depends_on

![image-20220730174456852](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207301744036.png)

![image-20220730174544931](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207301745121.png)

#### test

/oneAPI_Essentials/03_DPCPP_Unified_Shared_Memory

## 子组 Sub Groups

子组Sub-group是同时执行的或具有额外调度保证的工作项work-item 的子集。

利用子组Sub-group有助于将执行映射到低级硬件，并可帮助实现更高的性能。

![image-20220730210448215](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207302104441.png)

oneAPI有sync 1.2和2020两个版本的实现，最好用2020

子组(Sub-Group)是可以映射到矢量硬件的工作组(workgroup)中的工作项(work-item)的子集。

为何使用子组(Sub-Group)?

子组(Sub-Group)中的工作项(work-item)可以使用混编操作(shuffle operations))直接通信，而无需重复访问本地或全局内存，并且可以提供更高的性能。

子组(Sub-Group)中的工作项(work-item)可以访问子组组算法(sub-group group algorithm),从而快速实施常见的并行模式。

![image-20220730211459715](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207302114907.png)

### API

#### 获取

![image-20220730211540226](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207302115391.png)

![image-20220730211643344](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207302116539.png)

#### 自组算法

![image-20220730211737466](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207302117744.png)



#### 混编操作

![image-20220730212031790](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207302120041.png)

![image-20220730212047066](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207302120261.png)

#### test

/oneAPI_Essentials/04_DPCPP_Sub_Groups/

#### 并行化归约

子组组算法都是并行化，比如reduce （归约），那么是如何实现这个并行化的，获取到全部值，并行使用归约计算返回？i是如何并行获得的？编译器实现？

![image-20220730222039275](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207302220551.png)

![image-20220730222121251](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207302221476.png)

遵循2020的规范后DPC++的实现

![image-20220730223359464](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207302233655.png)

#### test

/oneAPI_Essentials/08_DPCPP_Reduction/

# 4

## cmcc

基于算力网络的超算服务

![image-20220730230933671](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207302309897.png)

### 网络体系

![image-20220730231021975](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207302310196.png)



![image-20220730231120581](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207302311859.png)



![image-20220730231400954](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207302314193.png)

## 性能分析工具

### Intel VTune Profiler

#### 优点

![image-20220730232101675](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207302321909.png)

####  Data Collection

**采集方式**

![image-20220730232304629](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207302323789.png)

#### Data Analysis

![image-20220730232406835](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207302324092.png)

#### Flexible workflow

工作方式

页面UI、command、ssh远程、云端

#### Key Analysis Type

分析类型

![image-20220730232544807](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20220730232544807.png)

#### CPU 分析方向

![image-20220730232924455](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207302329614.png)

### Intel Advisor

哪种运算在现在的平台上的瓶颈、优化方向，用哪个平台更合适，理论能提升的性能

定制化首先考虑通过cmd 的脚本进行实现，sample都是基于硬件的分析的（PMU）

![image-20220730235622787](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207302356932.png)

![image-20220731001527823](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207310015966.png)





# 5

##  优化

文档地址：

https://www.intel.com/content/www/us/en/develop/documentation/oneapi-gpu-optimization-guide/top/xe-arch.html

### 常见原则

![image-20220731003841564](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207310038678.png)

### Locality Matters(访问更近的内存)

·加速卡具有专用的内存，也有层级，latency:register>cache>DRAM/HBM.
·三个原则：
·尽可能在加速器上保留数据。
·内核执行时访问连续的内存块（不同数据结构读取方式不一）
·重构代码获得更高的数据重用。



### Xe 架构

![image-20220731093439217](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207310934477.png)

#### Xe Slice

![image-20220731095126122](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207310951374.png)



#### Xe Stack(tile)

![image-20220731095156142](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207310951393.png)

 

![image-20220731095242414](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207310952556.png)

### GPU执行模型

![image-20220731103549470](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207311035610.png)

一个subgroup对应一个EU，一个workgroup调用的是一个EU？



#### Thread mapping和GPU使用率

·两个重要的GPU的资源参数：
	·Thread context:内核应该有足够数量的线程来利用GPU的context.
		**每个EU上可放线程数量(Xe-LP是7，Xe-HPC是8)**
	·SIMD单元和SIMD寄存器：程序应该向量化利用SIMD指令。
		(例子中Xe-LP执行lnt是SIMD32。ATS-P上，FP32是SIMD16,FP64也是SIMD16)。
·*SYCL限制最大的workgroup size,是512.

#### Thread Mapping 解释

![image-20220731110456590](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207311104731.png)



![image-20220731110507827](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207311105979.png)

![image-20220731110920229](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207311109346.png)

### SLM(Shared Local Memory)


·所有work item之间通信/共享：使用global memory,但带宽低延迟高。

·一个subgroup里的work item,可以共享数据，但范围太小。（偏寄存器了）

·一个work group(Xe Core)里共享数据，使用SLM。ATS-P上64K。（多个EU共享，类似CPC的LDM共享端）

·SLM是显式的调用，通过sycl:malloc_shared,或accessor<.,target:local>

可以根据work item使用空间大小，确定一个Xe Core.上可以放的work group size.

例如一个workitem使用512个字节，workgroup size最大为65536/512=128。

此时thread context无法使用满。

问：work item使用的数据量多大时，刚好使用满SLM?

答：一个Xe Corei最多128个线程，sub-group size=16时，有2048个work item.

每个work item占用空间为65536/2048=32字节/work item。

SLM Bank:SLM上一个cache line也是64字节。64字节被分为16个Bank,每个Bank有4个字节。16个work item可以并行访问16个Bank。但访问同一bank里的不同地址会导致Bank冲突。（**尽量让每个core(sub slice)每个EU访问同一个Cache line** ）



#### Sub group

·Sub Group:是隐式的概念，workgroup在实际运行中要切分为sub group,一般一个Sub group可以设置8,16,32几种格式。

·可以理解为在一个EU上执行的SMD指令。

·查看subgroup.cpp文件。

![image-20220731130817296](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207311308434.png)

**·可以使用block.load/store方法提高内存拷贝性能。但目前只能支持subgroup_size=16.**


# 6

## 比赛相关

优化报告、环境登录、往届回顾

### 优化报告

![image-20220731131423356](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207311314533.png)

![image-20220731132239955](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207311322115.png)



![image-20220731132356395](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207311323498.png)



### oneAP|技术创新奖申报证明文档

·Makefile文件
运行脚本
分析软件截图
源代码函数调用部分截图

### 应用优化

#### ·基于体系结构的优化

* 多核/异构：多线程、多进程

* 微指令集：SSE/AVX2/AVX512 （向量化处理）

* Cache Locality：Memory Acess

#### 基于算法的优化

·降低算法复杂度
·减少复杂运算：如乘法，sin,cos

#### ·利用编译器和数学库

·Intel oneAPl提供的编译器，MKL
·PIGZ,jemalloc,etc



### 往届参赛队伍

* 减少重复调用
* 用调试命令查看编译器优化报告、将函数拆解的更适合自动向量化，编译器采用更合适的向量化微指令集合选项
* MPI+OPENMP，不建议在报告里说手动调参，建议根据某些硬件、算法特性自动选择某些参数



eg：

![image-20220731134725497](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207311347607.png)



#### 如何生成优化报告

* -qopt-report:编译选项可以生成YAML格式的优化报告

	* icpx -fiopenmp -fopenmp-targets=spir64 -qopt -report  foo.c

	* 生成两个文件icx8 iguij,yaml,foo.opt.yaml

* 使用opt-viewer.py生成html格式的文件
  * Opt-viewer.py foo.opt.yaml

  * 生成foo.c.html
* 使用web浏览器可以打开html可以查看优化报告

![image-20220731135616237](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207311356332.png)



5.其他参考资料
PA世
2013.2022
1、oneAPI工具包官网
https://www.intel.cn/content/www/cn/zh/developer/tools/oneapi/toolkits.html
2、DevCloud异构编程DPC++学习平台
https://devcloud.intel.com/oneapi/get_started/
3、JumpServer3平台官网
https://docs.jumpserver.org/zh/master/



### 编译器选择

![image-20220731141121403](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207311411490.png)

### 编译器性能对比

https://www.intel.com/content/www/us/en/developer/articles/technical/adoption-of-llvm-complete-icx.html

![image-20220731141401867](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202207311414925.png)
