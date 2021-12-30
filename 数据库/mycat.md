# mycat 常用配置

### 常用标签

**schema：**逻辑库，与MySQL中的Database（数据库）对应，一个逻辑库中定义了所包括的Table。 
**table**：表，即物理数据库中存储的某一张表，与传统数据库不同，这里的表格需要声明其所存储的逻辑数据节点DataNode，这是通过表格的分片规则定义来实现的，table可以定义其所属的“子表(childTable)”，子表的分片依赖于与“父表”的具体分片地址，简单的说，就是属于父表里某一条记录A的子表的所有记录都与A存储在同一个分片上。 
分片规则：是一个字段与函数的捆绑定义，根据这个字段的取值来返回所在存储的分片（DataNode）的序号，每个表格可以定义一个分片规则，分片规则可以灵活扩展，默认提供了基于数字的分片规则，字符串的分片规则等。 
**dataNode：** MyCAT的逻辑数据节点，是存放table的具体物理节点，也称之为分片节点，通过DataSource来关联到后端某个具体数据库上，一般来说，为了高可用性，每个DataNode都设置两个DataSource，一主一从，当主节点宕机，系统自动切换到从节点。 
**dataHost：**定义某个物理库的访问地址，用于捆绑到dataNode上。

### 常用配置文件

MyCAT目前通过配置文件的方式来定义逻辑库和相关配置： 
　　MYCAT_HOME/conf/schema.xml中定义逻辑库，表、分片节点等内容； 
　　MYCAT_HOME/conf/rule.xml中定义分片规则； 
　　MYCAT_HOME/conf/server.xml中定义用户以及系统相关变量，如端口等。

![](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/Snipaste_2021-12-27_14-53-52.png)

具体参数配置，mycat1，入门篇第七章，官网：https://www.yuque.com/ccazhw/tuacvk/gmbnwu#7C6Dt



拆分示例：https://blog.csdn.net/lovexiaotaozi/article/details/83022009

### 主从复制步骤

```sh
#在第一台服务器上创建用户并授权.
create user 'laohan'@'119.1.2.%' identified by 'Laohan0.0';
grant replication slave on *.* to root@'119.1.2.%';
 
#使用命令导出Mysql日志.
mysqldump --single-transaction --master-data=2 --trigger --routines --all-databases -uroot -p >bakup_imooc_db.sql
 
#使用scp命令将导出的日志复制到其它3台服务器上
scp bakup_imooc_db.sql 'root'@'119.1.2.2':/home
scp bakup_imooc_db.sql 'root'@'119.1.2.3':/home
scp bakup_imooc_db.sql 'root'@'119.1.2.4':/home
 
#在另外三台服务器上分别创建对应的数据库.
mysql -uroot -p -e"create database product_db"
mysql -uroot -p -e"create database order_db"
mysql -uroot -p -e"create database customer_db"
 
#在另外三台服务器上依次使用命令将日志恢复到服务器数据库中,完成数据导入工作.
mysql -uroot -p product_db < bakup_imooc_db.sql
mysql -uroot -p order_db < bakup_imooc_db.sql
mysql -uroot -p customer_db < bakup_imooc_db.sql
 
#在另外三台服务器上依次使用命令配置主从复制
change master to master_host='119.1.2.2',master_user='laohan',master_password='Laohan0.0',master_log_file='mysql-bin.000004',master_log_pos=1687;
change master to master_host='119.1.2.3',master_user='laohan',master_password='Laohan0.0',master_log_file='mysql-bin.000004',master_log_pos=1687;
change master to master_host='119.1.2.4',master_user='laohan',master_password='Laohan0.0',master_log_file='mysql-bin.000004',master_log_pos=1687;
 
#在另外三台服务器上依次使用命令更改主从复制库名不一致的问题
change replication filter replicate_rewrite_db=((imooc_db,product_db));
change replication filter replicate_rewrite_db=((imooc_db,order_db));
change replication filter replicate_rewrite_db=((imooc_db,customer_db));
 
#分别启动三台服务器的主从复制
start slave;
 
#通过命令观察三台服务器的IO和SQL进程是否都为YES
show slave status \G;
如果都为YES,说明主从复制配置成功.

```



# 表的垂直拆分

### 原理

1.什么是垂直拆分?

所谓垂直拆分就是指把原来一个库中的多个表,按照一定的拆分逻辑将他们拆分出来,放到不同服务器的不同库中.比如开发了一个类似淘宝的电商平台,一开始是把所有表都放在同一个库中,现在可以把用户模块,商品模块,订单模块相关的表各自拆分到不同的库中.

2.垂直拆分的优缺点?

优点:通过垂直拆分,可以使得业务关系更清晰,同时可以降低单台服务器的IO压力,提高数据库性能.

缺点:拆分后会使得一些联表查询变得困难,好在较新版本的**MyCat提供了全局表功能**可以较好的解决这个问题.

``` xml
<table name="company" primaryKey="id" type="global" dataNode="dn1,dn2" />
type属性
该属性定义了逻辑表的类型，目前逻辑表只有“全局表”和”普通表”两种类型。对应的配置：
·     全局表：global。
·     普通表：不指定该值为globla的所有表。
```

3.如何进行垂直拆分?

首先你必须清楚现有业务,弄清楚性能瓶颈主要出现在哪几张表中,另外要确定一下**如何拆分可以让不同模块之间的关联最小,同样遵循低耦合,高内聚**.让拆分后的表所属模块尽量清晰明朗,更为优雅,就像拆分订单模块商品模块这样...

![img](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/DataBaseImage/20181012100406993)

### schema.xml

配置MyCat垂直分库规则,因为这里暂时不做水平分库,所以只需要配置第一台服务器MyCat 的server.xml和schema.xml,不需要关心rule.xml

```xml
<?xml version="1.0"?>
<!DOCTYPE mycat:schema SYSTEM "schema.dtd">
<mycat:schema xmlns:mycat="http://io.mycat/">
        <!--然后配置这块,指明每张表对应的服务器,并把相应的表归属于相应的数据节点,节点名可以自己取,比如ordb-->
        <schema name="imooc_db" checkSQLschema="false" sqlMaxLimit="100">
                <table name="order_master" primaryKey="order_id" dataNode="ordb" />
                <table name="order_detail" primaryKey="order_detail_id" dataNode="ordb" />
                <table name="order_customer_addr" primaryKey="customer_addr_id" dataNode="ordb" />
                <table name="order_cart" primaryKey="cart_id" dataNode="ordb" />
                <!--这里region_info是全局表,所以配置略有不同,它归属于所有数据节点,用逗号隔开,type为global-->
                <table name="region_info" primaryKey="region_id" dataNode="ordb,prodb,custdb" type="global" />
                <table name="shipping_info" primaryKey="ship_id" dataNode="ordb" />
                <table name="warehouse_info" primaryKey="w_id" dataNode="ordb" />
                <table name="warehouse_proudct" primaryKey="wp_id" dataNode="ordb" />
 
                <table name="product_brand_info" primaryKey="brand_id" dataNode="prodb" />
                <table name="product_category" primaryKey="category_id" dataNode="prodb" />
                <table name="product_comment" primaryKey="comment_id" dataNode="prodb" />
                <table name="product_info" primaryKey="product_id" dataNode="prodb" />
                <table name="product_pic_info" primaryKey="product_pic_id" dataNode="prodb" />
                <table name="product_supplier_info" primaryKey="supplier_id" dataNode="prodb" />
 
                <table name="customer_balance_log" primaryKey="balance_id" dataNode="custdb" />
                <table name="customer_inf" primaryKey="customer_inf_id" dataNode="custdb" />
                <table name="customer_login" primaryKey="customer_id" dataNode="custdb" />
                <table name="customer_login_log" primaryKey="login_id" dataNode="custdb" />
                <table name="customer_point_log" primaryKey="point_id" dataNode="custdb" />
        </schema>
        <!--最后配置这块,指明每个数据节点对应的服务器名,以及该数据节点对应的实际数据库名-->
        <dataNode name="ordb" dataHost="mysql0132" database="order_db" />
        <dataNode name="prodb" dataHost="mysql0133" database="product_db" />
        <dataNode name="custdb" dataHost="mysql0134" database="customer_db" />
 
        <!--先配置这块,指明拆分后的三个库对应的服务器Ip等信息,至于writType之类的标签含义需自参阅官网文档:http://www.mycat.io/-->
        <dataHost name="mysql0132" maxCon="1000" minCon="10" balance="3" writeType="0" dbType="mysql" dbDriver="native" switchType="1">
                <heartbeat>select user() </heartbeat>
                <writeHost host="119.1.2.2" url="119.1.2.2:3306" user="dba" password="Woaiyinyin0.0" />
        </dataHost>
 
        <dataHost name="mysql0133" maxCon="1000" minCon="10" balance="3" writeType="0" dbType="mysql" dbDriver="native" switchType="1">
                <heartbeat>select user() </heartbeat>
                <writeHost host="119.1.2.3" url="119.1.2.3:3306" user="dba" password="Woaiyinyin0.0" />
        </dataHost>
 
        <dataHost name="mysql0134" maxCon="1000" minCon="10" balance="3" writeType="0" dbType="mysql" dbDriver="native" switchType="1">
                <heartbeat>select user() </heartbeat>
                <writeHost host="119.1.2.4" url="119.1.2.4:3306" user="dba" password="Woaiyinyin0.0" />
        </dataHost>
 
</mycat:schema>
```



### server.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mycat:server SYSTEM "server.dtd">
<mycat:server xmlns:mycat="http://io.mycat/">
        <!--system标签内的内容可以直接copy过去,没啥好说的,感兴趣可以在mycat官网去研究配置-->
        <system>
          <property name="serverPort">8066</property>
          <property name="managerPort">9066</property>
          <property name="nonePasswordLogin">0</property>
          <property name="bindIp">0.0.0.0</property>
          <property name="frontWriteQueueSize">2048</property>
 
          <property name="charset">utf8</property>
          <property name="txIsolation">2</property>
          <property name="processors">8</property>
          <property name="idleTimeout">1800000</property>
          <property name="sqlExecuteTimeout">300</property>
          <property name="useSqlStat">0</property>
          <property name="useGlobleTableCheck">0</property>
          <property name="sequnceHandlerType">2</property>
          <property name="defaultMaxLimit">100</property>
          <property name="maxPacketSize">104857600</property>
        </system>
        <!--重点,用户名,也就是到时候你的应用要连接的逻辑数据库的用户名和密码-->
        <!--建议跟原来拆分前的数据库用户名和密码保持一致,这样对后端来说修改最少-->
        <user name="app_laohan">
                <!--这行表示使用对明文密码加密-->
                <property name="usingDecrypt">1</property>
                <!--这行一串就是加密后的密码,加密需要通过下面命令去生成下面这串密码-->
                <!--进入mycat的lib目录下,使用java -cp Mycat-server-版本号(可用tab键填充).jar io.mycat.DecryptUtil 0:账号:密码-->
                <!--其中0的意思是前端加密,输完后敲回车就会生成下面这串密文-->
                <property name="password">BcuSAASJ9jyuQnyAOGHPKwIbB8YHlgLANvZe49RtFh0Wip66+jMZRDk5piVhp37EjF/xlEW6YvpbuBr3nN0EQw==</property>
                <property name="schemas">imooc_db</property>
        </user>
 
</mycat:server>
```

### 观察是否成功

```sh
#启动MyCat
mycat start
 
#观察是否启动成功(在Mycat的logs目录下)
tail -f wrapper.log
如果看到:
Mycat server starup successfully.
说明启动成功了.

 
#然后可以在另外随便哪台服务器上连接到Mycat去访问DB看看(账号和密码为server.xml配置的)
 
mysql -uapp_laohan -pLaohan0.0 -P8066 -h119.1.2.1
 
连接进去随便执行几条查询看看是否成功.

```

### 删除多余的表

如果第四步访问和查询都没问题,那现在可以停掉三台服务器的主从复制,并删除多余的表

```r
#停止其它三台服务器上的主从复制,在三台服务器上分别执行
stop slave;
reset slave all;
 
#用Navicat工具或者命令行删除多余的表,比如order_db库中当前是包含imooc_db中的所有表的,现在只需要保留拆分后的表,也就是跟oredr相关的表,其它无关表都可以删除掉了,值得注意的是需要保留一些全局的表,比如字典表,这些表在涉及到联表查询时会用到,除了留下它们外还要对它们进行配置,这些全局表的配置我在schema.xml中已经指明了如何配置了,这里不再赘述.
```

# 表的水平拆分

### 原理



1.什么是数据库表的水平拆分?

水平拆分是在垂直拆分的基础上进一步对现有压力过大的数据库进行横向拆分,比如原来的订单库在生产环境中压力过大,现在把该订单库根据一定的规则拆分成了4个订单库,每个订单库分别存储一部分数据,以减轻IO压力.

2.水平拆分的优缺点

优点:通过水平拆分后可以减少单个数据库中存储数据过多的情况,在查询中可以显著提高效率,同时也可以减轻数据库压力.

缺点:拆分后涉及到一些联表查询会因为数据存在分片不一致而无法完成查询.好在MyCat可以通过配置ER分片来解决此问题.

3.如何进行水平拆分
首先,水平拆分需要注意下面几个原则:

![img](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/DataBaseImage/20181012162407148)

```java
选择分片键的原则:
1. 尽可能的比较均匀分布数据到各个节点上
2. 该业务字段是最频繁的或者最重要的查询条件
```

水平分片步骤

![img](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/DataBaseImage/20181012162524956)



### 准备好基础数据

配置数据库并导入相应表的表结构

### schema.xml

```xml
<?xml version="1.0"?>
<!DOCTYPE mycat:schema SYSTEM "schema.dtd">
<mycat:schema xmlns:mycat="http://io.mycat/">
 
        <schema name="imooc_db" checkSQLschema="false" sqlMaxLimit="100">
                <!--然后修改这里需要拆分的order_master表所对应的数据节点,用逗号隔开即可,分片规则暂时命名为order_master-->
                <table name="order_master" primaryKey="order_id" dataNode="orderdb01,orderdb02,orderdb03,orderdb04" rule="order_master" />
                <table name="order_detail" primaryKey="order_detail_id" dataNode="ordb" />
                <table name="order_customer_addr" primaryKey="customer_addr_id" dataNode="ordb" />
                <table name="order_cart" primaryKey="cart_id" dataNode="ordb" />
                <table name="region_info" primaryKey="region_id" dataNode="ordb,prodb,custdb" type="global" />
                <table name="shipping_info" primaryKey="ship_id" dataNode="ordb" />
                <table name="warehouse_info" primaryKey="w_id" dataNode="ordb" />
                <table name="warehouse_proudct" primaryKey="wp_id" dataNode="ordb" />
 
                <table name="product_brand_info" primaryKey="brand_id" dataNode="prodb" />
                <table name="product_category" primaryKey="category_id" dataNode="prodb" />
                <table name="product_comment" primaryKey="comment_id" dataNode="prodb" />
                <table name="product_info" primaryKey="product_id" dataNode="prodb" />
                <table name="product_pic_info" primaryKey="product_pic_id" dataNode="prodb" />
                <table name="product_supplier_info" primaryKey="supplier_id" dataNode="prodb" />
 
                <table name="customer_balance_log" primaryKey="balance_id" dataNode="custdb" />
                <table name="customer_inf" primaryKey="customer_inf_id" dataNode="custdb" />
                <table name="customer_login" primaryKey="customer_id" dataNode="custdb" />
                <table name="customer_login_log" primaryKey="login_id" dataNode="custdb" />
                <table name="customer_point_log" primaryKey="point_id" dataNode="custdb" />
 
        </schema>
 
        <dataNode name="ordb" dataHost="mysql0132" database="order_db" />
        <dataNode name="prodb" dataHost="mysql0133" database="product_db" />
        <dataNode name="custdb" dataHost="mysql0134" database="customer_db" />
        <!--首先配置orderdb01-04所分布的数据节点-->
        <dataNode name="orderdb01" dataHost="mysql0132" database="orderdb01" />
        <dataNode name="orderdb02" dataHost="mysql0132" database="orderdb02" />
        <dataNode name="orderdb03" dataHost="mysql0133" database="orderdb03" />
        <dataNode name="orderdb04" dataHost="mysql0133" database="orderdb04" />
 
 
        <dataHost name="mysql0132" maxCon="1000" minCon="10" balance="3" writeType="0" dbType="mysql" dbDriver="native" switchType="1">
                <heartbeat>select user() </heartbeat>
                <writeHost host="119.1.2.2" url="119.1.2.2:3306" user="dba" password="Woaiyinyin0.0" />
        </dataHost>
 
        <dataHost name="mysql0133" maxCon="1000" minCon="10" balance="3" writeType="0" dbType="mysql" dbDriver="native" switchType="1">
                <heartbeat>select user() </heartbeat>
                <writeHost host="119.1.2.3" url="119.1.2.3:3306" user="dba" password="Woaiyinyin0.0" />
        </dataHost>
 
        <dataHost name="mysql0134" maxCon="1000" minCon="10" balance="3" writeType="0" dbType="mysql" dbDriver="native" switchType="1">
                <heartbeat>select user() </heartbeat>
                <writeHost host="119.1.2.4" url="119.1.2.4:3306" user="dba" password="Woaiyinyin0.0" />
        </dataHost>
 
</mycat:schema>
```

### rule.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mycat:rule SYSTEM "rule.dtd">
<mycat:rule xmlns:mycat="http://io.mycat/">
        <!--name要与schema中配置的name一致-->
        <tableRule name="order_master">
                <rule>
                        <!--这里即前面选出的分片键-->
                        <columns>customer_id</columns>
                        <!--指明分片算法为简单取模算法-->
                        <algorithm>mod-long</algorithm>
                </rule>
        </tableRule>
        <!--定义分片函数:简单取模算法,指明它所在的包名-->
        <function name="mod-long" class="io.mycat.route.function.PartitionByMod">
                <!--指明所要分片的数量,这里分了4个库,所以写4-->
                <property name="count">4</property>
        </function>
 
</mycat:rule>

```

### server.xml

因为只改变表的存储位置且放在原有的另外两个数据库，所以servel.xml同上

### 测试

修改完成后保存并重启Mycat,重启后观察日志是否重启成功,如果成功则可进入测试阶段:

连接Mycat数据库:

mysql -uapp_laohan -pLaohan0.0 -P8066 -h119.1.2.1
然后向Mycat的逻辑库Imooc_db中的order_master中插入几条数据,然后进入orderdb01-04中观察分布情况,如果分布ok,那说明已经配置成功.(建议使用Navicat这类可视化工具进行操作会更直观)

### ER分片

由于分片将order_master表中的数据分布在了不同的库中,当需其他表要Jion表order_master查询时可能会出现查询错误的情况,为了解决该问题就需要配ER分片.

场景:

◆适合一些数据比较多的大表.如果数据量不多的情况下使用垂直拆分时提到的全局表策略即可解决.

◆适合跟水平拆分表order_master关联查询比较多的表.

首先要找到需要联表查询比较多的表,然后将该表也做分片,而且配置时要把order_master当爸爸...

```xml
<?xml version="1.0"?>
<!DOCTYPE mycat:schema SYSTEM "schema.dtd">
<mycat:schema xmlns:mycat="http://io.mycat/">
 
        <schema name="imooc_db" checkSQLschema="false" sqlMaxLimit="100">
                <!--配置childTable,指明JionKey和ParentKey-->
                <table name="order_master" primaryKey="order_id" dataNode="orderdb01,orderdb02,orderdb03,orderdb04" rule="order_master" >
                    <childTable name="order_detail"  primaryKey="order_detail_id" joinKey="order_id" parentKey="order_id"/>
                </table>
<!--后面的配置就省略了,跟上面的一样-->
 
</mycat:schema>

```

