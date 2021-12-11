## 特性 

无侵入：只做增强不做改变，引入它不会对现有工程产生影响，如丝般顺滑 

损耗小：启动即会自动注入基本 CURD，性能基本无损耗，直接面向对象操作， BaseMapper  

**强大的 CRUD 操作：内置通用 Mapper、通用 Service，仅仅通过少量配置即可实现单表大部分 CRUD 操作，更有强大的条件构造器，满足各类使用需求, 以后简单的CRUD操作，它不用自己编写 了**！ 

支持 Lambda 形式调用：通过 Lambda 表达式，方便的编写各类查询条件，无需再担心字段写错 

支持主键自动生成：支持多达 4 种主键策略（内含分布式唯一 ID 生成器 - Sequence），可自由配 置，完美解决主键问题 

支持 ActiveRecord 模式：支持 ActiveRecord 形式调用，实体类只需继承 Model 类即可进行强大 的 CRUD 操作 

支持自定义全局通用操作：支持全局通用方法注入（ Write once, use anywhere ） 

**内置代码生成器：采用代码或者 Maven 插件可快速生成 Mapper 、 Model 、 Service 、 Controller 层代码，支持模板引擎，更有超多自定义配置等您来使用（自动帮你生成代码） **

**内置分页插件：基于 MyBatis 物理分页，开发者无需关心具体操作，配置好插件之后，写分页等同 于普通 List 查询 分页插件支持多种数据库：**

支持 MySQL、MariaDB、Oracle、DB2、H2、HSQL、SQLite、 Postgre、SQLServer 等多种数据库 

内置性能分析插件：可输出 Sql 语句以及其执行时间，建议开发测试时启用该功能，能快速揪出慢 查询 

内置全局拦截插件：提供全表 delete 、 update 操作智能分析阻断，也可自定义拦截规则，预防误

**id, gmt_create, gmt_modified。
说明：其中 id 必为主键，类型为 unsigned bigint、单表时自增、步长为 1。**

**gmt_create,gmt_modified 的类型均为 date_time 类型，前者现在时表示主动创建，后者过去分词表示被
动更新。**

eg：

```
create table `test`(
        `id` bigint unsigned not null auto_increment,
		`gmt_create` datetime null default current_timestamp,
		`gmt_modified` datetime null default current_timestamp on update current_timestamp,
		primary key(`id`)
		);

on update current_timestamp 有数据更改时自动记录修改时间，记录的是修改的时间，不是提交的时间，不能用来做同步
```



## 项目构建

#### 初始化项目

创建一个空的 Spring Boot 工程（工程将以 H2 作为默认数据库进行演示）

1.  创建数据库 mybatis_plus
2. 创建user表
3. 可以使用 [Spring Initializer](https://start.spring.io/) 快速初始化一个 Spring Boot 工程

 4、导入依赖

```
<!-- 数据库驱动 -->
<dependency>
<groupId>mysql</groupId>
<artifactId>mysql-connector-java</artifactId>
</dependency>
<!-- lombok -->
<dependency>
<groupId>org.projectlombok</groupId>
<artifactId>lombok</artifactId>
</dependency>
<!-- mybatis-plus -->
<!-- mybatis-plus 是自己开发，并非官方的！ -->
<dependency>
<groupId>com.baomidou</groupId>
<artifactId>mybatis-plus-boot-starter</artifactId>
<version>3.0.5</version>
</dependency>
```

说明：我们使用 mybatis-plus 可以节省我们大量的代码，尽量不要同时导入 mybatis 和 mybatisplus！版本的差异！

5、连接数据库！这一步和 mybatis 相同！

```
# mysql 5 驱动不同 com.mysql.jdbc.Driver
# mysql 8 驱动不同com.mysql.cj.jdbc.Driver、需要增加时区的配置
serverTimezone=GMT%2B8
spring.datasource.username=root
spring.datasource.password=123456
spring.datasource.url=jdbc:mysql://localhost:3306/mybatis_plus?
useSSL=false&useUnicode=true&characterEncoding=utf-8&serverTimezone=GMT%2B8
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
```

6、传统方式pojo-dao（连接mybatis，配置mapper.xml文件）-service-controller 

​		用了mybatis-plus 之后

* pojo

```
@Data
@AllArgsConstructor
@NoArgsConstructor
public class User {
private Long id;
private String name;
private Integer age;
private String email;
}
```

* mapper接口

```
package com.kuang.mapper;
import com.baomidou.mybatisplus.core.mapper.BaseMapper;
import com.kuang.pojo.User;
import org.springframework.stereotype.Repository;
// 在对应的Mapper上面继承基本的类 BaseMapper
@Repository // 代表持久层
public interface UserMapper extends BaseMapper<User> {
// 所有的CRUD操作都已经编写完成了
// 你不需要像以前的配置一大堆文件了！
}
```

注意点，我们需要在主启动类上去扫描我们的mapper包下的所有接口 @MapperScan("com.kuang.mapper")

* 测试类中测试

```
@SpringBootTest
class MybatisPlusApplicationTests {
// 继承了BaseMapper，所有的方法都来自己父类
// 我们也可以编写自己的扩展方法！
@Autowired
private UserMapper userMapper;
@Test
void contextLoads() {
// 参数是一个 Wrapper ，条件构造器，这里我们先不用 null
// 查询全部用户
List<User> users = userMapper.selectList(null);
users.forEach(System.out::println);
}
}
```

#### 配置日志

```
mybatis-plus.configuration.log-impl=org.apache.ibatis.logging.stdout.StdOutImpl  //控制台基本的日志
```

## CRUD

#### service CRUD接口和Mapper CRUD 接口

[https://mp.baomidou.com/guide/crud-interface.html#mapper-%E5%B1%82-%E9%80%89%E8%A3%85%E4%BB%B6](https://mp.baomidou.com/guide/crud-interface.html#mapper-层-选装件)

###### Service CRUD 接口

- 通用 Service CRUD 封装[IService](https://gitee.com/baomidou/mybatis-plus/blob/3.0/mybatis-plus-extension/src/main/java/com/baomidou/mybatisplus/extension/service/IService.java)接口，进一步封装 CRUD 采用 `get 查询单行` `remove 删除` `list 查询集合` `page 分页` 前缀命名方式区分 `Mapper` 层避免混淆，
- 泛型 `T` 为任意实体对象
- 建议如果存在自定义通用 Service 方法的可能，请创建自己的 `IBaseService` 继承 `Mybatis-Plus` 提供的基类
- 对象 `Wrapper` 为 [条件构造器](https://github.com/baomidou/mybatis-plus-doc/blob/master/guide/wrapper.html) 

> Save、Update、Get、List、Page、Count、Chain

###### Mapper CRUD 接口

- 通用 CRUD 封装[BaseMapper](https://gitee.com/baomidou/mybatis-plus/blob/3.0/mybatis-plus-core/src/main/java/com/baomidou/mybatisplus/core/mapper/BaseMapper.java)接口，为 `Mybatis-Plus` 启动时自动解析实体表关系映射转换为 `Mybatis` 内部对象注入容器
- 泛型 `T` 为任意实体对象
- 参数 `Serializable` 为任意类型主键 `Mybatis-Plus` 不推荐使用复合主键约定每一张表都有自己的唯一 `id` 主键
- 对象 `Wrapper` 为 [条件构造器](https://github.com/baomidou/mybatis-plus-doc/blob/master/guide/wrapper.html)

> Insert、Delete、Update、Select

> Insert

```
@Test
public void testInsert(){
    User user = new User();
    user.setName("eternal");
    user.setAge(21);
    user.setEmail("254908537@qq.com");

    int result = userMapper.insert(user);

    System.out.println(result);
    System.out.println(user);
}
```

![image-20200731132404211](F:\Typora数据储存\数据库\mybatisPlus.assets\image-20200731132404211.png)

#### 主键生成策略

**1. 数据库自增长序列或字段**

最常见的方式。利用数据库，全数据库唯一。

优点：

1）简单，代码方便，性能可以接受。

2）数字ID天然排序，对分页或者需要排序的结果很有帮助。

 

缺点：

1）不同数据库语法和实现不同，数据库迁移的时候或多数据库版本支持的时候需要处理。

2）在单个数据库或读写分离或一主多从的情况下，只有一个主库可以生成。有单点故障的风险。

3）在性能达不到要求的情况下，比较难于扩展。

4）如果遇见多个系统需要合并或者涉及到数据迁移会相当痛苦。

5）分表分库的时候会有麻烦。

优化方案：

1）针对主库单点，如果有多个Master库，则每个Master库设置的起始数字不一样，步长一样，可以是Master的个数。比如：Master1 生成的是 1，4，7，10，Master2生成的是2,5,8,11 Master3生成的是 3,6,9,12。这样就可以有效生成集群中的唯一ID，也可以大大降低ID生成数据库操作的负载。

 

**2. UUID**

常见的方式。可以利用数据库也可以利用程序生成，一般来说全球唯一。

优点：

1）简单，代码方便。

2）生成ID性能非常好，基本不会有性能问题。

3）全球唯一，在遇见数据迁移，系统数据合并，或者数据库变更等情况下，可以从容应对。

 

缺点：

1）没有排序，无法保证趋势递增。

2）UUID往往是使用字符串存储，查询的效率比较低。

3）存储空间比较大，如果是海量数据库，就需要考虑存储量的问题。

4）传输数据量大

5）不可读。

 UUID

[UUID(Universally Unique IDentifier)](https://en.wikipedia.org/wiki/Universally_unique_identifier)是一个128位数字的唯一标识。[RFC 4122](https://tools.ietf.org/html/rfc4122)描述了具体的规范实现。本文尝试从它的结构一步步分析为什么它能做到唯一性？及各个版本的使用场景。

**Format**

UUID使用16进制表示，共有36个字符(32个字母数字+4个连接符”-“)，格式为`8-4-4-4-12`，如：

```
6d25a684-9558-11e9-aa94-efccd7a0659b
xxxxxxxx-xxxx-Mxxx-Nxxx-xxxxxxxxxxxx
```

M中使用4位来表示UUID的版本，N中使用1-3位表示不同的variant。如上面所示：M =1, N = a表示此UUID为version-1，variant-1的UUID(Time-based ECE/RFC 4122 UUID)。

但是为什么最开始说它是一个128位的唯一标识呢？这里明明字母位数是(8+4+4+4+12)*8=256位。

因为上面的字母是用的16进制，一个16进制只代表4个bit，所以应该是(8+4+4+4+12)*4=128位。

UUID使用的是大数位format([big-endian](https://en.wikipedia.org/wiki/Endianness#Big))，比如：

`00112233-4455-6677-8899-aabbccddeeff` 编码就是 `00 11 22 33` `44 55` `66 77` `88 99` `aa bb cc dd ee ff`.

UUID现有5种版本，是根据不同的使用场景划分的，而不是根据精度，所以Version5并不会比Version1精度高，在精度上，**大家都能保证唯一性，重复的概率近乎于0**。

**V1(date-time MAC address)**

基于时间戳及MAC地址的UUID实现。它包括了48位的MAC地址和60位的时间戳，

接下来使用[ossp-uuid](https://web.archive.org/web/20190314034428/http://www.ossp.org/pkg/lib/uuid/)命令行创建5个UUID v1。(在Mac安装`brew install ossp-uuid`)

```
uuid -n 5 -v1
5b01c826-9561-11e9-9659-cb41250df352
5b01cc7c-9561-11e9-965a-57ad522dee7f
5b01cea2-9561-11e9-965b-a3d050dd0f99
5b01cf60-9561-11e9-965c-1b66505f58da
5b01d118-9561-11e9-965d-97354eb9e996
```

肉眼一看，有一种所有的UUID都很相似的感觉，几乎就要重复了！怎么回事？

其实v1为了保证唯一性，当时间精度不够时，会使用13~14位的clock sequence来扩展时间戳，比如：

当UUID的生产成速率太快，超过了系统时间的精度。时间戳的低位部分会每增加一个UUID就+1的操作来模拟更高精度的时间戳，换句话说，就是当系统时间精度无法区分2个UUID的时间先后顺序时，为了保证唯一性，会在其中一个UUID上+1。所以UUID重复的概率几乎为0，时间戳加扩展的clock sequence一共有74bits,(2的74次方，约为1.8后面加22个零),即在每个节点下，每秒可产生1630亿不重复的UUID(因为只精确到了秒,不再是74位，所以换算了一下)。

相对于其它版本，v1还加入48位的MAC地址，这依赖于网卡供应商能提供唯一的MAC地址，同时也可能通过它反查到对应的MAC地址。[Melissa](https://en.wikipedia.org/wiki/Melissa_(computer_virus))病毒就是这样做到的。

**V2(date-time Mac address)**

这是最神秘的版本，RFC没有提供具体的实现细节，以至于大部分的UUID库都没有实现它，只在特定的场景(DCE security)才会用到。所以绝大数情况，我们也不会碰到它。

**V3,5(namespace name-based)**

V3和V5都是通过hash namespace的标识符和名称生成的。V3使用[MD5](https://en.wikipedia.org/wiki/MD5)作为hash函数，V5则使用[SHA-1](https://en.wikipedia.org/wiki/SHA-1)。

因为里面没有不确定的部分，所以当namespace与输入参数确定时，得到的UUID都是确定唯一的。比如：

```
uuid -n 3 -v3 ns:URL http://www.baidu.com
2f67490d-55a4-395e-b540-457195f7aa95
2f67490d-55a4-395e-b540-457195f7aa95
2f67490d-55a4-395e-b540-457195f7aa95
```

可以看到这3个UUID都是一样的。

具体的流程就是

1. 把namespace和输入参数拼接在一起，如”http/wwwbaidu.com” ++ “/query=uuid”；
2. 使用MD5算法对拼接后的字串进行hash，截断为128位；
3. 把UUID的Version和variant字段都替换成固定的;
4. 如果需要to_string，需要转为16进制和加上连接符”-“。

把V3的hash算法由MD5换成SHA-1就成了V5。

**V4(random)**

这个版本使用最为广泛:

```
uuid -n 5 -v4
a3535b78-69dd-4a9e-9a79-57e2ea28981b
a9ba3122-d89b-4855-85f1-92a018e5c457
7c59d031-a143-4676-a8ce-1b24200ab463
533831da-eab4-4c7d-a3f6-1e2da5a4c9a0
def539e8-d298-4575-b769-b55d7637b51e
```

其中4位代表版本，2-3位代表variant。余下的122-121位都是全部随机的。即有2的122次方(5.3后面36个0)个UUID。一个标准实现的UUID库在生成了2.71万亿个UUID会产生重复UUID的可能性也只有50%的概率

![uuid](https://user-images.githubusercontent.com/3116225/59972542-82f89000-95c3-11e9-8f58-0ed541d5e409.jpg)

这相当于每秒产生10亿的UUID，持续85年，而把这些UUID都存入文件，每个UUID占16bytes,总需要45 EB(exabytes)，比目前最大的数据库(PB)还要大很多倍。

**Summary**

1. 如果只是需要生成一个唯一ID,你可以使用V1或V4。

   ```
   V1基于时间戳和Mac地址,这些ID有一定的规律(你给出一个，是有可能被猜出来下一个是多少的)，而且会暴露你的Mac地址。
   V4是完全随机(伪)的。
   ```

2. 如果对于相同的参数需要输出相同的UUID,你可以使用V3或V5。

   ```
   V3基于MD5 hash算法，如果需要考虑与其它系统的兼容性的话，就用它,因为它出来得早，大概率大家都是用它的。
   V5基于SHA-1 hash算法，这个是首选。
   ```

**3. UUID的变种**

1）为了解决UUID不可读，可以使用UUID to Int64的方法。及



```
/// <summary>
/// 根据GUID获取唯一数字序列
/// </summary>
public static long GuidToInt64()
{
    byte[] bytes = Guid.NewGuid().ToByteArray();
    return BitConverter.ToInt64(bytes, 0);
}
```

2）为了解决UUID无序的问题，NHibernate在其主键生成方式中提供了Comb算法（combined guid/timestamp）。保留GUID的10个字节，用另6个字节表示GUID生成的时间（DateTime）。

```
/// <summary> 
/// Generate a new <see cref="Guid"/> using the comb algorithm. 
/// </summary> 
private Guid GenerateComb()
{
    byte[] guidArray = Guid.NewGuid().ToByteArray();
 
    DateTime baseDate = new DateTime(1900, 1, 1);
    DateTime now = DateTime.Now;
 
    // Get the days and milliseconds which will be used to build    
    //the byte string    
    TimeSpan days = new TimeSpan(now.Ticks - baseDate.Ticks);
    TimeSpan msecs = now.TimeOfDay;
 
    // Convert to a byte array        
    // Note that SQL Server is accurate to 1/300th of a    
    // millisecond so we divide by 3.333333    
    byte[] daysArray = BitConverter.GetBytes(days.Days);
    byte[] msecsArray = BitConverter.GetBytes((long)
      (msecs.TotalMilliseconds / 3.333333));
 
    // Reverse the bytes to match SQL Servers ordering    
    Array.Reverse(daysArray);
    Array.Reverse(msecsArray);
 
    // Copy the bytes into the guid    
    Array.Copy(daysArray, daysArray.Length - 2, guidArray,
      guidArray.Length - 6, 2);
    Array.Copy(msecsArray, msecsArray.Length - 4, guidArray,
      guidArray.Length - 4, 4);
 
    return new Guid(guidArray);
}
```

用上面的算法测试一下，得到如下的结果：作为比较，前面3个是使用COMB算法得出的结果，最后12个字符串是时间序（统一毫秒生成的3个UUID），过段时间如果再次生成，则12个字符串会比图示的要大。后面3个是直接生成的GUID。

[![ODX}_`4N5X$F93OAS~`8Z)C](https://images2015.cnblogs.com/blog/15700/201602/15700-20160227213048721-177386520.png)](http://images2015.cnblogs.com/blog/15700/201602/15700-20160227213048174-1443183768.png)

如果想把时间序放在前面，可以生成后改变12个字符串的位置，也可以修改算法类的最后两个Array.Copy。

**4. Redis生成ID**

当使用数据库来生成ID性能不够要求的时候，我们可以尝试使用Redis来生成ID。这主要依赖于Redis是单线程的，所以也可以用生成全局唯一的ID。可以用Redis的原子操作 INCR和INCRBY来实现。

可以使用Redis集群来获取更高的吞吐量。假如一个集群中有5台Redis。可以初始化每台Redis的值分别是1,2,3,4,5，然后步长都是5。各个Redis生成的ID为：

A：1,6,11,16,21

B：2,7,12,17,22

C：3,8,13,18,23

D：4,9,14,19,24

E：5,10,15,20,25

这个，随便负载到哪个机确定好，未来很难做修改。但是3-5台服务器基本能够满足器上，都可以获得不同的ID。但是步长和初始值一定需要事先需要了。使用Redis集群也可以方式单点故障的问题。

另外，比较适合使用Redis来生成每天从0开始的流水号。比如订单号=日期+当日自增长号。可以每天在Redis中生成一个Key，使用INCR进行累加。

 

优点：

1）不依赖于数据库，灵活方便，且性能优于数据库。

2）数字ID天然排序，对分页或者需要排序的结果很有帮助。

缺点：

1）如果系统中没有Redis，还需要引入新的组件，增加系统复杂度。

2）需要编码和配置的工作量比较大。

 

**5. Twitter的snowflake算法**

snowflake是Twitter开源的分布式ID生成算法，结果是一个long型的ID。其核心思想是：使用41bit作为毫秒数，10bit作为机器的ID（5个bit是数据中心，5个bit的机器ID），12bit作为毫秒内的流水号（意味着每个节点在每毫秒可以产生 4096 个 ID），最后还有一个符号位，永远是0。具体实现的代码可以参看https://github.com/twitter/snowflake。

C#代码如下：



```
/// <summary>
    /// From: https://github.com/twitter/snowflake
    /// An object that generates IDs.
    /// This is broken into a separate class in case
    /// we ever want to support multiple worker threads
    /// per process
    /// </summary>
    public class IdWorker
    {
        private long workerId;
        private long datacenterId;
        private long sequence = 0L;

        private static long twepoch = 1288834974657L;

        private static long workerIdBits = 5L;
        private static long datacenterIdBits = 5L;
        private static long maxWorkerId = -1L ^ (-1L << (int)workerIdBits);
        private static long maxDatacenterId = -1L ^ (-1L << (int)datacenterIdBits);
        private static long sequenceBits = 12L;

        private long workerIdShift = sequenceBits;
        private long datacenterIdShift = sequenceBits + workerIdBits;
        private long timestampLeftShift = sequenceBits + workerIdBits + datacenterIdBits;
        private long sequenceMask = -1L ^ (-1L << (int)sequenceBits);

        private long lastTimestamp = -1L;
        private static object syncRoot = new object();

        public IdWorker(long workerId, long datacenterId)
        {

            // sanity check for workerId
            if (workerId > maxWorkerId || workerId < 0)
            {
                throw new ArgumentException(string.Format("worker Id can't be greater than %d or less than 0", maxWorkerId));
            }
            if (datacenterId > maxDatacenterId || datacenterId < 0)
            {
                throw new ArgumentException(string.Format("datacenter Id can't be greater than %d or less than 0", maxDatacenterId));
            }
            this.workerId = workerId;
            this.datacenterId = datacenterId;
        }

        public long nextId()
        {
            lock (syncRoot)
            {
                long timestamp = timeGen();

                if (timestamp < lastTimestamp)
                {
                    throw new ApplicationException(string.Format("Clock moved backwards.  Refusing to generate id for %d milliseconds", lastTimestamp - timestamp));
                }

                if (lastTimestamp == timestamp)
                {
                    sequence = (sequence + 1) & sequenceMask;
                    if (sequence == 0)
                    {
                        timestamp = tilNextMillis(lastTimestamp);
                    }
                }
                else
                {
                    sequence = 0L;
                }

                lastTimestamp = timestamp;

                return ((timestamp - twepoch) << (int)timestampLeftShift) | (datacenterId << (int)datacenterIdShift) | (workerId << (int)workerIdShift) | sequence;
            }
        }

        protected long tilNextMillis(long lastTimestamp)
        {
            long timestamp = timeGen();
            while (timestamp <= lastTimestamp)
            {
                timestamp = timeGen();
            }
            return timestamp;
        }

        protected long timeGen()
        {
            return (long)(DateTime.UtcNow - new DateTime(1970, 1, 1, 0, 0, 0, DateTimeKind.Utc)).TotalMilliseconds;
        }
    }
```

测试代码如下：

```
private static void TestIdWorker()
        {
            HashSet<long> set = new HashSet<long>();
            IdWorker idWorker1 = new IdWorker(0, 0);
            IdWorker idWorker2 = new IdWorker(1, 0);
            Thread t1 = new Thread(() => DoTestIdWoker(idWorker1, set));
            Thread t2 = new Thread(() => DoTestIdWoker(idWorker2, set));
            t1.IsBackground = true;
            t2.IsBackground = true;

            t1.Start();
            t2.Start();
            try
            {
                Thread.Sleep(30000);
                t1.Abort();
                t2.Abort();
            }
            catch (Exception e)
            {
            }

            Console.WriteLine("done");
        }

        private static void DoTestIdWoker(IdWorker idWorker, HashSet<long> set)
        {
            while (true)
            {
                long id = idWorker.nextId();
                if (!set.Add(id))
                {
                    Console.WriteLine("duplicate:" + id);
                }

                Thread.Sleep(1);
            }
        }
```

snowflake算法可以根据自身项目的需要进行一定的修改。比如估算未来的数据中心个数，每个数据中心的机器数以及统一毫秒可以能的并发数来调整在算法中所需要的bit数。

优点：

1）不依赖于数据库，灵活方便，且性能优于数据库。

2）ID按照时间在单机上是递增的。

缺点：

1）在单机上是递增的，但是由于涉及到分布式环境，每台机器上的时钟不可能完全同步，也许有时候也会出现不是全局递增的情况。

 

**6. 利用zookeeper生成唯一ID**

 

```
zookeeper主要通过其znode数据版本来生成序列号，可以生成32位和64位的数据版本号，客户端可以使用这个版本号来作为唯一的序列号。
很少会使用zookeeper来生成唯一ID。主要是由于需要依赖zookeeper，并且是多步调用API，如果在竞争较大的情况下，需要考虑使用分布式锁。因此，性能在高并发的分布式环境下，也不甚理想。
 
7. MongoDB的ObjectId
MongoDB的ObjectId和snowflake算法类似。它设计成轻量型的，不同的机器都能用全局唯一的同种方法方便地生成它。MongoDB 从一开始就设计用来作为分布式数据库，处理多个节点是一个核心要求。使其在分片环境中要容易生成得多。
```

其格式如下：

![img](http://images.blogjava.net/blogjava_net/dongbule/46046/o_111.PNG)

 

前4 个字节是从标准纪元开始的时间戳，单位为秒。时间戳，与随后的5 个字节组合起来，提供了秒级别的唯一性。由于时间戳在前，这意味着ObjectId 大致会按照插入的顺序排列。这对于某些方面很有用，如将其作为索引提高效率。这4 个字节也隐含了文档创建的时间。绝大多数客户端类库都会公开一个方法从ObjectId 获取这个信息。
接下来的3 字节是所在主机的唯一标识符。通常是机器主机名的散列值。这样就可以确保不同主机生成不同的ObjectId，不产生冲突。
为了确保在同一台机器上并发的多个进程产生的ObjectId 是唯一的，接下来的两字节来自产生ObjectId 的进程标识符（PID）。
前9 字节保证了同一秒钟不同机器不同进程产生的ObjectId 是唯一的。后3 字节就是一个自动增加的计数器，确保相同进程同一秒产生的ObjectId 也是不一样的。同一秒钟最多允许每个进程拥有2563（16 777 216）个不同的ObjectId。

实现的源码可以到MongoDB官方网站下载。

#### sequence

* oracle等一些数据库没有如mysql像ID这种的可以设置自增的字段，所有需要通过专门设置一个值来自增，一般有两个用途：

> 一：作为代理主键，唯一识别；ID = sequence_name.nextval 这种操作
>
> 二：用于记录数据库中最新动作的语句，只要语句有动作(I/U/D等)，sequence号都会随着更新，每有IUD就sequence_name+1，配合日志，我们就可以根据sequence号来select出更新的语句。

1. **Mybatis-Plus** 已经定义好了常见的数据库主键序列，我们首先只需要在 **@Configuration** 类中定义好 **@Bean**：

   > **Mybatis-Plus** 内置了如下数据库主键序列（如果内置支持不满足你的需求，可实现 接口来进行扩展）：
   >
   > - **DB2KeyGenerator**
   > - **H2KeyGenerator**
   > - **KingbaseKeyGenerator**
   > - **OracleKeyGenerator**
   > - **PostgreKeyGenerator**

   ```java
   @Bean
   public OracleKeyGenerator oracleKeyGenerator(){
       return new OracleKeyGenerator();
   }
   1234
   ```

2. 然后实体类配置主键 **Sequence**，指定主键策略为 **IdType.INPUT** 即可：

   > **提示**：支持父类定义 **@KeySequence** 子类使用，这样就可以几个表共用一个 **Sequence**

   ```java
   @TableName("TEST_SEQUSER")
   @KeySequence("SEQ_TEST")//类注解
   public class TestSequser{
     @TableId(value = "ID", type = IdType.INPUT)
     private Long id;
   }
   123456
   ```

3. 如果主键是 **String** 类型的，也可以使用：

   > 如何使用 **Sequence** **String** **varchar2** **sequence** 作为主键，但是实体主键类型是 也就是说，表的主键是 ，但是需要从中取值
   >
   > - 实体定义 @KeySequence 注解 clazz 指定类型 String.class
   > - 实体定义主键的类型 String
   > - 注意：oracle 的 sequence 返回的是 Long 类型，如果主键类型是 Integer，可能会引起 ClassCastException

   ```java
   @KeySequence(value = "SEQ_ORACLE_STRING_KEY", clazz = String.class)
   public class YourEntity{
       @TableId(value = "ID_STR", type = IdType.INPUT)
       private String idStr;
   }
   ```

#### IdType

* mybatis-PLUS默认实现的是雪花算法，若需要换成自增

1. 实体类字段上 @TableId(type = IdType.AUTO)
2. 数据库字段一定要是自增！

*  @TableId关键字说明

```
public enum IdType {
    AUTO(0),//自增
    NONE(1),//不做操作
    INPUT(2),//手动输入
    ASSIGN_ID(3),//默认的全局唯一ID：雪花算法
    ASSIGN_UUID(4),//全局唯一ID
    /** @deprecated */
    @Deprecated
    ID_WORKER(3),
    /** @deprecated */
    @Deprecated
    ID_WORKER_STR(3),
    /** @deprecated */
    @Deprecated
    UUID(4);

    private final int key;

    private IdType(int key) {
        this.key = key;
    }

    public int getKey() {
        return this.key;
    }
}
```

#### 更新

```
@Test
public void testUpdate(){
    User user = new User();
    user.setId(5l);
    user.setName("eternal");
    user.setAge(221);
    user.setEmail("254908537@qq.com");

    int result = userMapper.updateById(user);

    System.out.println(result);
    System.out.println(user);
}
```

#### 自动填充

> 方式一：数据库级别

1、在表中新增字段 create_time, update_time

阿里开发手册数据库必备：

```
create table `test`(
        `id` bigint unsigned not null auto_increment,
		`gmt_create` datetime null default current_timestamp,
		`gmt_modified` datetime null default current_timestamp on update current_timestamp,
		primary key(`id`)
		);
```

或者可视化操作

![image-20200731203010880](F:\Typora数据储存\数据库\mybatisPlus.assets\image-20200731203010880.png)

2、再次测试插入方法，我们需要先把实体类同步！

```
private Date createTime;
private Date updateTime;
```

> 方式二：代码级别
>
> （工作时突然要对某个属性进行填充，又不能更改数据库，想要自动填充就得自己编写一套工具会自动将需要填充的sql语句拼接上去，下面为该mybatisplus编写好的工具）

1、删除数据库的默认值、更新操作！

![image-20200731203223163](F:\Typora数据储存\数据库\mybatisPlus.assets\image-20200731203223163.png)

2、实体类字段属性上需要增加注解

```
//对该类的每次的插入操作，该属性都会触发一个自动填充handler处理该属性的值，此处为填充下面的Date类型
@TableField(fill = FieldFill.INSERT)
private Date gmt_create;
@TableField(fill = FieldFill.INSERT_UPDATE)
private Date gmt_modified;
```

3、编写处理器来处理这个注解即可！

```
@Slf4j//日志
@Component// IOC加入
public class MyMetaObjectHandler implements MetaObjectHandler {
    @Override
    public void insertFill(MetaObject metaObject) {
        log.info("start insert fill..");
        this.setFieldValByName("gmt_create",new Date(),metaObject);
        this.setFieldValByName("gmt_modified",new Date(),metaObject);
    }

    @Override
    public void updateFill(MetaObject metaObject) {
        log.info("start update fill..");
        this.setFieldValByName("gmt_modified",new Date(),metaObject);
    }
}
```

#### 乐观锁插件

#### 主要适用场景

意图：

当要更新一条记录的时候，希望这条记录没有被别人更新

乐观锁实现方式：

- 取出记录时，获取当前version
- 更新时，带上这个version
- 执行更新时， set version = newVersion where version = oldVersion
- 如果version不对，就更新失败

**乐观锁配置需要2步 记得两步**

**插件配置1.插件配置**

spring xml:

```xml
<bean class="com.baomidou.mybatisplus.extension.plugins.OptimisticLockerInterceptor"/>
```

spring boot:

```java
@Bean
public OptimisticLockerInterceptor optimisticLockerInterceptor() {
    return new OptimisticLockerInterceptor();
}
```

**2.注解实体字段 `@Version` 必须要!**

```java
@Version
private Integer version;
```

特别说明:

- **支持的数据类型只有:int,Integer,long,Long,Date,Timestamp,LocalDateTime**
- 整数类型下 `newVersion = oldVersion + 1`
- `newVersion` 会回写到 `entity` 中
- 仅支持 `updateById(id)` 与 `update(entity, wrapper)` 方法
- **在 `update(entity, wrapper)` 方法下, `wrapper` 不能复用!!!**

#### Select

```
// 测试查询
@Test
public void testSelectById(){
User user = userMapper.selectById(1L);
System.out.println(user);
}
// 测试批量查询！
@Test
public void testSelectByBatchId(){
List<User> users = userMapper.selectBatchIds(Arrays.asList(1, 2, 3));
users.forEach(System.out::println);
}
// 按条件查询之一使用map操作，map中的参数在sql自动转换成where条件，上面的都需要ID
@Test
public void testSelectByBatchIds(){
HashMap<String, Object> map = new HashMap<>();
// 自定义要查询
map.put("name","狂神说Java");
map.put("age",3);
List<User> users = userMapper.selectByMap(map);
users.forEach(System.out::println);
}
```

#### 分页查询

1、原始的 limit 进行分页

 2、pageHelper 第三方插件

 3、MP 其实也内置了分页插件！

配置：

```xml
<!-- spring xml 方式 -->
<property name="plugins">
    <array>
        <bean class="com.baomidou.mybatisplus.extension.plugins.PaginationInterceptor">
            <property name="sqlParser" ref="自定义解析类、可以没有"/>
            <property name="dialectClazz" value="自定义方言类、可以没有"/>
            <!-- COUNT SQL 解析.可以没有 -->
            <property name="countSqlParser" ref="countSqlParser"/>
        </bean>
    </array>
</property>

<bean id="countSqlParser" class="com.baomidou.mybatisplus.extension.plugins.pagination.optimize.JsqlParserCountOptimize">
    <!-- 设置为 true 可以优化部分 left join 的sql -->
    <property name="optimizeJoin" value="true"/>
</bean>
```

```java
//Spring boot方式
@EnableTransactionManagement
@Configuration
@MapperScan("com.baomidou.cloud.service.*.mapper*")
public class MybatisPlusConfig {

    @Bean
    public PaginationInterceptor paginationInterceptor() {
        PaginationInterceptor paginationInterceptor = new PaginationInterceptor();
        // 设置请求的页面大于最大页后操作， true调回到首页，false 继续请求  默认false
        // paginationInterceptor.setOverflow(false);
        // 设置最大单页限制数量，默认 500 条，-1 不受限制
        // paginationInterceptor.setLimit(500);
        // 开启 count 的 join 优化,只针对部分 left join
        paginationInterceptor.setCountSqlParser(new JsqlParserCountOptimize(true));
        return paginationInterceptor;
    }
}
```

示例：

```
// 测试分页查询
@Test
public void testPage(){
// 参数一：当前页
// 参数二：页面大小
// 使用了分页插件之后，所有的分页操作也变得简单的！
Page<User> page = new Page<>(2,5);
userMapper.selectPage(page,null);
page.getRecords().forEach(System.out::println);
System.out.println(page.getTotal());
}
```

#### 删除操作

1、根据 id 删除记录

```
// 测试删除
@Test
public void testDeleteById(){
userMapper.deleteById(1240620674645544965L);
}
// 通过id批量删除
@Test
public void testDeleteBatchId(){
userMapper.deleteBatchIds(Arrays.asList(1240620674645544961L,124062067464554496
2L));
}
// 通过map删除
@Test
public void testDeleteMap(){
HashMap<String, Object> map = new HashMap<>();
map.put("name","狂神说Java");
userMapper.deleteByMap(map);
}
```

2. 逻辑删除

* 物理删除 ：从数据库中直接移除 

* 逻辑删除 ：再数据库中没有被移除，而是通过一个变量来让他失效！ deleted = 0 => deleted = 1
* mybatis-plus 在将属性设定为逻辑删除属性后，删除和select语句会**自动注入deleted = 0在sql语句后面**

> 说明:
>
> 只对自动注入的sql起效:
>
> - 插入: 不作限制
> - 查找: 追加where条件过滤掉已删除数据,且使用 wrapper.entity 生成的where条件会忽略该字段
> - 更新: 追加where条件防止更新到已删除数据,且使用 wrapper.entity 生成的where条件会忽略该字段
> - 删除: 转变为 更新
>
> 例如:
>
> - 删除: `update user set deleted=1 where id = 1 and deleted=0`
> - 查找: `select id,name,deleted from user where deleted=0`
>
> 字段类型支持说明:
>
> - 支持所有数据类型(推荐使用 `Integer`,`Boolean`,`LocalDateTime`)
> - 如果数据库字段使用`datetime`,逻辑未删除值和已删除值支持配置为字符串`null`,另一个值支持配置为函数来获取值如`now()`
>
> 附录:
>
> - 逻辑删除是为了方便数据恢复和保护数据本身价值等等的一种方案，但实际就是删除。
> - 如果你需要频繁查出来看就不应使用逻辑删除，而是以一个状态去表示。

* 使用方法
* 步骤1: 配置`com.baomidou.mybatisplus.core.config.GlobalConfig$DbConfig`

```
yml:
mybatis-plus:
  global-config:
    db-config:
      logic-delete-field: flag  # 全局逻辑删除的实体字段名(since 3.3.0,配置后可以忽略不配置步骤2)
      logic-delete-value: 1 # 逻辑已删除值(默认为 1)
      logic-not-delete-value: 0 # 逻辑未删除值(默认为 0)
      
properties:
mybatis-plus.global-config.db-config.logic-delete-field=flag# 全局逻辑删除的实体字段名(since 3.3.0,配置后可以忽略不配置步骤2)
mybatis-plus.global-config.db-config.logic-delete-value=1
mybatis-plus.global-config.db-config.logic-not-delete-value=0
```

步骤2: 实体类字段上加上`@TableLogic`注解

```
@TableLogic
private Integer deleted;
```

## 性能分享插件

 	我们在平时的开发中，会遇到一些慢sql。测试！ druid,,,,, 

作用：性能分析拦截器，用于输出每条 SQL 语句及其执行时间 MP也提供性能分析插件，如果超过这个时间就停止运行！

 1、导入插件(记住，要在SpringBoot中配置环境为dev或者 test 环境！)

```
/**
* SQL执行效率插件
*/
@Bean
@Profile({"dev","test"})// 设置 dev test 环境开启，保证我们的效率
public PerformanceInterceptor performanceInterceptor() {
PerformanceInterceptor performanceInterceptor = new
PerformanceInterceptor();
performanceInterceptor.setMaxTime(100); // ms设置sql执行的最大时间，如果超过了则不
执行
performanceInterceptor.setFormat(true); // 是否格式化代码
return performanceInterceptor;
}
```

![image-20200801110504880](F:\Typora数据储存\数据库\mybatisPlus.assets\image-20200801110504880.png)

## 条件构造器

QueryWrapper(LambdaQueryWrapper) 和 UpdateWrapper(LambdaUpdateWrapper) 的父类
**用于生成 sql 的 where 条件**, entity 属性也用于生成 sql 的 where 条件
注意: entity 生成的 where 条件与 使用各个 api 生成的 where 条件**没有任何关联行为**

![image-20200801154305608](F:\Typora数据储存\数据库\mybatisPlus.assets\image-20200801154305608.png)

## 代码生成器

dao、pojo、service、controller都给我自己去编写完成！ AutoGenerator 是 MyBatis-Plus 的代码生成器，通过 AutoGenerator 可以快速生成 Entity、 Mapper、Mapper XML、Service、Controller 等各个模块的代码，极大的提升了开发效率。

```java
import com.baomidou.mybatisplus.annotation.DbType;
import com.baomidou.mybatisplus.annotation.FieldFill;
import com.baomidou.mybatisplus.annotation.IdType;
import com.baomidou.mybatisplus.annotation.TableField;
import com.baomidou.mybatisplus.generator.AutoGenerator;
import com.baomidou.mybatisplus.generator.config.DataSourceConfig;
import com.baomidou.mybatisplus.generator.config.GlobalConfig;
import com.baomidou.mybatisplus.generator.config.PackageConfig;
import com.baomidou.mybatisplus.generator.config.StrategyConfig;
import com.baomidou.mybatisplus.generator.config.po.TableFill;
import com.baomidou.mybatisplus.generator.config.rules.DateType;
import com.baomidou.mybatisplus.generator.config.rules.NamingStrategy;
import java.util.ArrayList;
// 代码自动生成器
public class KuangCode {
public static void main(String[] args) {
// 需要构建一个 代码自动生成器 对象
AutoGenerator mpg = new AutoGenerator();
// 配置策略
// 1、全局配置
GlobalConfig gc = new GlobalConfig();
String projectPath = System.getProperty("user.dir");
gc.setOutputDir(projectPath+"/src/main/java");
gc.setAuthor("狂神说");
gc.setOpen(false);
gc.setFileOverride(false); // 是否覆盖
gc.setServiceName("%sService"); // 去Service的I前缀
gc.setIdType(IdType.ID_WORKER);
gc.setDateType(DateType.ONLY_DATE);
gc.setSwagger2(true);
mpg.setGlobalConfig(gc);
//2、设置数据源
DataSourceConfig dsc = new DataSourceConfig();
dsc.setUrl("jdbc:mysql://localhost:3306/kuang_community?
useSSL=false&useUnicode=true&characterEncoding=utf-8&serverTimezone=GMT%2B8");
dsc.setDriverName("com.mysql.cj.jdbc.Driver");
dsc.setUsername("root");
dsc.setPassword("123456");
dsc.setDbType(DbType.MYSQL);
mpg.setDataSource(dsc);
//3、包的配置
PackageConfig pc = new PackageConfig();
pc.setModuleName("blog");
pc.setParent("com.kuang");
pc.setEntity("entity");
pc.setMapper("mapper");
pc.setService("service");
pc.setController("controller");
mpg.setPackageInfo(pc);
//4、策略配置
StrategyConfig strategy = new StrategyConfig();
strategy.setInclude("blog_tags","course","links","sys_settings","user_record","
user_say"); // 设置要映射的表名
strategy.setNaming(NamingStrategy.underline_to_camel);
strategy.setColumnNaming(NamingStrategy.underline_to_camel);
strategy.setEntityLombokModel(true); // 自动lombok；
strategy.setLogicDeleteFieldName("deleted");
// 自动填充配置
TableFill gmtCreate = new TableFill("gmt_create", FieldFill.INSERT);
TableFill gmtModified = new TableFill("gmt_modified",
FieldFill.INSERT_UPDATE);
ArrayList<TableFill> tableFills = new ArrayList<>();
tableFills.add(gmtCreate);
tableFills.add(gmtModified);
strategy.setTableFillList(tableFills);
// 乐观锁
strategy.setVersionFieldName("version");
strategy.setRestControllerStyle(true);
strategy.setControllerMappingHyphenStyle(true); //
localhost:8080/hello_id_2
mpg.setStrategy(strategy);
mpg.execute(); //执行
}
}
```

