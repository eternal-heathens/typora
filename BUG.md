1. FATAL ERROR in native method: JDWP No transports initialized, jvmtiError=AGENT_ERROR_TRANSPORT_LOAD(196)

> jre和JDK配置不一致，将Edit Configurations的Enviroment的jre路径改成当前jdk的Jre路径

2.   1.简单数据类型,
     此时#{id,jdbcType=INTEGER}中id可以取任意名字如#{a,jdbcType=INTEGER},

   ``` xml
     <select id="selectByPrimaryKey" resultMap="BaseResultMap" parameterType="Java.lang.Integer" >
      select 
      <include refid="Base_Column_List" />
      from base.tb_user
     <if test="_parameter != null">
      where id = #{id,jdbcType=INTEGER}
     </if>
     </select>
   ```

   

3. **resultMap封装映射为集合对象的包含类型，也可以接收返回的集合对象**

4. > 如何使用Interceptor呢？ 
   >
   > 实例化后的Interceptor会在ApplicationContext初始化时读取实现了Inteceptor接口的实例按实例顺序自动添加到责任链中
   >
   > Interceptor会在何时起作用？ 
   >
   > 我们需要先通过@Interceptor判断我们的Interceptor是对于在sql的Executor、StatementHandler、ParameterHandler、ResultSetHandler四个处理阶段中进行拦截的与拦截了四个类中的什么方法，以便更好的理解Interceptor在哪一步将Page信息往下传递。（因为在创建sqlSession 的时候就会同步创建Executor，并在query执行sql语句时，新建statementHandler并同时创建ParameterHandle和ResultSetHandler，所以Interceptor可以随心在哪个阶段进行拦截并顺序处理，也因此是分页插件可以实现） https://www.cnblogs.com/tanghaorong/p/14094521.html 
   >
   > 分页需要在Dao层进行哪些标记使得Inteceptor能判断哪些方法需要分页呢？ 
   >
   > 不同的Interceptor有不同的拦截逻辑，会使用不同的逻辑判断动态增强DAO层， 在拦截到标记的拦截类的拦截方法时，在intercept方法中对Dao方法封装成的statement进行相应条件的判断 
   >
   > 如PageHelper会在执行Dao层时判断ThreadLocal上的查看有没有Page 的属性存储，中间会进行判断该sql 的类型是查询，还是修改操作。若是查询有则动态增强，分页处理 
   >
   > ```java
   > this.checkDialectExists();
   > ```
   >
   > PaginationInterceptor则会判断Dao层的第一个参数中有没有实现IPage接口的如Page类，也会判断是否为查询操作，若是且有则读取Page类中存储的属性，动态增强，进行分页处理。
   >
   > ```java
   > IPage<?> page = (IPage)ParameterUtils.findPage(paramObj).orElse((Object)null);
   > if (null != page && page.getSize() >= 0L) {
   > ···
   > }
   > ```

   
   > 为什么只是实例化了Page和将其放入Dao接口，就在Intecepter还没起作用BoundRound就起作用了？
   >
   > springboot的AutoConfiguration生成的sqlsessionFactory在我们进行Mapper接口的sql操作时会创建一个sqlsession，sqlsession会包含Mapper.class和Mappper.xml的configure匹配信息并创建JDBC等的底层SQL语句通过socket与数据库进行交互的Executor等组件，但此时还没有将sqlsession与具体的那个Mapper进行绑定，因此此时还要再将sqlsession和Mapper进行动态增强，将该sqlsession和Mapper动态代理生成一个Mapperproxy对象，取代了Mapper原先的方法在JVM的调用入口，因此在ServiceImplement调用mapper方法时，他会先经由（org.apache.ibatis.binding）的Mapperproxy（Implement Sqlsession）进行增强处理，而此时其会读取Mapper的方法信息（包括方法名和入参等），其会先用MapperMethod进行预处理，依据INSERT/UPDATE/DELETE/SELECT进行不同处理（需要注意的是若是SELECT，其如果入参中有RowBounds的实现类，包括Page/Pagination等，其会自动在MapperMethod的RowBounds中注明该索引实现的位置，然后在调用sqlsessionTemplate/Sqlsession时，将其直接取出实例化为RowBounds，作为selectList的参数进行调用）。
   >
   > 再之后才是sqlsession经由Executor/StatementHandler等组件进行执行，在其间由各类自定义实现的sql相关的Interceptor进行拦截处理，如PageHelper/IPage等。
   >
   > 若是不想用ThreadLocal或者自己编写分页Interceptor又不想写对RowBounds进行处理的，又需要用到RowBounds等selectList等方法需要的信息的，可以用上面的预处理对MapperMethod中存储的信息进行合理的利用

5. 看导入的包的源码时要先downloadSource，不然有可能即使是java文件也是IDEA 反编译来的，比较明显的标志是有varxx等无意义表示的变量名，这种代码的可视化与内部执行逻辑可能不同，可能出现实际执行逻辑和你看到的不符合的情况

6. downloadSource时连接不了有可能是项目启动时使得本地配置出了问题，可以重启试试。

7. Swagger 在除了GET的时候的请求的时候，入参中有Response等会将其作为Swagger需要调试的参数，需要@APIIgnore忽略，不然测试端会有混乱。

8. 判断空等String相关的判断用apache的StringUtil

9. 动态sql判断空时要注意将sql和#取值的都放在判断语句中，避免取值null时，只有sql而没有值而sql异常。

10. 注意前端接收为空时的交互处理

11. **被观察者是自身有变化通知其他观察者的，观察者是收到通知再执行观察方法的**

    ​		**发布订阅和观察者其实可以说是一个东西，只是一般来说发布订阅可能更形象形容一种场景**
    
12. 因为zk是基于java编写的，因此对运行环境jre有要求，所以太新的zk版本若是不向后兼容，则会有异常

12. MYSQL中字符集若是没特殊情况使用默认的utf8mb4

> utf8 是 Mysql 中的一种字符集，只支持最长三个字节的 UTF-8字符，也就是 Unicode 中的基本多文本平面。即每个字符最多有3字节的物理空间
>
> 要在 Mysql 中保存 4 字节长度的 UTF-8 字符，需要使用 utf8mb4 字符集

13. 索引：一般主键pk\_，唯一索引uk\_，普通索引idx\_   + +数据库名+字段

14. dubbo使得客户端和服务端只需知道注册中心的地址来消费/注册需要的服务，因为消息会经由不同的中间件加工的资源而来，不同中间件的数据传输有不同的要求，而就需要提供不同的接口给不同的中间件使用，而由ZK去记录具体某个服务与其服务对应的ip位置，并使用负载均衡，保持功能的纯粹。

15. **千万要先知道各个服务本身的命令格式，别自己瞎搞浪费时间**

16. **注意逐字母检查配置文件的更改**

17. 配置服务器时，注意配置配置相应公或内网ip地址，而不是直接配置成localhost，在有内网外网区分的情况localhost会匹配成内网ip，这样通过公网ip接收的消息服务会接收不到。

18. 用户态到内核态本质上不过是CPU负责的进程的寄存器的模式位的改变而已，开放了不同的权限，但是用户态到内核态需要中断等异常流的操作，并且刷新一些寄存器的空间等给内核态进程使用，再恢复也会导致消耗巨大，内核态到用户态又需要设置标志位，频繁的切换消耗也不容小觑，内核态的内核线程上下文的切换和用户态的用户线程上下文的保护恢复等反而很多都相似，主要应是状态转换导致寄存器等的上下文切换导致消耗较大。

19.报确实org.apache.*.Configuration，可能是classess 文件缺失了包，直接clean reload

20. pom tool.jar导入需要用绝对路径，相对路径失效

21. 全部import爆红，file-> invalidate Cache 重启

22. 启动时zookeeper 找不到路径 ，内网限制，需要登陆sufflin
23. 一直报invalid internal transport message format，got(48,54,54,50) ,解决方案，更改配置（更改disconf版本，2版本仍不太稳定）
24. xml需要使用注解或Resolver类进行解析引用，properties需要@ImportSource等进行读取
25. xml中DisconfMgrBean的applicationContext没有property的时候是如何用set注入的
26. class文件downloadsource时，需要在IDea  Setting 的Maven的Local repository 开启Override
27. git pull 时出现 unable to access

> 1. 注意若是因为VPN而改过系统代理设置，此时需要开启VPN才行
> 2. 更改git配置  git config  – – global - - unset http.proxy

28. git commit 时要加备注信息，不然会报错
29. 没有 git clone ，因为桌面有.git 文件，导致git以为在仓库中，隐藏了git clone，需要用shift+右键
30. nothing added to commit but untracked files present,这是git没有把提交的文件加载进来，但是把需要提交的文件都列出来了，只需要用git add XXX(文件名) 把需要提交的文件加上 ，然后git commit -m "xx",再 git  push - u  origin  master重新提交就可以了
31. @Autowired Mapper 爆红 在Setting 的Inspections中更改SpringCore 的警报选项
32. javadoc的声明报Error，可以在Setting查JavaDoc，更改提示类型
33. gmt_creaete的datetime类型的长度为3，若需要**CURRENT_TIMESTAMP**则需要默认值设置为**CURRENT_TIMESTAMP(3)**
34. **无特殊需求下Innodb建议使用与业务无关的自增ID作为主键**

InnoDB引擎使用聚集索引，数据记录本身被存于主索引（一颗B+Tree）的叶子节点上。这就要求同一个叶子节点内（大小为一个内存页或磁盘页）的各条数据记录按主键顺序存放，因此每当有一条新的记录插入时，MySQL会根据其主键将其插入适当的节点和位置，如果页面达到装载因子（InnoDB默认为15/16），**则开辟一个新的页（节点）**

1、如果表使用自增主键，那么每次插入新的记录，记录就会顺序添加到当前索引节点的后续位置，当一页写满，就会自动开辟一个新的页。

这样就会形成一个紧凑的索引结构，近似顺序填满。**由于每次插入时也不需要移动已有数据，因此效率很高，也不会增加很多开销在维护索引上。**

2、 如果使用非自增主键（如果身份证号或学号等），由于每次插入主键的值近似于随机，因此每次新纪录都要被插到现有索引页得中间某个位置

**此时MySQL不得不为了将新记录插到合适位置而移动数据，甚至目标页面可能已经被回写到磁盘上而从缓存中清掉，此时又要从磁盘上读回来，这增加了很多开销，同时频繁的移动、分页操作造成了大量的碎片**，得到了不够紧凑的索引结构，后续不得不通过OPTIMIZE TABLE来重建表并优化填充页面。

在使用InnoDB存储引擎时，如果没有特别的需要，请永远使用一个与业务无关的自增字段作为主键。

**mysql 在频繁的更新、删除操作，会产生碎片。而含碎片比较大的表，查询效率会降低。此时需对表进行优化，这样才会使查询变得更有效率**

35. git 拉取项目时显示Authentication failed，又不会弹出账号密码填写的窗口，便是系统保存了凭证，需要在凭据管理器中修改

