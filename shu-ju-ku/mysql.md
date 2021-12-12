## 范式

https://blog.csdn.net/wenco1/article/details/88077279

#### 码

设K为R<U,F>中的属性或属性组合，若K-(F)>U,则K为R的候选码。

> 注意U是完全函数依赖与K，而不是部分函数依赖于K。如果U部分函数依赖于K，即K-（P）>U，则K称为超码。**候选码是最小的超码，即K的任意一个真子集都不是候选码**
>
> 若候选码多于一个，则选定其中一个为主码
>
> 包含在任意一个**候选码中的属性称为主属性**；**不包含在任何候选码中的属性称为非主属性**，最简单的情况，单个属性是码；最极端的情况，整个属性组是码，称为**全码**。

#### 第一范式

**1NF的定义为：符合1NF的关系中的每个属性都不可再分**

即每个属性每一行只能有一个值（多种但只有一个）

#### 第二范式

**2NF在1NF的基础之上，消除了非主属性对于码的部分函数依赖。**

**完全函数依赖**

在一张表中，若 X → Y，且对于 X 的任何一个真子集（假如属性组 X 包含超过一个属性的话），X ’ → Y 不成立，那么我们称 Y 对于 X 完全函数依赖，记作 X F→ Y。（那个F应该写在箭头的正上方，没办法打出来……，正确的写法如图1）
![img](https://img-blog.csdnimg.cn/20190304104502811.png#pic_center)
图1
例如： 学号 F→ 姓名 （学号，课名） F→ 分数 （注：因为同一个的学号对应的分数不确定，同一个课名对应的分数也不确定）

**2.1.2 部分函数依赖**

假如 Y 函数依赖于 X，但同时 Y 并不完全函数依赖于 X，那么我们就称 Y 部分函数依赖于 X，记作 X P→ Y，如图2。
![img](https://img-blog.csdnimg.cn/20190304104524755.png#pic_center)
图2

**2.1.2 传递函数依赖**

例如：（学号，课名） P→ 姓名 传递函数依赖假如 Z 函数依赖于 Y，且 Y 函数依赖于 X （感谢 @百达 指出的错误，这里改为：『Y 不包含于 X，且 X 不函数依赖于 Y』这个前提），那么我们就称 Z 传递函数依赖于 X ，记作 X T→ Z，如图3。
![img](https://img-blog.csdnimg.cn/20190304104543695.png#pic_center)

#### 第三范式

**3NF在2NF的基础之上，消除了非主属性对于码的传递函数依赖。**

#### BCNF

**在 3NF 的基础上消除主属性对于不包含它的码的部分与传递函数依赖。**（一般出现在由于1：1的方式出现的多个候选码的情况）

## sql函数

#### mysql_connect() 

mysql_connect() 函数打开非持久的 MySQL 连接。

```mysql
mysql_connect(server,user,pwd,newlink,clientflag)
```

#### mysql_pconnect()

mysql_pconnect() 函数打开一个到 MySQL 服务器的持久连接。

mysql_pconnect() 和 mysql_connect() 非常相似，但有两个主要区别：

1. 当连接的时候本函数将先尝试寻找一个在同一个主机上用同样的用户名和密码已经打开的（持久）连接，如果找到，则返回此连接标识而不打开新连接。
2. 其次，当脚本执行完毕后到 SQL 服务器的连接不会被关闭，此连接将保持打开以备以后使用（mysql_close() 不会关闭由 mysql_pconnect() 建立的连接）。

```mysql
mysql_pconnect(server,user,pwd,clientflag)
```

##   sql语句

#### case when end

```mysql
--简单case函数
case sex
  when '1' then '男'
  when '2' then '女’
  else '其他' end
--case搜索函数
case when sex = '1' then '男'
     when sex = '2' then '女'
     else '其他' end  别名
```

#### 执行顺序

select语句完整语法：

1)  select 目标表的列名或列表达式序列

2)  from 基本表名和（或）视图序列

3)  [where 行条件表达式]

4)   [group by 列名序列]

[having 组条件表达式]

5)  [order by 列名[asc | desc]]，则sql语句的执行顺序是：

写法顺序：select--from--where--group by--having--order by 

执行顺序：from--where--group by--having--select--order by

#### count

除了count(1)与count(*)，count（column）会忽略值为空的

#### group by

- where子句 = 指定行所对应的条件
- having子句 = 指定组所对应的条件
- D中是Group by才用来分组的，group by的作用是限定分组条件，而having则是对group by中分出来的组进行条件筛选。
- 所以用having就一定要和group by连用，且是先group by XXX 再having XXX，用group by不一有having（它只是一个筛选条件用的）

#### isnull与ifnull与nullif

mysql中：

1.isnull(exper) 判断exper是否为空，是则返回1，否则返回0

2.ifnull(exper1,exper2)判断exper1是否为空，是则用exper2代替

3.nullif(exper1,exper2)如果expr1= expr2 成立，那么返回值为NULL，否则返回值为  expr1。



#### like

字段 like ‘匹配串1’or 字段 like ‘匹配串2’or ... 有如下简写方式

oracle：

select * from tablex where REGEXP_LIKE(字段名, '(匹配串1|匹配串2|...)') ;//全模糊匹配

select * from tablex where REGEXP_LIKE(字段名, '^(匹配串1|匹配串2|...)') ";//右模糊匹配

select * from tablex where REGEXP_LIKE(字段名, '(匹配串1|匹配串2|...)$') ";//左模糊匹配

 

SqlServer

select * from tablex where f1 like '%[匹配串1|匹配串2|匹配串3]%'

 

#### UNION和UNION ALL

UNION 并集，两个表中的所有的数据项，并且去除重复数据，按默认排序方式排序（工作中主要用到的是这个）；

UNION ALL，两个表表中的所有都罗列出来；



#### between和时间

写时间条件 ，比如 把2014/3/1 到2014/3/31这个时间段做为条件 的话，很多人都会写成这样

```mysql
select date from table where date between '2014/3/1' and '2014/3/31'
```

其实这样查询出来的结果 是从2014/3/1 00:00:00 到 2014/3/31 00:00:00 

那么 问题来了，要是其中正好有一条数据的时间在 '2014/3/31 11:12:12'那样的话，会出现什么结果 呢？很明显的，这条数据 就会被忽略掉。所在在查询关于时间的时候 ，千万要记住 时： 分： 秒，

如查询三月份的数据正确的语句应该是

```mysql
select date from table where date between '2014/3/1' and ‘2014/3/1 23:59:59’
```

## B树与B+树

B树

 每个节点都存储key和data，所有节点组成这棵树，并且叶子节点指针为null。

 ![img](https://img-blog.csdn.net/20170920132504569?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvemh1YW56aGUxMTc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

 

B+树

 只有叶子节点存储data，叶子节点包含了这棵树的所有键值，叶子节点不存储指针。

 ![img](https://img-blog.csdn.net/20170920132523536?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvemh1YW56aGUxMTc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

 后来，在B+树上增加了顺序访问指针，也就是每个叶子节点增加一个指向相邻叶子节点的指针，这样一棵树成了数据库系统实现索引的首选数据结构。 

 原因有很多，最主要的是这棵树矮胖，呵呵。一般来说，索引很大，往往以索引文件的形式存储的磁盘上，索引查找时产生磁盘I/O消耗，相对于内存存取，I/O存取的消耗要高几个数量级，所以评价一个数据结构作为索引的优劣最重要的指标就是在查找过程中磁盘I/O操作次数的时间复杂度。树高度越小，I/O次数越少。 

 **那为什么是B+树而不是B树呢，因为它内节点不存储data，这样一个节点就可以存储更多的key。**

 

 在MySQL中，最常用的两个存储引擎是MyISAM和InnoDB，它们对索引的实现方式是不同的。

##  MyISAM与InnoDB

目前大部分数据库系统及文件系统都采用**B-Tree(B树)**或其变种**B+Tree(B+树)**作为索引结构。B+Tree是数据库系统实现索引的首选数据结构。在MySQL中,索引属于存储引擎级别的概念,不同存储引擎对索引的实现方式是不同的,本文主要讨论MyISAM和InnoDB两个存储引擎的索引实现方式。MyISAM索引实现MyISAM引擎使用B+Tree作为索引结构,叶节点的data域存放的是数据记录的地址。下图是MyISAM索引的原理图:image.png这里设表一共有三列,假设我  

​    在 MySQL 中,索引属于存储引擎级别的概念,不同存储引擎对索引的实现方式是不同的,本文主要讨论 MyISAM 和 InnoDB 两个存储引擎的索引实现方式。

#### 主键和唯一索引的区别：

主键是一种约束，唯一索引是一种索引，两者在本质上是不同的。

主键创建后一定包含一个唯一性索引，唯一性索引并不一定就是主键。

唯一性索引列允许空值，而主键列不允许为空值。

主键列在创建时，已经默认为非空值 + 唯一索引了。

主键可以被其他表引用为外键，而唯一索引不能。

一个表最多只能创建一个主键，但可以创建多个唯一索引。

主键和唯一索引都可以有多列。

主键更适合那些不容易更改的唯一标识，如自动递增列、身份证号等。

在 RBO 模式下，主键的执行计划优先级要高于唯一索引。 两者可以提高查询的速度。

## 主键索引、覆盖索引、联合索引

1. 若是我们的表有主键，会自动生成主键索引树，并且主键索引表的叶节点保存有全部数据
2. 若是无法用主键索引，而采取了普通索引，若是我们建立了条件字段的索引树（默认会包括主键，此时叶子节点只包括我们此时建立的索引的字段和主键），那我们查询时就可以通过索引树查询到相应叶子节点，此时若是叶子节点中包含需要查询的字段，则不用回表而是直接在索引树查询，若是没有则需要回表查询
3. 若是查询条件是联合索引，其也会走索引树，联合索引的查询也能调用覆盖索引的功能

#### **MyISAM 索引实现** 

**MyISAM 引擎使用 B+Tree 作为索引结构,叶节点的 data 域存放的是数据记录的地址**。下图是 MyISAM 索引的原理图:
![MySQL索引实现原理分析](http://aliyunzixunbucket.oss-cn-beijing.aliyuncs.com/jpg/4ab4988a2c158a128c8ec2406d71402a.jpg?x-oss-process=image/resize,p_100/auto-orient,1/quality,q_90/format,jpg/watermark,image_eXVuY2VzaGk=,t_100,g_se,x_0,y_0)

这里设表一共有三列,假设我们以 Col1 为主键,则图 8 是一个 MyISAM 表的主索引(Primary key)示意。可以看出 MyISAM 的索引文件仅仅保存数据记录的地址。

**辅助索引** 

在 MyISAM 中,主索引和辅助索引(Secondary key)在结构上没有任何区别,只是主索引要求 key 是唯一的,而辅助索引的 key 可以重复。如果我们在 Col2 上建立一个辅助索引,则此索引的结构如下图所示


![MySQL索引实现原理分析](http://aliyunzixunbucket.oss-cn-beijing.aliyuncs.com/jpg/8a27a81cde5d0f565d336d8b2501811b.jpg?x-oss-process=image/resize,p_100/auto-orient,1/quality,q_90/format,jpg/watermark,image_eXVuY2VzaGk=,t_100,g_se,x_0,y_0)

同样也是一颗 B+Tree,data 域保存数据记录的地址。因此,MyISAM 中索引检索的算法为首先按照 B+Tree 搜索算法搜索索引,如果指定的 Key 存在,则取出其data 域的值,然后以 data 域的值为地址,读取相应数据记录。

**MyISAM 的索引方式也叫做“非聚集索引”**,之所以这么称呼是为了与 InnoDB的聚集索引区分。

####  InnoDB 索引实现 		

虽然 InnoDB 也使用 B+Tree 作为索引结构,但具体实现方式却与 MyISAM 截然不同。

1.**第一个重大区别是 InnoDB 的数据文件本身就是索引文件。从上文知道,MyISAM 索引文件和数据文件是分离的,索引文件仅保存数据记录的地址**。

而在InnoDB 中,表数据文件本身就是按 B+Tree 组织的一个索引结构,这棵树的叶点data 域保存了完整的数据记录。这个索引的 key 是数据表的主键,因此 **InnoDB 表数据文件本身就是主索引。**


![MySQL索引实现原理分析](http://aliyunzixunbucket.oss-cn-beijing.aliyuncs.com/jpg/a1f0dd22be4459abf8b984c832ade3c0.jpg?x-oss-process=image/resize,p_100/auto-orient,1/quality,q_90/format,jpg/watermark,image_eXVuY2VzaGk=,t_100,g_se,x_0,y_0)

上图是 InnoDB 主索引(同时也是数据文件)的示意图,可以看到叶节点包含了完整的数据记录。这种索引叫做聚集索引。因为 InnoDB 的数据文件本身要按主键聚集,

 1 **.InnoDB 要求表必须有主键(MyISAM 可以没有),**如果没有显式指定,则 MySQL系统会自动选择一个可以唯一标识数据记录的列作为主键,如果不存在这种列,则MySQL 自动为 InnoDB 表生成一个隐含字段作为主键,类型为长整形。

 每个InnoDB的表都拥有一个特殊索引，此索引中存储着行记录（称之为聚簇索引Clustered Index），一般来说，聚簇索引是根据主键生成的。聚簇索引按照如下规则创建：

* 当定义了主键后，InnoDB会利用主键来生成其聚簇索引；
  如果没有主键，InnoDB会选择一个非空的唯一索引来创建聚簇索引；
  如果这也没有，InnoDB会隐式的创建一个自增的列(rowid)来作为聚簇索引。
        除了主键索引之外的索引，成为二级索引（Secondary Index）。二级索引可以有多个，二级索引建立在经常查询的列上。与聚簇索引的区别在于二级索引的叶子节点中存放的是除了这几个列外用来回表的主键信息（指针）。

 同时,**请尽量在 InnoDB 上采用自增字段做表的主键**。因为 InnoDB 数据文件本身是一棵B+Tree,非单调的主键会造成在插入新记录时数据文件为了维持 B+Tree 的特性而频繁的分裂调整,十分低效,而使用自增字段作为主键则是一个很好的选择。**如果表使用自增主键,那么每次插入新的记录,记录就会顺序添加到当前索引节点的后续位置,当一页写满,就会自动开辟一个新的页**。如下图所示:


![MySQL索引实现原理分析](http://aliyunzixunbucket.oss-cn-beijing.aliyuncs.com/jpg/eb34cbdbd601d2b3a6d658cafbe2a08b.jpg?x-oss-process=image/resize,p_100/auto-orient,1/quality,q_90/format,jpg/watermark,image_eXVuY2VzaGk=,t_100,g_se,x_0,y_0)

  在维护索引上。

 2.第二个与 MyISAM 索引的不同是 InnoDB 的**辅助索引 data 域存储相应记录主键的值而不是地址**。换句话说,InnoDB 的所有辅助索引都引用主键作为 data 域。
例如,图 11 为定义在 Col3 上的一个辅助索引:


![MySQL索引实现原理分析](http://aliyunzixunbucket.oss-cn-beijing.aliyuncs.com/jpg/78d019a47b6498aa770934ec102521a1.jpg?x-oss-process=image/resize,p_100/auto-orient,1/quality,q_90/format,jpg/watermark,image_eXVuY2VzaGk=,t_100,g_se,x_0,y_0)


**聚集索引这种实现方式使得按主键的搜索十分高效,但是辅助索引搜索需要检索两遍索引:首先检索辅助索引获得主键,然后用主键到主索引中检索获得记录。**

 引申:为什么不建议使用过长的字段作为主键?

 因为所有辅助索引都引用主索引,过长的主索引会令辅助索引变得过大。


**聚簇索引与非聚簇索引** 

InnoDB 使用的是聚簇索引, 将主键组织到一棵 B+树中, 而行数据就储存在叶子节点上, 若使用"where id = 14"这样的条件查找主键, 则按照 B+树的检索算法即可查找到对应的叶节点, 之后获得行数据。 若对 Name 列进行条件搜索, 则需要两个步骤:
第一步在辅助索引 B+树中检索 Name, 到达其叶子节点获取对应的主键。
第二步使用主键在主索引 B+树种再执行一次 B+树检索操作, 最终到达叶子节点即可获取整行数据。

MyISM 使用的是非聚簇索引, 非聚簇索引的两棵 B+树看上去没什么不同, 节点
的结构完全一致只是存储的内容不同而已, 主键索引 B+树的节点存储了主键, 辅助键索引B+树存储了辅助键。 表数据存储在独立的地方, 这两颗 B+树的叶子节点都使用一个地址指向真正的表数据, 对于表数据来说, 这两个键没有任何差别。 由于索引树是独立的, 通过辅助键检索无需访问主键的索引树。

为了更形象说明这两种索引的区别, 我们假想一个表如下图存储了 4 行数据。 其中Id 作为主索引, Name 作为辅助索引。 图示清晰的显示了聚簇索引和非聚簇索引的差异


![MySQL索引实现原理分析](http://aliyunzixunbucket.oss-cn-beijing.aliyuncs.com/jpg/345f9050358efa70033be95ed06e337b.jpg?x-oss-process=image/resize,p_100/auto-orient,1/quality,q_90/format,jpg/watermark,image_eXVuY2VzaGk=,t_100,g_se,x_0,y_0)

 

#### **联合索引及最左原则**

联合索引存储数据结构图：

![img](https://img-blog.csdnimg.cn/20190109134515235.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTMzMDg0OTA=,size_16,color_FFFFFF,t_70)

最左原则：

例如联合索引有三个索引字段（A,B,C）

建立了联合索引（A,B,C）实际上等同于建立了（A）,(A,B),(A,B,C)三个索引

查询条件：

（A，，）---会使用索引

（A，B，）---会使用索引

（A，B，C）---会使用索引

（，B，C）---不会使用索引

（，，C）---不会使用索引

（A，，C）---A会使用索引，因为B中断，C全表扫描，不使用索引



**最左匹配原则**



假定上图联合索引的为（a,b）。联合索引也是一棵B+树，不同的是B+树在对索引a排序的基础上，对索引b排序。所以数据按照（1,1),(1,2)......顺序排放。

对于selete * from table where a=XX and b=XX，显然是可以使用(a,b)联合索引的，

对于selete * from table where a=XX，也是可以使用(a,b)联合索引的。因为在这两种情况下，叶子节点中的数据都是有序的。

但是，对于b列的查询，selete * from table where b=XX。则不可以使用这棵B+树索引。可以发现叶子节点的b值为1,2,1,4,1,2。显然不是有序的，因此不能使用(a,b)联合索引。

By the way:selete * from table where b=XX and a=XX,也是可以使用到联合索引的，你可能会有疑问，这条语句并不符合最左匹配原则。这是由于查询优化器的存在，mysql查询优化器会判断纠正这条sql语句该以什么样的顺序执行效率最高，最后才生成真正的执行计划。所以，当然是我们能尽量的利用到索引时的查询顺序效率最高咯，所以mysql查询优化器会最终以这种顺序进行查询执行。

优化：在联合索引中将选择性最高的列放在索引最前面。

例如：在一个公司里以age 和gender为索引，显然age要放在前面，因为性别就两种选择男或女，选择性不如age。



**使用联合索引时，若有>或<，索引会到范围符中止，后面的不参加索引**

> 如：where a = 3 and b > 4 and c = 5 使用a 和 b索引，c不能在范围之后，c不使用索引



**使用like的模糊查询时，把%放前面，并不走索引，把%放关键字后面，还是会走索引的**

> where a = 3 and b like’kk%’ and c = 4 ，使用a、b、c三个索引
>
> where a = 3  and b like ‘%kk’  and c = 4，使用a索引
>
> where a = 3 and b like ‘%kk%’ and c = 4,  使用a索引
>
> where a = 3 and b like ‘k%kk%’ 使用 a、b、c 三个索引

#### 速率

对于innodb的B+树索引来说，所有的行数据都放在叶子节点，非叶子节点不存储行数据，是为了存储更多的索引建，从而降低B+树的高度，减少IO的次数。所以一般来说高度为3的树就可以存储千万级别的数据。 现在来回答问题： ·假设现在有一个高度为3的B+树，当我们查找数据时，会先发生第一次IO加载高度为1的磁盘块1到内存，然后在内存中二分查找得到下次需要加载的高度为2的磁盘块2的地址，然后再发生第二次IO加载磁盘块2到内存中再比较得到高度为3的磁盘块3的地址，再发生第三次IO加载磁盘3到内存比较就可以得到数据了。 ·mysql加载索引是以磁盘块（页）为单位的，并不是一次性全部加载到内存中，每次只需要加载需要的那一块。然后一般高度为3的树就可以存储千万级别的数据 ，所以一般只需要3次IO就可以得到数据，速度就很快了。



#### 区别：

1. InnoDB支持事务，MyISAM不支持，对于InnoDB每一条SQL语言都默认封装成事务，自动提交，这样会影响速度，所以最好把多条SQL语言放在begin和commit之间，组成一个事务； 

2. InnoDB支持外键，而MyISAM不支持。对一个包含外键的InnoDB表转为MYISAM会失败； 

3. InnoDB是聚集索引，使用B+Tree作为索引结构，数据文件是和（主键）索引绑在一起的（表数据文件本身就是按B+Tree组织的一个索引结构），必须要有主键，通过主键索引效率很高。但是辅助索引需要两次查询，先查询到主键，然后再通过主键查询到数据。因此，主键不应该过大，因为主键太大，其他索引也都会很大。

   MyISAM是非聚集索引，也是使用B+Tree作为索引结构，索引和数据文件是分离的，索引保存的是数据文件的指针。主键索引和辅助索引是独立的。
   
   也就是说：InnoDB的B+树主键索引的叶子节点就是数据文件，辅助索引的叶子节点是主键的值；而MyISAM的B+树主键索引和辅助索引的叶子节点都是数据文件的地址指针。

  

4. InnoDB不保存表的具体行数，执行select count(*) from table时需要全表扫描。而MyISAM用一个变量保存了整个表的行数，执行上述语句时只需要读出该变量即可，速度很快（注意不能加有任何WHERE条件）；

那么为什么InnoDB没有了这个变量呢？

    因为InnoDB的事务特性，在同一时刻表中的行数对于不同的事务而言是不一样的，因此count统计会计算对于当前事务而言可以统计到的行数，而不是将总行数储存起来方便快速查询。InnoDB会尝试遍历一个尽可能小的索引除非优化器提示使用别的索引。如果二级索引不存在，InnoDB还会尝试去遍历其他聚簇索引。
    如果索引并没有完全处于InnoDB维护的缓冲区（Buffer Pool）中，count操作会比较费时。可以建立一个记录总行数的表并让你的程序在INSERT/DELETE时更新对应的数据。和上面提到的问题一样，如果此时存在多个事务的话这种方案也不太好用。如果得到大致的行数值已经足够满足需求可以尝试SHOW TABLE STATUS


5. Innodb不支持全文索引，而MyISAM支持全文索引，在涉及全文索引领域的查询效率上MyISAM速度更快高；PS：5.7以后的InnoDB支持全文索引了

6. MyISAM表格可以被压缩后进行查询操作

7. InnoDB支持表、行(默认)级锁，而MyISAM支持表级锁

       InnoDB的行锁是实现在索引上的，而不是锁在物理行记录上。潜台词是，如果访问没有命中索引，也无法使用行锁，将要退化为表锁。

例如：

    t_user(uid, uname, age, sex) innodb;
     
    uid PK
    无其他索引
    update t_user set age=10 where uid=1;             命中索引，行锁。
     
    update t_user set age=10 where uid != 1;           未命中索引，表锁。
     
    update t_user set age=10 where name='chackca';    无索引，表锁。


8、InnoDB表必须有主键（用户没有指定的话会自己找或生产一个主键），而Myisam可以没有

9、Innodb存储文件有frm、ibd，而Myisam是frm、MYD、MYI

        Innodb：frm是表定义文件，ibd是数据文件
    
        Myisam：frm是表定义文件，myd是数据文件，myi是索引文件

如何选择：
    1. 是否要支持事务，如果要请选择innodb，如果不需要可以考虑MyISAM；

    2. 如果表中绝大多数都只是读查询，可以考虑MyISAM，如果既有读也有写，请使用InnoDB。
    
    3. 系统奔溃后，MyISAM恢复起来更困难，能否接受；
    
    4. MySQL5.5版本开始Innodb已经成为Mysql的默认引擎(之前是MyISAM)，说明其优势是有目共睹的，如果你不知道用什么，那就用InnoDB，至少不会差。

InnoDB为什么推荐使用自增ID作为主键？

    答：自增ID可以保证每次插入时B+索引是从右边扩展的，可以避免B+树和频繁合并和分裂（对比使用UUID）。如果使用字符串主键和随机主键，会使得数据随机插入，效率比较差。

innodb引擎的4大特性

       插入缓冲（insert buffer),二次写(double write),自适应哈希索引(ahi),预读(read ahead)

 


































## 四类事务

#### 事务的 四个特征（ACID）

事务具有四个特征：原子性（ Atomicity ）、一致性（ Consistency ）、隔离性（ Isolation ）和持续性（ Durability ）。这四个特性简称为 ACID 特性。

1 、原子性。事务是数据库的逻辑工作单位，事务中包含的各操作要么都做，要么都不做

2 、一致性。事务执行的结果必须是使数据库从一个一致性状态变到另一个一致性状态。因此当数据库只包含成功事务提交的结果时，就说数据库处于一致性状态。

>  物理方面：如果数据库系统运行中发生故障，有些事务尚未完成就被迫中断，这些未完成事务对数据库所做的修改有一部分已写入物理数据库，这时数据库就处于一种不正确的状态，或者说是不一致的状态。（持续性 ）
>
> 程序方面： 单线程情况下，事务要么一起提交要么全部不做（原子性）
>
> ​					高并发条件下，不同事物最终的处理结果要符合**逻辑的一致性**（隔离性）

3 、隔离性。一个事务的执行不能其它事务干扰。即一个事务内部的操作及使用的数据对其它并发事务是隔离的，并发执行的各个事务之间不能互相干扰。

4 、持续性。也称永久性，指一个事务一旦提交，它对数据库中的数据的改变就应该是永久性的。接下来的其它操作或故障不应该对其执行结果有任何影响。

> 一致性是基础，也是最终目的，其他三个特性（原子性、隔离性和持久性）都是为了保证一致性的
>
> 在比较简单的场景（没有高并发）下，可能会发生一些数据库崩溃等情况，这个时候，依赖于对日志的 REDO/UNDO 操作就可以保证一致性
>
> 而在比较复杂的场景（有高并发）下，可能会有很多事务并行的执行，这个时候，就很可能导致最终的结果无法保证一致性，比如
>
> 事务1需要将100元转入帐号A：先读取帐号A的值，然后在这个值上加上100。但是，在这两个操作之间，另一个事务2修改了帐号A的值，为它增加了100元。那么最后的结果应该是A增加了200元。但事实上，事务1最终完成后，帐号A只增加了100元，因为事务2的修改结果被事务1覆盖掉了。
>
> 这个时候，原子性不能保证一致性。因为从单个事务的角度看，不管是事务 1 还是事务 2，它们都保证的原子性（单个事务内的所有操作全部成功了），但最终，它们并没有保证数据库的一致性（因为从逻辑上说，账户 A 应该增加了 200 元，而不是 100 元）
>
> 所以，为了保证并发情况下的一致性，又引入了隔离性的概念
>
>  
>
> 隔离性：即事务之间感知不到彼此的存在，就好像只存在本身一个事务一样
>
> 而对于怎样实现隔离性，又涉及到了乐观锁和悲观锁的概念
>
> 不考虑隔离性的时候，可能导致脏读、幻读和不可重复读的问题（这些问题，其实就是导致无法保证一致性的几种情况）
>
> 而隔离级别的概念，就是为了解决上述三个问题

#### SQL Server中事务被分为3类常见的事务：

- 自动提交事务：是SQL Server默认的一种事务模式，每条Sql语句都被看成一个事务进行处理，你应该没有见过，一条Update 修改2个字段的语句，只修该了1个字段而另外一个字段没有修改。。
- 显式事务：T-sql标明，由Begin Transaction开启事务开始，由Commit Transaction 提交事务、Rollback Transaction 回滚事务结束。
- 隐式事务：使用Set IMPLICIT_TRANSACTIONS ON 将将隐式事务模式打开，不用Begin Transaction开启事务，当一个事务结束，这个模式会自动启用下一个事务，只用Commit Transaction 提交事务、Rollback Transaction 回滚事务即可。

 常用语句就四个。

- Begin Transaction：标记事务开始。
- Commit Transaction：事务已经成功执行，数据已经处理妥当。
- Rollback Transaction：数据处理过程中出错，回滚到没有处理之前的数据状态，或回滚到事务内部的保存点。
- Save Transaction：事务内部设置的保存点，就是事务可以不全部回滚，只回滚到这里，保证事务内部不出错的前提下。

#### **Mysql的四种隔离级别**

SQL标准定义了4类隔离级别，包括了一些具体规则，用来限定事务内外的哪些改变是可见的，哪些是不可见的。低级别的隔离级一般支持更高的并发处理，并拥有更低的系统开销。



**Read Uncommitted（读取未提交内容/未提交读）**

 

在该隔离级别，所有事务都可以看到其他未提交事务的执行结果。本隔离级别很少用于实际应用，因为它的性能也不比其他级别好多少。读取未提交的数据，也被称之为脏读（Dirty Read）。

 

**Read Committed（读取提交内容/提交读）**

 

这是大多数数据库系统的默认隔离级别（但不是MySQL默认的）。它满足了隔离的简单定义：一个事务只能看见已经提交事务所做的改变。这种隔离级别会导致所谓的不可重复读（Nonrepeatable Read）现象，如果一个用户在一个事务中多次读取一条数据，而另外一个用户则同时更新啦这条数据，造成第一个用户多次读取数据不一致。

 

**Repeatable Read（可重读）**

 

这是MySQL的默认事务隔离级别，它确保同一事务的多个实例在并发读取数据时，会看到同样的数据行。不过理论上，它也存在另一个棘手的问题：幻读 （Phantom Read）。简单的说，幻读指第一个事务读取一个结果集后，第二个事务，对这个结果集经行增删操作（第一个事务暂时没用到，没添加行锁），然而第一个事务中再次对这个结果集进行查询时，数据发现丢失或新增，出现了“幻影”。InnoDB和Falcon存储引擎通过多版本并发控制（MVCC，Multiversion Concurrency Control）机制解决了该问题。

 

**Serializable（可串行化）**

这是最高的隔离级别，它通过强制事务排序，使之不可能相互冲突，从而解决幻读问题。简言之，它是在每个读的数据行上加上共享锁。在这个级别，可能导致大量的超时现象和锁竞争。

 

#### **出现问题**

这四种隔离级别采取不同的锁类型来实现，若读取的是同一个数据的话，就容易发生问题。例如：

脏读(Drity Read)：某个事务已更新一份数据，另一个事务在此时读取了同一份数据，由于某些原因，前一个RollBack了操作，则后一个事务所读取的数据就会是不正确的。（一个事务中的更新操作在提交时才写入数据库中，未Commit时在副本操作，持续性，更高隔离级的处理方式也都兼容低级的）

 

不可重复读(Non-repeatable read):事务 A 多次读取同一数据，事务 B 在事务A多次读取的过程中，对数据作了更新并提交，导致事务A多次读取同一数据时，结果不一致。**主要针对的是修改的情况，用到行锁**（使用共享锁/排他锁都可以，低隔离性）

 

幻读(Phantom Read):在一个事务的两次查询中数据笔数不一致，例如有一个事务查询了几列(Row)数据，而另一个事务却在此时插入了新的几列数据，先前的事务在接下来的查询中，就会发现有几列数据是它先前所没有的。**侧重于新增和删除，需用到表锁，如间隙锁**（高隔离性）

 

在MySQL中，实现了这四种隔离级别，分别有可能产生问题如下所示：

![img](https://img2018.cnblogs.com/blog/1646034/201904/1646034-20190430095830286-1397235000.png)

## **锁机制**

#### **共享锁与排他锁**

**共享锁也是基于信号量的读者与写者问题为基础的对互斥和共享资源的更高的级别的抽象**

- 共享锁（读锁）：其他事务可以读，但不能写。
- 排他锁（写锁） ：其他事务不能读取，也不能写。

#### **粒度锁**

MySQL 不同的存储引擎支持不同的锁机制，所有的存储引擎都以自己的方式显现了锁机制，服务器层完全不了解存储引擎中的锁实现：

- MyISAM 和 MEMORY 存储引擎采用的是表级锁（table-level locking）
- BDB 存储引擎采用的是页面锁（page-level locking），但也支持表级锁
- InnoDB 存储引擎既支持行级锁（row-level locking），也支持表级锁，但默认情况下是采用行级锁。

默认情况下，表锁和行锁都是自动获得的， 不需要额外的命令。

但是在有的情况下， 用户需要明确地进行锁表或者进行事务的控制， 以便确保整个事务的完整性，这样就需要使用事务控制和锁定语句来完成。

#### **不同粒度锁的比较：**

- 表级锁：开销小，加锁快；不会出现死锁；锁定粒度大，发生锁冲突的概率最高，并发度最低。

- - 这些存储引擎通过总是一次性同时获取所有需要的锁以及总是按相同的顺序获取表锁来避免死锁。
  - 表级锁更适合于以查询为主，并发用户少，只有少量按索引条件更新数据的应用，如Web 应用

- 行级锁：开销大，加锁慢；会出现死锁；锁定粒度最小，发生锁冲突的概率最低，并发度也最高。

- - 最大程度的支持并发，同时也带来了最大的锁开销。
  - 在 InnoDB 中，除单个 SQL 组成的事务外，
    锁是逐步获得的，这就决定了在 InnoDB 中发生死锁是可能的。
  - 行级锁只在存储引擎层实现，而Mysql服务器层没有实现。 行级锁更适合于有大量按索引条件并发更新少量不同数据，同时又有并发查询的应用，如一些在线事务处理（OLTP）系统

- 页面锁：开销和加锁时间界于表锁和行锁之间；会出现死锁；锁定粒度界于表锁和行锁之间，并发度一般。

### **MyISAM 表锁**

#### **MyISAM表级锁模式：**

- 表共享读锁 （Table Read Lock）：不会阻塞其他用户对同一表的读请求，但会阻塞对同一表的写请求；
- 表独占写锁 （Table Write Lock）：会阻塞其他用户对同一表的读和写操作；

MyISAM 表的读操作与写操作之间，以及写操作之间是串行的。当一个线程获得对一个表的写锁后， 只有持有锁的线程可以对表进行更新操作。 其他线程的读、 写操作都会等待，直到锁被释放为止。

默认情况下，写锁比读锁具有更高的优先级：当一个锁释放时，这个锁会优先给写锁队列中等候的获取锁请求，然后再给读锁队列中等候的获取锁请求。 （This ensures that updates to a table are not “starved” even when there is heavy SELECT activity for the table. However, if there are many updates for a table, SELECT statements wait until there are no more updates.）。

这也正是 MyISAM 表不太适合于有大量更新操作和查询操作应用的原因，因为，大量的更新操作会造成查询操作很难获得读锁，从而可能永远阻塞。同时，一些需要长时间运行的查询操作，也会使写线程“饿死” ，应用中应尽量避免出现长时间运行的查询操作（在可能的情况下可以通过使用中间表等措施对SQL语句做一定的“分解” ，使每一步查询都能在较短时间完成，从而减少锁冲突。如果复杂查询不可避免，应尽量安排在数据库空闲时段执行，比如一些定期统计可以安排在夜间执行）。

可以设置改变读锁和写锁的优先级：

- 通过指定启动参数low-priority-updates，使MyISAM引擎默认给予读请求以优先的权利。
- 通过执行命令SET LOW_PRIORITY_UPDATES=1，使该连接发出的更新请求优先级降低。
- 通过指定INSERT、UPDATE、DELETE语句的LOW_PRIORITY属性，降低该语句的优先级。
- 给系统参数max_write_lock_count设置一个合适的值，当一个表的读锁达到这个值后，MySQL就暂时将写请求的优先级降低，给读进程一定获得锁的机会。

#### **MyISAM加表锁方法：**

MyISAM 在执行查询语句（SELECT）前，会自动给涉及的表加读锁，在执行更新操作
（UPDATE、DELETE、INSERT 等）前，会自动给涉及的表加写锁，这个过程并不需要用户干预，因此，用户一般不需要直接用 LOCK TABLE 命令给 MyISAM 表显式加锁。

在自动加锁的情况下，MyISAM 总是一次获得 SQL 语句所需要的全部锁，这也正是 MyISAM 表不会出现死锁（Deadlock Free）的原因。



MyISAM存储引擎支持并发插入，以减少给定表的读和写操作之间的争用：

如果MyISAM表在数据文件中间没有空闲块，则行始终插入数据文件的末尾。 在这种情况下，你可以自由混合并发使用MyISAM表的INSERT和SELECT语句而不需要加锁——你可以在其他线程进行读操作的时候，同时将行插入到MyISAM表中。 文件中间的空闲块可能是从表格中间删除或更新的行而产生的。 如果文件中间有空闲快，则并发插入会被禁用，但是当所有空闲块都填充有新数据时，它又会自动重新启用。 要控制此行为，可以使用MySQL的concurrent_insert系统变量。

如果你使用LOCK TABLES显式获取表锁，则可以请求READ LOCAL锁而不是READ锁，以便在锁定表时，其他会话可以使用并发插入。

- 当concurrent_insert设置为0时，不允许并发插入。
- 当concurrent_insert设置为1时，如果MyISAM表中没有空洞（即表的中间没有被删除的行），MyISAM允许在一个线程读表的同时，另一个线程从表尾插入记录。这也是MySQL的默认设置。
- 当concurrent_insert设置为2时，无论MyISAM表中有没有空洞，都允许在表尾并发插入记录。

#### **查询表级锁争用情况：**

可以通过检查 table_locks_waited 和 table_locks_immediate 状态变量来分析系统上的表锁的争夺，如果 Table_locks_waited 的值比较高，则说明存在着较严重的表级锁争用情况：

```text
mysql> SHOW STATUS LIKE 'Table%';
+-----------------------+---------+
| Variable_name | Value |
+-----------------------+---------+
| Table_locks_immediate | 1151552 |
| Table_locks_waited | 15324 |
+-----------------------+---------+
```

### **InnoDB行级锁和表级锁**

#### **InnoDB锁模式：**

InnoDB 实现了以下两种类型的**行锁**：

- 共享锁（S）：允许一个事务去读一行，阻止其他事务获得相同数据集的排他锁。
- 排他锁（X）：允许获得排他锁的事务更新数据，阻止其他事务取得相同数据集的共享读锁和排他写锁。

为了允许行锁和表锁共存，实现多粒度锁机制，InnoDB 还有两种内部使用的意向锁（Intention Locks），这两种意向锁都是**表锁**：

- 意向共享锁（IS）：事务打算给数据行加行共享锁，事务在给一个数据行加共享锁前必须先取得该表的 IS 锁。
- 意向排他锁（IX）：事务打算给数据行加行排他锁，事务在给一个数据行加排他锁前必须先取得该表的 IX 锁。

#### **锁模式的兼容情况：**

![img](https://pic4.zhimg.com/80/v2-37761612ead11ddc3762a4c20ddab3f3_720w.jpg)

（如果一个事务请求的锁模式与当前的锁兼容， InnoDB 就将请求的锁授予该事务； 反之， 如果两者不兼容，该事务就要等待锁释放。）

#### **InnoDB加锁方法：**

- 意向锁是 InnoDB 自动加的， 不需用户干预。

- 对于 UPDATE、 DELETE 和 INSERT 语句， InnoDB
  会自动给涉及数据集加排他锁（X)；

- 对于普通 SELECT 语句，InnoDB 不会加任何锁；
  事务可以通过以下语句显式给记录集加共享锁或排他锁：

- - 共享锁（S）：SELECT * FROM table_name WHERE ... LOCK IN SHARE MODE。 其他 session 仍然可以查询记录，并也可以对该记录加 share mode 的共享锁。但是如果当前事务需要对该记录进行更新操作，则很有可能造成死锁。
  - 排他锁（X)：SELECT * FROM table_name WHERE ... FOR UPDATE。其他 session 可以查询该记录，但是不能对该记录加共享锁或排他锁，而是等待获得锁

* InnoDB这种行锁实现特点意味着：只有通过索引条件检索数据，InnoDB才使用行级锁，**否则，InnoDB将使用表锁！** 



- **隐式锁定：**

InnoDB在事务执行过程中，使用两阶段锁协议：

随时都可以执行锁定，InnoDB会根据隔离级别在需要的时候自动加锁；

锁只有在执行commit或者rollback的时候才会释放，并且所有的锁都是在**同一时刻**被释放。

- **显式锁定 ：**

```text
select ... lock in share mode //共享锁 
select ... for update //排他锁 
```

**select for update：**

在执行这个 select 查询语句的时候，会将对应的索引访问条目进行上排他锁（X 锁），也就是说这个语句对应的锁就相当于update带来的效果。

select *** for update 的使用场景：为了让自己查到的数据确保是最新数据，并且查到后的数据只允许自己来修改的时候，需要用到 for update 子句。

**select lock in share mode ：**in share mode 子句的作用就是将查找到的数据加上一个 share 锁，这个就是表示其他的事务只能对这些数据进行简单的select 操作，并不能够进行 DML 操作。select *** lock in share mode 使用场景：为了确保自己查到的数据没有被其他的事务正在修改，也就是说确保查到的数据是最新的数据，并且不允许其他人来修改数据。但是自己不一定能够修改数据，因为有可能其他的事务也对这些数据 使用了 in share mode 的方式上了 S 锁。

**性能影响：**
select for update 语句，相当于一个 update 语句。在业务繁忙的情况下，如果事务没有及时的commit或者rollback 可能会造成其他事务长时间的等待，从而影响数据库的并发使用效率。
select lock in share mode 语句是一个给查找的数据上一个共享锁（S 锁）的功能，它允许其他的事务也对该数据上S锁，但是不能够允许对该数据进行修改。如果不及时的commit 或者rollback 也可能会造成大量的事务等待。

**for update 和 lock in share mode 的区别：**

前一个上的是排他锁（X 锁），一旦一个事务获取了这个锁，其他的事务是没法在这些数据上执行 for update ；后一个是共享锁，多个事务可以同时的对相同数据执行 lock in share mode。

#### **InnoDB 行锁实现方式：**

- InnoDB 行锁是通过给索引上的索引项加锁来实现的，这一点 MySQL 与 Oracle 不同，后者是通过在数据块中对相应数据行加锁来实现的。InnoDB 这种行锁实现特点意味着：只有通过索引条件检索数据，InnoDB 才使用行级锁，否则，InnoDB 将使用表锁！
- 不论是使用主键索引、唯一索引或普通索引，InnoDB 都会使用行锁来对数据加锁。
- 只有执行计划真正使用了索引，才能使用行锁：即便在条件中使用了索引字段，但是否使用索引来检索数据是由 MySQL 通过判断不同执行计划的代价来决定的，如果 MySQL 认为全表扫描效率更高，比如对一些很小的表，它就不会使用索引，这种情况下 InnoDB 将使用表锁，而不是行锁。因此，在分析锁冲突时，
  别忘了检查 SQL 的执行计划（可以通过 explain 检查 SQL 的执行计划），以确认是否真正使用了索引。（更多阅读：[MySQL索引总结](https://link.zhihu.com/?target=http%3A//mp.weixin.qq.com/s/h4B84UmzAUJ81iBY_FXNOg)）
- 由于 MySQL 的行锁是针对索引加的锁，不是针对记录加的锁，所以虽然多个session是访问不同行的记录， 但是如果是使用相同的索引键， 是会出现锁冲突的（后使用这些索引的session需要等待先使用索引的session释放锁后，才能获取锁）。 应用设计的时候要注意这一点。

#### **InnoDB的间隙锁：**

当我们用范围条件而不是相等条件检索数据，并请求共享或排他锁时，InnoDB会给符合条件的已有数据记录的索引项加锁；对于键值在条件范围内但并不存在的记录，叫做“间隙（GAP)”，InnoDB也会对这个“间隙”加锁，这种锁机制就是所谓的间隙锁（Next-Key锁）。

很显然，在使用范围条件检索并锁定记录时，InnoDB这种加锁机制会阻塞符合条件范围内键值的并发插入，这往往会造成严重的锁等待。因此，在实际应用开发中，尤其是并发插入比较多的应用，我们要尽量优化业务逻辑，尽量使用相等条件来访问更新数据，避免使用范围条件。

##### **InnoDB使用间隙锁的目的：**

1. 防止幻读，以满足相关隔离级别的要求；
2. 满足恢复和复制的需要：

MySQL 通过 BINLOG 录入执行成功的 INSERT、UPDATE、DELETE 等更新数据的 SQL 语句，并由此实现 MySQL 数据库的恢复和主从复制。MySQL 的恢复机制（复制其实就是在 Slave Mysql 不断做基于 BINLOG 的恢复）有以下特点：

一是 MySQL 的恢复是 SQL 语句级的，也就是重新执行 BINLOG 中的 SQL 语句。

二是 MySQL 的 Binlog 是按照事务提交的先后顺序记录的， 恢复也是按这个顺序进行的。

由此可见，MySQL 的恢复机制要求：在一个事务未提交前，其他并发事务不能插入满足其锁定条件的任何记录，也就是不允许出现幻读。

#### **InnoDB 在不同隔离级别下的一致性读及锁的差异：**

锁和多版本数据（MVCC）是 InnoDB 实现一致性读和 ISO/ANSI SQL92 隔离级别的手段。

因此，在不同的隔离级别下，InnoDB 处理 SQL 时采用的一致性读策略和需要的锁是不同的：

![img](https://pic2.zhimg.com/80/v2-c83c6459f8dc93a5f157fe1e3080088d_720w.jpg)



![img](https://pic1.zhimg.com/80/v2-568951f4cdfeb9416042627a7b94c4ac_720w.jpg)



对于许多 SQL，隔离级别越高，InnoDB 给记录集加的锁就越严格（尤其是使用范围条件的时候），产生锁冲突的可能性也就越高，从而对并发性事务处理性能的 影响也就越大。

因此， 我们在应用中， 应该尽量使用较低的隔离级别， 以减少锁争用的机率。实际上，通过优化事务逻辑，大部分应用使用 Read Commited 隔离级别就足够了。对于一些确实需要更高隔离级别的事务， 可以通过在程序中执行 SET SESSION TRANSACTION ISOLATION

LEVEL REPEATABLE READ 或 SET SESSION TRANSACTION ISOLATION LEVEL SERIALIZABLE 动态改变隔离级别的方式满足需求。

#### **获取 InnoDB 行锁争用情况：**

可以通过检查 InnoDB_row_lock 状态变量来分析系统上的行锁的争夺情况：

```text
mysql> show status like 'innodb_row_lock%'; 
+-------------------------------+-------+ 
| Variable_name | Value | 
+-------------------------------+-------+ 
| InnoDB_row_lock_current_waits | 0 | 
| InnoDB_row_lock_time | 0 | 
| InnoDB_row_lock_time_avg | 0 | 
| InnoDB_row_lock_time_max | 0 | 
| InnoDB_row_lock_waits | 0 | 
+-------------------------------+-------+ 
5 rows in set (0.01 sec)
```

### **LOCK TABLES 和 UNLOCK TABLES**

Mysql也支持lock tables和unlock tables，这都是在服务器层（MySQL Server层）实现的，和存储引擎无关，它们有自己的用途，并不能替代事务处理。 （除了禁用了autocommint后可以使用，其他情况不建议使用）：

- LOCK TABLES 可以锁定用于当前线程的表。如果表被其他线程锁定，则当前线程会等待，直到可以获取所有锁定为止。
- UNLOCK TABLES 可以释放当前线程获得的任何锁定。当前线程执行另一个 LOCK TABLES 时，
  或当与服务器的连接被关闭时，所有由当前线程锁定的表被隐含地解锁

#### **LOCK TABLES语法：**

- 在用 LOCK TABLES 对 InnoDB 表加锁时要注意，要将 AUTOCOMMIT 设为 0，否则MySQL 不会给表加锁；
- 事务结束前，不要用 UNLOCK TABLES 释放表锁，因为 UNLOCK TABLES会隐含地提交事务；
- COMMIT 或 ROLLBACK 并不能释放用 LOCK TABLES 加的表级锁，必须用UNLOCK TABLES 释放表锁。

正确的方式见如下语句：
例如，如果需要写表 t1 并从表 t 读，可以按如下做：

```text
SET AUTOCOMMIT=0; 
LOCK TABLES t1 WRITE, t2 READ, ...; 
[do something with tables t1 and t2 here]; 
COMMIT; 
UNLOCK TABLES;
```

#### **使用LOCK TABLES的场景：**

给表显示加表级锁（InnoDB表和MyISAM都可以），一般是为了在一定程度模拟事务操作，实现对某一时间点多个表的一致性读取。（与MyISAM默认的表锁行为类似）

在用 LOCK TABLES 给表显式加表锁时，必须同时取得所有涉及到表的锁，并且 MySQL 不支持锁升级。也就是说，在执行 LOCK TABLES 后，只能访问显式加锁的这些表，不能访问未加锁的表；同时，如果加的是读锁，那么只能执行查询操作，而不能执行更新操作。

其实，在MyISAM自动加锁（表锁）的情况下也大致如此，MyISAM 总是一次获得 SQL 语句所需要的全部锁，这也正是 MyISAM 表不会出现死锁（Deadlock Free）的原因。

例如，有一个订单表 orders，其中记录有各订单的总金额 total，同时还有一个 订单明细表 order_detail，其中记录有各订单每一产品的金额小计 subtotal，假设我们需要检 查这两个表的金额合计是否相符，可能就需要执行如下两条 SQL：

```text
Select sum(total) from orders; 
Select sum(subtotal) from order_detail; 
```

这时，如果不先给两个表加锁，就可能产生错误的结果，因为第一条语句执行过程中，
order_detail 表可能已经发生了改变。因此，正确的方法应该是：

```text
Lock tables orders read local, order_detail read local; 
Select sum(total) from orders; 
Select sum(subtotal) from order_detail; 
Unlock tables;
```

（在 LOCK TABLES 时加了“local”选项，其作用就是允许当你持有表的读锁时，其他用户可以在满足 MyISAM 表并发插入条件的情况下，在表尾并发插入记录（MyISAM 存储引擎支持“并发插入”））

# **死锁（Deadlock Free）**



## **死锁产生的原因**

> 可归结为如下两点：
>
> a. 竞争资源
>
> - 系统中的资源可以分为两类：
>
> 1. 可剥夺资源，是指某进程在获得这类资源后，该资源可以再被其他进程或系统剥夺，CPU和主存均属于可剥夺性资源；
> 2. 另一类资源是不可剥夺资源，当系统把这类资源分配给某进程后，再不能强行收回，只能在进程用完后自行释放，如磁带机、打印机等。
>
> - 产生死锁中的竞争资源之一指的是竞争不可剥夺资源（例如：系统中只有一台打印机，可供进程P1使用，假定P1已占用了打印机，若P2继续要求打印机打印将阻塞）
> - 产生死锁中的竞争资源另外一种资源指的是竞争临时资源（临时资源包括硬件中断、信号、消息、缓冲区内的消息等），通常消息通信顺序进行不当，则会产生死锁
>
> b. 进程间推进顺序非法
>
> - 若P1保持了资源R1,P2保持了资源R2，系统处于不安全状态，因为这两个进程再向前推进，便可能发生死锁
> - 例如，当P1运行到P1：Request（R2）时，将因R2已被P2占用而阻塞；当P2运行到P2：Request（R1）时，也将因R1已被P1占用而阻塞，于是发生进程死锁

## **死锁产生四个必要条件：**

> 产生死锁的**四个必要条件**：
> （1） 互斥条件：一个资源每次只能被一个进程使用。
> （2） 占有且等待：一个进程因请求资源而阻塞时，对已获得的资源保持不放。
> （3）不可强行占有:进程已获得的资源，在末使用完之前，不能强行剥夺。
> （4） 循环等待条件:若干进程之间形成一种头尾相接的循环等待资源关系。

- - 死锁是指两个或多个事务在同一资源上相互占用，并请求锁定对方占用的资源，从而导致恶性循环。
  - 当事务试图以不同的顺序锁定资源时，就可能产生死锁。多个事务同时锁定同一个资源时也可能会产生死锁。
  - 锁的行为和顺序和存储引擎相关。以同样的顺序执行语句，有些存储引擎会产生死锁有些不会——死锁有双重原因：真正的数据冲突；存储引擎的实现方式。
  
- **检测死锁：**数据库系统实现了各种死锁检测和死锁超时的机制。InnoDB存储引擎能检测到死锁的循环依赖并立即返回一个错误。

- **死锁恢复：**死锁发生以后，只有部分或完全回滚其中一个事务，才能打破死锁，InnoDB目前处理死锁的方法是，将持有最少行级排他锁的事务进行回滚。所以事务型应用程序在设计时必须考虑如何处理死锁，多数情况下只需要重新执行因死锁回滚的事务即可。

- **外部锁的死锁检测：**发生死锁后，InnoDB 一般都能自动检测到，并使一个事务释放锁并回退，另一个事务获得锁，继续完成事务。但在涉及外部锁，或涉及表锁的情况下，InnoDB 并不能完全自动检测到死锁， 这需要通过设置锁等待超时参数 innodb_lock_wait_timeout 来解决

- **死锁影响性能：**死锁会影响性能而不是会产生严重错误，因为InnoDB会自动检测死锁状况并回滚其中一个受影响的事务。在高并发系统上，当许多线程等待同一个锁时，死锁检测可能导致速度变慢。 有时当发生死锁时，禁用死锁检测（使用innodb_deadlock_detect配置选项）可能会更有效，这时可以依赖innodb_lock_wait_timeout设置进行事务回滚。

  
## **预防死锁：**

  - 资源一次性分配：一次性分配所有资源，这样就不会再有请求了：（破坏请求条件）
  - 只要有一个资源得不到分配，也不给这个进程分配其他的资源：（破坏请保持条件）
  - 可剥夺资源：即当某进程获得了部分资源，但得不到其它资源，则释放已占有的资源（破坏不可剥夺条件）超时放弃
  - 资源有序分配法：系统给每类资源赋予一个编号，每一个进程按编号递增的顺序请求资源，释放则相反（破坏环路等待条件）以确定的顺序获得锁


## 避免死锁:

 银行家算法：首先需要定义状态和安全状态的概念。系统的状态是当前给进程分配的资源情况。因此，状态包含两个向量Resource（系统中每种资源的总量）和Available（未分配给进程的每种资源的总量）及两个矩阵Claim（表示进程对资源的需求）和Allocation（表示当前分配给进程的资源）。

**安全状态是指至少有一个资源分配序列不会导致死锁。当进程请求一组资源时，假设同意该请求，从而改变了系统的状态，然后确定其结果是否还处于安全状态。如果是，同意这个请求；如果不是，阻塞该进程知道同意该请求后系统状态仍然是安全的。**

# 死锁检测

**1、Jstack命令**

jstack是java虚拟机自带的一种堆栈跟踪工具。jstack用于打印出给定的java进程ID或core file或远程调试服务的Java堆栈信息。 Jstack工具可以用于生成java虚拟机当前时刻的线程快照。线程快照是当前java虚拟机内每一条线程正在执行的方法堆栈的集合，生成线程快照的主要目的是定位线程出现长时间停顿的原因，如线程间死锁、死循环、请求外部资源导致的长时间等待等。 线程出现停顿的时候通过jstack来查看各个线程的调用堆栈，就可以知道没有响应的线程到底在后台做什么事情，或者等待什么资源。

**2、JConsole工具**

Jconsole是JDK自带的监控工具，在JDK/bin目录下可以找到。它用于连接正在运行的本地或者远程的JVM，对运行在Java应用程序的资源消耗和性能进行监控，并画出大量的图表，提供强大的可视化界面。而且本身占用的服务器内存很小，甚至可以说几乎不消耗。

## 解除死锁:

当发现有进程死锁后，便应立即把它从死锁状态中解脱出来，常采用的方法有：

- 剥夺资源：从其它进程剥夺足够数量的资源给死锁进程，以解除死锁状态；
- 撤消进程：可以直接撤消死锁进程或撤消代价最小的进程，直至有足够的资源可用，死锁状态.消除为止；所谓代价是指优先级、运行代价、进程的重要性和价值等。

#### **MyISAM避免死锁：**

- 在自动加锁的情况下，MyISAM 总是一次获得 SQL 语句所需要的全部锁，所以 MyISAM 表不会出现死锁。

#### **InnoDB避免死锁：**

- 为了在单个InnoDB表上执行多个并发写入操作时避免死锁，可以在事务开始时通过为预期要修改的每个元祖（行）使用SELECT ... FOR UPDATE语句来获取必要的锁，即使这些行的更改语句是在之后才执行的。
- 在事务中，如果要更新记录，应该直接申请足够级别的锁，即排他锁，而不应先申请共享锁、更新时再申请排他锁，因为这时候当用户再申请排他锁时，其他事务可能又已经获得了相同记录的共享锁，从而造成锁冲突，甚至死锁
- 如果事务需要修改或锁定多个表，则应在每个事务中以相同的顺序使用加锁语句。 在应用中，如果不同的程序会并发存取多个表，应尽量约定以相同的顺序来访问表，这样可以大大降低产生死锁的机会
- 通过SELECT ... LOCK IN SHARE MODE获取行的读锁后，如果当前事务再需要对该记录进行更新操作，则很有可能造成死锁。
- 改变事务隔离级别

如果出现死锁，可以用 SHOW INNODB STATUS 命令来确定最后一个死锁产生的原因。返回结果中包括死锁相关事务的详细信息，如引发死锁的 SQL 语句，事务已经获得的锁，正在等待什么锁，以及被回滚的事务等。据此可以分析死锁产生的原因和改进措施。

### **一些优化锁性能的建议**

- 尽量使用较低的隔离级别；
- 精心设计索引， 并尽量使用索引访问数据， 使加锁更精确， 从而减少锁冲突的机会
- 选择合理的事务大小，小事务发生锁冲突的几率也更小
- 给记录集显示加锁时，最好一次性请求足够级别的锁。比如要修改数据的话，最好直接申请排他锁，而不是先申请共享锁，修改时再请求排他锁，这样容易产生死锁
- 不同的程序访问一组表时，应尽量约定以相同的顺序访问各表，对一个表而言，尽可能以固定的顺序存取表中的行。这样可以大大减少死锁的机会
- 尽量用相等条件访问数据，这样可以避免间隙锁对并发插入的影响
- 不要申请超过实际需要的锁级别
- 除非必须，查询时不要显示加锁。 MySQL的MVCC可以实现事务中的查询不用加锁，优化事务性能；MVCC只在COMMITTED READ（读提交）和REPEATABLE READ（可重复读）两种隔离级别下工作
- 对于一些特定的事务，可以使用表锁来提高处理速度或减少死锁的可能

### **乐观锁、悲观锁**

- **乐观锁(Optimistic Lock)**：假设不会发生并发冲突，只在提交操作时检查是否违反数据完整性。 乐观锁不能解决脏读的问题。

乐观锁, 顾名思义，就是很乐观，每次去拿数据的时候都认为别人不会修改，所以不会上锁，但是在更新的时候会判断一下在此期间别人有没有去更新这个数据，可以使用版本号等机制。乐观锁适用于多读的应用类型，这样可以提高吞吐量，像数据库如果提供类似于write_condition机制的其实都是提供的乐观锁。

> 1. 如修改时先进行version等CAS判断手段

- **悲观锁(Pessimistic Lock)**：假定会发生并发冲突，屏蔽一切可能违反数据完整性的操作。

悲观锁，顾名思义，就是很悲观，每次去拿数据的时候都认为别人会修改，所以每次在拿数据的时候都会上锁，这样别人想拿这个数据就会block直到它拿到锁。传统的关系型数据库里边就用到了很多这种锁机制，比如行锁，表锁等，读锁，写锁等，都是在做操作之前先上锁。

> 使用事务,进行索引的使用时,会依据事务的隔离级别,若是应对读提交隔离级别的不可重复读问题,则用行锁,若是应对可重读的幻读问题,则用表锁等.

# MySQL 存储过程

### 优点

- 存储过程可封装，并隐藏复杂的商业逻辑。
- 存储过程可以回传值，并可以接受参数。
- 存储过程无法使用 SELECT 指令来运行，因为它是子程序，与查看表，数据表或用户定义函数不同。
- 存储过程可以用在数据检验，强制实行商业逻辑等。

### 缺点

- 存储过程，往往定制化于特定的数据库上，因为支持的编程语言不同。当切换到其他厂商的数据库系统时，需要重写原有的存储过程。
- 存储过程的性能调校与撰写，受限于各种数据库系统。

## 一、存储过程的创建和调用

- 存储过程就是具有名字的一段代码，用来完成一个特定的功能。
- 创建的存储过程保存在数据库的数据字典中。

### 创建存储过程

```mysql
CREATE
    [DEFINER = { user | CURRENT_USER }]
　PROCEDURE sp_name ([proc_parameter[,...]])
    [characteristic ...] routine_body
 
proc_parameter:
    [ IN | OUT | INOUT ] param_name type
 
characteristic:
    COMMENT 'string'
  | LANGUAGE SQL
  | [NOT] DETERMINISTIC
  | { CONTAINS SQL | NO SQL | READS SQL DATA | MODIFIES SQL DATA }
  | SQL SECURITY { DEFINER | INVOKER }
 
routine_body:
　　Valid SQL routine statement
 
[begin_label:] BEGIN
　　[statement_list]
　　　　……
END [end_label]
```

**MYSQL 存储过程中的关键语法**

声明语句结束符，可以自定义:

```
DELIMITER $$
或
DELIMITER //
```

声明存储过程:

```
CREATE PROCEDURE demo_in_parameter(IN p_in int)       
```

存储过程开始和结束符号:

```
BEGIN .... END    
```

变量赋值:

```
SET @p_in=1  
```

变量定义:

```
DECLARE l_int int unsigned default 4000000; 
```

创建mysql存储过程、存储函数:

```
create procedure 存储过程名(参数)
```

存储过程体:

```
create function 存储函数名(参数)
```

### 实例

下面是存储过程的例子，删除给定球员参加的所有比赛：

```mysql
mysql> delimiter $$　　#将语句的结束符号从分号;临时改为两个$$(可以是自定义)
mysql> CREATE PROCEDURE delete_matches(IN p_playerno INTEGER)
    -> BEGIN
    -> 　　DELETE FROM MATCHES
    ->    WHERE playerno = p_playerno;
    -> END$$
Query OK, 0 rows affected (0.01 sec)
 
mysql> delimiter;　　#将语句的结束符号恢复为分号
```

**解析：**默认情况下，存储过程和默认数据库相关联，如果想指定存储过程创建在某个特定的数据库下，那么在过程名前面加数据库名做前缀。 在定义过程时，使用 **DELIMITER $$** 命令将语句的结束符号从分号 **;** 临时改为两个 **$$**，使得过程体中使用的分号被直接传递到服务器，而不会被客户端（如mysql）解释。

调用存储过程：

```
call sp_name[(传参)];
```

```mysql
mysql> select * from MATCHES;
+---------+--------+----------+-----+------+
| MATCHNO | TEAMNO | PLAYERNO | WON | LOST |
+---------+--------+----------+-----+------+
|       1 |      1 |        6 |   3 |    1 |
|       7 |      1 |       57 |   3 |    0 |
|       8 |      1 |        8 |   0 |    3 |
|       9 |      2 |       27 |   3 |    2 |
|      11 |      2 |      112 |   2 |    3 |
+---------+--------+----------+-----+------+
5 rows in set (0.00 sec)
 
mysql> call delete_matches(57);
Query OK, 1 row affected (0.03 sec)
 
mysql> select * from MATCHES;
+---------+--------+----------+-----+------+
| MATCHNO | TEAMNO | PLAYERNO | WON | LOST |
+---------+--------+----------+-----+------+
|       1 |      1 |        6 |   3 |    1 |
|       8 |      1 |        8 |   0 |    3 |
|       9 |      2 |       27 |   3 |    2 |
|      11 |      2 |      112 |   2 |    3 |
+---------+--------+----------+-----+------+
4 rows in set (0.00 sec)
```

**解析：**在存储过程中设置了需要传参的变量p_playerno，调用存储过程的时候，通过传参将57赋值给p_playerno，然后进行存储过程里的SQL操作。

**存储过程体**

- 存储过程体包含了在过程调用时必须执行的语句，例如：dml、ddl语句，if-then-else和while-do语句、声明变量的declare语句等
- 过程体格式：以begin开始，以end结束(可嵌套)

```
BEGIN
　　BEGIN
　　　　BEGIN
　　　　　　statements; 
　　　　END
　　END
END
```

**注意：**每个嵌套块及其中的每条语句，必须以分号结束，表示过程体结束的begin-end块(又叫做复合语句compound statement)，则不需要分号。

为语句块贴标签:

```
[begin_label:] BEGIN
　　[statement_list]
END [end_label]
```

## 二、存储过程的参数

MySQL存储过程的参数用在存储过程的定义，共有三种参数类型,IN,OUT,INOUT,形式如：

```
CREATEPROCEDURE 存储过程名([[IN |OUT |INOUT ] 参数名 数据类形...])
```

- IN 输入参数：表示调用者向过程传入值（传入值可以是字面量或变量）
- OUT 输出参数：表示过程向调用者传出值(可以返回多个值)（传出值只能是变量）
- INOUT 输入输出参数：既表示调用者向过程传入值，又表示过程向调用者传出值（值只能是变量）

## 三、变量

### 1. 变量定义

局部变量声明一定要放在存储过程体的开始：

```
DECLAREvariable_name [,variable_name...] datatype [DEFAULT value];
```

其中，datatype 为 MySQL 的数据类型，如: int, float, date,varchar(length)

例如:

DECLARE l_int int unsigned default 4000000;   DECLARE l_numeric number(8,2) DEFAULT 9.95;   DECLARE l_date date DEFAULT '1999-12-31';   DECLARE l_datetime datetime DEFAULT '1999-12-31 23:59:59';   DECLARE l_varchar varchar(255) DEFAULT 'This will not be padded';

### 2. 变量赋值

```
SET 变量名 = 表达式值 [,variable_name = expression ...]
```

## 四、注释

MySQL 存储过程可使用两种风格的注释

两个横杆**--**：该风格一般用于单行注释。

**c 风格**： 一般用于多行注释。

例如：

```mysql
mysql > DELIMITER //  
mysql > CREATE PROCEDURE proc1 --name存储过程名  
     -> (IN parameter1 INTEGER)   
     -> BEGIN   
     -> DECLARE variable1 CHAR(10);   
     -> IF parameter1 = 17 THEN   
     -> SET variable1 = 'birds';   
     -> ELSE 
     -> SET variable1 = 'beasts';   
    -> END IF;   
    -> INSERT INTO table1 VALUES (variable1);  
    -> END   
    -> //  
mysql > DELIMITER ;
```

### MySQL存储过程的调用

用call和你过程名以及一个括号，括号里面根据需要，加入参数，参数包括输入参数、输出参数、输入输出参数。具体的调用方法可以参看上面的例子。

### MySQL存储过程的查询

我们像知道一个数据库下面有那些表，我们一般采用 **showtables;** 进行查看。那么我们要查看某个数据库下面的存储过程，是否也可以采用呢？答案是，我们可以查看某个数据库下面的存储过程，但是是另一钟方式。

我们可以用以下语句进行查询：

```
selectname from mysql.proc where db='数据库名';

或者

selectroutine_name from information_schema.routines where routine_schema='数据库名';

或者

showprocedure status where db='数据库名';
```

### MySQL查看存储过程的详细

```
SHOWCREATE PROCEDURE 数据库.存储过程名;
```

就可以查看当前存储过程的详细。

### MySQL存储过程的修改

```
ALTER PROCEDURE
```

更改用 CREATE PROCEDURE 建立的预先指定的存储过程，其不会影响相关存储过程或存储功能。

### MySQL存储过程的删除

删除一个存储过程比较简单，和删除表一样：

```
DROPPROCEDURE
```

### MySQL存储过程的控制语句

**(1). 变量作用域**

内部的变量在其作用域范围内享有更高的优先权，当执行到 end。变量时，内部变量消失，此时已经在其作用域外，变量不再可见了，应为在存储过程外再也不能找到这个申明的变量，但是你可以通过 out 参数或者将其值指派给会话变量来保存其值。

```mysql
mysql > DELIMITER //  
mysql > CREATE PROCEDURE proc3()  
     -> begin 
     -> declare x1 varchar(5) default 'outer';  
     -> begin 
     -> declare x1 varchar(5) default 'inner';  
      -> select x1;  
      -> end;  
       -> select x1;  
     -> end;  
     -> //  
mysql > DELIMITER ;
```

**(2). 条件语句**

1. if-then-else 语句

```mysql
mysql > DELIMITER //  
mysql > CREATE PROCEDURE proc2(IN parameter int)  
     -> begin 
     -> declare var int;  
     -> set var=parameter+1;  
     -> if var=0 then 
     -> insert into t values(17);  
     -> end if;  
     -> if parameter=0 then 
     -> update t set s1=s1+1;  
     -> else 
     -> update t set s1=s1+2;  
     -> end if;  
     -> end;  
     -> //  
mysql > DELIMITER ;
```

2.   case语句：

```mysql
mysql > DELIMITER //  
mysql > CREATE PROCEDURE proc3 (in parameter int)  
     -> begin 
     -> declare var int;  
     -> set var=parameter+1;  
     -> case var  
     -> when 0 then   
     -> insert into t values(17);  
     -> when 1 then   
     -> insert into t values(18);  
     -> else   
     -> insert into t values(19);  
     -> end case;  
     -> end;  
     -> //  
mysql > DELIMITER ; 
case
    when var=0 then
        insert into t values(30);
    when var>0 then
    when var<0 then
    else
end case
```

**(3). 循环语句**

1. while ···· end while

```mysql
mysql > DELIMITER //  
mysql > CREATE PROCEDURE proc4()  
     -> begin 
     -> declare var int;  
     -> set var=0;  
     -> while var<6 do  
     -> insert into t values(var);  
     -> set var=var+1;  
     -> end while;  
     -> end;  
     -> //  
mysql > DELIMITER ;
while 条件 do
    --循环体
end while
```

2. repeat···· end repeat

它在执行操作后检查结果，而 while 则是执行前进行检查。

```mysql
mysql > DELIMITER //  
mysql > CREATE PROCEDURE proc5 ()  
     -> begin   
     -> declare v int;  
     -> set v=0;  
     -> repeat  
     -> insert into t values(v);  
     -> set v=v+1;  
     -> until v>=5  
     -> end repeat;  
     -> end;  
     -> //  
mysql > DELIMITER ;
repeat
    --循环体
until 循环条件  
end repeat;
```

3. loop ·····endloop

loop 循环不需要初始条件，这点和 while 循环相似，同时和 repeat 循环一样不需要结束条件, leave 语句的意义是离开循环。

```mysql
mysql > DELIMITER //  
mysql > CREATE PROCEDURE proc6 ()  
     -> begin 
     -> declare v int;  
     -> set v=0;  
     -> LOOP_LABLE:loop  
     -> insert into t values(v);  
     -> set v=v+1;  
     -> if v >=5 then 
     -> leave LOOP_LABLE;  
     -> end if;  
     -> end loop;  
     -> end;  
     -> //  
mysql > DELIMITER ;
```

4. LABLES 标号：

标号可以用在 begin repeat while 或者 loop 语句前，语句标号只能在合法的语句前面使用。可以跳出循环，使运行指令达到复合语句的最后一步。

**(4). ITERATE迭代**

ITERATE 通过引用复合语句的标号,来从新开始复合语句:

```mysql
mysql > DELIMITER //  
mysql > CREATE PROCEDURE proc10 ()  
     -> begin 
     -> declare v int;  
     -> set v=0;  
     -> LOOP_LABLE:loop  
     -> if v=3 then   
     -> set v=v+1;  
     -> ITERATE LOOP_LABLE;  
     -> end if;  
     -> insert into t values(v);  
     -> set v=v+1;  
     -> if v>=5 then 
     -> leave LOOP_LABLE;  
     -> end if;  
     -> end loop;  
     -> end;  
     -> //  
mysql > DELIMITER ;
```

#	索引长度限制 

MySQL中给varchar和text字段建立普通索引时,最好指定索引长度.

 InnoDB单列索引长度不能超过767字节,联合索引不能超过3072字节. 

utf8每个字符占用3个字节,那么该列索引最大长度为floor(767/3)=255. 

utf8mb4每个字符占用4个字节,那么该列索引最大长度为floor(767/4)=191.

 比如WordPress的wp_posts表的post_title字段类型为utf8mb4_unicode_520_ci,它的索引定义是: KEY `post_name` (`post_name`(191)) 其中191就是索引长度. 

全文索引应该是没有长度限制的. 错误提示是语法错误,全文索引是这样创建的,

比如给attr_value_ids字段创建全文索引: 

``` sql
text CREATE TABLE IF NOT EXISTS `goods_attr_value_fts` 
( `goods_id` bigint unsigned NOT NULL COMMENT '商品编号', 
 `attr_value_ids` text NOT NULL COMMENT '商品具有的属性值编号', 
 PRIMARY KEY (`goods_id`),
 FULLTEXT KEY `ft_attr_value_ids` (`attr_value_ids`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_general_ci COMMENT='商品和属性筛选表';
```

