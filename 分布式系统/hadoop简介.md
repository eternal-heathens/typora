# hadoop简介

一、Hadoop基本概念 1、什么是Hadoop

专业版解释 Hadoop是Apache 公司开发的一款可靠的、可扩展性的、分布式计算的开源软件。以Hadoop分布式文件系统（HDFS）和分布式运算编程框架（MapReduce）为核心，允许在集群服务器上使用简单的编程模型对大数据集进行分布式处理。Hadoop被设计成能够从单台服务器扩展到数以千计的服务器，每台服务器都有本地的计算和存储资源。Hadoop的高可用性并不依赖硬件，其代码库自身就能在应用层侦测并处理硬件故障，因此能基于服务器集群提供高可用性的服务。

通俗版解释 Hadoop是一个开源的框架，可编写和运行分布式应用处理大规模数据，是专为离线和大规模数据分析而设计的。以分布式文件系统（HDFS）和分布式运算编程框架（MapReduce）为核心，其中HDFS实现将文件分布式的存储在多台服务器上，MapReduce则实现了在多台机器上分布式的并行运算。

2、Hadoop用来做什么？

2.1 存储了大规模的数据，我们要干什么呢，当然是分析数据中的价值，Hadoop+MapReduce用于离线大数据的分析挖掘，比如：电商数据的分析挖掘、社交数据的分析挖掘，企业客户关系的分析挖掘，最终的目标就是BI了，提高企业运作效率，实现精准营销，各个垂直领域的推荐系统，发现潜在客户等等。总结了Hadoop的应用场景如下：

大数据量存储：分布式存储（各种云盘，百度，360\~还有云平台均有hadoop应用） 日志处理: Hadoop擅长这个 海量计算: 并行计算 ETL:数据抽取到Oracle、Mysql、DB2、Mongdb及主流数据库 使用HBase做数据分析: 用扩展性应对大量读写操作—Facebook构建了基于HBase的实时数据分析系统 机器学习: 比如Apache Mahout项目（Apache Mahout简介 常见领域：协作筛选、集群、归类） 搜索引擎:hadoop + lucene实现 数据挖掘：目前比较流行的广告推荐 大量地从文件中顺序读。HDFS对顺序读进行了优化，代价是对于随机的访问负载较高。 用户行为特征建模 个性化广告推荐 智能仪器推荐 2.2 例如，Facebook公司运行了世界第二大Hadoop集群，数据超过2PB，每天加入10TB数据，2400个内核，9TB内存，大部分时间硬件满负荷运行。如用户数，网页浏览次数，网站访问时间增常情况，广告活动效果数据，计算用户喜欢人和应用程序。通过分析历史数据，以设计和改进产品，以及管理。

二、Hadoop核心架构

HDFS和MapReduce是Hadoop的两大核心，除此之外Hbase、Hive这两个核心工具也随着Hadoop发展变得越来越重要。同时，在Hadoop2.0后，在HDFS的基础上增加了YARN，是一个资源管理框架，在YARN上既可以放MapReduce，也可以放置其他的计算资源，主要是管理资源的，如CPU，硬盘，内存，网络等。

1、HDFS的体系架构

![Hadoop架构图](https://img-blog.csdn.net/20170919230308379?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMjA4MDUxMDM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

概念 Hadoop分布式文件系统(HDFS)被设计成适合运行在通用硬件上的分布式文件系统。HDFS是一个高度容错性的系统，能提供高吞吐量的数据访问，非常适合大规模数据集上的应用。 数据块（block）：大文件会被分割成多个block进行存储，block大小默认为64MB。每一个block会在多个datanode上存储多份副本，默认是3份。 NameNode：namenode负责管理文件目录、文件和block的对应关系以及block和datanode的对应关系。 DataNode：datanode就负责存储了，当然大部分容错机制都是在datanode上实现的。

HDFS的工作机制 （1）客户把一个文件存入HDFS，其实HDFS会把这个文件切块后，分散存储在N台linux机器系统中（负责存储文件块的角色：data node）<准确来说：切块的行为是由客户端决定的> （2）一旦文件被切块存储，那么，HDFS中就必须有一个机制，来记录用户的每一个文件的切块信息，及每一块的具体存储机器（负责记录块信息的角色是：name node） （3）为了保证数据的安全性，HDFS可以将每一个文件块在集群中存放多个副本（到底存几个副本，是由当时存入该文件的客户端指定的） 综述：一个HDFS系统，由一台运行了namenode的服务器，和N台运行了datanode的服务器组成！ 总结：一个HDFS系统，由一台运行了namenode的服务器，和N台运行了datanode的服务器组成！

2、MapReduce体系架构

![MapReduce分布式程序详细内部运行原理图](https://img-blog.csdn.net/20170924165441654?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMjA4MDUxMDM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

概念 MapReduce是Goodle公司的核心计算模型，它运行在大规模集群的并行计算过程中，并抽象为两个函数：map和reduce。一个MapReduce作业，会把输入的数据切分为若干块，先由map任务（task）一并行的方式处理它们，框架会先对map的输出进行排序，把所有具有相同的key值的value结合在一起，然后输入给reduce任务。接下来由reduce任务进行相应的并行计算。最后把计算的结果输出到HDFS存储起来。需要我们记忆的几个点，mapreduce作业执行涉及4个独立的实体： （1）客户端（client）：编写mapreduce程序，配置作业，提交作业，这就是程序员完成的工作； （2）JobTracker：初始化作业，分配作业，与TaskTracker通信，协调整个作业的执行； （3）TaskTracker：保持与JobTracker的通信，在分配的数据片段上执行Map或Reduce任务，TaskTracker和JobTracker的不同有个很重要的方面，就是在执行任务时候TaskTracker可以有n多个，JobTracker则只会有一个（JobTracker只能有一个就和hdfs里namenode一样存在单点故障，我会在后面的mapreduce的相关问题里讲到这个问题的） （4）HDFS：保存作业的数据、配置信息等等，最后的结果也是保存在hdfs上面 MapReduce运行原理 图片看不清可以右击鼠标，在新连接标签中打开图片。接下来对上边的MapReduce运行原理进行描述。 （1） 输入分片（input split）：在进行map计算之前，MapReduce会根据输入文件计算输入分片，划分输入切片的任务是由job客户端负责的，每个输入分片针对一个map任务，输入分片存储的并非数据本身，而是一个分片长度和一个记录数据的位置的数组，输入分片往往和HDFS的block（块）关系很密切，假如我们设定HDFS的块的大小是64MB，如果我们输入有三个文件，大小分别是3MB、65MB和127MB，那么MapReduce会把3MB文件分为一个输入分片，65mb则是两个输入分片而127mb也是两个输入分片，换句话说我们如果在map计算前做输入分片调整，例如合并小文件，那么就会有5个map任务（对象）将执行，而且每个map执行的数据大小不均，这个也是MapReduce优化计算的一个关键点。接下来，job客户端会把扫描形成的这5个对象放入一个ArrayList中，并序列化成一个文件job.split。最后由MRAppMaster来读这个文件，从而决定起动几个yarn child（map task），从而决定好哪个yarn child读哪一块分片上的文件。 （2） map task(yarn child)阶段：map task先会去调TextInputFormat这个类，拿到一个LineRecordReader对象，通过这个对象反复的去调用next()方法一行行的读取文件，从而产生一对对的kv值，k代表行起始偏移量，v代表行内容。然后，它会去调用我们自己实现的一个类，这个类是继承自Mapper的类，里面有一个map(k,v,context)方法，正好接受一个k,v和一个context，所以它会被next()反复的去调用。同样的，map()也会产生一个或一些kv值，然后context会调用write()方法，把kv序列化后缓存起来（在内存里）。缓存起来后，也就是第四步shuffle阶段要做的事。 （3）combiner阶段：combiner阶段是程序员可以选择的，combiner其实也是一种reduce操作。Combiner是一个本地化的reduce操作，它是map运算的后续操作，主要是在map计算出中间文件前做一个简单的合并重复key值的操作，例如我们对文件里的单词频率做统计，map计算时候如果碰到一个hadoop的单词就会记录为1，但是这篇文章里hadoop可能会出现n多次，那么map输出文件冗余就会很多，因此在reduce计算前对相同的key做一个合并操作，那么文件会变小，这样就提高了宽带的传输效率，毕竟hadoop计算力宽带资源往往是计算的瓶颈也是最为宝贵的资源，但是combiner操作是有风险的，使用它的原则是combiner的输入不会影响到reduce计算的最终输入，例如：如果计算只是求总数，最大值，最小值可以使用combiner，但是做平均值计算使用combiner的话，最终的reduce计算结果就会出错。 （4） shuffle阶段：将map task生成的数据传输给reduce task的过程就是shuffle了，这个是MapReduce优化的重点地方。这里我不讲怎么优化shuffle，讲讲shuffle阶段的原理。shuffle一开始就是map阶段做输出操作，一般MapReduce计算的都是海量数据，map输出时候不可能把所有文件都放到内存操作，因此map写入磁盘的过程十分的复杂，更何况map输出时候要对结果进行排序，内存开销是很大的，map在做输出时候会在内存里开启一个环形内存缓冲区，这个缓冲区专门用来输出的，默认大小是100MB，并且在配置文件里为这个缓冲区设定了一个阀值，默认是0.80（这个大小和阀值都是可以在配置文件里进行配置的），同时map还会为输出操作启动一个守护线程(Spiller)，如果缓冲区的内存达到了80%时候，这个守护线程就会把内容写到磁盘上，这个过程叫spill，另外的20%内存可以继续写入要写进磁盘的数据，写入磁盘和写入内存操作是互不干扰的，如果缓存区被撑满了，那么map就会阻塞写入内存的操作，让写入磁盘操作完成后再继续执行写入内存操作。前面我讲到过map task是一行行读文件的，所以读文件到写入磁盘前还会有个分区和排序操作，这个是在写入磁盘操作时候进行，不是在写入内存时候进行的，如果我们定义了combiner函数，那么排序前还会执行combiner操作。分区会调用一个叫Partitioner的组件，排序则会调用k上的CompareTo()来比大小。 每次spill操作也就是写入磁盘操作时候就会写一个文件，这个文件我们一般叫溢出文件，溢出文件里区号小的在前面，同区中按k有序。等map输出全部做完后，map会合并这些输出文件，这些文件也是分区且有序的。合并的过程中还有一个很细节的点，就是合并后会产生一个分区索引文件，用来指明每个分区的起始点以及它的偏移量。这个过程里还会有一个Partitioner操作，对于这个操作很多人都很迷糊，其实Partitioner操作和map阶段的输入分片很像，一个Partitioner对应一个reduce作业，如果我们MapReduce操作只有一个reduce操作，那么Partitioner就只有一个，如果我们有多个reduce操作，那么Partitioner对应的就会有多个，Partitioner因此就是reduce的输入分片，这个程序员可以编程控制，主要是根据实际key和value的值，根据实际业务类型或者为了更好的reduce负载均衡要求进行，这是提高reduce效率的一个关键所在。到了reduce阶段就是合并map输出文件了，Partitioner会找到对应的map输出文件，然后进行复制操作，复制操作时reduce会开启几个复制线程，这些线程默认个数是5个，程序员也可以在配置文件更改复制线程的个数，这个复制过程和map写入磁盘过程类似，也有阀值和内存大小，阀值一样可以在配置文件里配置，而内存大小是直接使用reduce的tasktracker的内存大小，复制时候reduce还会进行排序操作和合并文件操作，这些操作完了就会进行reduce计算了。 （5） reduce task(yarn child)阶段：map task阶段完成后，它的程序就会退出，那么reduce task要去哪里拿到这些处理完的数据呢？答案是这些文件会被纳入到一个叫NodeManager的web程序的document目录中，reduce task会通过web服务器去把相应区号的文件下载下来，然后将这些文件合并。接下来，就是处理数据。首先，我们会自己实现一个类，这个类是继承自Reducer的类，里面有一个reduce(k,迭代器,context)方法。然后，通过反射构造出一个对象去调用reduce()，里面的参数k和迭代器会分别去创建一个对象，每迭代一次就会按文件里的顺序去读一次，然后把读出来的k传给对象k，然后把v传给对象values里。这个过程也就是从磁盘上把二进制数据读取出来，然后反序列化把数据填入对象的一个过程。这迭代的过程中还会有一个分组比较器（GroupingComparator）去判断迭代的k是否相同，不同则终止迭代。每迭代完一次，context.write()输出一次聚合后的结果，这个聚合的结果会通过TextOutputFormat类里的getRecordWriter()拿到一个RecordWriter对象，通过这个对象去调一个write(k，v)方法，将这些数据通过文件的方式写到HDFS里。 3、Hive的体系架构

概念 Hive是基于Hadoop的一个数据仓库工具(离线)，可以将结构化的数据文件映射为一张数据库表，并提供类SQL查询功能。

为什么使用Hive 直接使用hadoop所面临的问题 ： 人员学习成本太高 项目周期要求太短 MapReduce实现复杂查询逻辑开发难度太大 使用Hive 操作接口采用类SQL语法，提供快速开发的能力。 避免了去写MapReduce，减少开发人员的学习成本。 功能扩展很方便。

4、Hbase数据管理

概念 Hbase就是Hadoop database，可以提供数据的实时随机读写。Hbase与Mysql、Oralce、db2、SQLserver等关系型数据库不同，它是一个NoSQL数据库（非关系型数据库） 。 RowKey：是Byte array，是表中每条记录的“主键”，方便快速查找，Rowkey的设计非常重要。 Column Family：列族，拥有一个名称(string)，包含一个或者多个相关列 Column：属于某一个columnfamily，familyName:columnName，每条记录可动态添加 Version Number：类型为Long，默认值是系统时间戳，可由用户自定义 Value(Cell)：Byte array

Hbase的表模型与关系型数据库的表模型不同： Hbase的表没有固定的字段定义 Hbase的表中每行存储的都是一些key-value对 Hbase的表中有列族的划分，用户可以指定将哪些kv插入哪个列族 Hbase的表在物理存储上，是按照列族来分割的，不同列族的数据一定存储在不同的文件中 Hbase的表中的每一行都固定有一个行键，而且每一行的行键在表中不能重复 Hbase中的数据，包含行键，包含key，包含value，都是byte\[ ]类型，hbase不负责为用户维护数据类型 HBASE对事务的支持很差

Hbase使用场景 大数据量存储，大数据量高并发操作 需要对数据随机读写操作 读写访问均是非常简单的操作

5、Yarn的体系架构

概念 Yarn是一个分布式程序的运行调度平台，是hadoop2.X版本后新加入的模块。 yarn中有两大核心角色： （1）Resource Manager 接受用户提交的分布式计算程序，并为其划分资源。 管理、监控各个Node Manager上的资源情况，以便于均衡负载。 （2）Node Manager 管理它所在机器的运算资源（cpu + 内存）。 负责接受Resource Manager分配的任务，创建容器、回收资源。

Yarn的工作机制

（1）用户向YARN中提交应用程序，其中包括ApplicationMaster程序、启动ApplicationMaster的命令、用户程序等。 （2）ResourceManager为该应用程序分配第一个Container，并与对应的NodeManager通信，要求它在这个Container中启动应用程序的ApplicationMaster。 （3）ApplicationMaster首先向ResourceManager注册，这样用户可以直接通过ResourceManager查看应用程序的运行状态，然后它将为各个任务申请资源，并监控它的运行状态，直到运行结束，即重复步骤4\~7。 （4）ApplicationMaster采用轮询的方式通过RPC协议向ResourceManager申请和领取资源。 （5）一旦ApplicationMaster申请到资源后，便与对应的NodeManager通信，要求它启动任务。 （6）NodeManager为任务设置好运行环境（包括环境变量、JAR包、二进制程序等）后，将任务启动命令写到一个脚本中，并通过运行该脚本启动任务。 （7）各个任务通过某个RPC协议向ApplicationMaster汇报自己的状态和进度，以让ApplicationMaster随时掌握各个任务的运行状态，从而可以在任务失败时重新启动任务。 在应用程序运行过程中，用户可随时通过RPC向ApplicationMaster查询应用程序的当前运行状态。 （8）应用程序运行完成后，ApplicationMaster向ResourceManager注销并关闭自己。
