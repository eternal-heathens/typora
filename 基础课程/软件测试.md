### 黑盒

#### 错误推测法

* 基于经验和直觉推测程序中所有可能存在的各种错误，从而针对性的设计测试用例的方法。（可以在等价类划分边界值法之外补充设计一些测试用例）

#### 因果图法

* 一种利用分析输入的各种组合情况，从而设计测试用例的方法

![image-20200323164147205](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200323164147205.png)

![image-20200323164328239](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200323164328239.png)

![image-20200323164407073](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200323164407073.png)

![image-20200323204507629](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200323204507629.png)

![image-20200323204522914](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200323204522914.png)

### 白盒测试

* 什么是白盒测试
> 对代码本身进行测试![image-20200323171832290](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200323171832290.png)
>

* 优势

> 1. 针对性强，便于快速定位缺陷
> 2. 在函数级别开始测试工作，缺陷修复成本低
> 3. 有助于了解测试的覆盖程度
> 4. 有助于代码优化和缺陷预防

* 不足

> 1. 对测试人员要求高
> 2. 成本高

#### 控制流分析技术

* 判定节点导致结构的复杂

* 该技术的核心是：围绕判定节点为核心设计测试用例，展开测试

* 控制流分析的内容

  > 1.  关注判定节点固有的复杂性
  >
  >    焦点：判定表达式
  >
  >    方法： 逻辑覆盖测试
  >
  > 2. 关注判定结构与循环结构对执行路径产生的影响
  >
  >    焦点：路径
  >
  >    方法：独立路径测试
  >
  > 3. 关注循环结构本身的复杂性
  >
  >    焦点：循环体
  >
  >    方法：基于数据的静态分析



#### 对判定的测试

1. 语句覆盖（点覆盖）：设计测试用例时应保证程序中每一条可执行语句至少应执行一次
2. 判定覆盖（边覆盖）：每个判定节点取得每种可能的结果至少一次
3. 条件覆盖：程序中每个复合判定表达式中，每个简单判定条件的取真和取假情况至少执行一次
4. 判定/条件覆盖：判定覆盖+条件覆盖
5. 条件组合覆盖（真值表）：每个判定节点中，所有简单判定条件的所有可能的取值组合情况至少执行一次
6. 修正的判定/条件覆盖：在条件组合覆盖的基础上，每个简单判定条件都应独立地影响到整个判定表达式的取值。



#### 静态白盒测试

![image-20200401095312838](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200401095312838.png)

![image-20200401095003435](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200401095003435.png)

![image-20200401095407033](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200401095407033.png)

![image-20200401095437369](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200401095437369.png)

![image-20200401095715101](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200401095715101.png)

![image-20200401095835365](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200401095835365.png)

![image-20200401100120353](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200401100120353.png)

![image-20200401100133249](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200401100133249.png)



#### 对路径的测试

* 等价路径的测试：用规则将路径归类，并从每类中随意抽取 ，将需要测试的数据替换成需要测试的路径，等价类测试一样可以用于对程序路径的测试。通过分析程序路径中的处理机制，找到合适的等价划分方法，将所有路径划分为有限个等价路径的集合

> * 三个约束：分而不交 合而不变 类内等价
>
> * 方法关键：如何找到路径划分的规律/如何定义路径的有效等价类和无效等价类



* 基于独立路径的测试

若将向量空间视作向量组，则基就是向量组的极大线性无关组，维数就是向量组的秩。

该向量空间内的任意向量，都可以用这组向量基线性表示
> *  一个约束：线性无关
> * 方法关键：如何确定向量空间的维数
			如何确定这组独立路径 


* 区别：
> 基于等价路径：
> 思想：基于共性分析->数据归类为不同子集
> 过程：对全集划分子集->子集中抽取个体
基于独立路径：
> 思想：基于个性分析-> 独立路径集合

##### 程序图

![image-20200330171440756](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200330171440756.png)

* 程序图：压缩的流程图

>* 忽略数据声明
>* 忽略注释语句
>* 压缩串行语句
>* 压缩循环次数

* 环复杂度
> McCabe复杂性度量
> 是一种定量描述程序结构复杂度的度量模型
> 能够反映判定节点和循环的引入对程序结构以及执行路径数目带来的不利影响

* 直观观察法：

  > 将二维平面分割为封闭为封闭区域和开放区域的个数

* 公式计算法：

> ![image-20200330173027665](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200330173027665.png)



> 构建死循环来形成强连通图：构建一条从出口到入口的路径

* 判定节点法：

  ![image-20200330174113380](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200330174113380.png)

![image-20200330174648106](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200330174648106.png)

* 抽取其他独立路径：（依次覆盖判定节点的不同执行分支）

![image-20200330174718227](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200330174718227.png)

##### 不可行路径
* 需求与现实不一致

#### 从路径看场景测试

![image-20200401093353348](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200401093353348.png)

![image-20200401093416198](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200401093416198.png)

![image-20200401093425978](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200401093425978.png)

![image-20200401093500532](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200401093500532.png)

### 单元测试

#### 软件测试过程

![image-20200408150312269](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200408150312269.png)

#### 代码检查法

1. 检查代码和设计的一致性
2. 代码的可读性及对设计标准的遵循情况
3. 代码逻辑表达的正确性
4. 代码结构的合理性
5. 程序中不安全、不明确和模糊的部分
6. 编程风格

#### 静态结构分析法

![image-20200408150152059](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200408150152059.png)

##### 单元测试具体流程
* 单元测试的主要任务

  > 模块接口测试、模块局部数据结构测试、模块 中所有独立执行路径测试、各种错误处理测试、模块边界条件测试

* 单元测试的执行过程

![image-20200408150618256](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200408150618256.png)

* 单元测试技术和测试数据

> 1. 静态测试
> 2. 白盒测试
> 3. 状态转换测试
> 4. 功能测试和非功能测试

* 测试人员

####  面向对象的单元测试

* (OO Unit Test)

##### 单元测试的内容

* 面向对象的单元:类
* 单元测试:对类的测试

##### 方法的测试

* 类函数成员的正确行为只是类能够实现要求的功能的基础,类成员函数的作用和类之间的服务调用时单元测试无法确定的.引起需要进行面向对象的集成测试
* 面向对象系统中方法的执行是通过**消息来驱动**执行的,要测试类中的方法,必须用一个**驱动程序**(上级)来对被测方法发一条信息以驱动其执行,如果需要调用子模块,则需设计**存根程序**(桩)
* **驱动程序**,**存根程序**,**被测模块**或**方法**组成一个独立的可执行的单元

##### 测试驱动的实现方式

本质:创建被测类的实例和测试这些实例的行为来测试类

> 1. 利用main函数
> 2. 嵌入静态方法
> 3. 设计独立测试类

#### 类测试的延伸

* 继承类、接口类、抽象类等进行测试的方法

##### 继承类

增补原则

1. 如果子类新增了一个或者多个新的操作，就需要增加相应的测试用例
2. 如果子类定义的同名方法覆盖了父类的方法，就需要增加相应的测试用例

* 对于基类我们要全部测试，底层的测试类可以对其父类的测试方法回归

##### 接口类测试

1. 如果接口没有被任何类实现就无需进行测试
2. 如果已被别的类实现，就针对实现该接口的类进行测试

##### 抽象类测试

* 如果要构造抽象类的测试驱动程序首先要继承测试驱动类，并且需要同时继承被测试抽象类，因为该类不能被具体化。但Java采用单继承机制，因此对该抽象类的测试驱动程序就不能同时继承两个类，因此，采取下列方法：

1. 利用Java的内类机制，在抽象类的测试驱动程序内引入内类，让内类实现对被测试抽象类的继承，然后把它作为引用体，这样对内类的测试就等价于对被测试抽象类的测试：

![image-20200415173732328](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200415173732328.png)

2. 如果抽象类被具体类继承，那么，在创建该具体类的测试驱动程序时要继承抽象类的测试驱动程序，在以后的回归测试中，只要执行最底层的测试类，就可以对其父测试类重新执行一次测试，同时将测试结果分别返回。

##### 重载和覆盖（重写）测试

1. 要对类实例方法的所有重载形式分别进行测试。
2. 子类的测试驱动程序在继承父类测试驱动程序的同时，要对重写了父类的同名方法进行测试，而且应该重写对父类的类实例方法的所有重载形式执行一次测试。

---

### 集成测试

#### 集成测试的任务

* 将经过单元测试的模块按设计要求连接起来，组成所规定的软件系统的过程为“集成”。
* 进行集成测试的主要任务时：要求软件系统符合个实际软件结构，发现与接口有关的各种错误。集成测试的主要任务：

> 1. 将各模块连接起来，检查相互调用时，数据经过接口是否丢失
> 2. 将各个子功能结合起来，检查能否达到与其要求的各项功能
> 3. 一个模块的功能是否对另一个模块的功能产生不利的影响
> 4. 全局数据结构是否有问题，会不会被异常修改
> 5. 单个模块的误差积累起来，是否被放大，从而达到不可接受的程度

 ![image-20200415190516353](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200415190516353.png)

#### 集成策略

* 非增量方式

> 按程序结构图将各模块连接起来，把连接后的程序当作一个整体来测试

* 增量方式

> 逐步把下一个要被组装的软件单元或部件，同已测好的额软件部件结合起来测试

![image-20200415191024587](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200415191024587.png)

##### 大爆炸集成

* 将所有通过单元测试的模块全部集成在一起，直接组装成软件系统进行测试

<img src="C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200415191221480.png" alt="image-20200415191221480" style="zoom: 50%;" />

##### 自顶向下测试

 

* 按系统程序结构，将模块沿着控制层次从主程序（根）开始逐渐下推至叶子，实现集成

![image-20200415191458772](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200415191458772.png)

![image-20200415191548691](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200415191548691.png)

![image-20200415191803302](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200415191803302.png)

##### 自底向上集成

* 按系统程序结构，从程序模块结构的最底层模块（叶子）开始，沿控制层次逐渐上行至主程序（根），实现继承

![image-20200415191953364](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200415191953364.png)

![image-20200415192002356](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200415192002356.png)

![image-20200415192016199](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200415192016199.png)

##### 三明治集成 

![image-20200415194831371](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200415194831371.png)

![image-20200415195153877](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200415195153877.png)

#### 集成测试分析

##### 体系结构分析

![image-20200415195408432](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200415195408432.png)

##### 模块分析

![image-20200415195508094](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200415195508094.png)

![image-20200415195457651](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200415195457651.png)

##### 接口分析

![image-20200415195603206](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200415195603206.png)

![image-20200415195615258](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200415195615258.png)

##### 风险分析

![image-20200415195635990](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200415195635990.png)  

#### 继承策略分析

![image-20200415195704432](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200415195704432.png)

#### 集成测试用例设计思路

![image-20200415195723900](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200415195723900.png)

#### 集成测试过程

1.计划阶段

2.设计阶段

3.实现阶段

4.执行阶段

#### 集成测试环境

![image-20200415195842315](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200415195842315.png)

#### 集成测试计划书

* 公司会发

----

### 系统测试

* 将经过集成测试后的软件，作为计算机系统的一个部分，与计算机硬件、某些支持软件等系统元素结合起来，在实际运行环境下对计算机系统进行一系列的测试，保证系统的运行。

 #### 系统测试的任务

##### 功能需求测试

1. 功能测试（主要根据产品规格说明书，来检测被测试的系统是否满足各方面功能的使用需求）

> * 根据需求来细分功能点
> * 根据功能点派生测试需求
> * 根据测试需求设计功能测试用例
> * 逐项执行功能测试用例验证产品

1. 性能测试
2. 恢复测试
3. 安全测试
4. 强度测试
5. 其他限制条件的测试等等

#### 系统测试技术和测试数据

* 黑盒测试
* 真实数据（大小、复杂性）
* 无真实数据使用一个真实数据的拷贝

#### 系统测试人员

* 组长，被邀测试人员

### 验收测试

![image-20200420172754748](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200420172754748.png)

###  α、β测试

![image-20200420172824788](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200420172824788.png)

### 非功能性测试

1. 性能测试

2. 压力测试

3. 容量测试

4. 安全性测试

5. 可靠性测试
6. 恢复性测试

7. 备份测试
8. GUI测试

9. 健壮性测试

   ![image-20200420174132138](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200420174132138.png)

10. 兼容性测试

### 测试用例管理

* 主要关注如何保证测试用例能很快的发现缺陷，能有利于发现最严重的缺陷，一个测试用例的核心内容仅包括输入，预期输出和测试环境。

为什么：1. 数量庞大、需要分级管理起来

​				2.测试用例是连接需求与缺陷的关键纽带

​				3.项目组人员众多，必须保证所有人正确理解测试用例

如何组织：

![image-20200422092558521](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200422092558521.png)

如何报告：

![image-20200422092636317](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200422092636317.png)

* 测试用例报告构成： 

> 1. ID
> 2. 责任人
> 3. 被测对象
> 4. 测试需求
> 5. 预置条件
> 6. 参考文档
> 7. 测试环境
> 8. 输入和预期输出（核心）
> 9. 优先级
> 10. 与其他测试用例的关联

### 缺陷管理

* 是在软件生命周期中识别和管理缺陷的过程（从缺陷的识别到缺陷的解决关闭），确保缺陷被跟踪管理而不丢失（需要跟踪管理工具）

#### 缺陷的属性

* 可重现性、严重性、优先级等属性

> 在有限的成本和压力下，程序员根据标签进行优先处理

##### 可重现性

* 对测试人员报告的缺陷进行重现
* 确认最终出现的结果与报告中缺陷的呈现完全一致
* 在此基础上才有可能对缺陷进行后续的调试、定位和修复工作

* 无法重现的bug是不可以修复的

![image-20200422155257784](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200422155257784.png)

* 如何确保缺陷能重现：

![image-20200422155521101](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200422155521101.png)

##### 严重性

![image-20200422155556033](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200422155556033.png)

##### 优先级

![image-20200422155633283](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200422155633283.png)

##### 可修复性

![image-20200422155714722](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200422155714722.png)

![image-20200422155731348](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200422155731348.png)

* 需在报告中明确体现：

> * 可重现性
> * 严重性
> * 优先级

* 不在报告中体现

> * 可修复性

### 缺陷报告

#### 具体内容

![image-20200422160543850](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200422160543850.png)

#### 如何保证缺陷得到及时提交和解决

![image-20200422160809862](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200422160809862.png)

#### 对缺陷如何跟踪管理

![image-20200422160845633](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200422160845633.png)

> 激活
>
> 分配
>
> 解决
>
> 关闭 

### 软件质量模型

* 反应软件满足明确或隐含需要能力的特性总和

#### 影响质量的特性：

![image-20200422161450458](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200422161450458.png)

#### McCall质量模型

![image-20200422161600490](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200422161600490.png)

![image-20200422161758782](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200422161758782.png)

![image-20200422162312286](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200422162312286.png)

### 软件质量度量

* 产品质量度量

> 产品本质质量
>
> > 平均失效时间
> >
> > ![image-20200422163054620](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200422163054620.png)
> >
> > 缺陷密度
> >
> > * 基于代码行的缺陷率
> >
> > ![image-20200422163111802](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200422163111802.png)![image-20200422163309651](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200422163309651.png)
> >
> > ![image-20200422163355608](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200422163355608.png)
> >
> > ![image-20200422163434766](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200422163434766.png)
> >
> > * 基于功能点的缺陷率
> >
> > ![image-20200422163554617](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200422163554617.png)
> >
> > 代码行方法与功能点方法的比较
> >
> > ![image-20200422163641680](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200422163641680.png)
>
> 用户满意度度量
>
> > 用户问题
> >
> > ![image-20200422163853751](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200422163853751.png)
> >
> > ![image-20200422163922408](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200422163922408.png)
> >
> > * 如何降低PUM
> >
> > ![image-20200422163952889](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200422163952889.png)
> >
> > 缺陷率度量方法 vs 用户问题度量方法
> >
> > ![image-20200422164047200](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200422164047200.png)
> >
> > 用户满意度
> >
> > ![image-20200422164131640](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200422164131640.png)
> >
> > ![image-20200422164203092](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200422164203092.png)

* 过程中质量度量
* 软件维护中质量度量