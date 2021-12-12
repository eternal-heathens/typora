# zookeeper

## 概述

Zookeeper 是一个开源的分布式的，为分布式应用提供**协调服务**的Apache项目。

### 工作机制

Zookeeper从从设计模式来理解：是一个基于观察者模式设计的分布式管理框架，它存储一些关键数据，然后接受观察者的注册，一旦数据的状态发生变化，就负责通知再Zookeeper上注册了的观察者做出相应的反应。

Zookeeper相对于服务器是观察者，相对于客户端是被观察者

Zookeeper = 文件系统 + 通知机制

### 特点

1. Zookeeper：一个领导者（Leader），多个跟随者（Follower）组成的集群
2. **集群中只要有半数以上节点存活，Zookeeper集群就能存活**
3. 全局数据一致：每个Server保存一份相同的数据副本，Client无论连接到哪个Server，数据都是一致的。
4. 更新请求顺序：Zo'okeeper符合顺序一致性，所有到达follower的写请求都要转交给Leader，经过Leader的处理后，其他follower看到的这些请求顺序便是一致的，来自同一个Client的更新请求按其发送顺序依次执行。
5. 数据更新原子性：一次数据更新要么成功，要么失败。
6. 实时性：在一定时间范围内：Client能读到最新数据。

### 数据结构

与Unix文件系统很类似，整体上可以看成一棵树，**每个节点称作一个ZNode，每一个ZNode默认存储1MB的数据，每个ZNode可以通过其路径唯一标识。**

![image-20210414214637015](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F%5Czookeeper.assets%5Cimage-20210414214637015.png)

### 应用场景

​ 提供的服务包括：统一命名服务、统一配置管理、统一集群管理、服务器节点动态上下线、软负载均衡等。

#### 统一命名服务

对应用/服务进行统一命名，便于识别。

如：客户端请求到统一域名，然后在Zookeeper分配主机IP转发请求。

#### 统一配置管理

分布式环境中，统一配置管理的原因：

1. 保持所有节点的配置信息是一致的
2. 对配置文件的修改，希望快速同步到各个节点上

#### 统一集群管理

#### 服务器动态上下线

#### 软负载均衡

在Zookeeper中记录每台服务器的访问数，让访问数最少的服务器去处理最新的客户端请求

## Zookeeper安装

#### 下载并解压

1. http://mirror.bit.edu.cn/apache/zookeeper/zookeeper-3.5.9/

**上面的镜像站需要VPN才能访问，Linux需要自己配VPN或者在Windows下载再从CRT拉过去,一定要下bin的，不然启动不了**

1. 解压

```shell
tar -zxvf zookeeper-3.4.13.bin.tar.gz
```

1. **因为zk是用java编写的，因此需要jre环境调库运行**

#### 编辑配置文件

1．tickTime =2000：通信心跳数，Zookeeper服务器与客户端心跳时间，单位毫秒

Zookeeper使用的基本时间，服务器之间或客户端与服务器之间维持心跳的时间间隔，也就是每个tickTime时间就会发送一个心跳，时间单位为毫秒。

它用于心跳机制，并且设置最小的session超时时间为两倍心跳时间。(session的最小超时时间是2\*tickTime)

2．initLimit =10：LF初始通信时限

集群中的Follower跟随者服务器与Leader领导者服务器之间初始连接时能容忍的最多心跳数（tickTime的数量），用它来限定集群中的Zookeeper服务器连接到Leader的时限。

3．syncLimit =5：LF同步通信时限

集群中Leader与Follower之间的最大响应时间单位，假如响应超过syncLimit \* tickTime，Leader认为Follwer死掉，从服务器列表中删除Follwer。

4．dataDir：数据文件目录+数据持久化路径

主要用于保存Zookeeper中的数据。

5．clientPort =2181：客户端连接端口

监听客户端连接的端口。

1. electionAlg：（1-3）

> 1. LeaderElection算法
> 2. AuthFastLeaderElection算法
> 3. FastLeaderElection算法（默认）

## 选举机制

1. 半数机制：集群中半数以上机器存活，集群可用。所以Zookeeper适合安装**奇数台服务器**。（多一台半数的时候也不能用，浪费资源）
2. 选举机制中的概念：

### 名词意义

> serverId（服务器ID SID)：用来唯一标识一台zk集群中的机器，每台机器不能重复，和myid一致
>
> zxid（最新事务ID 即LastLoggedZxid）：事务ID，用来标识一次服务器状态的更新，根据处理逻辑的不同而不同，一般值越大说明数据越新
>
> epoch（逻辑时钟 即PeerEpoch）：每个Leader任期的代号，没有Leader的时候会用逻辑时钟来代替

### 投票内容

> 选举人ID
>
> 选举人数据ID
>
> 选举人选举轮数
>
> 选举人选举状态
>
> 推举人ID
>
> 推举人选举轮数

### 选举类型

#### 服务器启动时期的 Leader 选举

在集群初始化阶段，当有一台服务器Server1启动时，其单独无法进行和完成Leader选举，当第二台服务器Server2启动后，此时两台机器可以相互通信，每台机器都试图找到Leader，于是进入Leader选举过程。选举过程如下：

**(1) 每个Server启动时，都会将自己设定成Looking状态，进行一次选举，并在没选出Leader时投票给自己**。由于是初始情况，Server1和Server2都会将自己作为Leader服务器来进行投票，**每次投票会包含所推举的服务器的myid和ZXID**，使用(myid, ZXID)来表示，此时Server1的投票为(1, 0)，Server2的投票为(2, 0)，然后各自将这个投票发给集群中其他机器。

**(2) 接受来自各个服务器的投票**。集群的每个服务器收到投票后，**首先判断该投票的有效性，如检查是否是本轮投票、是否来自LOOKING状态的服务器**。

**(3) 处理投票**。针对每一个投票，服务器都需要将别人的投票和自己的投票进行PK，PK规则如下（**在运行期间重新选举也符合一下规则，启动时期一般ZXID和Eporch都是一致的默认值**）

**1、Eporch大的胜出**

**2、优先检查ZXID。ZXID比较大的服务器优先作为Leader**。

**3、如果ZXID相同，那么就比较myid。myid较大的服务器作为Leader服务器**。

对于Server1而言，它的投票是(1, 0)，接收Server2的投票为(2, 0)，首先会比较两者的ZXID，均为0，再比较myid，此时Server2的myid最大，于是更新自己的投票为(2, 0)，然后重新投票，对于Server2而言，其无须更新自己的投票，只是再次向集群中所有机器发出上一次投票信息即可。

**(4) 统计投票**。每次投票后，服务器都会统计投票信息，判断是否已经有过半机器接受到相同的投票信息，若**是超过半数，此时便认为已经选出了Leader，此时投票最多的服务器改为Leading状态，其他已投票的服务器改为Following状态**，后续新启动的服务器仍会投票给自己，但**因为其他服务器不是Looking状态了，便会直接统计投票信息而不是交换投票。**

**(5) 改变服务器状态**。一旦确定了Leader，每个服务器就会更新自己的状态，如果是Follower，那么就变更为FOLLOWING，如果是Leader，就变更为LEADING。

![image-20210718062858816](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F%5Czookeeper.assets%5Cimage-20210718062858816.png)

#### 服务器运行时期的 Leader 选举

在Zookeeper运行期间，即便当有非Leader服务器宕机或新加入，此时也不会影响Leader，但是**一旦Leader服务器挂了，那么整个集群将暂停对外服务，进入新一轮Leader选举**，其过程和启动时期的Leader选举过程基本一致。假设正在运行的有Server1、Server2、Server3三台服务器，当前Leader是Server2，若某一时刻Leader挂了，此时便开始Leader选举。选举过程如下：

**(1)变更状态**。Leader挂后，余下的非Observer服务器都会将自己的服务器状态变更为LOOKING，然后开始进入Leader选举流程。

**(2)每个Server会发出一个投票**。在这个过程中，需要生成投票信息(myid,ZXID)每个服务器上的ZXID可能不同，我们假定Server1的ZXID为123，而Server3的ZXID为122；在第一轮投票中，Server1和Server3都会投自己，产生投票(1, 123)，(3, 122)，然后各自将投票发送给集群中所有机器。

**(3)接收来自各个服务器的投票**。与启动时过程相同。

**(4)处理投票**。与启动时过程相同，此时，Server1将会成为Leader。

**(5)统计投票**。与启动时过程相同。

**(6)改变服务器的状态**。与启动时过程相同。

![image-20210718062909156](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F%5Czookeeper.assets%5Cimage-20210718062909156.png)

### Leader选举算法分析

在3.4.0后的Zookeeper的版本只保留了TCP版本的FastLeaderElection选举算法。当一台机器进入Leader选举时，当前集群可能会处于以下两种状态

当ZooKeeper集群中的一台服务器出现以下两种情况之一时，就会开始进入Leader选举。

* 服务器初始化启动。
* 服务器运行期间无法和Leader保持连接。

而当一台机器进入Leader选举流程时，当前集群也可能会处于以下两种状态。

* 集群中本来就巳经存在一个Leader。
* 集群中确实不存在Leader。

#### leader已存在

我们先来看第一种巳经存在Leader的情况。此种情况一般都是某台机器启动得较晚，在其启动之前，集群已经在正常工作，对这种情况，该机器试图去选举Leader时，会被告知当前服务器的Leader信息，对于该机器而言，仅仅需要和Leader机器建立起连接，并进行状态同步即可。

#### Leader不存在的情况下

常有两种情况会导致集群中不存在Leader

* 一种情况是在整个服务器刚刚初始化启动时，此时尚未产生一台Leader服务器。
* 另一种情况就是在运行期间当前Leader所在的服务器挂了。

无论是哪种情况，此时集群中的所有机器都处于一种试图选举出一个Leader的状态，我们把这种状态称为“LOOKING”，意思是说正在寻找Leader。当一台服务器处于LOOKING状态的时候，那么它就会向集群中所有其他机器发送消息，我们称这个消息为“投票”。

**在这个投票消息中包含两个最基本的信息**：

**所推举的服务器的SID和ZXID**,**分别表示了被推举服务器的唯一标识和事务ID**。

下文中我们将以“（SID, ZXID)”这样的形式 来标识一次投票信息。举例来说，如果当前服务器要推举SID为1、ZXID为8的服务器成为Leader,那么它的这次投票信息可以表示为（1，8）。

我们假设ZooKeeper由5台机器组成，SID分別为1、2、3、4和5, ZXID分别为9、9、9、8和8,并且此时SID为2的机器是Leader服务器。某一时刻，1和2所在的机器出现故障，因此集群开始进行Leader选举。

**在第一次投票的时候，由于还无法检测到集群中其他机器的状态信息，因此每台机器都是将自己作为被推举的对象来进行投票**，于是SID为3、4和5的机器，投票情况分别为：（3, 9）、（4, 8）和（5, 8）。

**变更投票**

集群中的每台机器发出自己的投票后，也会接收到来自集群中其他机器的投票。每台机器都会根据一定的规则，来处理收到的其他机器的投票，并以此来决定是否需要变更自己的投票。这个规则也成为了整个Leader选举算法的核心所在。为了便于描述，我们首先定义一些术语。

* vote\_sid：接收到的投票中所推举Leader服务器的SID。
* vote\_zxid：接收到的投票中所推举Leader服务器的ZXID，既事物ID。
* self\_sid:：当前服务器自己的SID。
* self\_zxid：当前服务器自己的ZXID。

每次对于收到的投票的处理，都是一个对（votesid，votezxid)和（selfsid，selfzxid) 对比的过程，假设Epoch相同的情况下。

规则如下：

\*\*1、\*\*如果votezxid大于自己的selfzxid，就认可当前收到的投票，并再次将该投票发送出去。

\*\*2、\*\*如果votezxid小于自己的selfzxid,那么就坚持自己的投票，不做任何变更。

\*\*3、\*\*如果votezxid等于自己的selfzxid,那么就对比两者的SID。如果votesid大于selfsid，那么就认可当前接收到的投票，并再次将该投票发送出去。

\*\*4、\*\*如果votezxid等于自己的selfzxid，并且votesid小于selfsid，那么同样坚持自己的投票，不做变更。

根据上面这个规则，我们结合图来分折上面提到的5台机器组成的ZooKeeper集群的投票变更过程。

![img](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F%5Czookeeper.assets%5C1708987-20190608231027312-807663468.png)

每台机器都把投票发出后，同时也会接收到来自另外两台机器的投票。

对干Server3来说，它接收到了（4, 8）和（5, 8）两个投票，对比后，由子自己的ZXID要大于接收到的两个投票，因此不需要做任何变更。

对于Server4来说，它接收到了（3, 9）和（5, 8）两个投票，对比后，由于（3, 9）这个投票的ZXID大于自己，因此需要变更投票为（3, 9）,然后继续将这个投票发送给另外两台机器。

同样，对TServer5来说，它接收到了（3, 9）和（4, 8）两个投票，对比后，由于（3, 9）这个投票的ZXID大于自己，因此需要变更投票为（3, 9），然后继续将这个投票发送给另外两台机器。

**确定Leader**

经过这第二次投票后，集群中的每台机器都会再次收到其他机器的投票，然后开始统计投票。如果一台机器收到了超过半数的相同的投票，那么这个投票对应的SID机器即为 Leader。

如上图所示的Leader选举例子中，因为ZooKeeper集群的总机器数为5台，那么 quorum = ( 5/2 + 1 ) = 3

也就是说，只要收到3个或3个以上（含当前服务器自身在内）一致的投票即可。在这里，Server3、Server4 和Server5 都投票（3, 9），因此确定了 Server3为 Leader。

### Leader选举实现细节

**1、服务器状态**

服务器具有四种状态，分别是LOOKING、FOLLOWING、LEADING、OBSERVING。

* **LOOKING**：寻找Leader状态。当服务器处于该状态时，它会认为当前集群中没有Leader，因此需要进入Leader选举状态。
* **FOLLOWING**：跟随者状态。表明当前服务器角色是Follower。
* **LEADING**：领导者状态。表明当前服务器角色是Leader。
* **OBSERVING**：观察者状态。表明当前服务器角色是Observer。

**2、投票数据结构**

每个投票中包含了两个最基本的信息，所推举服务器的SID和ZXID，投票（Vote）在Zookeeper中包含字段如下。

* **id**：被推举的Leader的SID。
* **zxid**：被推举的Leader事务ID。
* **electionEpoch**：逻辑时钟，用来判断多个投票是否在同一轮选举周期中，该值在服务端是一个自增序列，每次进入新一轮的投票后，都会对该值进行加1操作。
* **peerEpoch**：被推举的Leader的epoch。
* **state**：当前服务器的状态。

**3、QuorumCnxManager：网络I/O**

每台服务器在启动的过程中，会启动一个QuorumPeerManager，负责各台服务器之间的底层Leader选举过程中的网络通信。

**(1)消息队列**。QuorumCnxManager内部维护了一系列的队列，用来保存接收到的、待发送的消息以及消息的发送器，除接收队列以外，其他队列都按照SID分组形成队列集合，如一个集群中除了自身还有3台机器，那么就会为这3台机器分别创建一个发送队列，互不干扰。

* **recvQueue**：消息接收队列，用于存放那些从其他服务器接收到的消息。
* **queueSendMap**：消息发送队列，用于保存那些待发送的消息，按照SID进行分组。
* **·senderWorkerMap**：发送器集合，每个SenderWorker消息发送器，都对应一台远程Zookeeper服务器，负责消息的发送，也按照SID进行分组。
* **lastMessageSent**：最近发送过的消息，为每个SID保留最近发送过的一个消息。

**(2)建立连接**。为了能够相互投票，Zookeeper集群中的所有机器都需要两两建立起网络连接。QuorumCnxManager在启动时会创建一个ServerSocket来监听Leader选举的通信端口(默认为3888)。开启监听后，Zookeeper能够不断地接收到来自其他服务器的创建连接请求，在接收到其他服务器的TCP连接请求时，会进行处理。**为了避免两台机器之间重复地创建TCP连接，Zookeeper只允许SID大的服务器主动和其他机器建立连接，否则断开连接。在接收到创建连接请求后，服务器通过对比自己和远程服务器的SID值来判断是否接收连接请求，如果当前服务器发现自己的SID更大，那么会断开当前连接，然后自己主动和远程服务器建立连接。(是否有作用存疑)一旦连接建立，就会根据远程服务器的SID来创建相应的消息发送器SendWorker和消息接收器RecvWorker，并启动**。

**(3)消息接收与消息发送**。

**消息接收**：由消息接收器RecvWorker负责，**由于Zookeeper为每个远程服务器都分配一个单独的RecvWorker**，因此，每个RecvWorker只需要不断地从这个TCP连接中读取消息，并将其保存到recvQueue队列中。

**消息发送**：由于Zookeeper为每个远程服务器都分配一个单独的SendWorker，因此，每个SendWorker只需要不断地从对应的消息发送队列中获取出一个消息发送即可，同时将这个消息放入lastMessageSent中。**在SendWorker中，一旦Zookeeper发现针对当前服务器的消息发送队列为空，那么此时需要从lastMessageSent中取出一个最近发送过的消息来进行再次发送，这是为了解决接收方在消息接收前或者接收到消息后服务器挂了，导致消息尚未被正确处理。同时，Zookeeper能够保证接收方在处理消息时，会对重复消息进行正确的处理**。

**4、FastLeaderElection：选举算法核心**

* **外部投票**：特指其他服务器发来的投票。
* **内部投票**：服务器自身当前的投票。
* **选举轮次**：Zookeeper服务器Leader选举的轮次，即logicalclock。
* **PK**：对内部投票和外部投票进行对比来确定是否需要变更内部投票。

**(1) 选票管理**

* **sendqueue**：选票发送队列，用于保存待发送的选票。
* **recvqueue**：选票接收队列，用于保存接收到的外部投票。
* **WorkerReceiver**：选票接收器。其会不断地从QuorumCnxManager中获取其他服务器发来的选举消息，并将其转换成一个选票，然后保存到recvqueue中，在选票接收过程中，如果发现该外部选票的选举轮次小于当前服务器的，那么忽略该外部投票，同时立即发送自己的内部投票。
* **WorkerSender**：选票发送器，不断地从sendqueue中获取待发送的选票，并将其传递到底层QuorumCnxManager中。

**(2) 算法核心**

![img](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F%5Czookeeper.assets%5C1708987-20190608231015263-1796449651.png)

上图展示了FastLeaderElection模块是如何与底层网络I/O进行交互的。Leader选举的基本流程如下

**1、自增选举轮次**。Zookeeper规定所有有效的投票都必须在同一轮次中，在开始新一轮投票时，会首先对logicalclock进行自增操作。

**2、初始化选票**。在开始进行新一轮投票之前，每个服务器都会初始化自身的选票，并且在初始化阶段，每台服务器都会将自己推举为Leader。

**3、发送初始化选票**。完成选票的初始化后，服务器就会发起第一次投票。Zookeeper会将刚刚初始化好的选票放入sendqueue中，由发送器WorkerSender负责发送出去。

**4、接收外部投票**。每台服务器会不断地从recvqueue队列中获取外部选票。如果服务器发现无法获取到任何外部投票，那么就会立即确认自己是否和集群中其他服务器保持着有效的连接，如果没有连接，则马上建立连接，如果已经建立了连接，则再次发送自己当前的内部投票。

**5、判断选举轮次**。在发送完初始化选票之后，接着开始处理外部投票。在处理外部投票时，会根据选举轮次来进行不同的处理。

* **外部投票的选举轮次大于内部投票**。若服务器自身的选举轮次落后于该外部投票对应服务器的选举轮次，那么就会立即更新自己的选举轮次(logicalclock)，并且清空所有已经收到的投票，然后使用初始化的投票来进行PK以确定是否变更内部投票。最终再将内部投票发送出去。
* **外部投票的选举轮次小于内部投票**。若服务器接收的外选票的选举轮次落后于自身的选举轮次，那么Zookeeper就会直接忽略该外部投票，不做任何处理，并返回步骤4。
* **外部投票的选举轮次等于内部投票**。此时可以开始进行选票PK。

**6、选票PK**。在进行选票PK时，符合任意一个条件就需要变更投票。

* 若外部投票中推举的Leader服务器的选举轮次大于内部投票，那么需要变更投票。
* 若选举轮次一致，那么就对比两者的ZXID，若外部投票的ZXID大，那么需要变更投票。
* 若两者的ZXID一致，那么就对比两者的SID，若外部投票的SID大，那么就需要变更投票。

**7、变更投票**。经过PK后，若确定了外部投票优于内部投票，那么就变更投票，即使用外部投票的选票信息来覆盖内部投票，变更完成后，再次将这个变更后的内部投票发送出去。

**8、选票归档**。无论是否变更了投票，都会将刚刚收到的那份外部投票放入选票集合recvset中进行归档。recvset用于记录当前服务器在本轮次的Leader选举中收到的所有外部投票（按照服务队的SID区别，如{(1, vote1), (2, vote2)...}）。

**9、统计投票**。完成选票归档后，就可以开始统计投票，统计投票是为了统计集群中是否已经有过半的服务器认可了当前的内部投票，如果确定已经有过半服务器认可了该投票，则终止投票。否则返回步骤4。

**10、更新服务器状态**。若已经确定可以终止投票，那么就开始更新服务器状态，服务器首选判断当前被过半服务器认可的投票所对应的Leader服务器是否是自己，若是自己，则将自己的服务器状态更新为LEADING，若不是，则根据具体情况来确定自己是FOLLOWING或是OBSERVING。

**以上10个步骤就是FastLeaderElection的核心，其中步骤4-9会经过几轮循环，直到有Leader选举产生**。#

### 节点类型

1. 持久化目录节点：客户端与ZK断开连接后，该节点**仍旧存在**
2. 持久化顺序编号目录节点：与上同并且在节点名称后进行顺序编号
3. 临时目录节点：客户端与ZK断开连接后，该节点**删除**
4. 临时顺序编号目录节点：与3同并且在节点名称后进行顺序编号

## 集群安装使用

```shell
# 启动3个zookeeper服务
zkServer start /usr/local/etc/zookeeper/zoo1.cfg
zkServer start /usr/local/etc/zookeeper/zoo2.cfg
zkServer start /usr/local/etc/zookeeper/zoo3.cfg

# 查看每个zookeeper对应的角色
zkServer status /usr/local/etc/zookeeper/zoo1.cfg
zkServer status /usr/local/etc/zookeeper/zoo2.cfg
zkServer status /usr/local/etc/zookeeper/zoo3.cfg

# 停止zookeeper服务
zkServer stop /usr/local/etc/zookeeper/zoo1.cfg

# 连接服务器 zkCli -server IP:PORT
zkCli -server 127.0.0.1:2181

# 帮助命令
help

# 获取根节点列表，默认会有一个zookeeper节点，节点列表就像linux目录一样，根节点为/
ls /

# 创建znode并关联值, create /节点名称 “值”
create /myZnode "my znode"

# 获取znode
get /myZnode

# 修改znode
set /myZnode "my znode value string"

# 删除单个znode
delete /myZnode

# 递归删除(删除myZnode的子节点和myZnode节点)
rmr /myZnode
```

zoo.cfg的server配置参数解读

server.A=B:C:D

**A**是一个数字，表示这个是第几号服务器；

集群模式下配置一个文件myid，这个文件在dataDir目录下，这个文件里面有一个数据就是A的值，Zookeeper启动时读取此文件，拿到里面的数据与zoo.cfg里面的配置信息比较从而判断到底是哪个server。

**B**是这个服务器的ip地址；

**C**是这个服务器与集群中的Leader服务器交换信息的端口；

**D**是万一集群中的Leader服务器挂了，需要一个端口来重新进行选举，选出一个新的Leader，而这个端口就是用来执行选举时服务器相互通信的端口。

#### Stat 结构体

1）czxid-创建节点的事务zxid

每次修改ZooKeeper状态都会收到一个zxid形式的时间戳，也就是ZooKeeper事务ID。

事务ID是ZooKeeper中所有修改总的次序。每个修改都有唯一的zxid，如果zxid1小于zxid2，那么zxid1在zxid2之前发生。

2）ctime - znode被创建的毫秒数(从1970年开始)

3）mzxid - znode最后更新的事务zxid

4）mtime - znode最后修改的毫秒数(从1970年开始)

5）pZxid-znode最后更新的子节点zxid

6）cversion - znode子节点变化号，znode子节点修改次数

7）dataversion - znode数据变化号

8）aclVersion - znode访问控制列表的变化号

9）ephemeralOwner- 如果是临时节点，这个是znode拥有者的session id。如果不是临时节点则是0。

10）dataLength- znode的数据长度

11）numChildren - znode子节点数量

#### 监听器原理

1. 由zk提供的./zkCli.sh或者spring集成自己实现的Zookeeper类创建客户端（若是spring需要保持main线程别结束，设置如sleep）
2. 通过connect线程将注册的监听事件发送给zk
3. 在zk的注册监听器列表中将注册的监听事件添加到列表中
4. zk监听到注册的被监听的服务器有数据/路径变化，则将消息发送给listener
5. Listener线程调用其process()方法

常见的监听器:1)监听节点数据的变化 get path\[watch] 2）ls path\[watch]

#### Spring集成监听器使用流程

服务器端：

1. 创建到zk集群的服务端连接new Zk(connectString,sessionTimeout,Watcher)
2. 将服务注册到zk集群中的某一服务器并监听路径zk.create(Nodepath,zkhostname.byte(),ids,mode)
3. 启动业务服务

客户器端：

1. 创建到zk集群的客户端连接new Zk(connectString,sessionTimeout,Watcher)
2. 客户端获取zk服务器节点信息，并依据业务功能获取服务器在zk中保存的信息，决定如何处理，也通过开关决定是否使用watcher监听（监听的process实现监听到变化时如何处理），zk.getChildren(parentNode,watcherBollean)
3. 启动业务服务

dubbo使得客户端和服务端只需知道注册中心的地址来消费/注册需要的服务，因为消息会经由不同的中间件加工的资源而来，不同中间件的数据传输有不同的要求，而就需要提供不同的接口给不同的中间件使用，而由ZK去记录具体某个服务与其服务对应的ip位置，并使用负载均衡

## 文件作用

1. zkData中myid 是zk服务的ID；version-2是服务保存节点数据等的文件；pid是进程号，zkClient通过这个去寻找服务

## 分布式锁

基本思路：

![image-20210715094417091](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F%5CZookeeper.assets%5Cimage-20210715094417091.png)

### 案例代码实现

* 先用标签写逻辑，然后分方法块，再具体实现

```java
package com.atguigu.lock2;
import org.apache.zookeeper.*;
import org.apache.zookeeper.data.Stat;
import java.io.IOException;
import java.util.Collections;
import java.util.List;
import java.util.concurrent.CountDownLatch;
public class DistributedLock {
    // zookeeper server 列表
    private String connectString =
            "hadoop102:2181,hadoop103:2181,hadoop104:2181";
    // 超时时间
    private int sessionTimeout = 2000;
    private ZooKeeper zk;
    private String rootNode = "locks";
    private String subNode = "seq-";
    // 当前 client 等待的子节点
    private String waitPath;
    //ZooKeeper 连接
    private CountDownLatch connectLatch = new CountDownLatch(1);
    //ZooKeeper 将线程挂起，防止程序直接结束，使得监听器能一直起作用
    private CountDownLatch waitLatch = new CountDownLatch(1);
    // 当前 client 创建的子节点
    private String currentNode;
    // 和 zk 服务建立连接，并创建根节点
    public DistributedLock() throws IOException,
            InterruptedException, KeeperException {
        zk = new ZooKeeper(connectString, sessionTimeout, new
                Watcher() {
                    @Override
                    public void process(WatchedEvent event) {
                        // 连接建立时, 打开 latch, 唤醒 wait 在该 latch 上的线程
                        if (event.getState() ==
                                Event.KeeperState.SyncConnected) {
                            connectLatch.countDown();
                        }
                        // 发生了 waitPath 的删除事件
                        if (event.getType() ==
                                Event.EventType.NodeDeleted && event.getPath().equals(waitPath))
                        {
                            waitLatch.countDown();
                        }
                    }
                });
        // 等待连接建立
        connectLatch.await();
        //获取根节点状态
        Stat stat = zk.exists("/" + rootNode, false);
        //如果根节点不存在，则创建根节点，根节点类型为永久节点
        if (stat == null) {
            System.out.println("根节点不存在");
            zk.create("/" + rootNode, new byte[0],
                    ZooDefs.Ids.OPEN_ACL_UNSAFE, CreateMode.PERSISTENT);
        }
    }
    // 加锁方法
    public void zkLock() {
        try {
            //在根节点下创建临时顺序节点，返回值为创建的节点路径
            currentNode = zk.create("/" + rootNode + "/" + subNode,
                    null, ZooDefs.Ids.OPEN_ACL_UNSAFE,
                    CreateMode.EPHEMERAL_SEQUENTIAL);
            // wait 一小会, 让结果更清晰一些
            Thread.sleep(10);
            // 注意, 没有必要监听"/locks"的子节点的变化情况
            List<String> childrenNodes = zk.getChildren("/" +
                    rootNode, false);
            // 列表中只有一个子节点, 那肯定就是 currentNode , 说明
            client 获得锁
            if (childrenNodes.size() == 1) {
                return;
            } else {
                //对根节点下的所有临时顺序节点进行从小到大排序
                Collections.sort(childrenNodes);
                //当前节点名称
                String thisNode = currentNode.substring(("/" +
                        rootNode + "/").length());
                //获取当前节点的位置
                int index = childrenNodes.indexOf(thisNode);
                if (index == -1) {
                    System.out.println("数据异常");
                } else if (index == 0) {
                    // index == 0, 说明 thisNode 在列表中最小, 当前client 获得锁
                    
                    return;
                } else {
                    // 获得排名比 currentNode 前 1 位的节点
                    this.waitPath = "/" + rootNode + "/" +
                            childrenNodes.get(index - 1);
                    // 在 waitPath 上注册监听器, 当 waitPath 被删除时, zookeeper 会回调监听器的 process 方法
                    zk.getData(waitPath, true, new Stat());
                    //进入等待锁状态
                    waitLatch.await();
                    return;
                }
            }
        } catch (KeeperException e) {
            e.printStackTrace();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
    // 解锁方法
    public void zkUnlock() {
        try {
            zk.delete(this.currentNode, -1);
        } catch (InterruptedException | KeeperException e) {
            e.printStackTrace();
        }
    } }
```

### 测试

```java
package com.atguigu.lock2;
import org.apache.zookeeper.KeeperException;
import java.io.IOException;
public class DistributedLockTest {
    public static void main(String[] args) throws
            InterruptedException, IOException, KeeperException {
        // 创建分布式锁 1
        final DistributedLock lock1 = new DistributedLock();
        // 创建分布式锁 2
        final DistributedLock lock2 = new DistributedLock();
        new Thread(new Runnable() {
            @Override
            public void run() {
                // 获取锁对象
                try {
                    lock1.zkLock();
                    System.out.println("线程 1 获取锁");
                    Thread.sleep(5 * 1000);
                    lock1.zkUnlock();
                    System.out.println("线程 1 释放锁");
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        }).start();
        new Thread(new Runnable() {
            @Override
            public void run() {
                // 获取锁对象
                try {
                    lock2.zkLock();
                    System.out.println("线程 2 获取锁");
                    Thread.sleep(5 * 1000);
                    lock2.zkUnlock();
                    System.out.println("线程 2 释放锁");
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        }).start();
    } }
```

### **Curator** **框架实现分布式锁案例**

**1**\*\*）原生的\*\* **Java API** **开发存在的问题**

（1）会话连接与后续的操作是异步的，需要自己去处理。比如使用 CountDownLatch

（2）Watch 需要重复注册，不然就不能生效

（3）开发的复杂性还是比较高的

（4）不支持多节点删除和创建。需要自己去递归

**2**\*\*）\*\***Curator** **是一个专门解决分布式锁的框架，解决了原生** **JavaAPI** **开发分布式遇到的问题。**

详情请查看官方文档：https://curator.apache.org/index.html

**3**\*\*）\*\***Curator** **案例实操**

（1）添加依赖

```xml
<dependency>

 <groupId>org.apache.curator</groupId>

 <artifactId>curator-framework</artifactId>

 <version>4.3.0</version>

</dependency>

<dependency>

 <groupId>org.apache.curator</groupId>

 <artifactId>curator-recipes</artifactId>

 <version>4.3.0</version>

</dependency>

<dependency>

 <groupId>org.apache.curator</groupId>

 <artifactId>curator-client</artifactId>

 <version>4.3.0</version>

</dependency>
```

（2）代码实现

```java
import org.apache.curator.RetryPolicy;

import org.apache.curator.framework.CuratorFramework;

import org.apache.curator.framework.CuratorFrameworkFactory;

import org.apache.curator.framework.recipes.locks.InterProcessLock;

import org.apache.curator.framework.recipes.locks.InterProcessMutex;

import org.apache.curator.retry.ExponentialBackoffRetry;

public class CuratorLockTest {

    private String rootNode = "/locks";

// zookeeper server 列表

    private String connectString =

            "hadoop102:2181,hadoop103:2181,hadoop104:2181";

    // connection 超时时间

    private int connectionTimeout = 2000;

    // session 超时时间

    private int sessionTimeout = 2000;

    public static void main(String[] args) {

        new CuratorLockTest().test();

    }

    // 测试

    private void test() {

        // 创建分布式锁 1

        final InterProcessLock lock1 = new

                InterProcessMutex(getCuratorFramework(), rootNode);

        // 创建分布式锁 2

        final InterProcessLock lock2 = new

                InterProcessMutex(getCuratorFramework(), rootNode);

        new Thread(new Runnable() {

            @Override

            public void run() {

                // 获取锁对象

                try {

                    lock1.acquire();

                    System.out.println("线程 1 获取锁");

                    // 测试锁重入

                    lock1.acquire();

                    System.out.println("线程 1 再次获取锁");

                    Thread.sleep(5 * 1000);

                    lock1.release();

                    System.out.println("线程 1 释放锁");

                    lock1.release();

                    System.out.println("线程 1 再次释放锁");

                } catch (Exception e) {

                    e.printStackTrace();

                }

            }

        }).start();

        new Thread(new Runnable() {

            @Override

            public void run() {

                // 获取锁对象

                try {

                    lock2.acquire();

                    System.out.println("线程 2 获取锁");

                    // 测试锁重入

                    lock2.acquire();

                    System.out.println("线程 2 再次获取锁");

                    Thread.sleep(5 * 1000);

                    lock2.release();

                    System.out.println("线程 2 释放锁");

                    lock2.release();

                    System.out.println("线程 2 再次释放锁");

                } catch (Exception e) {

                    e.printStackTrace();

                }

            }

        }).start();

    }

    // 分布式锁初始化

    public CuratorFramework getCuratorFramework (){

        //重试策略，初试时间 3 秒，重试 3 次

        RetryPolicy policy = new ExponentialBackoffRetry(3000, 3);

        //通过工厂创建 Curator

        CuratorFramework client =

                CuratorFrameworkFactory.builder()

                        .connectString(connectString)

                        .connectionTimeoutMs(connectionTimeout)

                        .sessionTimeoutMs(sessionTimeout)

                        .retryPolicy(policy).build();

        //开启连接

        client.start();

        System.out.println("zookeeper 初始化完成...");

        return client;

    }

}
```

## **企业面试真题（面试重点）**

**6.1** **选举机制**

半数机制，超过半数的投票通过，即通过。

（1）第一次启动选举规则：

投票过半数时，服务器 id 大的胜出

（2）第二次启动选举规则：

①EPOCH 大的直接胜出

②EPOCH 相同，事务 id 大的胜出

③事务 id 相同，服务器 id 大的胜出

**6.2** **生产集群安装多少** **zk** **合适？**

安装奇数台。

生产经验：

* 10 台服务器：3 台 zk；
* 20 台服务器：5 台 zk；
* 100 台服务器：11 台 zk；
* 200 台服务器：11 台 zk

服务器台数多：好处，提高可靠性；坏处：提高通信延时

**6.3** **常用命令**

ls、get、create、delete

## Zk算法

### 拜占庭将军问题

拜占庭将军问题是一个协议问题，拜占庭帝国军队的将军们必须全体一致的决定是否攻击某一支敌军。问题是这些将军在地理上是分隔开来的，并且将

军中存在叛徒。叛徒可以任意行动以达到以下目标：**欺骗某些将军采取进攻行动**；**促成一个不是所有将军都同意的决定，如当将军们不希望进攻时促成进攻**

**行动**；**或者迷惑某些将军，使他们无法做出决定**。如果叛徒达到了这些目的之一，则任何攻击行动的结果都是注定要失败的，只有完全达成一致的努力才能

获得胜利。

* 该问题的关键是存在消息不一致问题（即数据一致性）

### Paxos算法

Paxos算法是基于**消息传递**且具有**高度容错特性**的**一致性算法**，是目前公认的解决**分布式一致性**问题**最有效**的算法之一。

（这里**某个数据的值**并不只是狭义上的某个数，它可以是一条日志，也可以是一条命令（command）。。。根据应用场景不同，**某个数据的值**有不同的含义。）

![image-20210717162647446](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F%5Czookeeper.assets%5Cimage-20210717162647446.png)

Paxos算法描述：

在一个Paxos系统中，首先将所有节点划分为Proposer（提议者），Acceptor（接受者），和Learner（学习者）。（注意：每个节点都可以身兼数职）。

Paxos的目标：保证最终有一个value会被选定，当value被选定后，进程最终也能获取到被选定的value。

Proposer、Acceptor、Learner分别在什么情况下才能认为某个value被选定呢？

* Proposer：只要Proposer发的提案被Acceptor接受（刚开始先认为只需要一个Acceptor接受即可，在推导过程中会发现需要半数以上的Acceptor同意才行），Proposer就认为该提案里的value被选定了。
* Acceptor：只要Acceptor接受了某个提案，Acceptor就任务该提案里的value被选定了。
* Learner：Acceptor告诉Learner哪个value被选定，Learner就认为那个value被选定。

### 推导过程

https://www.cnblogs.com/linbingdong/p/6253479.html#!comments

#### Proposer生成提案

为了满足P2b，这里有个比较重要的思想：Proposer生成提案之前，应该先去\*\*『学习』**已经被选定或者可能被选定的value，然后以该value作为自己提出的提案的value。如果没有value被选定，Proposer才可以自己决定value的值。这样才能达成一致。这个学习的阶段是通过一个**『Prepare请求』\*\*实现的。

于是我们得到了如下的**提案生成算法**：

1.  Proposer选择一个**新的提案编号N**，然后向**某个Acceptor集合**（半数以上）发送请求，要求该集合中的每个Acceptor做出如下响应（response）。 (a) 向Proposer承诺保证**不再接受**任何编号**小于N的提案**。 (b) 如果Acceptor已经接受过提案，那么就向Proposer响应**已经接受过**的编号小于N的**最大编号的提案**。

    我们将该请求称为**编号为N**的**Prepare请求**。
2. 如果Proposer收到了**半数以上**的Acceptor的**响应**，那么它就可以生成编号为N，Value为V的**提案\[N,V]**。这里的V是所有的响应中**编号最大的提案的Value**。如果所有的响应中**都没有提案**，那 么此时V就可以由Proposer**自己选择**。 生成提案后，Proposer将该**提案**发送给**半数以上**的Acceptor集合，并期望这些Acceptor能接受该提案。我们称该请求为**Accept请求**。（注意：此时接受Accept请求的Acceptor集合**不一定**是之前响应Prepare请求的Acceptor集合）

#### Acceptor接受提案

Acceptor**可以忽略任何请求**（包括Prepare请求和Accept请求）而不用担心破坏算法的**安全性**。因此，我们这里要讨论的是什么时候Acceptor可以响应一个请求。

我们对Acceptor接受提案给出如下约束：

> P1a：一个Acceptor只要尚**未响应过**任何**编号大于N**的**Prepare请求**，那么他就可以**接受**这个**编号为N的提案**。

如果Acceptor收到一个编号为N的Prepare请求，在此之前它已经响应过编号大于N的Prepare请求。根据P1a，该Acceptor不可能接受编号为N的提案。因此，该Acceptor可以忽略编号为N的Prepare请求。当然，也可以回复一个error，让Proposer尽早知道自己的提案不会被接受。

因此，一个Acceptor**只需记住**：1. 已接受的编号最大的提案 2. 已响应的请求的最大编号。

#### Paxos算法描述

经过上面的推导，我们总结下Paxos算法的流程。

Paxos算法分为**两个阶段**。具体如下：

*   **阶段一：**

    (a) Proposer选择一个**提案编号N**，然后向**半数以上**的Acceptor发送编号为N的**Prepare请求**。

    (b) 如果一个Acceptor收到一个编号为N的Prepare请求，且N**大于**该Acceptor已经**响应过的**所有**Prepare请求**的编号，那么它就会将它已经**接受过的编号最大的提案（如果有的话）作为响应反馈给Proposer，同时该Acceptor承诺不再接受**任何**编号小于N的提案**。
*   **阶段二：**

    (a) 如果Proposer收到**半数以上**Acceptor对其发出的编号为N的Prepare请求的**响应**，那么它就会发送一个针对\*\*\[N,V]提案**的**Accept请求**给**半数以上**的Acceptor。注意：V就是收到的**响应**中**编号最大的提案的value\*\*，如果响应中**不包含任何提案**，那么V就由Proposer**自己决定**。

    (b) 如果Acceptor收到一个针对编号为N的提案的Accept请求，只要该Acceptor**没有**对编号**大于N**的**Prepare请求**做出过**响应**，它就**接受该提案**。

![image-20210717170315426](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F%5Czookeeper.assets%5Cimage-20210717170315426.png)

* \==图片中接受时，应是N>=ResN的时候接受，N\<ResN的时候不接受==

### Paxos算法实践情况

只有一个proposer提议的proposal的时候：

![image-20210717170703304](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F%5Czookeeper.assets%5Cimage-20210717170703304.png)

有多个proposer的时候：每个proposer的proposal都算一个新的acceptN和accepetV，若是acceptN是最新的，也会再Accept时被比较更新

![image-20210717171441925](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F%5Czookeeper.assets%5Cimage-20210717171441925.png)

多个proposer在follower为accept时同时竞争

![image-20210717172559700](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F%5Czookeeper.assets%5Cimage-20210717172559700.png)

### Learner学习被选定的value

Learner学习（获取）被选定的value有如下三种方案：

![image-20210717170514867](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F%5Czookeeper.assets%5Cimage-20210717170514867.png)

### 如何保证Paxos算法的活性

![image-20210717170528362](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F%5Czookeeper.assets%5Cimage-20210717170528362.png)

### **ZAB** **协议**

ZAB协议借鉴了Paxos算法，但其只有一个Leader进行proposal的发起，而且增加了算法所需功能的具体实现。

#### **Zab** **协议内容**

Zab 协议包括两种基本的模式：**消息广播、崩溃恢复。**

**消息广播**

![image-20210718061812622](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F%5Czookeeper.assets%5Cimage-20210718061812622.png)

**崩溃恢复**

![image-20210718061843121](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F%5Czookeeper.assets%5Cimage-20210718061843121.png)

![image-20210718061932294](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F%5Czookeeper.assets%5Cimage-20210718061932294.png)

Leader\*\*选举：\*\*根据上述要求，Zab协议需要保证选举出来的Leader需要满足以下条件：

（1）新选举出来的Leader不能包含未提交的Proposal。**即新**Leader**必须都是已经提交了**Proposal**的**Follower**服务器节点**。

（2）**新选举的**Leader**节点中含有最大的**zxid。这样做的好处是可以避免Leader服务器检查Proposal的提交和丢弃工作。

Zab**如何数据同步**：

（1）完成Leader选举后，在正式开始工作之前（接收事务请求，然后提出新的Proposal），Leader**服务器会首先确认事务日**

**志中的所有的**Proposal **是否已经被集群中过半的服务器**Commit\*\*。\*\*

（2）Leader服务器需要确保所有的Follower服务器能够接收到每一条事务的Proposal，并且能将所有已经提交的事务Proposal

应用到内存数据中。**等到**Follower**将所有尚未同步的事务**Proposal**都从**Leader**服务器上同步过**，**并且应用到内存数据中以后，**

Leader才会把该Follower加入到真正可用的Follower列表中。

### **CAP**

CAP理论告诉我们，一个分布式系统不可能同时满足以下三种

CAP**理论**

* 一致性（C:Consistency）
* 可用性（A:Available）
* 分区容错性（P:Partition Tolerance）

这三个基本需求，最多只能同时满足其中的两项，因为P是必须的，因此往往选择就在CP或者AP中。

1\*\*）一致性（**C:Consistency**）\*\*

在分布式环境中，一致性是指数据在多个副本之间是否能够保持数据一致的特性。在一致性的需求下，当一个系统在数

据一致的状态下执行更新操作后，应该保证系统的数据仍然处于一致的状态。

2\*\*）可用性（**A:Available**）\*\*

可用性是指系统提供的服务必须一直处于可用的状态，对于用户的每一个操作请求总是能够在有限的时间内返回结果。

3\*\*）分区容错性（**P:Partition Tolerance**）\*\*

分布式系统在遇到任何网络分区故障的时候，仍然需要能够保证对外提供满足一致性和可用性的服务，除非是整个网络

环境都发生了故障。

ZooKeeper**保证的是**CP

**（1）ZooKeeper不能保证每次服务请求的可用性。**（注：在极端环境下，ZooKeeper可能会丢弃一些请求，消费者程序需要

重新请求才能获得结果）。所以说，ZooKeeper不能保证服务可用性。

**（2）进行**Leader**选举时集群都是不可用。**

## **持久化源码**

Leader 和 Follower 中的数据会在内存和磁盘中各保存一份。所以需要将内存中的数据持久化到磁盘中。

在 org.apache.zookeeper.server.persistence 包下的相关类都是序列化相关的代码。

![image-20210718062238310](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F%5Czookeeper.assets%5Cimage-20210718062238310.png)

（1）zk 中的数据模型，是一棵树，DataTree，每个节点，叫做 DataNode

（2）zk 集群中的 DataTree 时刻保持状态同步

（3）Zookeeper 集群中每个 zk 节点中，数据在内存和磁盘中都有一份完整的数据。

* 内存数据：DataTree
* 磁盘数据：快照文件 + 编辑日志

## **序列化源码**

![image-20210718062257349](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F%5Czookeeper.assets%5Cimage-20210718062257349.png)

## **ZK** **服务端环境配置**

![image-20210718062319394](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F%5Czookeeper.assets%5Cimage-20210718062319394.png)

## ZK服务端数据初始化

一个服务器代表了一个QuorumPeer，在选举后会通过getPeerState（）标记各自的状态，调用对应不同状态的服务如leader和follower类进行监听

![image-20210718062507207](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F%5Czookeeper.assets%5Cimage-20210718062507207.png)

## **ZK** **选举源码解析**

![image-20210718062925875](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F%5Czookeeper.assets%5Cimage-20210718062925875.png)

![image-20210718062933606](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F%5Czookeeper.assets%5Cimage-20210718062933606.png)

![image-20210718062959716](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F%5Czookeeper.assets%5Cimage-20210718062959716.png)

## 选举后Follower和Leader状态同步源码

* **这是选举后Follower和Leader的状态同步过程，不同于由Client提出proposal时类似paxos算法的实现过程**

最终总结同步的方式：

（1）DIFF 咱两一样，不需要做什么

（2）TRUNC follower 的 zxid 比 leader 的 zxid 大，所以 Follower 要回滚

（3）COMMIT leader 的 zxid 比 follower 的 zxid 大，发送 Proposal 给 foloower 提交执行

（4）如果 follower 并没有任何数据，直接使用 SNAP 的方式来执行数据同步（直接把数据全部序列到 follower） ![image-20210718063139205](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F%5Czookeeper.assets%5Cimage-20210718063139205.png)

![image-20210718063149522](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F%5Czookeeper.assets%5Cimage-20210718063149522.png)

## 服务端Leader的启动

选举后Leader.lead方法执行的最后会执行startZkServer方法，代表状态同步完成，准备执行其他的请求进行paxos算法流程

![image-20210718063323820](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F%5Czookeeper.assets%5Cimage-20210718063323820.png)

## **服务端** **Follower** **启动**

选举后followLeader状态同步完成后，调用follower.followLeader()，准备执行其他的请求进行paxos算法流程

![image-20210718063525688](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F%5Czookeeper.assets%5Cimage-20210718063525688.png)

## **客户端启动**

底层也是通过apache的Zookeeper类进行Socket层面的Connection

![image-20210718064236248](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F%5Czookeeper.assets%5Cimage-20210718064236248.png)
