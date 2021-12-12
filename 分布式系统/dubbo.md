# dubbo

## 发展演变

ORM 单一应用架构：一个项目装到一个服务器当中，也可以运行多个服务器每一个服务器当中都装一个项目。 缺点：1.如果要添加某一个功能的话就要把一个项目重新打包，在分别部署到每一个服务器当中去。2.如果后期项目越来越大的话单台服务器跑一个项目压力会很大的。会不利于维护，开发和程序的性能。

![image-20210728100322410](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F%5Cdubbo.assets%5Cimage-20210728100322410.png)

MVC 垂直应用架构：将应用切割成几个互不相干的小应用，在将每个小应用独立放到一个服务器上，如果哪一个应用的访问数量多就多加几台服务器。

![image-20210728100331836](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F%5Cdubbo.assets%5Cimage-20210728100331836.png)

分布式服务架构

![image-20210728100341761](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F%5Cdubbo.assets%5Cimage-20210728100341761.png)

**SOA** **流动计算架构**：在分布式应用架构的基础上增加了一个**调度、治理中心**基于访问压力实时管理集群容量、提高集群的利用率，用于提高机器利用率的 资源调度和治理中心(SOA) 是关键 **(不浪费计算机资源)**

## Dubbo的特性

（1）服务注册中心

* 相比Hessian类RPC框架，Dubbo有自己的服务中心， 写好的服务可以注册到服务中心， 客户端从服务中心寻找服务，然后再到相应的服务提供者机器获取服务。通过服务中心可以实现集群、负载均衡、高可用(容错) 等重要功能。
* 服务中心一般使用zookeeper实现，也有redis和其他一些方式。以使用zookeeper作为服务中心为例，服务提供者启动后会在zookeeper的/dubbo节点下创建提供的服务节点，包含服务提供者ip、port等信息。服务提供者关闭时会从zookeeper中移除对应的服务。
* 服务使用者会从注册中心zookeeper中寻找服务，同一个服务可能会有多个提供者，Dubbo会帮我们找到合适的服务提供者，也就是针对服务提供者的负载均衡。

（2）负载均衡

```xml
当同一个服务有多个提供者在提供服务时，客户端如何正确的选择提供者实 现负载均衡呢？dubbo也给我们提供了几种方案：
```

* random 随机选提供者，并可以给提供者设置权重
* roundrobin 轮询选择提供者
* leastactive 最少活跃调用数，相同活跃数的随机，活跃数：指调用前后计数差。使慢的提供者收到更少请求，因为越慢的提供者的调用前后计数差会越大。
* consistenthash 一致性hash，相同参数的请求发到同一台机器上。

（3）简化测试，允许直连提供者 在开发阶段为了方便测试，通常系统客户端能指定调用某个服务提供者，那么可以在引用服务时加一个url参数去指定服务提供者。 配置如下：

\<dubbo:reference id="xxxService"interface="com.alibaba.xxx.XxxService"url="dubbo://localhost:20890"/>

（4）服务版本，服务分组 在Dubbo配置文件中可以通过制定版本实现连接制定提供者，也就是通过服务版本可以控制服务的不兼容升级；当同一个服务有多种实现时，可以使用服务分组进行区分。

## dubbo-admin

1. 安装启动zk服务
2. https://github.com/apache/dubbo-admin
   * 下载整个dubbo-admin，注意若是需要得更改端口（更改admin的端口，在application.properties加上server.port=端口，admin-ui的vue.config.js的proxy的对应端口）
3. 按官网启动dubbo服务（记得关闭VPN代理和开放防火墙端口，否则会出现连接不上zk的问题，当然，还有可能网络和sessiontime的设置及问题）

![image-20210727102229323](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F%5Cdubbo.assets%5Cimage-20210727102229323.png)

spring-dubbo案例

https://blog.csdn.net/qq\_41157588/article/details/106737191

https://blog.csdn.net/qq\_41157588/article/details/106737191的快速开始

## dubbo-monitor-simple简易监控中心

1. 进入dubbo-monitor-simple文件，执行cmd命令，mvn package打包成jar包
2. 将 dubbo-monitor-simple-2.0.0-assembly.tar.gz 压缩包解压至当前文件夹，解压后config文件查看properties的配置是否是本地的zookeeper。 打开解压后的 assembly.bin 文件，start.bat 启动dubbo-monitor-simple监控中心
3. localhost:8080 ，可以看到一个监控中心。
4.  **在服务提供者和消费者的xml**中配置以下内容，再次启动服务提供和消费者启动类。

    \<dubbo:monitor protocol="registry">\</dubbo:monitor>

## Dubbo与SpringBoot整合

![image-20210728101219191](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F%5Cdubbo.assets%5Cimage-20210728101219191.png)

1. 配置springboot和dubbo依赖

```xml
<parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.4.RELEASE</version>
        <relativePath />
    </parent>

    <dependencies>
        <dependency>
            <groupId>com.lemon.gmail</groupId>
            <artifactId>gmail-interface</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>

        <dependency>
            <groupId>com.alibaba.boot</groupId>
            <artifactId>dubbo-spring-boot-starter</artifactId>
            <version>0.2.0</version>
        </dependency>
    </dependencies>
```

1. 使用注解/xml 配置暴露或消费服务
2. 配置application.properties
3. 开启基于注解的dubbo功能

## Dubbo配置

https://dubbo.apache.org/zh/docsv2.7/user/configuration/

### 1、配置原则

* JVM 启动 -D 参数优先，这样可以使用户在部署和启动时进行参数重写，比如在启动时需改变协议的端口。
* XML 次之，如果在 XML 中有配置，则 dubbo.properties 中的相应配置项无效。
* Properties 最后，相当于缺省值，只有 XML 没有配置时，dubbo.properties 的相应配置项才会生效，通常用于共享公共配置，比如应用名。

### 2、启动时检查

* Dubbo 缺省会在启动时检查依赖的服务是否可用，不可用时会抛出异常，阻止 Spring 初始化完成，以便上线时，能及早发现问题，默认 check=“true”。
* 可以通过 check=“false” 关闭检查，比如，测试时，有些服务不关心，或者出现了循环依赖，必须有一方先启动。
* 另外，如果你的 Spring 容器是懒加载的，或者通过 API 编程延迟引用服务，请关闭 check，否则服务临时不可用时，会抛出异常，拿到 null 引用，如果 check=“false”，总是会返回引用，当服务恢复时，能自动连上。

以order-service-consumer消费者为例，在consumer.xml中添加配置

```xml
<!--配置当前消费者的统一规则,当前所有的服务都不启动时检查-->
 <dubbo:consumer check="false"></dubbo:consumer>
添加后，即使服务提供者不启动，启动当前的消费者，也不会出现错误。
```

### 3、全局配置

```xml
全局超时配置
<dubbo:provider timeout="5000" />

指定接口以及特定方法超时配置
<dubbo:provider interface="com.foo.BarService" timeout="2000">
    <dubbo:method name="sayHello" timeout="3000" />
```

配置原则 dubbo推荐在Provider上尽量多配置Consumer端属性

**1、作服务的提供者，比服务使用方更清楚服务性能参数，如调用的超时时间，合理的重试次数，等等** **2、在Provider配置后，Consumer不配置则会使用Provider的配置值，即Provider配置可以作为Consumer的缺省值。否则，Consumer会使用Consumer端的全局设置，这对于Provider不可控的，并且往往是不合理的**

配置的覆盖规则：

* 方法级配置别优于接口级别，即小Scope优先
* Consumer端配置 优于 Provider配置 优于 全局配置，
* 最后是Dubbo Hard Code的配置值（见配置文档）

![image-20210728102034491](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F%5Cdubbo.assets%5Cimage-20210728102034491.png)

### 4、多版本控制

http://dubbo.apache.org/zh-cn/docs/user/demos/multi-versions.html 在服务提供者中复制多个impl。起不同的名字

配置多个文件的路径及信息。 服务消费者调用时，可自由配置版本

## dubbo与springboot整合的三种方式

1. 导入dubbo-starter。在application.properties配置属性，使用@Service【暴露服务】，使用@Reference【引用服务】
2. 保留Dubbo 相关的xml配置文件 导入dubbo-starter，使用@ImportResource导入Dubbo的xml配置文件

**3、使用@Configuration的方式自己配置xml生成解析后的configuration类并注入容器中**

​ 将每一个组件手动配置到容器中,让dubbo的@EnableDubbo的scanBasePackage指定dubbo的Configuration是哪个类，但是，仍需要用@Service【暴露服务】，使用@Reference【引用服务】，来保证服务注入容器

## 高可用

zookeeper宕机与dubbo直连 现象：zookeeper注册中心宕机，还可以消费dubbo暴露的服务。 原因：

健壮性↓

监控中心宕掉不影响使用，只是丢失部分采样数据 数据库宕掉后，注册中心仍能通过缓存提供服务列表查询，但不能注册新服务 注册中心对等集群，任意一台宕掉后，将自动切换到另一台 注册中心全部宕掉后，服务提供者和服务消费者仍能通过本地缓存通讯 服务提供者无状态，任意一台宕掉后，不影响使用 服务提供者全部宕掉后，服务消费者应用将无法使用，并无限次重连等待服务提供者恢复 高可用：通过设计，减少系统不能提供服务的时间；

## 集群下dubbo负载均衡配置

在集群负载均衡时，Dubbo 提供了多种均衡策略，缺省为 random 随机调用。

负载均衡策略如下

Random LoadBalance 基于权重的随机负载均衡机制（默认）

* 随机，按权重设置随机概率。 在一个截面上碰撞的概率高，但调用量越大分布越均匀，而且按概率使用权重后也比较均匀，有利于动态调整提供者权重。

RoundRobin LoadBalance 基于权重的轮询负载均衡机制

* 轮循，按公约后的权重设置轮循比率。 存在慢的提供者累积请求的问题，比如：第二台机器很慢，但没挂，当请求调到第二台时就卡在那，久而久之，所有请求都卡在调到第二台上。

LeastActive LoadBalance最少活跃数负载均衡机制

* 最少活跃调用数，相同活跃数的随机，活跃数指调用前后计数差。 使慢的提供者收到更少请求，因为越慢的提供者的调用前后计数差会越大。

ConsistentHash LoadBalance一致性hash 负载均衡机制

* 一致性 Hash，相同参数的请求总是发到同一提供者。 当某一台提供者挂时，原本发往该提供者的请求，基于虚拟节点，平摊到其它提供者，不会引起剧烈变动。算法参见：http://en.wikipedia.org/wiki/Consistent\_hashing 缺省只对第一个参数 Hash，如果要修改，请配置 \<dubbo:parameter key="hash.arguments" value="0,1" /> 缺省用 160 份虚拟节点，如果要修改，请配置 \<dubbo:parameter key="hash.nodes" value="320" />

## 整合hystrix，服务熔断与降级处理

### 服务降级

当服务器压力剧增的情况下，根据实际业务情况及流量，对一些服务和页面有策略的不处理或换种简单的方式处理，从而释放服务器资源以保证核心交易正常运作或高效运作。 **可以通过服务降级功能临时屏蔽某个出错的非关键服务，并定义降级后的返回策略。**

1. 向注册中心写入动态配置覆盖规则：

```java
RegistryFactory registryFactory = ExtensionLoader.getExtensionLoader(RegistryFactory.class).getAdaptiveExtension();
Registry registry = registryFactory.getRegistry(URL.valueOf("zookeeper://10.20.153.10:2181"));
registry.register(URL.valueOf("override://0.0.0.0/com.foo.BarService?category=configurators&dynamic=false&application=foo&mock=force:return+null"));
```

其中： mock=force:return+null 表示消费方对该服务的方法调用都直接返回 null 值，不发起远程调用。用来屏蔽不重要服务不可用时对调用方的影响。 还可以改为 mock=fail:return+null 表示消费方对该服务的方法调用在失败后，再返回 null 值，不抛异常。用来容忍不重要服务不稳定时对调用方的影响。

1. 直接在admin管理中心进行可视化控制

### 集群容错

在集群调用失败时，Dubbo 提供了多种容错方案，缺省为 failover 重试。 集群容错模式

Failover Cluster 失败自动切换，当出现失败，重试其它服务器。通常用于读操作，但重试会带来更长延迟。可通过 retries=“2” 来设置重试次数(不含第一次)。

```xml
重试次数配置如下：
<dubbo:service retries=“2” />
或
<dubbo:reference retries=“2” />
或
dubbo:reference
<dubbo:method name=“findFoo” retries=“2” />
</dubbo:reference>
```

Failfast Cluster 快速失败，只发起一次调用，失败立即报错。通常用于非幂等性的写操作，比如新增记录。

Failsafe Cluster 失败安全，出现异常时，直接忽略。通常用于写入审计日志等操作。

Failback Cluster 失败自动恢复，后台记录失败请求，定时重发。通常用于消息通知操作。

Forking Cluster 并行调用多个服务器，只要一个成功即返回。通常用于实时性要求较高的读操作，但需要浪费更多服务资源。可通过 forks=“2” 来设置最大并行数。

Broadcast Cluster 广播调用所有提供者，逐个调用，任意一台报错则报错 \[2]。通常用于通知所有提供者更新缓存或日志等本地资源信息。

集群模式配置 按照以下示例在服务提供方和消费方配置集群模式

```xml
<dubbo:service cluster=“failsafe” />或
<dubbo:reference cluster=“failsafe” />
```

### 整合hystrix

服务熔断错处理配置参考=> https://www.cnblogs.com/xc-xinxue/p/12459861.html

Hystrix 旨在通过控制那些访问远程系统、服务和第三方库的节点，从而对延迟和故障提供更强大的容错能力。Hystrix具备拥有回退机制和断路器功能的线程和信号隔离，请求缓存和请求打包，以及监控和配置等功能

1. 配置spring-cloud-starter-netflix-hystrix
2. 然后在Application类上增加@EnableHystrix来启用hystrix starter：
3. **配置Provider端**
4. **配置consumer端**

## **RPC工作原理**

![image-20210728100411900](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F%5Cdubbo.assets%5Cimage-20210728100411900.png)

1. **Client像调用本地服务似的调用远程服务；**
2. Client stub接收到调用后，将方法、参数序列化
3. 客户端通过sockets将消息发送到服务端
4. Server stub 收到消息后进行解码（将消息对象反序列化）
5. Server stub 根据解码结果调用本地的服务
6. 本地服务执行(对于服务端来说是本地执行)并将结果返回给Server stub
7. Server stub将返回结果打包成消息（将结果消息对象序列化）
8. 服务端通过sockets将消息发送到客户端
9. Client stub接收到结果消息，并进行解码（将结果消息发序列化）
10. **客户端得到最终结果。**

* 除了第一点和第十点，2-9全都由PRC封装，对用户来讲完全透明

## netty通信原理

Netty是一个异步事件驱动的网络应用程序框架， 用于快速开发可维护的高性能协议服务器和客户端。它极大地简化并简化了TCP和UDP套接字服务器等网络编程。 **BIO：(Blocking IO)**

![image-20210728103947934](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F%5Cdubbo.assets%5Cimage-20210728103947934.png)

**NIO (Non-Blocking IO)**

![image-20210728103957148](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F%5Cdubbo.assets%5Cimage-20210728103957148.png)

Selector 一般称 为选择器 ，也可以翻译为 多路复用器， Connect（连接就绪）、Accept（接受就绪）、Read（读就绪）、Write（写就绪）

**Netty基本原理：**

![image-20210728104021320](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F%5Cdubbo.assets%5Cimage-20210728104021320.png) netty基本原理，可参考https://www.sohu.com/a/272879207\_463994

## dubbo原理 -框架设计

![image-20210728224626626](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F%5Cdubbo.assets%5Cimage-20210728224626626.png)

## dubbo原理 -启动解析、加载配置信息

![image-20210728225244876](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F%5Cdubbo.assets%5Cimage-20210728225244876.png)

## dubbo原理 -服务暴露

![image-20210728225300292](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F%5Cdubbo.assets%5Cimage-20210728225300292.png)

## dubbo原理 -服务引用

![image-20210728225321671](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F%5Cdubbo.assets%5Cimage-20210728225321671.png)

## dubbo原理 -服务调用

![image-20210728225347136](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5C%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F%5Cdubbo.assets%5Cimage-20210728225347136.png)

## zk与dubbo

dubbo是将jar信息注册到zk的一个远程调用规则制定并借用netty通信的rpc框架，zk负责将服务信息进行记录以便dubbo拿取远程服务信息
