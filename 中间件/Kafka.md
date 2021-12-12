# Kafka

## 定义

Kafka是一个分布式的基于**发布/订阅模式**的消息队列（Message Queue），主要用于大数据实时处理领域

### 使用消息队列的好处

解耦（因为解耦了，才能做到后面两个）/削峰/异步

1.  解耦

    （类似Spring的IOC）

    * 允许你独立的扩展或修改两边的处理过程，只要确保它们遵守同样的接口约束。
2. 可恢复性
   * 系统的一部分组件失效时，不会影响到整个系统。消息队列降低了进程间的耦合度，所以即使一个处理消息的进程挂掉，加入队列中的消息仍然可以在系统恢复后被处理。
3. 缓冲
   * 有助于控制和优化数据流经过系统的速度， 解决生产消息和消费消息的处理速度不一致的情况。
4.  灵活性 & 峰值处理能力

    （削峰）

    * 在访问量剧增的情况下，应用仍然需要继续发挥作用，但是这样的突发流量并不常见。如果为以能处理这类峰值访问为标准来投入资源随时待命无疑是巨大的浪费。使用消息队列能够使关键组件顶住突发的访问压力，而不会因为突发的超负荷的请求而完全崩溃。
5. 异步通信
   * 很多时候，用户不想也不需要立即处理消息。消息队列提供了异步处理机制，允许用户把一个消息放入队列，但并不立即处理它。想向队列中放入多少消息就放多少，然后在需要的时候再去处理它们。

## 消息队列的两种模式

### 点对点模式

**一对一，消费者主动拉取数据，消息收到后清除**

消息被消费以后， queue 中不再有存储，所以消息消费者不可能消费到已经被消费的消息。Queue 支持存在多个消费者，但是对一个消息而言，只会有一个消费者可以消费。

![image-20210427191552255](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E4%B8%AD%E9%97%B4%E4%BB%B6%5CKafka.assets%5Cimage-20210427191552255.png)

### 发布/订阅模式

**一对多，消费者消费数据之后不会清除消息**

消费消息时分为kafka自己发布

> 优点：能及时通知消费者，更快地给每个消费者消息；缺点：kafka与消费者之间的消息传递速度难以协调，若kafka过快甚至会导致信息丢失

消费者自己拉取：

> 优点：能充分利用consumer的接收速度；缺点：consumer需要轮询访问kafka，浪费资源

消息生产者（发布）将消息发布到 topic 中，同时有多个消息消费者（订阅）消费该消息。和点对点方式不同，发布到 topic 的消息会被所有订阅者消费。

![image-20210427191706749](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E4%B8%AD%E9%97%B4%E4%BB%B6%5CKafka.assets%5Cimage-20210427191706749.png)

## 基础架构

![image-20210427192331932](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E4%B8%AD%E9%97%B4%E4%BB%B6%5CKafka.assets%5Cimage-20210427192331932.png)

1. **Producer** ： 消息生产者，就是向 Kafka ；
2. **Consumer** ： 消息消费者，向 Kafka broker 取消息的客户端；
3. **Consumer Group （CG）**： 消费者组，由多个 consumer 组成。 消费者组内每个消费者负责消费不同分区的数据，一个分区只能由一个组内消费者消费；消费者组之间互不影响。 所有的消费者都属于某个消费者组，即消费者组是逻辑上的一个订阅者。
4. **Broker** ：经纪人 一台 Kafka 服务器就是一个 broker。一个集群由多个 broker 组成。一个 broker可以容纳多个 topic。
5. **Topic** ： 话题，可以理解为一个队列， 生产者和消费者面向的都是一个 topic；
6. **Partition**： 为了实现扩展性，一个非常大的 topic 可以分布到多个 broker（即服务器）上，一个 topic 可以分为多个 partition，每个 partition 是一个有序的队列；**分区的目的是为了更好的负载均衡**
7. **Replica**： 副本（Replication），为保证集群中的某个节点发生故障时， 该节点上的 partition 数据不丢失，且 Kafka仍然能够继续工作， Kafka 提供了副本机制，一个 topic 的每个分区都有若干个副本，一个 leader 和若干个 follower。
8. **Leader**： 每个分区多个副本的“主”，生产者发送数据的对象，以及消费者消费数据的对象都是 leader。
9. **Follower**： 每个分区多个副本中的“从”，实时从 leader 中同步数据，保持和 leader 数据的同步。 leader 发生故障时，某个 Follower 会成为新的 leader。

## 集群环境搭建

1. 下载文件
2. tar -zxvf解压
3. 创建3个server.properties

> 需要修改的：
>
> 1. broker.id
> 2.  delete.topic.enable=true
>
>     // 若是最终为logs文件夹则日志和目录一起存放，否则存放data在log.dirs中，logs会自动生成文件夹
> 3. log.dirs=
> 4. zookeeper.connect=
> 5. 将advertised.listeners=PLAINTEXT://localhost:9092 中的localhost换成公网IP，不然会匹配成内网ip，客户端访问不到消息

1. 创建日志文件夹
2. 启动集群

> \[root@hadoop100 kafka-0.11.0.0]# bin/kafka-server-start.sh -daemon config/server1.properties \[root@hadoop101 kafka-0.11.0.0]# bin/kafka-server-start.sh -daemon config/server2.properties \[root@hadoop102 kafka-0.11.0.0]# bin/kafka-server-start.sh -daemon config/server3.properties

1. 关闭集群

```shell
> [root@hadoop100 kafka-0.11.0.0]#  bin/kafka-server-stop.sh -daemon config/server1.properties
> [root@hadoop101 kafka-0.11.0.0]#  bin/kafka-server-stop.sh -daemon config/server2.properties
> [root@hadoop102 kafka-0.11.0.0]#  bin/kafka-server-stop.sh -daemon config/server3.properties
```

**server.properties**

```properties
#broker 的全局唯一编号，不能重复
broker.id=0
#删除 topic 功能使能
delete.topic.enable=true
#处理网络请求的线程数量
num.network.threads=3
#用来处理磁盘 IO 的现成数量
num.io.threads=8
#发送套接字的缓冲区大小
socket.send.buffer.bytes=102400
#接收套接字的缓冲区大小
socket.receive.buffer.bytes=102400
#请求套接字的缓冲区大小
socket.request.max.bytes=104857600
#kafka 运行日志存放的路径
log.dirs=/opt/module/kafka/logs
#topic 在当前 broker 上的分区个数
num.partitions=1
#用来恢复和清理 data 下数据的线程数量
num.recovery.threads.per.data.dir=1
#segment 文件保留的最长时间，超时将被删除
log.retention.hours=168
#配置连接 Zookeeper 集群地址
zookeeper.connect=hadoop102:2181,hadoop103:2181,hadoop104:2181
```

## kafka操作

1. 创建topic

```shell
>  bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 3 --partitions 3 --topic second
# 创建topic需要连接的是zk 客户端   --replication-factor 备份数 --partitions topic的分区数
```

1. 查询集群中的topic

```shell
> bin/kafka-topics.sh --list --zookeeper localhost:2181
#  查询需要连接的是zk 客户端 
```

1. 查询集群中的topic详情

```shell
 bin/kafka-topics.sh --describe --zookeeper localhost:2181 --topic first
 #  查询详情需要连接的是zk 客户端 
```

1. 连接kafka生产消息

```shell
> bin/kafka-console-producer.sh  --broker-list localhost:9092 --topic second 
 
```

1. 消费kafka消息

```shell
> bin/kafka-console-consumer.sh --topic second --zookeeper localhost:2181 --from-beginning
# 旧版，连接的是zk客户端 --from-beginning消费所有topic缓存的消息
> bin/kafka-console-consumer.sh --topic second --bootstrap-server localhost:9092 --from-beginning
# 新版，连接的是kafka服务器 offset此时放在kakfa集群中
```

1. 删除topic

```shell
> bin/kafka-topics.sh --zookeeper localhost:2181 --delete --topic 
# 连接ZK客户端
```

## 工作流程

1. topic是逻辑上的概念，partition是物理上的
2. 每个partition对应于一个topicName-partitionNum文件，文件中存储着producer生产的数据
3. 一个topicName-partitionNum文件夹中会分成数个segment提高索引效率
4. 每个segment会有一个.log文件和.index文件
   * .log存储的为数据，.index为offset和相应的offset在log中的偏移量与该消息的大小
5. Producer 生产的数据会被不断追加到该log 文件末端，且每条数据都有自己的 offset，用.index记录offset对应在.log中的大小 。 consumer组中的每个consumer， 都会实时记录自己消费到了哪个 offset，以便出错恢复时，从上次的位置继续消费。（producer -> log with offset -> consumer(s)）

## 生产者策略

#### 分区的原因

使得一个topic可以在多个服务器中存在，提高topic的发送量，也易于调整

#### 分区的原则

我们需要将 producer 发送的数据封装成一个 `ProducerRecord` 对象。

![image-20210429172158564](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E4%B8%AD%E9%97%B4%E4%BB%B6%5CKafka.assets%5Cimage-20210429172158564.png)

发送的分区规则：

1. 指明 partition 的情况下，直接将指明的值直接作为 partiton 值；
2. 没有指明 partition 值但有 key 的情况下，将 key 的 hash 值与 topic 的 partition 数进行取余得到 partition 值；
3. 既没有 partition 值又没有 key 值的情况下，第一次调用时随机生成一个整数（后面每次调用在这个整数上自增），将这个值与 topic 可用的 partition 总数取余得到 partition值，也就是常说的 round-robin 算法。

#### 生产者ISR

为了保证producer发送的数据的可靠性，partition收到数据后都需要向producer发送ack，进行下一轮的发送，否则重传

*   **何时发送ack？**

    确保有follower与leader同步完成，leader再发送ack，这样才能保证leader挂掉之后，能在follower中选举出新的leader。
*   **多少个follower同步完成之后发送ack？**

    **副本数据同步策略**

    | 序号 |         方案        |                   优点                  | 缺点                                                                         |
    | -- | :---------------: | :-----------------------------------: | -------------------------------------------------------------------------- |
    | 1  | 半数以上完成同步， 就发送 ack |                  延迟低                  | 选举新的 leader 时，容忍 n 台节点的故障，需要 2n+1 个副本。半数完成同步即n+1台，此时若是n台故障，还剩一台副本正常运行,可以交互 |
    | 2  |   全部完成同步，才发送ack   | 选举新的 leader 时， 容忍 n 台节点的故障，需要 n+1 个副本 | 延迟高                                                                        |

Kafka 选择了第二种方案，原因如下：

1. 同样为了容忍 n 台节点的故障，第一种方案需要 2n+1 个副本，而第二种方案只需要 n+1 个副本，而 Kafka 的每个分区都有大量的数据， 第一种方案会造成大量数据的冗余。
2. 虽然第二种方案的网络延迟会比较高，但网络延迟对 Kafka 的影响较小。

* 采用第二种方案之后，设想以下情景： leader 收到数据，所有 follower 都开始同步数据，但有一个 follower，因为某种故障，迟迟不能与 leader 进行同步，那 leader 就要一直等下去，直到它完成同步，才能发送 ack。这个问题怎么解决呢？
  * Leader 维护了一个动态的 **in-sync replica set** (ISR)，意为和 leader 保持同步的 follower 集合。
  * 当 ISR 中的 follower 完成数据的同步之后，就会给 leader 发送 ack。
  * 如果 follower长时间未向leader同步数据，则该follower将被踢出ISR，该时间阈值由`replica.lag.time.max.ms`参数设定。 （以前有时间/记录差数两种判断条件，但实际生产发现记录差数会导致ISR更改频繁）
  * Leader 发生故障之后，就会从 ISR 中选举新的 leader。

#### 生产者ACk机制

对于某些不太重要的数据，对数据的可靠性要求不是很高，能够容忍数据的少量丢失，所以没必要等 ISR 中的 follower 全部接收成功。

所以 Kafka 为用户提供了三种可靠性级别，用户根据对可靠性和延迟的要求进行权衡，选择以下的配置。

**acks 参数配置**：

* 0： producer 不等待 broker 的 ack，这一操作提供了一个最低的延迟， broker 一接收到还没有写入磁盘就已经返回，当 broker 故障时有可能**丢失数据**；
* 1： producer 等待 broker 的 ack， partition 的 leader 落盘成功后返回 ack，如果在 follower同步成功之前 leader 故障，那么将会**丢失数据**；
* \-1（all） ： producer 等待 broker 的 ack， partition 的 leader 和 ISR 的follower 全部落盘成功后才返回 ack。但是如果在 follower 同步完成后， broker 发送 ack 之前， leader 发生故障，那么会造成**数据重复**。（若是极端情况下ISR只有Leader，其他的都在时间参数外，则也有可能数据丢失）

#### 数据一致性问题

![image-20210429193248732](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E4%B8%AD%E9%97%B4%E4%BB%B6%5CKafka.assets%5Cimage-20210429193248732.png)

**follower 故障和 leader 故障**

* **follower 故障**：follower 发生故障后会被临时踢出 ISR，待该 follower 恢复后， follower 会读取本地磁盘记录的上次的 HW，**并将 log 文件高于 HW 的部分截取掉**，**从 HW 开始向 leader 进行同步**。等该 follower 的 LEO 大于等于该 Partition 的 HW，即 follower 追上 leader 之后，就可以重新加入 ISR 了。
* **leader 故障**：leader 发生故障之后，会从 ISR 中选出一个新的 leader，之后，为保证多个副本之间的数据一致性， 其余的 follower 会先将各自的 log 文件**高于 HW 的部分截掉**，然后从**新的 leader同步数据。**

注意： \*\*这只能保证副本之间的数据一致性，\*\*并不能保证数据不丢失或者不重复。（ACK才是用来处理producer到Server的丢失/重复的）

#### ExactlyOnce

将服务器的 ACK 级别设置为-1（all），可以保证 **Producer 到 Server 之间**不会丢失数据，最少发送一次即 **At Least Once** 语义。

相对的，将服务器 ACK 级别设置为 0，可以保证生产者每条消息只会被发送一次，即 **At Most Once** 语义。

At Least Once 可以保证数据不丢失，但是不能保证数据不重复；相对的， At Most Once可以保证数据不重复，但是不能保证数据不丢失。 但是，对于一些非常重要的信息，比如说**交易数据**，下游数据消费者要求数据既不重复也不丢失，即 **Exactly Once** 语义。

在 0.11 版本以前的 Kafka，对此是无能为力的，只能保证数据不丢失，再在下游消费者对数据做全局去重。对于多个下游应用的情况，每个都需要单独做全局去重，这就对性能造成了很大影响。

0.11 版本的 Kafka，引入了一项重大特性：**幂等性**。**所谓的幂等性就是指 Producer 不论向 Server 发送多少次重复数据， Server 端都只会持久化一条**。幂等性结合 At Least Once 语义，就构成了 Kafka 的 Exactly Once 语义。即：

```
At Least Once + 幂等性 = Exactly Once
```

要启用幂等性，只需要将 Producer 的参数中 `enable.idempotence` 设置为 true 即可。**(此时ACK默认为-1)** Kafka的幂等性实现其实就是将原来下游需要做的去重放在了数据上游。开启幂等性的 Producer 在初始化的时候会被分配一个 PID，发往同一 Partition 的消息会附带 Sequence Number。而Broker 端会对`<PID, Partition, SeqNumber>`做缓存，当具有相同主键的消息提交时， Broker 只会持久化一条。

* 这个SeqNumber是序列号，与offset没有关系

但是 PID 重启就会变化，而且Partition也作为参数，每个Partion只对自己的分区负责，所以幂等性无法保证**跨分区跨会话**的 Exactly Once。

## 消费者分区分配策略

**push（推）模式很难适应消费速率不同的消费者，因为消息发送速率是由 broker 决定的**。它的目标是尽可能以最快速度传递消息，但是这样很容易造成 consumer 来不及处理消息，典型的表现就是拒绝服务以及网络拥塞。而 pull 模式则可以根据 consumer 的消费能力以适当的速率消费消息。

**pull 模式不足之处**是，如果 kafka 没有数据，消费者可能会陷入循环中， 一直返回空数据。 针对这一点， Kafka 的消费者在消费数据时会传入一个时长参数 timeout，如果当前没有数据可供消费， consumer 会等待一段时间之后再返回，这段时长即为 timeout。

#### 分区分配策略

Kafka 有两种分配策略：

* round-robin循环
* range

**Round Robin**

关于Roudn Robin重分配策略，其主要采用的是一种轮询的方式分配所有的分区，该策略主要实现的步骤如下。这里我们首先假设有三个topic：t0、t1和t2，这三个topic拥有的分区数分别为1、2和3，那么总共有六个分区，这六个分区分别为：t0-0、t1-0、t1-1、t2-0、t2-1和t2-2。这里假设我们有三个consumer：C0、C1和C2，它们订阅情况为：C0订阅t0，C1订阅t0和t1，C2订阅t0、t1和t2。那么这些分区的分配步骤如下：

* 首先将所有的partition和consumer按照字典序进行排序，所谓的字典序，就是按照其名称的字符串顺序，那么上面的六个分区和三个consumer排序之后分别为：

![image-20210506143856596](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E4%B8%AD%E9%97%B4%E4%BB%B6%5CKafka.assets%5Cimage-20210506143856596.png)

那么按照轮询的方式分配就是C0订阅的主题是t0-0、t2-0，C1订阅的主题是t1-0、t2-1，C2订阅的主题是t1-1、t2-2。

* **按照这样的分配方式，容易使得消费者消费不到其订阅的主题，因此，这个只适合订阅相同的同一组消费者**

**Range**

所谓的Range重分配策略，就是首先会计算各个consumer将会承载的分区数量，然后将指定数量的分区分配给该consumer。这里我们假设有两个consumer：C0和C1，两个topic：t0和t1，这两个topic分别都有三个分区，那么总共的分区有六个：t0-0、t0-1、t0-2、t1-0、t1-1和t1-2。那么Range分配策略将会按照如下步骤进行分区的分配：

* 需要注意的是，Range策略是按照topic依次进行分配的，比如我们以t0进行讲解，其首先会获取t0的所有分区：t0-0、t0-1和t0-2，以及所有订阅了该topic的consumer：C0和C1，并且会将这些分区和consumer按照字典序进行排序；
* 然后按照平均分配的方式计算每个consumer会得到多少个分区，如果没有除尽，则会将多出来的分区依次计算到前面几个consumer。比如这里是三个分区和两个consumer，那么每个consumer至少会得到1个分区，而3除以2后还余1，那么就会将多余的部分依次算到前面几个consumer，也就是这里的1会分配给第一个consumer，总结来说，那么C0将会从第0个分区开始，分配2个分区，而C1将会从第2个分区开始，分配1个分区；
* 同理，按照上面的步骤依次进行后面的topic的分配。
* 最终上面六个分区的分配情况如下：

![image-20210506144350171](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E4%B8%AD%E9%97%B4%E4%BB%B6%5CKafka.assets%5Cimage-20210506144350171.png)

可以看到，如果按照`Range`分区方式进行分配，其本质上是依次遍历每个topic，然后将这些topic的分区按照其所订阅的consumer数量进行平均的范围分配。这种方式从计算原理上就会导致排序在前面的consumer分配到更多的分区，从而导致各个consumer的压力不均衡。

## 消费者offset的存储

由于 consumer 在消费过程中可能会出现断电宕机等故障， consumer 恢复后，需要从故障前的位置的继续消费，所以 **consumer 需要实时记录自己消费到了哪个 offset**，以便故障恢复后继续消费。

![image-20210506143128604](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E4%B8%AD%E9%97%B4%E4%BB%B6%5CKafka.assets%5Cimage-20210506143128604.png)

**Kafka 0.9 版本之前， consumer 默认将 offset 保存在 Zookeeper 中，从 0.9 版本开始，consumer 默认将 offset 保存在 Kafka 一个内置的 topic 中，该 topic 为\_\_consumer\_offsets**。

若要读取这个特殊的topic中的内容，需要以特殊的配置启动consumer实例：

1. 修改配置文件 consumer.properties

```properties
exclude.internal.topics=false
```

1. 启动consumer实例

```shell
0.11.0.0 之前版本 - bin/kafka-console-consumer.sh --topic __consumer_offsets --zookeeper hadoop102:2181 --formatter "kafka.coordinator.GroupMetadataManager\$OffsetsMessageFormatter" --consumer.config config/consumer.properties --from-beginning
0.11.0.0 及之后版本 - bin/kafka-console-consumer.sh --topic __consumer_offsets --zookeeper hadoop102:2181 --formatter "kafka.coordinator.group.GroupMetadataManager\$OffsetsMessageFormatter" --consumer.config config/consumer.properties --from-beginning

# formatter是为了使得consumer的输出格式化
```

## 消费者组注意事项

1. 同一个消费者组中的消费者，同一时刻只能有一个消费者消费
2. 不同消费者组中的消费者，若是消费同一个主题，可以同时消费

## 高效读写\&ZK作用

分布式的集群还有分布式提供高效读写

但以下两个无论是在单机还是在集群中都适用。

#### 顺序写磁盘

Kafka 的 producer 生产数据，要写入到 log 文件中，写的过程是一直追加到文件末端，为顺序写。 官网有数据表明，同样的磁盘，顺序写能到 600M/s，而随机写只有 100K/s。这与磁盘的机械机构有关，顺序写之所以快，是因为其**省去了大量磁头寻址的时间**。

#### 零复制技术

![image-20210506160509773](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E4%B8%AD%E9%97%B4%E4%BB%B6%5CKafka.assets%5Cimage-20210506160509773.png)

* NIC network interface controller 网络接口控制器

## Zookeeper 在 Kafka 中的作用

Kafka 集群中有一个 broker 会被选举为 Controller，负责管理集群 broker 的上下线，所有 topic 的分区副本分配和 leader 选举等工作。[Reference](https://www.oschina.net/action/GoToLink?url=http%3A%2F%2Fkafka.apache.org%2F0110%2Fdocumentation%2F%23design\_replicamanagment)

Controller 的管理工作都是依赖于 Zookeeper 的。

以下为 partition 的 leaer 选举过程：

![image-20210506160711202](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E4%B8%AD%E9%97%B4%E4%BB%B6%5CKafka.assets%5Cimage-20210506160711202.png)

## 事务

Kafka 从 0.11 版本开始引入了事务支持。事务可以保证 Kafka 在 Exactly Once 语义的基础上，生产和消费可以跨分区和会话，要么全部成功，要么全部失败。

#### Producer 事务

为了实现跨分区跨会话的事务，需要引入一个全局唯一的 Transaction ID，并将 Producer 获得的PID 和Transaction ID 绑定。这样当Producer 重启后就可以通过正在进行的 TransactionID 获得原来的 PID。

为了管理 Transaction， Kafka 引入了一个新的组件 **Transaction Coordinator**。 Producer 就是通过和 Transaction Coordinator 交互获得 Transaction ID 对应的任务状态。 **Transaction Coordinator 还负责将事务所有写入 Kafka 的一个内部 Topic，这样即使整个服务重启，由于事务状态得到保存，进行中的事务状态可以得到恢复，从而继续进行。**

#### Consumer 事务

上述事务机制主要是从 Producer 方面考虑，对于 Consumer 而言，事务的保证就会相对较弱，尤其是无法保证 Commit 的信息被精确消费。这是由于 Consumer 可以通过 offset 访问任意信息，而且不同的 Segment File 生命周期不同，同一事务的消息可能会出现重启后被删除的情况。

## API生产者流程

Kafka 的 Producer 发送消息采用的是异步发送的方式。在消息发送的过程中，涉及到了两个线程——main 线程和 Sender 线程，以及一个线程共享变量——RecordAccumulator。 main 线程将消息发送给 RecordAccumulator， Sender 线程不断从 RecordAccumulator 中拉取消息发送到 Kafka broker。

* 其中main线程是我们编写的Application，RecordAccumulator和Sender都是创建Producer时创建的

![image-20210507104649216](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E4%B8%AD%E9%97%B4%E4%BB%B6%5CKafka.assets%5Cimage-20210507104649216.png)

相关参数：

* **batch.size**： 只有数据积累到 batch.size 之后， sender 才会发送数据。
* **linger.ms**： 如果数据迟迟未达到 batch.size， sender 等待 linger.time 之后就会发送数据。
* 因此若是两个都没达到且没有producer.close则会导致main线程直接关闭，消息直接随着程序结束而消除

## 异步发送API普通生产者/异步调用

#### 导入依赖

```xml
<dependency>
	<groupId>org.apache.kafka</groupId>
	<artifactId>kafka-clients</artifactId>
	<version>0.11.0.0</version>
</dependency>
```

#### 编写代码

```java
public class Myproducer {
    public static void main(String[] args) throws ExecutionException,InterruptedException {
        Properties props = new Properties();
        // kafka 集群， broker-list

        props.put("bootstrap.servers", "39.108.123.103:9092");

        //可用ProducerConfig.ACKS_CONFIG 代替 "acks"
        //props.put(ProducerConfig.ACKS_CONFIG, "all");
        props.put("acks", "all");
        // 重试次数
        props.put("retries", 1);
        // 批次大小
        props.put("batch.size", 16384);
        // 等待时间
        props.put("linger.ms", 1);
        // RecordAccumulator 缓冲区大小
        props.put("buffer.memory", 33554432);
        //序列化器
        props.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializer");
        props.put("value.serializer", "org.apache.kafka.common.serialization.StringSerializer");
        // 自定义分区器，优先级没有RecordMetada中的传参高
        props.put("partitioner.class","com.zwf.partitioner.Mypartitioner");

        //拦截器
        ArrayList<String> list = new ArrayList<>();
        list.add("com.zwf.interceptors.CountInterceptor");
        list.add("com.zwf.interceptors.TimeInterceptor");
        props.put(ProducerConfig.INTERCEPTOR_CLASSES_CONFIG,list);

        Producer<String, String> producer = new KafkaProducer<>(props);

        for (int i = 0; i < 10; i++) {
            producer.send(new ProducerRecord<String, String>("bigdata",1,"test",
                    "bigdata--" + i), new Callback() {
                // 回调函数，默认异步调用
                @Override
                public void onCompletion(RecordMetadata metadata, Exception exception) {
                    if(exception == null){
                        System.out.println(metadata.partition()+"-"+metadata.offset());
                    }else {
                        exception.printStackTrace();
                    }
                }
            });
        }
        producer.close();
    }
}
```

## API带自定义分区器的生成者

```java
public class Mypartitioner implements Partitioner {

    @Override
    public int partition(String topic, Object key, byte[] keyBytes, Object value, byte[] valueBytes, Cluster cluster) {
        return 0;
    }

    @Override
    public void close() {

    }

    @Override
    public void configure(Map<String, ?> configs) {

    }
}
```

具体内容填写可参考默认分区器`org.apache.kafka.clients.producer.internals.DefaultPartitioner`

然后Producer配置中注册使用

```java
Properties props = new Properties();
...
props.put(ProducerConfig.PARTITIONER_CLASS_CONFIG, MyPartitioner.class);
...
Producer<String, String> producer = new KafkaProducer<>(props);
```

## API同步发送生成者

由于send方法返回一个Future对象，其get（）方法是同步实现的，因此，我们只需在调用Future对象的get方法，便会阻塞当前进程，直至返回ACK

```java
Producer<String, String> producer = new KafkaProducer<>(props);
for (int i = 0; i < 100; i++) {
	producer.send(new ProducerRecord<String, String>("test",  "test - 1"), new Callback() {
		@Override
		public void onCompletion(RecordMetadata metadata, Exception exception) {
			...
		}
	}).get();//<----------------------默认返回的Future对象，调用其get方法
}
```

## API简单消费者

* **KafkaConsumer**： 需要创建一个消费者对象，用来消费数据
* **ConsumerConfig**： 获取所需的一系列配置参数
* **ConsuemrRecord**： 每条数据都要封装成一个 ConsumerRecord 对象

自动提交 offset 的相关参数：

* **enable.auto.commit**：是否开启自动提交 offset 功能
* **auto.commit.interval.ms**：自动提交 offset 的时间间隔

```java
public class MyConsumer {
    private static Map<TopicPartition, Long> currentOffset = new HashMap<>();

    public static void main(String[] args) {

        Properties props = new Properties();

        props.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG,"39.108.123.103:9092");
        props.put("group.id","test");
        props.put("enable.auto.commit","false");
        props.put("auto.commit.interval.ms","1000");
        props.put("key.deserializer",
                "org.apache.kafka.common.serialization.StringDeserializer");
        props.put("value.deserializer",
                "org.apache.kafka.common.serialization.StringDeserializer");

        KafkaConsumer<String, String> consumer = new KafkaConsumer<>(props);

        consumer.subscribe(Arrays.asList("bigdata"), new ConsumerRebalanceListener() {
            @Override
            public void onPartitionsRevoked(Collection<TopicPartition> partitions) {
                commitOffset(currentOffset);
            }

            @Override
            public void onPartitionsAssigned(Collection<TopicPartition> partitions) {
                currentOffset.clear();
                for(TopicPartition partition: partitions){
                    consumer.seek(partition,getOffset(partition));
                }
            }
        });

        while (true){
            ConsumerRecords<String, String> records = consumer.poll(100);
            for (ConsumerRecord<String,String> record :records){
                System.out.printf("offset = %d,key = %s, value = %s%n",record.offset(),record.key(),record.value());
            }
            consumer.commitAsync();
        }
    }

    private static long getOffset(TopicPartition partition) {
        return 0;
    }

    private static void commitOffset(Map<TopicPartition, Long> currentOffset) {
    }

}
```

### API消费者重置offset

```java
Properties props = new Properties();
...
props.put(ConsumerConfig.AUTO_OFFSET_RESET_CONFIG, "earliest");
props.put("group.id", "abcd");//组id需另设，否则看不出上面一句的配置效果
...
KafkaConsumer<String, String> consumer = new KafkaConsumer<>(props);
```

重置需要的条件：

1. 需要将ConsumerConfig.AUTO\_OFFSET\_RESET\_CONFIG设置为earliest，但需要符合以下条件
   * What to do when there is no initial offset in Kafka or if the current offset does not exist any more on the server (e.g. because that data has been deleted):
2. 为了使得服务器没有初始offset，我们需要更换消费者组，否则会直接读取offset（可能是为了保证Message在同一个组中的多个消费者的情况下不会发生读取错乱）

## offset保存问题

#### 自动保存

```java
props.put("enable.auto.commit", "true");
```

#### 手动保存

手动提交 offset 的方法有两种：

1. commitSync（同步提交）
2. commitAsync（异步提交）

两者的**相同点**是，都会将本次 poll 的一批数据最高的偏移量提交；

**不同点**是，commitSync 阻塞当前线程，一直到提交成功，并且会自动失败重试（由不可控因素导致，也会出现提交失败）；而 commitAsync 则没有失败重试机制，故有可能提交失败。

**commitSync**

```java
public class SyncCommitOffset {
	public static void main(String[] args) {
		Properties props = new Properties();
		...
		//<-----------------
		//关闭自动提交 offset
		props.put("enable.auto.commit", "false");
		...
		KafkaConsumer<String, String> consumer = new KafkaConsumer<>(props);
		consumer.subscribe(Arrays.asList("first"));//消费者订阅主题
		while (true) {
			//消费者拉取数据
			ConsumerRecords<String, String> records =
			consumer.poll(100);
			for (ConsumerRecord<String, String> record : records) {
				System.out.printf("offset = %d, key = %s, value= %s%n", record.offset(), record.key(), record.value());
			}
			//<---------------------------------------
			//同步提交，当前线程会阻塞直到 offset 提交成功
			consumer.commitSync();
		}
	}
}
```

**commitAsync**

```java
public class AsyncCommitOffset {
	public static void main(String[] args) {
		Properties props = new Properties();
		...
		//<--------------------------------------
		//关闭自动提交
		props.put("enable.auto.commit", "false");
		...
		KafkaConsumer<String, String> consumer = new KafkaConsumer<>(props);
		consumer.subscribe(Arrays.asList("first"));// 消费者订阅主题
		while (true) {
			ConsumerRecords<String, String> records = consumer.poll(100);// 消费者拉取数据
			for (ConsumerRecord<String, String> record : records) {
				System.out.printf("offset = %d, key = %s, value = %s%n", record.offset(), record.key(), record.value());
			}
			//<----------------------------------------------
			// 异步提交
			consumer.commitAsync(new OffsetCommitCallback() {
				@Override
				public void onComplete(Map<TopicPartition, OffsetAndMetadata> offsets, Exception exception) {
					if (exception != null) {
						System.err.println("Commit failed for" + offsets);
					}
				}
			});
		}
	}
}
```

#### 上述保存的缺点

无论是同步提交还是异步提交 offset，**都有可能会造成数据的漏消费或者重复消费**。先提交 offset 后消费，有可能造成数据的漏消费；而先消费后提交 offset，有可能会造成数据的重复消费。

#### 自定义Rebalance时存储 offset

消费者对于offset的保存机制，除了服务本身提交时的offset保存，还需要考虑到kafka集群发生意外时同一消费者组的对于分区的重新分配问题Rebalance

* 若是Rebalance时先保存此时的offset，消费者再获取到Rebalance后自己被重新分配到的分区，**并且定位到每个分区最近提交的 offset 位置继续消费**

```java
public class CustomSaveOffset {
	private static Map<TopicPartition, Long> currentOffset = new HashMap<>();

	public static void main(String[] args) {
		// 创建配置信息
		Properties props = new Properties();
		...
		//<--------------------------------------
		// 关闭自动提交 offset
		props.put("enable.auto.commit", "false");
		...
		// 创建一个消费者
		KafkaConsumer<String, String> consumer = new KafkaConsumer<>(props);
		// 消费者订阅主题
		consumer.subscribe(Arrays.asList("first"), 
			//<-------------------------------------
			new ConsumerRebalanceListener() {
			// 该方法会在 Rebalance 之前调用
			@Override
			public void onPartitionsRevoked(Collection<TopicPartition> partitions) {
				commitOffset(currentOffset);
			}

			// 该方法会在 Rebalance 之后调用
			@Override
			public void onPartitionsAssigned(Collection<TopicPartition> partitions) {

				currentOffset.clear();
				for (TopicPartition partition : partitions) {
					consumer.seek(partition, getOffset(partition));// 定位到最近提交的 offset 位置继续消费
				}
			}
		});
		
		while (true) {
			ConsumerRecords<String, String> records = consumer.poll(100);// 消费者拉取数据
			for (ConsumerRecord<String, String> record : records) {
				System.out.printf("offset = %d, key = %s, value = %s%n", record.offset(), record.key(), record.value());
				currentOffset.put(new TopicPartition(record.topic(), record.partition()), record.offset());
			}
			commitOffset(currentOffset);// 异步提交
		}
	}

	// 获取某分区的最新 offset
	private static long getOffset(TopicPartition partition) {
		return 0;
	}

	// 提交该消费者所有分区的 offset
	private static void commitOffset(Map<TopicPartition, Long> currentOffset) {
	}
}
```

## 自定义拦截器

#### 拦截器原理

Intercetpor 的实现接口是`org.apache.kafka.clients.producer.ProducerInterceptor`，其定义的方法包括：

* `configure(configs)`：获取配置信息和初始化数据时调用。
* `onSend(ProducerRecord)`：该方法封装进 KafkaProducer.send 方法中，即它运行在用户主线程中。 Producer 确保**在消息被序列化以及计算分区前**调用该方法。 用户可以在该方法中对消息做任何操作，但最好保证不要修改消息所属的 topic 和分区， 否则会影响目标分区的计算。
* `onAcknowledgement(RecordMetadata, Exception)`：**该方法会在消息从 RecordAccumulator 成功发送到 Kafka Broker 之后，或者在发送过程中失败时调用**。 并且通常都是在 producer 回调逻辑触发之前。 onAcknowledgement 运行在producer 的 IO 线程中，因此不要在该方法中放入很重的逻辑，否则会拖慢 producer 的消息发送效率。
* `close()`：关闭 interceptor，主要用于执行一些**资源清理**工作

实现一个简单的双 interceptor 组成的拦截链。

* 第一个 interceptor 会在消息发送前将时间戳信息加到消息 value 的最前部；
* 第二个 interceptor 会在消息发送后更新成功发送消息数或失败发送消息数。

#### 案例实操

**增加时间戳拦截器**

[TimeInterceptor.java](https://gitee.com/jallenkwong/LearnKafka/blob/master/src/main/java/com/lun/kafka/interceptor/TimeInterceptor.java)

```java
import java.util.Map;

import org.apache.kafka.clients.producer.ProducerInterceptor;
import org.apache.kafka.clients.producer.ProducerRecord;
import org.apache.kafka.clients.producer.RecordMetadata;

public class TimeInterceptor implements ProducerInterceptor<String, String> {
	@Override
	public void configure(Map<String, ?> configs) {
	}

	@Override
	public ProducerRecord<String, String> onSend(ProducerRecord<String, String> record) {
		// 创建一个新的 record，把时间戳写入消息体的最前部
		return new ProducerRecord(record.topic(), record.partition(), record.timestamp(), record.key(),
				"TimeInterceptor: " + System.currentTimeMillis() + "," + record.value().toString());
	}

	@Override
	public void close() {
	}

	@Override
	public void onAcknowledgement(RecordMetadata metadata, Exception exception) {
		// TODO Auto-generated method stub
		
	}
}
```

**增加计数拦截器**

统计发送消息成功和发送失败消息数，并在 producer 关闭时打印这两个计数器

[CounterInterceptor.java](https://gitee.com/jallenkwong/LearnKafka/blob/master/src/main/java/com/lun/kafka/interceptor/CounterInterceptor.java)

```java
import java.util.Map;

import org.apache.kafka.clients.producer.ProducerInterceptor;
import org.apache.kafka.clients.producer.ProducerRecord;
import org.apache.kafka.clients.producer.RecordMetadata;

public class CounterInterceptor implements ProducerInterceptor<String, String>{

	private int errorCounter = 0;
	private int successCounter = 0;
	
	@Override
	public void configure(Map<String, ?> configs) {
		// TODO Auto-generated method stub
		
	}

	@Override
	public ProducerRecord<String, String> onSend(ProducerRecord<String, String> record) {
		return record;
	}

	@Override
	public void onAcknowledgement(RecordMetadata metadata, Exception exception) {
		// 统计成功和失败的次数
		if (exception == null) {
			successCounter++;
		} else {
			errorCounter++;
		}
	}

	@Override
	public void close() {
		// 保存结果
		System.out.println("Successful sent: " + successCounter);
		System.out.println("Failed sent: " + errorCounter);
		
	}

}
```

**producer 主程序**

[InterceptorProducer.java](https://gitee.com/jallenkwong/LearnKafka/blob/master/src/main/java/com/lun/kafka/producer/InterceptorProducer.java)

```java
import java.util.ArrayList;
import java.util.List;
import java.util.Properties;

import org.apache.kafka.clients.producer.KafkaProducer;
import org.apache.kafka.clients.producer.Producer;
import org.apache.kafka.clients.producer.ProducerConfig;
import org.apache.kafka.clients.producer.ProducerRecord;

public class InterceptorProducer {
	public static void main(String[] args) {
		// 1 设置配置信息
		Properties props = new Properties();
		props.put("bootstrap.servers", "127.0.0.1:9092");
		props.put("acks", "all");
		props.put("retries", 3);
		props.put("batch.size", 16384);
		props.put("linger.ms", 1);
		props.put("buffer.memory", 33554432);
		props.put("key.serializer",
				"org.apache.kafka.common.serialization.StringSerializer");
		props.put("value.serializer",
				"org.apache.kafka.common.serialization.StringSerializer");
		//<--------------------------------------------
		// 2 构建拦截链
		List<String> interceptors = new ArrayList<>();
		interceptors.add("com.lun.kafka.interceptor.TimeInterceptor");
		interceptors.add("com.lun.kafka.interceptor.CounterInterceptor");
		
		props.put(ProducerConfig.INTERCEPTOR_CLASSES_CONFIG, interceptors);
		
		String topic = "test";
		Producer<String, String> producer = new KafkaProducer<>(props);
		// 3 发送消息
		for (int i = 0; i < 10; i++) {
			ProducerRecord<String, String> record = new ProducerRecord<>(topic, "message" + i);
			producer.send(record);
		}
		
		// 4 一定要关闭 producer，这样才会调用 interceptor 的 close 方法
		producer.close();
		
	}
}
```

## 监控Eagle的安装

1. 下载解压Kafka Eagele安装包
2. 配环境变量KE\_HOME
3. 更改kafka-server-start.sh启动配置

```shell
EXPORT KAFKA_HEAP_OPTS=-server -Xms2G -Xmx2G -XX:PermSize=128m -XX:+UseG1GC -XX:MaxGCPauseMillis=200 -XX:ParallelGCThreads=8 -XX:ConcGCThreads=5 -XX:InitiatingHeapOccupancyPercent=70
EXPORT JMX_PORT="9999"
```

1. 修改配置文件`%KE_HOME%\conf\system-config.properties`

```shell
######################################
# multi zookeeper&kafka cluster list
######################################<---设置ZooKeeper的IP地址
kafka.eagle.zk.cluster.alias=cluster1
cluster1.zk.list=localhost:2181

######################################
# zk client thread limit
######################################
kafka.zk.limit.size=25

######################################
# kafka eagle webui port
######################################
kafka.eagle.webui.port=8048

######################################
# kafka offset storage
######################################
cluster1.kafka.eagle.offset.storage=kafka
#cluster2.kafka.eagle.offset.storage=zk

######################################
# enable kafka metrics
######################################<---metrics.charts从false改成true
kafka.eagle.metrics.charts=true
kafka.eagle.sql.fix.error=false

######################################
# kafka sql topic records max
######################################
kafka.eagle.sql.topic.records.max=5000

######################################
# alarm email configure
######################################
kafka.eagle.mail.enable=false
kafka.eagle.mail.sa=alert_sa@163.com
kafka.eagle.mail.username=alert_sa@163.com
kafka.eagle.mail.password=mqslimczkdqabbbh
kafka.eagle.mail.server.host=smtp.163.com
kafka.eagle.mail.server.port=25

######################################
# alarm im configure
######################################
#kafka.eagle.im.dingding.enable=true
#kafka.eagle.im.dingding.url=https://oapi.dingtalk.com/robot/send?access_token=

#kafka.eagle.im.wechat.enable=true
#kafka.eagle.im.wechat.token=https://qyapi.weixin.qq.com/cgi-bin/gettoken?corpid=xxx&corpsecret=xxx
#kafka.eagle.im.wechat.url=https://qyapi.weixin.qq.com/cgi-bin/message/send?access_token=
#kafka.eagle.im.wechat.touser=
#kafka.eagle.im.wechat.toparty=
#kafka.eagle.im.wechat.totag=
#kafka.eagle.im.wechat.agentid=

######################################
# delete kafka topic token
######################################
kafka.eagle.topic.token=keadmin

######################################
# kafka sasl authenticate
######################################
cluster1.kafka.eagle.sasl.enable=false
cluster1.kafka.eagle.sasl.protocol=SASL_PLAINTEXT
cluster1.kafka.eagle.sasl.mechanism=PLAIN
cluster1.kafka.eagle.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="admin" password="kafka-eagle";

#cluster2 在此没有用到，将其注释掉
#cluster2.kafka.eagle.sasl.enable=false
#cluster2.kafka.eagle.sasl.protocol=SASL_PLAINTEXT
#cluster2.kafka.eagle.sasl.mechanism=PLAIN
#cluster2.kafka.eagle.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="admin" password="kafka-eagle";

######################################
# kafka jdbc driver address
######################################
kafka.eagle.driver=org.sqlite.JDBC
#将url设置在本地
kafka.eagle.url=jdbc:sqlite:/C:/Kafka/kafka-eagle-web-1.3.7/db/ke.db
#进入系统需要用到的账号与密码
kafka.eagle.username=root
kafka.eagle.password=123456
```

1. bin/ke.sh start

## Kafka 面试

1. Kafka 中的 ISR(InSyncRepli)、 OSR(OutSyncRepli)、 AR(AllRepli)代表什么？
2. Kafka 中的 HW、 LEO 等分别代表什么？
3. Kafka 中是怎么体现消息顺序性的？
4. Kafka 中的分区器、序列化器、拦截器是否了解？它们之间的处理顺序是什么？
5. Kafka 生产者客户端的整体结构是什么样子的？使用了几个线程来处理？分别是什么？
6. “消费组中的消费者个数如果超过 topic 的分区，那么就会有消费者消费不到数据”这句 话是否正确？
7. 消费者提交消费位移时提交的是当前消费到的最新消息的 offset 还是 offset+1？**offset+1**
8. 有哪些情形会造成重复消费？
9. 那些情景会造成消息漏消费？
10. 当你使用 kafka-topics.sh 创建（删除）了一个 topic 之后， Kafka 背后会执行什么逻辑？
    * 会在 zookeeper 中的/brokers/topics 节点下创建一个新的 topic 节点，如：/brokers/topics/first
    * 触发 Controller 的监听程序
    * kafka Controller 负责 topic 的创建工作，并更新 metadata cache
11. topic 的分区数可不可以增加？如果可以怎么增加？如果不可以，那又是为什么？
12. topic 的分区数可不可以减少？如果可以怎么减少？如果不可以，那又是为什么？（不行）
13. Kafka 有内部的 topic 吗？如果有是什么？有什么所用？（\_\_consumer\_offset）
14. Kafka 分区分配的概念？
15. 简述 Kafka 的日志目录结构？
    * log，index
16. 如果我指定了一个 offset， Kafka Controller 怎么查找到对应的消息？
    * log，index
17. 聊一聊 Kafka Controller 的作用？
    * 使得能通过zk管理整个kafka集群
18. Kafka 中有那些地方需要选举？这些地方的选举策略又有哪些？
    * KAFKA controller 暴力竞争
    * leader ISR
19. Kafka 的哪些设计让它有如此高的性能？
    * 分布式
    * 顺序写磁盘
    * 零拷贝
20. kafka消息数据积压，消费能力不足时
    * 增加topic数，提高消费者数量，使得消费者数=分区数
    * API控制提高每批次poll的数量
21. kafka挂掉（短期没事）
    * 副本
    * Flume记录
    * sql日志记录
22. kafka实际数据量

[Kafka常见面试题(附个人解读答案+持续更新)](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fblog.csdn.net%2FC\_Xiang\_Falcon%2Farticle%2Fdetails%2F100917145)
