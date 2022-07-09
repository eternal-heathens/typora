# 作为配置中心的对比



## 配置实时推送的对比

当配置变更的时候，配置中⼼需要将配置实时推送到应⽤客户端。

* Nacos和Apollo配置推送都是基于HTTP长轮询，客户端和配置中⼼建⽴HTTP长联接，当配置变更的的时候，配置中⼼把配置推送到客户
  端。

![image-20220623223852176](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/202206232238220.png)



* Spring Cloud Config原⽣不⽀持配置的实时推送，需要依赖Git的WebHook、Spring Cloud Bus和客户端/bus/refresh端点:基于Git的WebHook，配置变更触发server端refresh，Server端接收到请求并发送给Spring Cloud Bus，Spring Cloud Bus接到消息并通知给客户端客户端接收到通知，请求Server端获取最新配置

整体⽐较下来，Nacos和Apollo在配置实时推送链路上是⽐较简单⾼效的，Spring Cloud Config的配置推送引⼊Spring Cloud Bus，链路较长，⽐较复杂。



部署结构 & ⾼可⽤的对⽐
--------------------------------------------------------
### 1、Spring Cloud Config

Spring Cloud Config包含config-server、Git和Spring Cloud Bus三⼤组件：

* config-server提供给客户端获取配置;
* Git⽤于存储和修改配置;
* Spring Cloud Bus通知客户端配置变更;

#### 部署情况

* 本地测试模式下，Spring Cloud Bus和config-server需要部署⼀个节点，Git使⽤GitHub就可以。

* 在⽣产环境中，config-server需要部署⾄少两个节点。

* Spring Cloud Bus如果使⽤RabbitMQ，普通集群模式⾄少需要两个节点。 

* Git服务如果使⽤GitHub就不⽤考虑⾼可⽤问题，如果考虑到安全性要⾃建Git私有仓库，整体的成本⽐较⾼。

Web服务可以部署多节点⽀持⾼可⽤

由于Git有数据的⼀致性问题，可以通过以下的⽅式来⽀持⾼可⽤：
  	Git+Keepalived冷备模式，当主Git挂了可以马上切到备Git;
  	Git多节点部署，存储使⽤⽹络⽂件系统或者通过DRBD实现多个Git节点的数据同步;

### 2、Apollo

Apollo分为MySQL，Config Service，Admin Service，Portal四个模块：

* MySQL存储Apollo元数据和⽤户配置数据;
* Config Service提供配置的读取、推送等功能，客户端请求都是落到Config Service上;
* Admin Service提供配置的修改、发布等功能，Portal操作的服务就是Admin Service;
* Portal提供给⽤户配置管理界⾯;

#### 部署情况

本地测试Config Service，Admin Service，Portal三个模块可以合并⼀起部署，MySQL单独安装并创建需要的表结构。

在⽣产环境使⽤Apollo，Portal可以两个节点单独部署，稳定性要求没那么⾼的话，Config Service和Admin Service可以部署在⼀起，数据库⽀持主备
  容灾。

### 3、Nacos

* Nacos部署需要Nacos Service和MySQL：
* Nacos对外提供服务，⽀持配置管理和服务发现;
* MySQL提供Nacos的数据持久化存储;

单机模式下，Nacos可以使⽤嵌⼊式数据库部署⼀个节点，就能启动。如果对MySQL⽐较熟悉，想要了解整体数据流向，可以安装

MySQL提供给Nacos数据持久化服务。⽣产环境使⽤Nacos，Nacos服务需要⾄少部署三个节点，再加上MySQL主备。

### 4、整体来看

Nacos的部署结构⽐较简单，运维成本较低。Apollo部署组件较多，运维成本⽐Nacos⾼。Spring Cloud Config⽣产⾼可⽤的成本最⾼

## 性能对比

性能也是配置中⼼绕不过的⼀环，在同样的机器规格下，如果能⽀撑更⼤的业务量，势必能替公司节省更多的资源成本，提⾼资源利⽤率。
应⽤客户端对配置中⼼的接⼝操作有读、写和变更通知，由于变更通知需要⼤量的客户端实例，不好模拟测试场景，下⾯仅对读和写操作做
了测试。
硬件环境
Nacos和Apollo使⽤同样的数据库（32C128G），部署Server服务的机器使⽤的8C16G配置的容器，磁盘是100G SSD。
版本
Spring Cloud Config使⽤2.0.0.M9版本，Apollo使⽤1.2.0 release版本，Nacos使⽤0.5版本。
单机读场景
客户端测试程序通过部署多台机器，每台机器开启多个线程从配置中⼼读取不同的配置（3000个）。Nacos QPS可以达到
15000，Apollo分为读内存缓存和从数据库中读两种⽅式，从数据库中读能达到7500，从内存读缓存性能可以达到9000QPS。Spring
Cloud Config使⽤jGit读写Git，由于有客户端限制，单机读能⼒被限制在7QPS。
3节点读场景
将配置中⼼的压测节点数都部署成3个节点。Nacos QPS可以达到45000 QPS，Apollo读内存缓存可以达到27000 QPS。Nacos和
Apollo由于读场景各个节点是独⽴的，基本就是单机读场景的3倍关系。Spring Cloud Config三个节点读能⼒可以到达21QPS。
单机写场景
同样的⽅式，多台机器同时在配置中⼼修改不同的配置。Nacos QPS可以达到1800，Apollo未使⽤默认的数据库连接池（10）QPS只能
达到800 QPS（CPU未压满），调整连接池⾄100可以达到1100 QPS（CPU压满）。Git在提交同⼀个项⽬的时候会加锁，单机Git写能
在5QPS左右，Spring Cloud Config在使⽤的时候以⼀个项⽬作为数据源，写能⼒受到Git限制。
3节点写场景
同样的⽅式，将配置中⼼的压测节点数都部署成3个节点。Nacos QPS可以达到6000，Apollo可以达到3300 QPS（CPU压满），此时
MySQL数据库因为配置较⾼，未成为性能瓶颈。Spring Cloud Config三个节点时候，Git也是⼀个节点，写QPS为5。

**整体上来看，Nacos的读写性能最⾼，Apollo次之，Spring Cloud Config的依赖Git场景不适合开放的⼤规模⾃动化运维API。**
