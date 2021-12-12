一。MyBatis

    1.简介
    
    ① MyBatis是一个持久层框架，完成的是对数据库的访问和操作；（CRUD）
    ② 它解决了JDBC对数据库的操作与访问过程中存在的问题，是对原有JDBC技术的封装
    ③ MyBatis解决JDBC的问题
    	【1】虽然JDBC操作数据库的方式很直观，但其核心就是对于数据库的操作。多个方法间存在大量的冗余
    	【2】基于Java中面向对象的基本思想，所以我们会将查询出来的结果封装成一个对象，这一系列操作都需要手动完成；将对象和表单做一个映射关系，将一个个属性提取出来并赋值给对象，手动ORM映射；
    	【3】对于多个用户的相同查询操作，没有进行优化，这样会造成运行效率偏低（主要指缓存层Cache）
    ④ 以.xml配置文件的方式实现DAO接口去除其中冗余代码
    2.MyBatis运行原理与流程图
    
        ① 运行原理
    
            MyBatis应用程序根据XML配置文件创建SqlSessionFactory，SqlSessionFactory在根据配置，获取一个SqlSession，配置和来源于两个地方，一处是配置文件，一处来源于Java代码的注解。SqlSession包含了执行sql所需要的所有方法，可以通过SqlSession实例直接运行映射的sql语句，完成对数据的CRUD操作和事务提交等，完成后关闭。


​                

    3.MyBatis缓存
    
    ① 原理：
    
            将频繁查询的数据存储在硬盘中，作为缓存区;当客户端发送请求时，缓存区没有相应的结果，那么就进入数据库查询结果，先在缓存区中存储在返回给客户端，如果缓存区中有，那么就直接返回给客户端.
    
    目的：较少与数据库的通信次数，提高程序的查询效率
    缺点：成本高  不安全  缓存在实践张一丁只存储那些频繁查询的数据   以硬盘的空间换取程序运行的时间
    
        ② 注意事项：
        【1】 只有当SqlSession关闭时，数据才会存入缓存区
        【2】 脏数据问题：当缓存区中数据与数据库中数据不一致时，我们成缓存中的这一部分为脏数据
        【3】 MyBatis在进行事务提交时，会自动清空缓存
    
        【4】 在查询操作后一定要关闭SqlSession   增删改操作一定要控制事务
    
    4.$与#的区别
    	① #{} 是以？占位符的形式成成sql语句，然后在赋值，相当于调用了pstmt.setXXX()这个方法给sql语句中的条件赋值
    	    ${} 是以字符串拼接的形式来给sql语句赋值的（将sql语句拆分成多个字符串，将所要插入的值放入字符串拼接的部分，并将两部分sql语句连接起来）
    	② #{}替换的是值，不管输入什么内容都将其当做一个字符串看待 
    
    	    ${}替换的是字段  存在sql注入问题 
    
    5.事务的四个特性
     ① 原子性：同一事务的多条sql语句时不可分割的整体，都成功才成功，有一条失败则全部失败
     ② 一致性：事务开始前和事务结束后，数据库的数据保持一致
     ③ 隔离性：事务与事务之间相互独立，互不干涉
    
     ④ 持久性：事务执行后，对数据库的影响是永久的
    
    6.数据库的事务隔离级别
     	① 脏读：读未提交（事务锁）
     		一个事务读取了另一个事务没有提交的临时数据
     	② 不可重复读：读提交（行级锁）
     		一个事务中对相同的数据，进行多次读取，读取结果不一致
     	③ 幻影读：重复读（表级锁）
     		一个事务中对同一张表的多次查询，但结果不一致
     	④ 序列化
    
     		序列化是事务隔离级别最高的级别，在该模式下所有事务按顺序执行，可以避免以上三种情况

二。Redis

    1.简介
    	redis是一个基于内存的高性能的key value的NoSQL数据库,其中内部数据类型丰富，包括以下五种：
     	① List：有序可重复
     	② Set：无序不可重复
     	③ hash：存储键值对
     	④ String：所有key的类型
     	⑤ ZSet：可排序  按分排序  可做排行榜使用，使用ArrayList实现
     	
    2.什么是NoSQL（Not Only SQL）
    	① 关系型数据库（RDB）：以表格的形式存储数据
    	② NoSQL类型数据库：不易表格的形式存储数据
    	
    3.NoSQL类型数据库的特点：
    	① Schemaless：弱结构化（忽略表格 行 列），表格式存储数据结构太过僵化
    	② In-Memory 内存型产品
    	③ 弱化事务（没有事务  事务简单）：有事务会影响并发，弱化事务会提高系统运行效率
    	④ 适用于Cluster（集群）环境
    	⑤ 没有复杂的表连接查询操作：表连接的工作原理：拿a表的一条数据与b表的所有数据一一进行比对，运行效率低
    	⑥ 支持脚本编程语言（JavaScript  lua ...）	
    	⑦ 常见的NOSQL产品
    		[1]Redis（key value）
    		[2]MongoDB（JSON）
    		[3]HBASE  存储列类型
    		[4]Cassandra  存储列类型 永远没有单节点故障
    
    4.Redis的特点
    	① Redis是一个高性能的key value数据库
    	② 是一个基于内存的数据库产品
    	③ 内部数据类型丰富
    	④ 可持久化
    	⑤ 可用于订阅/发布 模型
    
    5.Redis 与 Memcache的区别
    	① Redis是一个内存行数据库产品，而Mmcache是一个内存型缓存产品，Redis可以对内存中的数据进行持久化，而Memcache不行
    	② Redis不仅仅支持简单的key value类型的数据，同时还提供list，set，hash等数据结构存储
    	③ Redis支持数据的主从赋值，即集群环境下的数据备份
    	④ Redis支持数据的持久化，可以将内存中年的数据保存到磁盘中，重启的时候可以再次加载原有数据
    	⑤ Redis单个value最大限制是1GB，memcached只能保存1MB的数据
    
    6.基础命令
    	① String类型
    		[1]flushall  		：清空所有数据库中的所有key	
    			flushdb  		：清空当前数据库中的所有key
    		[2]keys *    		：查询所有key
    		[3]get key   		：取值
    		[4]set key value	：存值
    		[5]msetnx key key	：创建多个key
    		[6]mget key key 	：一次取多个值
    		[7]getset key value ：获取原始值并设置新值
    		[8]strlen key 		：获取值长度
    		[9]append key value ：追加值内容
    		[10]getrange key a b：截取value值，从a下标开始到b下标结束
    		[11]setnx key value ：如果key不存在则创建，如果key存在则不创建
    		[12]decr key  		：对数值类型的值-1
    		[13]decrby key x 	：对数值类型的值指定-x
    		[14]incr key  		：对数值类型的值+1
    		[15]incrby key x 	：对数值类型的值指定+x
    		[16]incrbyfloat key y 		：对数值类型的值指定+y浮点数
    		[17]setex key x value		：设置一个key的存活时间为x秒
    		[18]psetex key x value		：设置一个key的存活时间为x毫秒
    		[19]mset key1 value1 key2 value2	：一次设置多个key value
    		
    	② List类型（有序可重复）
    		[1]lpush key value 	：存值 每次将value存入下标0位置   类似压栈
    		[2]lrang key 0 -1  	：从下标0开始获取到返回-1结束
    		[3]lpushx key value	：向已存在key中存值
    		[4]rpush key value 	：存值
    		[5]rpushx key value	：向已存在key中存值
    		[6]lpop key			：返回并移除第一个元素
    		[7]rpop key 		：返回并移除最后一个元素
    		[8]lrange key a b 	：取值从下标a开始到下标b结束
    		[9]llen key 		：获取元素个数
    		[10]lset key x value：替换下标x位置的值
    		[12]lindex key x  	：获取下标x位置的值
    		[13]lrem key x value：删除key中重复value的x个元素
    		[14]lrange key a b	：截取从下标a开始到下标b结束
    		[15]linsert key before value1 value2：在value1之前插入value2
    		
    	③ Set类型（无序不可重复）
    		[1]sadd key value 	：存值
    		[2]smembers key   	：显示所有值
    		[3]scard key	  	：返回元素个数
    		[4]spop key 	  	：随机获取一个元素 并移除
    		[5]sunion key1 key2	：查询key1和key2
    		[6]srem key value   ：删除指定value
    		[7]srandmember key 	：随机返回一个元素
    		[8]sdiff key1 key2 	：删除key1 key2共有的元素
    		[9]sinter key1 key2	：查询key1 key2共有的元素
    		[10]smove key1 value key2	：将key1中的一个value移动到key2中
    		[11]sismember key value 	：判断值是否存在
    		
    	④ ZSet（可排序  按分排序  可做排行榜）
    		[1]zadd key n value ：添加一个key 分数为n
    		[2]zcard key		：返回元素个数
    		[3]zrange key a b	：查询从下标a开始到下标b结束
    		   zrange key a -1 	：查询从下标a开始到返回-1结束
    		[4]zrem key value   ：移除
    		[5]zrank key value	：查询指定value的下标
    		[6]zscore key value ：查询指定value的分数
    		[7]zrangebyscore key x y	：查询分数x到y之间的value
    		[8]zrevrank key value		：倒序查询指定value的下标
    		[9]zincrby key x value 		：为指定value分数增加x
    		
    	⑤ Hash（存储键值对）
    		[1]hset Key key value	：在Key中存储一个键值对
    		[2]hget Key key 		：通过键取值
    		[3]hgetall Key  		：查询所有键值对
    		[4]hdel Key key 		：通过key删除对应的键值对
    		[5]hexists Key key		：判断Key中key是否存在
    		[6]hkeys Key 			：查询所有key
    		[7]hvals Key 			：查询所有value
    		[8]hmset Key key value key value：设置多个键值对
    		[9]hmget Key key1 key2	：获取多个key的值
    		[10]hsetnx key value	：设置一个不存在的key的值
    		[11]hincrby Key key x	：对数值类型的值指定+x
    
    		[12]hincrbyfloat Key key y 		：对数值类型的值指定+y浮点数
    
        7.Redis的持久化
    	Redis是一个支持持久化的内存数据库，也就是说Redis需要经常将内存中的数据同步到磁盘来保证持久化。
    	Redis支持两种持久化方式，一种是Snapshottiong（快照RDB）（默认），另一种是Append-Only File（缩写AOF）的方式
    	① RDB
    		【1】运行机制：在某个时间点保存一个完整的数据快照
    		【2】运行原理：
    			Redis是一个单进程的服务，通过fork产生子进程，父进程继续处理Client请求，子进程负责将快照写入临时文件中，子进程写完后，用临时文件替换原来的快照文件，然后子进程退出
    	② AOF
    		【1】 运行机制：直接将Redis的执行命令，直接写在log文件中
    		【2】运行原理：
    			Redis通过fork一个子进程，父进程继续处理Client请求，子进程把AOF内容写入缓冲区，子进程写完退出，父进程接收退出消息，将缓冲区AOF写入临时文件，临时文件重命名为appendonly.aof 原来文件被覆盖，整个过程完成
    8.分布式缓存原理
     	 分布式缓存由服务端实现管理和控制，由多个客户端节点存储数据，可以进一步提高数据的读取速度。当我们要读取某个数据的时候会根据一致性哈希算法确定数据的存储和读取节点，通过对应的哈希值找到对应的节点，一致性哈希算法的好处在于节点个数发生变化时无需重新计算哈希值，保证数据存储或读取时间可以正确、快速的找到对应的节点、分布式缓存能够高性能的读取数据、能够动态的扩展缓存节点、能够自动发现和切换故障节点、能够自动均衡数据分区，而且能够为使用者提供图形化的界面管理，部署和维护都十分方便，一致性哈希算法是使用MD5与MurmurHash两种计算方式，计算hash值，通过java的TreeMap来模拟换装结构实现均匀分布

由于Redis的数据都存放在内存中，如果没有配置持久化，redis重启后数据就全丢失了，于是需要开启redis的持久化功能，将数据保存到磁盘上，当redis重启后，可以从磁盘中恢复数据。redis提供两种方式进行持久化，一种是RDB持久化（原理是将Reids在内存中的数据库记录定时dump到磁盘上的RDB持久化），另外一种是AOF（append only file）持久化（原理是将Reids的操作日志以追加的方式写入文件）。那么这两种持久化方式有什么区别呢，改如何选择呢？网上看了大多数都是介绍这两种方式怎么配置，怎么使用，就是没有介绍二者的区别，在什么应用场景下使用。

2、二者的区别

RDB持久化是指在指定的时间间隔内将内存中的数据集快照写入磁盘，实际操作过程是fork一个子进程，先将数据集写入临时文件，写入成功后，再替换之前的文件，用二进制压缩存储。

![img](https://images2017.cnblogs.com/blog/388326/201707/388326-20170726161552843-904424952.png)

 

AOF持久化以日志的形式记录服务器所处理的每一个写、删除操作，查询操作不会记录，以文本的方式记录，可以打开文件看到详细的操作记录。

![img](https://images2017.cnblogs.com/blog/388326/201707/388326-20170726161604968-371688235.png)

 

3、二者优缺点

RDB存在哪些优势呢？

1). 一旦采用该方式，那么你的整个Redis数据库将只包含一个文件，这对于文件备份而言是非常完美的。比如，你可能打算每个小时归档一次最近24小时的数据，同时还要每天归档一次最近30天的数据。通过这样的备份策略，一旦系统出现灾难性故障，我们可以非常容易的进行恢复。

2). 对于灾难恢复而言，RDB是非常不错的选择。因为我们可以非常轻松的将一个单独的文件压缩后再转移到其它存储介质上。

3). 性能最大化。对于Redis的服务进程而言，在开始持久化时，它唯一需要做的只是fork出子进程，之后再由子进程完成这些持久化的工作，这样就可以极大的避免服务进程执行IO操作了。

4). 相比于AOF机制，如果数据集很大，RDB的启动效率会更高。

RDB又存在哪些劣势呢？

1). 如果你想保证数据的高可用性，即最大限度的避免数据丢失，那么RDB将不是一个很好的选择。因为系统一旦在定时持久化之前出现宕机现象，此前没有来得及写入磁盘的数据都将丢失。

2). 由于RDB是通过fork子进程来协助完成数据持久化工作的，因此，如果当数据集较大时，可能会导致整个服务器停止服务几百毫秒，甚至是1秒钟。

AOF的优势有哪些呢？

1). 该机制可以带来更高的数据安全性，即数据持久性。Redis中提供了3中同步策略，即每秒同步、每修改同步和不同步。事实上，每秒同步也是异步完成的，其效率也是非常高的，所差的是一旦系统出现宕机现象，那么这一秒钟之内修改的数据将会丢失。而每修改同步，我们可以将其视为同步持久化，即每次发生的数据变化都会被立即记录到磁盘中。可以预见，这种方式在效率上是最低的。至于无同步，无需多言，我想大家都能正确的理解它。

2). 由于该机制对日志文件的写入操作采用的是append模式，因此在写入过程中即使出现宕机现象，也不会破坏日志文件中已经存在的内容。然而如果我们本次操作只是写入了一半数据就出现了系统崩溃问题，不用担心，在Redis下一次启动之前，我们可以通过redis-check-aof工具来帮助我们解决数据一致性的问题。

3). 如果日志过大，Redis可以自动启用rewrite机制。即Redis以append模式不断的将修改数据写入到老的磁盘文件中，同时Redis还会创建一个新的文件用于记录此期间有哪些修改命令被执行。因此在进行rewrite切换时可以更好的保证数据安全性。

4). AOF包含一个格式清晰、易于理解的日志文件用于记录所有的修改操作。事实上，我们也可以通过该文件完成数据的重建。

AOF的劣势有哪些呢？

1). 对于相同数量的数据集而言，AOF文件通常要大于RDB文件。RDB 在恢复大数据集时的速度比 AOF 的恢复速度要快。

2). 根据同步策略的不同，AOF在运行效率上往往会慢于RDB。总之，每秒同步策略的效率是比较高的，同步禁用策略的效率和RDB一样高效。

二者选择的标准，就是看系统是愿意牺牲一些性能，换取更高的缓存一致性（aof），还是愿意写操作频繁的时候，不启用备份来换取更高的性能，待手动运行save的时候，再做备份（rdb）。rdb这个就更有些 eventually consistent的意思了。

4、常用配置

RDB持久化配置

Redis会将数据集的快照dump到dump.rdb文件中。此外，我们也可以通过配置文件来修改Redis服务器dump快照的频率，在打开6379.conf文件之后，我们搜索save，可以看到下面的配置信息：

save 900 1       #在900秒(15分钟)之后，如果至少有1个key发生变化，则dump内存快照。

save 300 10      #在300秒(5分钟)之后，如果至少有10个key发生变化，则dump内存快照。

save 60 10000    #在60秒(1分钟)之后，如果至少有10000个key发生变化，则dump内存快照。

AOF持久化配置

在Redis的配置文件中存在三种同步方式，它们分别是：

appendfsync always   #每次有数据修改发生时都会写入AOF文件。

appendfsync everysec #每秒钟同步一次，该策略为AOF的缺省策略。

appendfsync no     #从不同步。高效但是数据不会被持久化。

#### MySql+Memcached架构的问题

　　实际MySQL是适合进行海量数据存储的，通过Memcached将热点数据加载到cache，加速访问，很多公司都曾经使用过这样的架构，但随着业务数据量的不断增加，和访问量的持续增长，我们遇到了很多问题：

　　1.MySQL需要不断进行拆库拆表，Memcached也需不断跟着扩容，扩容和维护工作占据大量开发时间。

　　2.Memcached与MySQL数据库数据一致性问题。

　　3.Memcached数据命中率低或down机，大量访问直接穿透到DB，MySQL无法支撑。

　　4.跨机房cache同步问题。

　　众多NoSQL百花齐放，如何选择

　　最近几年，业界不断涌现出很多各种各样的NoSQL产品，那么如何才能正确地使用好这些产品，最大化地发挥其长处，是我们需要深入研究和思考的问题，实际归根结底最重要的是了解这些产品的定位，并且了解到每款产品的tradeoffs，在实际应用中做到扬长避短，总体上这些NoSQL主要用于解决以下几种问题

　　1.少量数据存储，高速读写访问。此类产品通过数据全部in-momery 的方式来保证高速访问，同时提供数据落地的功能，实际这正是Redis最主要的适用场景。

　　2.海量数据存储，分布式系统支持，数据一致性保证，方便的集群节点添加/删除。

　　3.这方面最具代表性的是dynamo和bigtable 2篇论文所阐述的思路。前者是一个完全无中心的设计，节点之间通过gossip方式传递集群信息，数据保证最终一致性，后者是一个中心化的方案设计，通过类似一个分布式锁服务来保证强一致性,数据写入先写[内存](http://product.it168.com/list/b/0205_1.shtml)和redo log，然后定期compat归并到磁盘上，将随机写优化为顺序写，提高写入性能。

　　4.Schema free，auto-sharding等。比如目前常见的一些文档数据库都是支持schema-free的，直接存储json格式数据，并且支持auto-sharding等功能，比如mongodb。

​    Redis最适合所有数据in-momory的场景，虽然Redis也提供持久化功能，但实际更多的是一个disk-backed的功能，跟传统意义上的持久化有比较大的差别，那么可能大家就会有疑问，似乎Redis更像一个加强版的Memcached，那么何时使用Memcached,何时使用Redis呢?

​    如果简单地比较Redis与Memcached的区别，大多数都会得到以下观点：

   1 、Redis不仅仅支持简单的k/v类型的数据，同时还提供list，set，zset，hash等数据结构的存储。
   2 、Redis支持数据的备份，即master-slave模式的数据备份。
   3 、Redis支持数据的持久化，可以将内存中的数据保持在磁盘中，重启的时候可以再次加载进行使用。

　　通过一张图了解下Redis内部内存管理中是如何描述这些不同数据类型的：

![img](https://images2018.cnblogs.com/blog/1368782/201808/1368782-20180821201924742-1470196696.png)

　　首先Redis内部使用一个redisObject对象来表示所有的key和value，redisObject最主要的信息如上图所示：type代表一个value对象具体是何种数据类型，encoding是不同数据类型在redis内部的存储方式，比如：type=string代表value存储的是一个普通字符串，那么对应的encoding可以是raw或者是int，如果是int则代表实际redis内部是按数值型类存储和表示这个字符串的，当然前提是这个字符串本身可以用数值表示，比如:"123" "456"这样的字符串。

　　这里需要特殊说明一下vm字段，只有打开了Redis的虚拟内存功能，此字段才会真正的分配内存，该功能默认是关闭状态的。通过上图我们可以发现Redis使用redisObject来表示所有的key/value数据是比较浪费内存的，当然这些内存管理成本的付出主要也是为了给Redis不同数据类型提供一个统一的管理接口，实际作者也提供了多种方法帮助我们尽量节省内存使用，我们随后会具体讨论。

 

　　Redis支持5种数据类型：string（字符串），hash（哈希），list（列表），set（集合）及zset(sorted set：有序集合)。

　　① **string** 是 redis 最基本的类型，你可以理解成与 Memcached 一模一样的类型，一个 key 对应一个 value。value其实不仅是String，也可以是数字。string 类型是二进制安全的。意思是 redis 的 string 可以包含任何数据。比如jpg图片或者序列化的对象。string 类型是 Redis 最基本的数据类型，string 类型的值最大能存储 512MB。

　　常用命令：get、set、incr、decr、mget等。

　　应用场景：String是最常用的一种数据类型，普通的key/ value 存储都可以归为此类，即可以完全实现目前 Memcached 的功能，并且效率更高。还可以享受Redis的定时持久化，操作日志及 Replication等功能。除了提供与 Memcached 一样的get、set、incr、decr 等操作外，Redis还提供了下面一些操作： 

- 获取字符串长度
- 往字符串append内容
- 设置和获取字符串的某一段内容
- 设置及获取字符串的某一位（bit）
- 批量设置一系列字符串的内容

　　使用场景：常规key-value缓存应用。常规计数: 微博数, 粉丝数。

　　实现方式：String在redis内部存储默认就是一个字符串，被redisObject所引用，当遇到incr,decr等操作时会转成数值型进行计算，此时redisObject的encoding字段为int。

```
redis 127.0.0.1:6379> SET name "runoob"
"OK"
redis 127.0.0.1:6379> GET name
"runoob"
```

　　在以上实例中我们使用了 Redis 的 **SET** 和 **GET** 命令。键为 name，对应的值为 **runoob**。

　　**注意：**一个键最大能存储512MB。

　　② **Redis hash** 是一个键值(key => value)对集合。Redis hash 是一个 string 类型的 field 和 value 的映射表，hash 特别适合用于存储对象。

　　常用命令：hget,hset,hgetall 等。

　　应用场景：我们简单举个实例来描述下Hash的应用场景，比如我们要存储一个用户信息对象数据，包含以下信息：

　　　　用户ID为查找的key，存储的value用户对象包含姓名，年龄，生日等信息，如果用普通的key/value结构来存储，主要有以下2种存储方式：

![img](https://images2018.cnblogs.com/blog/1368782/201808/1368782-20180821202451984-479691318.png)

　　　　第一种方式将用户ID作为查找key,把其他信息封装成一个对象以序列化的方式存储，这种方式的缺点是，增加了序列化/反序列化的开销，并且在需要修改其中一项信息时，需要把整个对象取回，并且修改操作需要对并发进行保护，引入CAS等复杂问题。

![img](https://images2018.cnblogs.com/blog/1368782/201808/1368782-20180821202546802-636845502.png)

　　　　第二种方法是这个用户信息对象有多少成员就存成多少个key-value对儿，用用户ID+对应属性的名称作为唯一标识来取得对应属性的值，虽然省去了序列化开销和并发问题，但是用户ID为重复存储，如果存在大量这样的数据，内存浪费还是非常可观的。

　　　　那么Redis提供的Hash很好的解决了这个问题，Redis的Hash实际是内部存储的Value为一个HashMap，并提供了直接存取这个Map成员的接口，如下图：

![img](https://images2018.cnblogs.com/blog/1368782/201808/1368782-20180821202632663-939669694.png)

　　　　也就是说，Key仍然是用户ID, value是一个Map，这个Map的key是成员的属性名，value是属性值，这样对数据的修改和存取都可以直接通过其内部Map的Key(Redis里称内部Map的key为field), 也就是通过 key(用户ID) + field(属性标签) 就可以操作对应属性数据了，既不需要重复存储数据，也不会带来序列化和并发修改控制的问题，很好的解决了问题。

　　　　这里同时需要注意，Redis提供了接口(hgetall)可以直接取到全部的属性数据，但是如果内部Map的成员很多，那么涉及到遍历整个内部Map的操作，由于Redis单线程模型的缘故，这个遍历操作可能会比较耗时，而另其它客户端的请求完全不响应，这点需要格外注意。

　　使用场景：存储部分变更数据，如用户信息等。

　　实现方式：上面已经说到Redis Hash对应Value内部实际就是一个HashMap，实际这里会有2种不同实现，这个Hash的成员比较少时Redis为了节省内存会采用类似一维数组的方式来紧凑存储，而不会采用真正的HashMap结构，对应的value redisObject的encoding为zipmap，当成员数量增大时会自动转成真正的HashMap，此时encoding为ht。

```
redis> HSET myhash field1 "Hello" field2 "World"
"OK"
redis> HGET myhash field1
"Hello"
redis> HGET myhash field2
"World"
```

　　实例中我们使用了 Redis **HMSET, HGET** 命令，**HMSET** 设置了两个 field=>value 对, HGET 获取对应 **field** 对应的 **value**。每个 hash 可以存储 232 -1 键值对（40多亿）。

　　③ **Redis list** 列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素到列表的头部（左边）或者尾部（右边）。

　　常用命令：lpush（添加左边元素）,rpush,lpop（移除左边第一个元素）,rpop,lrange（获取列表片段，LRANGE key start stop）等。

　　应用场景：Redis list的应用场景非常多，也是Redis最重要的数据结构之一，比如twitter的关注列表，粉丝列表等都可以用Redis的list结构来实现。

　　　　List 就是链表，相信略有数据结构知识的人都应该能理解其结构。使用List结构，我们可以轻松地实现最新消息排行等功能。List的另一个应用就是消息队列，
可以利用List的PUSH操作，将任务存在List中，然后工作线程再用POP操作将任务取出进行执行。Redis还提供了操作List中某一段的api，你可以直接查询，删除List中某一段的元素。

　　实现方式：Redis list的实现为一个双向链表，即可以支持反向查找和遍历，更方便操作，不过带来了部分额外的内存开销，Redis内部的很多实现，包括发送缓冲队列等也都是用的这个数据结构。

　　Redis的list是每个子元素都是String类型的双向链表，可以通过push和pop操作从列表的头部或者尾部添加或者删除元素，这样List即可以作为栈，也可以作为队列。 获取越接近两端的元素速度越快，但通过索引访问时会比较慢。

使用场景：

　　消息队列系统：使用list可以构建队列系统，使用sorted set甚至可以构建有优先级的队列系统。比如：将Redis用作日志收集器，实际上还是一个队列，多个端点将日志信息写入Redis，然后一个worker统一将所有日志写到磁盘。

　　取最新N个数据的操作：记录前N个最新登陆的用户Id列表，超出的范围可以从数据库中获得。

```
//把当前登录人添加到链表里
ret = r.lpush("login:last_login_times", uid)
//保持链表只有N位
ret = redis.ltrim("login:last_login_times", 0, N-1)
//获得前N个最新登陆的用户Id列表
last_login_list = r.lrange("login:last_login_times", 0, N-1)
```

比如微博：

　　在Redis中我们的最新微博ID使用了常驻缓存，这是一直更新的。但是我们做了限制不能超过5000个ID，因此我们的获取ID函数会一直询问Redis。只有在start/count参数超出了这个范围的时候，才需要去访问数据库。我们的系统不会像传统方式那样“刷新”缓存，Redis实例中的信息永远是一致的。SQL数据库（或是硬盘上的其他类型数据库）只是在用户需要获取“很远”的数据时才会被触发，而主页或第一个评论页是不会麻烦到硬盘上的数据库了。

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
redis 127.0.0.1:6379> lpush runoob redis
(integer) 1
redis 127.0.0.1:6379> lpush runoob mongodb
(integer) 2
redis 127.0.0.1:6379> lpush runoob rabitmq
(integer) 3
redis 127.0.0.1:6379> lrange runoob 0 10
1) "rabitmq"
2) "mongodb"
3) "redis"
redis 127.0.0.1:6379>
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　列表最多可存储 232 - 1 元素 (4294967295, 每个列表可存储40多亿)。

　　④ **Redis set**是string类型的无序集合。集合是通过hashtable实现的，概念和数学中个的集合基本类似，可以交集，并集，差集等等，set中的元素是没有顺序的。所以添加，删除，查找的复杂度都是O(1)。

　　*sadd 命令：*添加一个 string 元素到 key 对应的 set 集合中，成功返回1，如果元素已经在集合中返回 0，如果 key 对应的 set 不存在则返回错误。

　　常用命令：sadd,spop,smembers,sunion 等。

　　应用场景：Redis set对外提供的功能与list类似是一个列表的功能，特殊之处在于set是可以自动排重的，当你需要存储一个列表数据，又不希望出现重复数据时，set是一个很好的选择，并且set提供了判断某个成员是否在一个set集合内的重要接口，这个也是list所不能提供的。

　　Set 就是一个集合，集合的概念就是一堆不重复值的组合。利用Redis提供的Set数据结构，可以存储一些集合性的数据。

　　案例：在微博中，可以将一个用户所有的关注人存在一个集合中，将其所有粉丝存在一个集合。Redis还为集合提供了求交集、并集、差集等操作，可以非常方便的实现如共同关注、共同喜好、二度好友等功能，对上面的所有集合操作，你还可以使用不同的命令选择将结果返回给客户端还是存集到一个新的集合中。

　　实现方式： set 的内部实现是一个 value永远为null的HashMap，实际就是通过计算hash的方式来快速排重的，这也是set能提供判断一个成员是否在集合内的原因。

　　使用场景：

　　①交集，并集，差集：(Set)

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//book表存储book名称
set book:1:name    ”The Ruby Programming Language”
set book:2:name     ”Ruby on rail”
set book:3:name     ”Programming Erlang”
//tag表使用集合来存储数据，因为集合擅长求交集、并集
sadd tag:ruby 1
sadd tag:ruby 2
sadd tag:web 2
sadd tag:erlang 3
//即属于ruby又属于web的书？
inter_list = redis.sinter("tag.web", "tag:ruby") 
//即属于ruby，但不属于web的书？
inter_list = redis.sdiff("tag.ruby", "tag:web") 
//属于ruby和属于web的书的合集？
inter_list = redis.sunion("tag.ruby", "tag:web")
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　②获取某段时间所有数据去重值

　　这个使用Redis的set数据结构最合适了，只需要不断地将数据往set中扔就行了，set意为集合，所以会自动排重。

```
sadd key member
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
redis 127.0.0.1:6379> sadd runoob redis
(integer) 1
redis 127.0.0.1:6379> sadd runoob mongodb
(integer) 1
redis 127.0.0.1:6379> sadd runoob rabitmq
(integer) 1
redis 127.0.0.1:6379> sadd runoob rabitmq
(integer) 0
redis 127.0.0.1:6379> smembers runoob
1) "redis"
2) "rabitmq"
3) "mongodb"
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　**注意：**以上实例中 rabitmq 添加了两次，但根据集合内元素的唯一性，第二次插入的元素将被忽略。集合中最大的成员数为 232 - 1(4294967295, 每个集合可存储40多亿个成员)。

　　⑤ **Redis zset** 和 set 一样也是string类型元素的集合,且不允许重复的成员。

　　*zadd 命令：*添加元素到集合，元素在集合中存在则更新对应score。

　　常用命令：zadd,zrange,zrem,zcard等

　　使用场景：Redis sorted set的使用场景与set类似，区别是set不是自动有序的，而sorted set可以通过用户额外提供一个优先级(score)的参数来为成员排序，并且是插入有序的，即自动排序。当你需要一个有序的并且不重复的集合列表，那么可以选择sorted set数据结构，比如twitter 的public timeline可以以发表时间作为score来存储，这样获取时就是自动按时间排好序的。和Set相比，**Sorted Set关联了一个double类型权重参数score**，使得集合中的元素能够按score进行有序排列，redis正是通过分数来为集合中的成员进行从小到大的排序。zset的成员是唯一的,但分数(score)却可以重复。比如一个存储全班同学成绩的Sorted Set，其集合value可以是同学的学号，而score就可以是其考试得分，这样在数据插入集合的时候，就已经进行了天然的排序。另外还可以用Sorted Set来做带权重的队列，比如普通消息的score为1，重要消息的score为2，然后工作线程可以选择按score的倒序来获取工作任务。让重要的任务优先执行。

　　实现方式：Redis sorted set的内部使用HashMap和跳跃表(SkipList)来保证数据的存储和有序，HashMap里放的是成员到score的映射，而跳跃表里存放的是所有的成员，排序依据是HashMap里存的score,使用跳跃表的结构可以获得比较高的查找效率，并且在实现上比较简单。

```
zadd key score member
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
redis 127.0.0.1:6379> zadd runoob 0 redis
(integer) 1
redis 127.0.0.1:6379> zadd runoob 0 mongodb
(integer) 1
redis 127.0.0.1:6379> zadd runoob 0 rabitmq
(integer) 1
redis 127.0.0.1:6379> zadd runoob 0 rabitmq
(integer) 0
redis 127.0.0.1:6379> > ZRANGEBYSCORE runoob 0 1000
1) "mongodb"
2) "rabitmq"
3) "redis"
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

各个数据类型应用场景：

| 类型                 | 简介                                                   | 特性                                                         | 场景                                                         |
| -------------------- | ------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| String(字符串)       | 二进制安全                                             | 可以包含任何数据,比如jpg图片或者序列化的对象,一个键最大能存储512M | ---                                                          |
| Hash(字典)           | 键值对集合,即编程语言中的Map类型                       | 适合存储对象,并且可以像数据库中update一个属性一样只修改某一项属性值(Memcached中需要取出整个字符串反序列化成对象修改完再序列化存回去) | 存储、读取、修改用户属性                                     |
| List(列表)           | 链表(双向链表)                                         | 增删快,提供了操作某一段元素的API                             | 1、最新消息排行等功能(比如朋友圈的时间线) 2、消息队列        |
| Set(集合)            | 哈希表实现,元素不重复                                  | 1、添加、删除、查找的复杂度都是O(1) 2、为集合提供了求交集、并集、差集等操作 | 1、共同好友 2、利用唯一性,统计访问网站的所有独立ip 3、好友推荐时,根据tag求交集,大于某个阈值就可以推荐 |
| Sorted Set(有序集合) | 将Set中的元素增加一个权重参数score,元素按score有序排列 | 数据插入集合时,已经进行天然排序                              | 1、排行榜 2、带权重的消息队列                                |

###  

### Redis实际应用场景

　　Redis在很多方面与其他数据库解决方案不同：它使用内存提供主存储支持，而仅使用硬盘做持久性的存储；它的数据模型非常独特，用的是单线程。另一个大区别在于，你可以在开发环境中使用Redis的功能，但却不需要转到Redis。

　　转向Redis当然也是可取的，许多开发者从一开始就把Redis作为首选数据库；但设想如果你的开发环境已经搭建好，应用已经在上面运行了，那么更换数据库框架显然不那么容易。另外在一些需要大容量数据集的应用，Redis也并不适合，因为它的数据集不会超过系统可用的内存。所以如果你有大数据应用，而且主要是读取访问模式，那么Redis并不是正确的选择。

　　然而我喜欢Redis的一点就是你可以把它融入到你的系统中来，这就能够解决很多问题，比如那些你现有的数据库处理起来感到缓慢的任务。这些你就可以通过Redis来进行优化，或者为应用创建些新的功能。在本文中，我就想探讨一些怎样将Redis加入到现有的环境中，并利用它的原语命令等功能来解决 传统环境中碰到的一些常见问题。在这些例子中，Redis都不是作为首选数据库。

**1、显示最新的项目列表**

　　下面这个语句常用来显示最新项目，随着数据多了，查询毫无疑问会越来越慢。

```
SELECT * FROM foo WHERE ... ORDER BY time DESC LIMIT 10 
```

　　在Web应用中，“列出最新的回复”之类的查询非常普遍，这通常会带来可扩展性问题。这令人沮丧，因为项目本来就是按这个顺序被创建的，但要输出这个顺序却不得不进行排序操作。

　　类似的问题就可以用Redis来解决。比如说，我们的一个Web应用想要列出用户贴出的最新20条评论。在最新的评论边上我们有一个“显示全部”的链接，点击后就可以获得更多的评论。

​    我们假设数据库中的每条评论都有一个唯一的递增的ID字段。我们可以使用分页来制作主页和评论页，使用Redis的模板，每次新评论发表时，我们会将它的ID添加到一个Redis列表：

```
LPUSH latest.comments <ID> 
```

　　我们将列表裁剪为指定长度，因此Redis只需要保存最新的5000条评论：

```
LTRIM latest.comments 0 5000 
```

   每次我们需要获取最新评论的项目范围时，我们调用一个函数来完成（使用伪代码）：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
FUNCTION get_latest_comments(start, num_items):  
    id_list = redis.lrange("latest.comments",start,start+num_items - 1)  
    IF id_list.length < num_items  
        id_list = SQL_DB("SELECT ... ORDER BY time LIMIT ...")  
    END  
    RETURN id_list  
END 
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　这里我们做的很简单。在Redis中我们的最新ID使用了常驻缓存，这是一直更新的。但是我们做了限制不能超过5000个ID，因此我们的获取ID函数会一直询问Redis。只有在start/count参数超出了这个范围的时候，才需要去访问数据库。我们的系统不会像传统方式那样“刷新”缓存，Redis实例中的信息永远是一致的。SQL数据库（或是硬盘上的其他类型数据库）只是在用户需要获取“很远”的数据时才会被触发，而主页或第一个评论页是不会麻烦到硬盘上的数据库了。

**2、删除与过滤**

　　我们可以使用LREM来删除评论。如果删除操作非常少，另一个选择是直接跳过评论条目的入口，报告说该评论已经不存在。

```
redis 127.0.0.1:6379> LREM KEY_NAME COUNT VALUE
```

　　有些时候你想要给不同的列表附加上不同的过滤器。如果过滤器的数量受到限制，你可以简单的为每个不同的过滤器使用不同的Redis列表。毕竟每个列表只有5000条项目，但Redis却能够使用非常少的内存来处理几百万条项目。

**3、排行榜相关**

　　另一个很普遍的需求是各种数据库的数据并非存储在内存中，因此在按得分排序以及实时更新这些几乎每秒钟都需要更新的功能上数据库的性能不够理想。

　　典型的比如那些在线游戏的排行榜，比如一个Facebook的游戏，根据得分你通常想要：

　　　　 - 列出前100名高分选手

　　　　 - 列出某用户当前的全球排名

　　这些操作对于Redis来说小菜一碟，即使你有几百万个用户，每分钟都会有几百万个新的得分。

　　模式是这样的，每次获得新得分时，我们用这样的代码：

```
 ZADD leaderboard  <score>  <username>
```

　　你可能用userID来取代username，这取决于你是怎么设计的。

　　得到前100名高分用户很简单：ZREVRANGE leaderboard 0 99。

　　用户的全球排名也相似，只需要：ZRANK leaderboard <username>。

**4、按照用户投票和时间排序**

　　排行榜的一种常见变体模式就像Reddit或Hacker News用的那样，新闻按照类似下面的公式根据得分来排序：

　　score = points / time^alpha 

　　因此用户的投票会相应的把新闻挖出来，但时间会按照一定的指数将新闻埋下去。下面是我们的模式，当然算法由你决定。

　　模式是这样的，开始时先观察那些可能是最新的项目，例如首页上的1000条新闻都是候选者，因此我们先忽视掉其他的，这实现起来很简单。

　　每次新的新闻贴上来后，我们将ID添加到列表中，使用LPUSH + LTRIM，确保只取出最新的1000条项目。

　　有一项后台任务获取这个列表，并且持续的计算这1000条新闻中每条新闻的最终得分。计算结果由ZADD命令按照新的顺序填充生成列表，老新闻则被清除。这里的关键思路是排序工作是由后台任务来完成的。

**5、处理过期项目**

　　另一种常用的项目排序是按照时间排序。我们使用unix时间作为得分即可。

　　模式如下：

　　　　- 每次有新项目添加到我们的非Redis数据库时，我们把它加入到排序集合中。这时我们用的是时间属性，current_time和time_to_live。

　　　　- 另一项后台任务使用ZRANGE…SCORES查询排序集合，取出最新的10个项目。如果发现unix时间已经过期，则在数据库中删除条目。

**6、计数**

　　Redis是一个很好的计数器，这要感谢INCRBY和其他相似命令。

　　我相信你曾许多次想要给数据库加上新的计数器，用来获取统计或显示新信息，但是最后却由于写入敏感而不得不放弃它们。

　　好了，现在使用Redis就不需要再担心了。有了原子递增（atomic increment），你可以放心的加上各种计数，用GETSET重置，或者是让它们过期。

　　例如这样操作：

```
INCR user:<id> EXPIRE 

user:<id> 60 
```

　　你可以计算出最近用户在页面间停顿不超过60秒的页面浏览量，当计数达到比如20时，就可以显示出某些条幅提示，或是其它你想显示的东西。

**7、特定时间内的特定项目**

　　另一项对于其他数据库很难，但Redis做起来却轻而易举的事就是统计在某段特点时间里有多少特定用户访问了某个特定资源。比如我想要知道某些特定的注册用户或IP地址，他们到底有多少访问了某篇文章。

　　每次我获得一次新的页面浏览时我只需要这样做：

```
SADD page:day1:<page_id> <user_id>
```

　　当然你可能想用unix时间替换day1，比如time()-(time()%3600*24)等等。

　　想知道特定用户的数量吗？只需要使用

```
SCARD page:day1:<page_id>
```

　　需要测试某个特定用户是否访问了这个页面？

```
SISMEMBER page:day1:<page_id>
```

**8、实时分析正在发生的情况，用于数据统计与防止垃圾邮件等**

　　我们只做了几个例子，但如果你研究Redis的命令集，并且组合一下，就能获得大量的实时分析方法，有效而且非常省力。使用Redis原语命令，更容易实施垃圾邮件过滤系统或其他实时跟踪系统。

**9、Pub/Sub**

　　Redis的Pub/Sub非常非常简单，运行稳定并且快速。支持模式匹配，能够实时订阅与取消频道。

**10、队列**

　　你应该已经注意到像list push和list pop这样的Redis命令能够很方便的执行队列操作了，但能做的可不止这些：比如Redis还有list pop的变体命令，能够在列表为空时阻塞队列。

　　现代的互联网应用大量地使用了消息队列（Messaging）。消息队列不仅被用于系统内部组件之间的通信，同时也被用于系统跟其它服务之间的交互。消息队列的使用可以增加系统的可扩展性、灵活性和用户体验。非基于消息队列的系统，其运行速度取决于系统中最慢的组件的速度（注：短板效应）。而基于消息队列可以将系统中各组件解除耦合，这样系统就不再受最慢组件的束缚，各组件可以异步运行从而得以更快的速度完成各自的工作。

　　此外，当服务器处在高并发操作的时候，比如频繁地写入日志文件。可以利用消息队列实现异步处理。从而实现高性能的并发操作。

**11、缓存**

　　Redis的缓存部分值得写一篇新文章，我这里只是简单的说一下。Redis能够替代memcached，让你的缓存从只能存储数据变得能够更新数据，因此你不再需要每次都重新生成数据了。