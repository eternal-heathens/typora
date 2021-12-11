## mybatis  的优缺点

MyBatis 避免了几乎所有的 JDBC 代码和手动设置参数以及获取结果集。MyBatis 可以对配置和原生Map使用简单的 XML 或注解（实体和数据库的映射可以在XML中间中，也可以使用注解），将接口和 Java 的 POJOs(Plain Old Java Objects,普通的 Java对象)映射成数据库中的记录。

**一、MyBatis框架的优点：**

　　1. 与JDBC相比，减少了50%以上的代码量。（Mybatis中Connection的commit等具体实现也是jdbc实现的）

　　2. MyBatis语法较为简单，小巧并且简单易学。

　　3. sql实现灵活，SQL写在XML里，从程序代码中彻底分离，降低耦合度，便于统一管理和优化，可重用。

　　**4. 提供XML标签，支持编写动态SQL语句（XML中使用if, else）。**

　　5. 提供映射标签，支持对象与[数据库](http://lib.csdn.net/base/mysql)的ORM字段关系映射（在XML中配置映射关系，也可以使用注解）。

**二、MyBatis框架的缺点：**

　　**1. SQL语句的编写工作量较大，尤其是字段多、关联表多时，更是如此，对开发人员编写SQL语句的功底有一定要求。**

　　**2. SQL语句依赖于数据库，导致数据库移植性差，不能随意更换数据库。**



## 入门案例

![image-20200718213831059](F:\Typora数据储存\数据库\mybatis.assets\image-20200718213831059.png)

* 实体类属性和表的列名需要保持一致的原因是excutor中执行完sql语句后返回的列名需要和你接收的实体类的属性一致才能成功给实体类赋值。

> - **由于在 Windows 环境下 MySQL 不区分大小，所以 `userName` 等同 `username` ，不过要注意在 Linux 环境下 MySQL 严格区别大小写。**
> - **为了解决实体类属性名与数据库表列名不一致，有以下解决方法：**
>
> 1. **在 SQL 语句中为所有列起别名，使别名与实体类属性名一致（执行效率相对高，开发效率低）**
>
> ```xml
> <select id="listAllUsers" resultType="cn.ykf.pojo.User">
> ```
> 2. **使用 resultMap 完成结果集到实体类的映射（执行效率相对低，开发效率高）**
> ```
> <mapper namespace="cn.ykf.mapper.UserMapper">
>     <!-- 配置 resultMap ，完成实体类与数据库表的映射 -->
>     <resultMap id="userMap" type="cn.ykf.pojo.User">
>         <id property="userId" column="id" />
>         <result property="userName" column="username"/>
>         <result property="userBirthday" column="birthday"/>
>         <result property="userAddress" column="address"/>
>         <result property="userSex" column="sex"/>
>     </resultMap>
>     <!-- 配置查询所有用户 -->
>     <select id="listAllUsers" resultMap="userMap">
>        SELECT * FROM user
>     </select>
> </mapper>
> ```
>
> 

![image-20200718222122318](F:\Typora数据储存\数据库\mybatis.assets\image-20200718222122318.png![image-20200719121634547](F:\Typora数据储存\数据库\mybatis.assets\image-20200719121634547.png)

##### 所用模式

![image-20200719131042766](F:\Typora数据储存\数据库\mybatis.assets\image-20200719131042766.png)

**Mybatis 在使用代理 Mapper 的方式（session.getMapper(xxx.class)）实现增删查改的时候只做了以下两件事（划重点）**

- 创建代理对象
- 在代理对象中调用 selectList

<img src="https://img-blog.csdnimg.cn/20200127102223857.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ExMDkyODgyNTgw,size_16,color_FFFFFF,t_70#pic_center" alt="img" style="zoom:200%;" />

##### 自定义mybatis流程

* https://blog.csdn.net/a1092882580/article/details/104086181
* day01_eesy_04mybatis

![image-20200720083531672](F:\Typora数据储存\数据库\mybatis.assets\image-20200720083531672.png)

![image-20200720083645169](F:\Typora数据储存\数据库\mybatis.assets\image-20200720083645169.png)

* **sqlsessionfactoryBuilder的作用是将xml的解析部分与factory的构造函数拿出来，便于xml解析器维护和使得factory别太臃肿**

1. 编写读取配置文件Resource类，获得配置文件字节流（XMLConfigBuilder形参）
2. 编写SqlsessionFactoryBuilder类
3. 准备工具类XMLConfigBuilder,用于解析XML文件（SqlsessionfactoryBuilder使用）
4. 准备自定义注解接口、配置类Configuration、封装Sql语句和结果类型的Mapper类（XMLConfigBuilder保存解析信息）
5. 编写SqlsessionFactory接口、实现SqlsessionFacatory（SqlsessionFactoryBuilder生成对象）
6. 编写Sqlsession接口，实现Sqlsession（SqlsessionFactory生成对象）
7. 编写链接数据库的工具类DataSourceUtil，编写代理类MapperProxy(Implements InvocationHandler，其实就是调用Executor的selectList方法)（sqlSession类使用）
8. 编写Exector类，用于执行jdbc操作（MapperProxy使用）

## CURD

* 获取参数时

* parameterType = "与表对应的实体类"
* #{实体类属性}： 读取实体类的属性

####  模糊查询

* #{} 需要在测试类中自己家%等通配符，**像PreparedStatement ，#{}可以有效防止sql注入，${}则可能导致sql注入成功。**

  ```
  public void testListUsersByName() {
  List<User> users = mapper.listUsersByName("%王%");
  // 使用 Stream 流 + 方法引用，需要至少jdk8
  users.forEach(System.out::println);
  }
  由于映射文件中的 SQL 语句并没有对参数进行模糊查询处理，所以在调用方法的时候我们必须手动为查询的关键字进行%拼接，这样很不方便。如果想调用方法查询时，只传入查询的关键字，那么可以采用以下方法 ：
  SELECT * FROM user WHERE username LIKE concat('%',#{name},'%');
  SELECT * FROM user WHERE username LIKE "%"#{name}"%"；
  ```

  


![image-20200720154032187](F:\Typora数据储存\数据库\mybatis.assets\image-20200720154032187.png)

#### parameterType

* 简单对象

* 传递pojo对象

  首先介绍一下 OGNL 表达式
  全称 Object Graphic Navigation Language ，即对象图导航语言

  它是通过对象的取值方法来获取数据。在写法上把get给省略了。

* 传递pojo包装对象
在开发中如果想实现复杂查询 ，查询条件不仅包括用户查询条件，还包括其它的查询条件（比如将用户购买商品信息也作为查询条件），这时可以使用 pojo 包装对象传递输入参数，即将多个需要操作的类放到一个总体类中

#### **实体类属性名与数据库表列名不一致**

**在某些情况下，实体类的属性和数据库表的列名并不一致，那么就会出现查询结果无法封装进实体类。因为select的返回的数据是依据列名与实体类属性的对应关系进行赋值的，失败便为空，修改实体类进行演示**

![image-20200720223047768](F:\Typora数据储存\数据库\mybatis.assets\image-20200720223047768.png)

1. **在 SQL 语句中为所有列起别名，使别名与实体类属性名一致（执行效率相对高，开发效率低）**

```
<select id="listAllUsers" resultType="cn.ykf.pojo.User">
    SELECT id AS userId, username AS userName, birthday AS userBirthday, sex AS userSex, address AS userAddress FROM user
</select>
```

2. **使用 resultMap 完成结果集到实体类的映射（执行效率相对低，开发效率高）**

```xml
<mapper namespace="cn.ykf.mapper.UserMapper">
    <!-- 配置 resultMap ，完成实体类与数据库表的映射 -->
    <resultMap id="userMap" type="cn.ykf.pojo.User">
        <id property="userId" column="id" />
        <result property="userName" column="username"/>
        <result property="userBirthday" column="birthday"/>
        <result property="userAddress" column="address"/>
        <result property="userSex" column="sex"/>
    </resultMap>
    <!-- 配置查询所有用户 -->
    <select id="listAllUsers" resultMap="userMap">
       SELECT * FROM user
    </select>
</mapper>
 
```

#### 使用Dao实现类（划重点->对比源码）

* **若使用sqlsession（defaultSqlSession）的selectList等方法，就是对传入的单个方法（“statement”，arg）进行相应executor(CachingExecutor->SimpleExecutor(extends BaseExecutor))操作**
* **接着进入执行SQL语句处理（MyBatis在第一次执行SQL操作时，在获取Statement时，会去获取数据库链接，如果我们配置的数据源为POOLED，这里会使用PooledDataSource来获取connection），statementHandler（RoutingStatementHandler->PreparedStatementHandler(extends BaseStatementHandler)）**
* **接着封装处理ResultSetHandler（DefaultResultSetHandler），接着调用excute方法进行JDBC的操作并进行封装，返回结果集。**

#### 使用Dao代理类（划重点->对比源码、自定义mybatis）

* **若是用getmapper则是会在代理对象调用方法时调用动态代理的proxyhadler对像中的invoke方法进行增强，增强时对调用的方法进行switch case 选择，继而调用sqlsession的相应CRUD方法（即上面的流程），返回了一个整个增强的代理类实例，再调用实例方法时增强返回增强后的结果集。**


#### propeties

* 全局变量

  第一种，采用全局的内部配置。采用这种方式的话，如果需要配置多个数据库环境，那么像 username、password 等属性就可以复用，提高开发效率。

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  
  <!DOCTYPE configuration
          PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-config.dtd">
  <configuration>
      <!-- 全局变量 -->
      <properties>
          <property name="driver" value="com.mysql.jdbc.Driver"/>
          <property name="url" value="jdbc:mysql://localhost:3306/db_mybatis"/>
          <property name="username" value="root"/>
          <property name="password" value="123456"/>
      </properties>
  
  
      <!--配置环境-->
      <environments default="development">
          <environment id="development">
              <!-- 配置事务类型 -->
              <transactionManager type="JDBC"/>
              <!-- 配置数据源（连接池） -->
              <dataSource type="POOLED">
                  <property name="driver" value="${driver}"/>
                  <property name="url" value="${url}"/>
                  <property name="username" value="${username}"/>
                  <property name="password" value="${password}"/>
              </dataSource>
          </environment>
      </environments>
      
      <!-- 指定映射文件 -->
      <mappers>
          <mapper resource="UserMapper.xml"/>
      </mappers>
  
  </configuration>
  ```

  

* resource：

![image-20200720212414351](F:\Typora数据储存\数据库\mybatis.assets\image-20200720212414351.png)

* url

![image-20200720212823840](F:\Typora数据储存\数据库\mybatis.assets\image-20200720212823840.png)

#### typeAliases 标签

之前在编写映射文件的时候， resultType 这个属性可以写 int、INT 等，就是因为 Mybatis 给这些类型起了别名。Mybatis 内置的别名如表格所示：
<img src="F:\Typora数据储存\数据库\mybatis.assets\image-20200720223623594.png" alt="image-20200720223623594"  />
如果我们也想给某个实体类指定别名的时候，就可以采用 typeAliases 标签。用法如下：

```
<configuration>
    <!--配置别名-->
    <typeAliases>
        <typeAlias type="cn.ykf.pojo.User" alias="user"/>
    </typeAliases>
    <!-- 其他配置省略... -->
</configuration>
```

typeAlias 子标签用于配置别名。其中 type 属性用于指定要配置的类的全限定类名（该类只能是某个domain实体类）, alias 属性指定别名。一旦指定了别名，那么别名就不再区分大小写。
也就是说，此时我们可以在映射文件中这样写 resultType="user" ，也可以写 resultType="USER"。
当我们有多个实体类需要起别名的时候，那么我们就可以使用 package 标签。


当我们有多个实体类需要起别名的时候，那么我们就可以使用 package 标签。

```
<typeAliases><!--在configuration中使用-->
    <!-- 包下所有实体类起别名 -->
    <package name="cn.ykf.pojo"/>
</typeAliases>
```

package 标签指定要配置别名的包，当指定之后，该包下的**所有实体类都会注册别名**，并且**类名就是别名**，不再区分大小写
package 标签还可以将**某个包内的映射器接口实现全部注册为映射器**，如下所示

```
<!-- 指定映射文件 -->
<mappers>
    <package name="cn.ykf.mapper"/>
</mappers>
```

这样配置后，我们就无需一个一个地配置 Mapper 接口了。不过，**这种配置方式的前提是映射配置文件位置必须和dao接口的包结构相同**

> * mapper的recourse加载，扫描映射文件来查找接口类，映射文件命名为**接口全类名**（接口类名可以和映射文件名不同，因为映射文件有namespace），但可以dao接口和mapper文件不在同一路径下,**mybatis原始开发Dao.xml文件与接口文件不在同一路径下,仅能用resource加载映射文件**
> * mapper的class加载，仅适用于类路径下,**接口文件与映射文件在同一路径下**,且**接口名与映射文件名相同**的情况.（扫描接口类来查找映射文件，注解的时候用的多）
> * package,适用于类路径下,接口文件与映射文件在**同一路径**下,且接口名与映射**文件名相同**的情况.(package 的优势是若在同一路径且接口名与映射**文件名相同**，则可以定位到包名就行，不要定位到每个类名)
>
>
> package定位时，若包不同且写的为xml所在的包，会报Type interface com.itheima.dao.IUserDao is not known to the MapperRegistry.
>
> 若写的为接口的包，则直接找不到statement
>

> Mybatis中接口和对应的mapper文件**不一定要放在同一个包下**，放在一起的目的是为了Mybatis进行自动扫描，并且要注意此时java接口类的名称和mapper文件的名称要相同，否则会报异常，由于此时**Mybatis会自动解析对应的接口和相应的配置文件**（class和package），所以就不需要配置mapper文件的位置了。**因为通常的xml文件的namespace、id需要到接口类的信息，而此时mybatis最先开始的是 XML的解析，若是不同包，config.xml调用的将是mappers中mapper的包（和接口类不在同一级），尚未将其他的为用到的接口类的结构信息通过类加载器加载到JVM的方法区中，因此会缺乏相关的信息组成mappers的成员对象，调用失败导致报错。而若是在同一个包，编译成.class到target包中时，recourse和java包会合并，则可以自动寻找同一级中是否有对应的同名接口信息，便可省去配置的麻烦**，resouce不用受此拘束肯定额外调用了别的方法，会造成更多的资源浪费，为了特殊需求设计的。
>
> 1. 接口和文件在同一个包中
>    1.1 默认maven构建
>    如果在工程中使用了maven构建工具，那么就会出现一个问题：我们知道在典型的maven工程中，目录结构有：src/main/java和src/main/resources，前者是用来存放java源代码的，后者则是存放一些资源文件，比如配置文件等，**在默认的情况下maven打包的时候，对于src/main/java目录只打包源代码，而不会打包其他文件。所以此时如果把对应的mapper文件放到src/main/java目录下时，不会打包到最终的jar文件夹中，也不会输出到target文件夹中，**由于在进行单元测试的时候执行的是/target目录下/test-classes下的代码，所以在测试的时候也不会成功。
>
>    为了实现在maven默认环境下打包时，Mybatis的接口和mapper文件在同一包中，可以通过将接口文件放在src/main/java某个包中，而在src/main/resources目录中建立同样的包，这是一种约定优于配置的方式，这样在maven打包的时候就会将src/main/java和src/main/resources相同包下的文件合并到同一包中。
>
>    在默认maven打包的环境下，不要将接口文件和mapper文件全部放到src/main/java，这样也不会把mapper文件打包进去
>
> 简要的过程如下（与如下的例子无关）：
>
> ```
> src/main/java
>     edu.zju.mapper
>        UserMapper.java
> src/main/resources
>     config.xml
>     edu.zju.mapper
>         UserMapper.xml
> ```
>
> 如上这种方式在maven打包之后的目录如下：
>
> ```
> -src/main/java
>     -config.xml
>     -edu.zju.mapper
>         -UserMapper.java
>         -UserMapper.xml
> ```
>
> 2.1 更改maven构建配置
>
> 如果不想将接口和mapper文件分别放到`src/main/java`和`src/main/resources`中，而是全部放到`src/main/java`，那么在构建的时候需要指定maven打包需要包括xml文件，具体配置如下：
>
> ```
> <build>
>     <resources>
>         <resource>
>             <directory>src/main/java</directory>
>             <includes>
>                 <include>**/*.xml</include>
>             </includes>
>             <filtering>false</filtering>
>         </resource>
>     </resources>
> </build>
> ```
>
> 

#### 动态SQL

###### if

```
<!-- 根据查询条件进行复合查询 -->
<select id="listUsersByCondition" parameterType="cn.ykf.pojo.User" resultType="cn.ykf.pojo.User">
    SELECT * FROM user WHERE 1 = 1
    <if test="username != null and username != ''">
        AND username LIKE CONCAT('%',#{username},'%')
    </if>
    <if test="sex != null and sex != ''">
        AND sex = #{sex}
    </if>
    <if test="address != null and address != ''">
        AND address = #{address}
    </if>
    <if test="birthday != null">
        AND birthday = #{birthday}
    </if>
</select>
这里加上 WHERE 1 =1 是防止所有条件都为空时拼接 SQL 语句出错。因为不加上 1 = 1 这个恒等条件的话，如果后面查询条件都没拼接成功，那么 SQL 语句最后会带有一个 WHERE 关键字而没有条件，不符合 SQL 语法。
<if></if> 标签中 test 属性是必须的，表示判断的条件。其中有几点需要注意：
如果 test 有多个条件，那么必须使用 and 进行连接，而不能使用 Java 中的 && 运算符,mybatis自己设立的，需要符合他的XML解析规则。
test 中的参数名称必须与实体类的属性保持一致，也就是和 #{参数符号} 保持一致。
如果判断条件为字符串，那么除了判断是否为 null 外，最好也判断一下是否为空字符串，'' ，防止 SQL语句将其作为条件查询。
```



###### where

```
<select id="listUsersByCondition" parameterType="cn.ykf.pojo.User" resultType="cn.ykf.pojo.User">
    SELECT * FROM user
    <where>
        <if test="username != null and username != ''">
            AND username LIKE CONCAT('%',#{username},'%')
        </if>
        <if test="sex != null and sex != ''">
            AND sex = #{sex}
        </if>
        <if test="address != null and address != ''">
            AND address = #{address}
        </if>
        <if test="birthday != null">
            AND birthday = #{birthday}
        </if>
    </where>
</select>
可以发现，相比之前的 SQL 语句，我们少写了 WHERE 1 = 1，而是使用 <where></where> 标签来代替它。
<where></where> 标签只会在至少有一个子元素的条件返回 SQL 子句的情况下才去插入 WHERE 子句。而且，若语句的开头为 AND 或 OR，<where></where> 标签也会将它们去除。
简单来说，就是该标签可以动态添加 WHERE 关键字，并且剔除掉 SQL 语句中多余的 AND 或者 OR。
```

###### foreach

假如我们现在有一个新的需求，就是根据一个 id 集合，来查询出 id 在该集合中的所有用户，那么又该怎么实现呢？
如果使用普通 SQL 语句的话，那么查询语句应该这样写：SELECT * FROM user WHERE id IN(41,42,43);
因此，如果想使用动态 SQL 来完成的话，那么我们就应该考虑如何拼接上 id IN(41,42,43) 这一串内容，这时候，我们的 <foreach></foreach> 标签就出场了。

**foreach元素的属性主要有 item，index，collection，open，separator，close。**

```
item表示集合中每一个元素进行迭代时的别名，
index指 定一个名字，用于表示在迭代过程中，每次迭代到的位置，
open表示该语句以什么开始，
separator表示在每次进行迭代之间以什么符号作为分隔 符，
close表示以什么结束。12345
```

在使用foreach的时候最关键的也是最容易出错的就是collection属性，该属性是必须指定的，但是在不同情况 下，该属性的值是不一样的，主要有一下3种情况：

```
1. 如果传入的的是一个普通对象（没有特殊的数据结构），而该类中有list等collection属性的，需要用到该属性并需要foreach，collection则为其中属性的值，即用的为包装类Vo时。
2. 如果传入的是单参数且参数类型是一个List的时候，collection属性值为list
3. 如果传入的是单参数且参数类型是一个array数组的时候，collection的属性值为array
4. 如果传入的参数是多个的时候，我们就需要把它们封装成一个Map了，collection的属性值为map的key属性
```

```
<!-- 根据id集合查询用户 -->
<select id="listUsersByIds" parameterType="cn.ykf.pojo.QueryVo" resultType="cn.ykf.pojo.User">
    SELECT * FROM user
    <where>
        <if test="ids != null and ids.size > 0">
            <foreach collection="ids" open="AND id IN (" close=")" item="id" separator=",">
                #{id}
            </foreach>
        </if>
    </where>
</select>
<foreach></foreach> 标签用于遍历集合，每个属性的作用如下所示：
collection ： 代表要遍历的集合或数组，这个属性是必须的。如果是遍历数组，那么该值只能为 array
open ： 代表语句的开始部份。
close ： 代表语句的结束部份。
item ： 代表遍历集合时的每个元素，相当于一个临时变量。
separator ： 代表拼接每个元素之间的分隔符。
你可以将任何可迭代对象（如 List、Set 等）、Map 对象或者数组对象传递给 foreach 作为集合参数。当使用可迭代对象或者数组时，index 是当前迭代的次数，item 的值是本次迭代获取的元素。当使用 Map 对象（或者 Map.Entry 对象的集合）时，index 是键，item 是值。
注意，SQL 语句中的参数符号 #{id} 应该与 item="id" 保持一致，也就是说，item 属性如果把临时变量声明为 uid 的话，那么使用时就必须写成 #{uid}。
```

###### 定义 SQL 片段

- **在上面的例子中，我们在每条 SQL 中都用到了 `SELECT \* FROM user` ，因此，我们可以把该语句定义为 SQL 片段，以供复用，减少工作量。**
- **首先在映射文件中定义代码片段**

```
<!-- 定义代码片段 -->
<sql id="defaultSql">
    SELECT * FROM user
</sql>

```
接着就可以在需要的地方引用该片段了
```
<!-- 根据id集合查询用户 -->
<select id="listUsersByIds" parameterType="cn.ykf.pojo.QueryVo" resultMap="userMap">
    <!-- 引用代码片段 -->
    <include refid="defaultSql"></include>
    <where>
        <if test="ids != null and ids.size > 0">
            <foreach collection="ids" open="AND id IN (" close=")" item="id" separator=",">
                #{id}
            </foreach>
        </if>
    </where>
</select>
```



## 连接池

我们在配置数据源的时候一般都==已经配置好数据库连接池的参数==，然后数据源会自动帮我们维护这些。所有这些对开发者而言是透明的。

在 Mybatis 中，数据源 dataSource 共有三类，分别是：
UNPOOLED ： 不使用连接池的数据源。采用传统的 javax.sql.DataSource 规范中的连接池，Mybatis 中有针对规范的实现
POOLED ： 使用连接池的数据源。采用池的思想
JNDI ： 使用 JNDI 实现的数据源，采用服务器提供的 JNDI 技术实现，来获取 DataSource 对象，不同的服务器所能拿到的 DataSource 是不一样的。（如果不是web或者maven的war工程，是不能使用的，tomcat-dbcp连接池：**因为需要相应的代码需要在服务器端才能启动，连接池连接的数据源是部署在服务器上的，如tomcat你可以在servlet或者jsp（最终还是会转换成servlet）上进行sqlsession的创建才能在jndi上getConnection到相应的connection，通过浏览器或客户端对服务器的访问调用了服务器上部署的有相应需要连接数据库的方法，才能调用服务器连接的数据库源**）

> 连接池和线程池：
>
> 连接池：（降低物理连接损耗）
> 1、连接池是面向数据库连接的
> 2、连接池是为了优化数据库连接资源
> 3、连接池有点类似在客户端做优化
>
> 数据库连接是一项有限的昂贵资源，一个数据库连接对象均对应一个物理数据库连接，每次操作都打开一个物理连接，使用完都关闭连接，这样造成系统的性能低下。 数据库连接池的解决方案是在应用程序启动时建立足够的数据库连接，并将这些连接组成一个连接池，由应用程序动态地对池中的连接进行申请、使用和释放。**对于多于连接池中连接数的并发请求，应该在请求队列中排队等待。并且应用程序可以根据池中连接的使用率，动态增加或减少池中的连接数**。 
>
> 线程池：（降低线程创建销毁损耗）
> 1.、线程池是面向后台程序的
> 2、线程池是是为了提高内存和CPU效率
> 3、线程池有点类似于在服务端做优化
>
> 线程池是一次性创建一定数量的线程（应该可以配置初始线程数量的），当用请求过来不用去创建新的线程，直接使用已创建的线程，使用后又放回到线程池中。
> 避免了频繁创建线程，及销毁线程的系统开销，提高是内存和CPU效率。
>
> 相同点：
> 都是事先准备好资源，避免频繁创建和销毁的代价。
>
> 扩展：
> 对象池：
> 对象池技术基本原理的核心有两点：缓存和共享，即对于那些被频繁使用的对象，在使用完后，不立即将它们释放，而是将它们缓存起来，以供后续的应用程序重复使用，从而减少创建对象和释放对象的次数，进而改善应用程序的性能。事实上，由于对象池技术将对象限制在一定的数量，也有效地减少了应用程序内存上的开销。
>
> 对于两者是否有联系：
>
> 1. 首先，每个连接要启动，都需要依赖于一个线程的调用，否则，即使连接在连接池里，没有断开连接，也无法做出行为
> 2. 当线程使用完时，会将连接与线程解绑交还于连接池
> 3. 一般会是多个连接并发执行，即需要多个线程，因此前面也有线程池的管理
> 4. 每个连接都是线程安全的，用synchronized锁住
>
> ```
> while(conn == null) {
>     synchronized(this.state) {
>         PoolState var10000;
>         if (!this.state.idleConnections.isEmpty()) {
>             conn = (PooledConnection)this.state.idleConnections.remove(0);
>             if (log.isDebugEnabled()) {
>                 log.debug("Checked out connection " + conn.getRealHashCode() + " from pool.");
>             }
>         } else if (this.state.activeConnections.size() < this.poolMaximumActiveConnections) {
>             conn = new PooledConnection(this.dataSource.getConnection(), this);
>             if (log.isDebugEnabled()) {
>                 log.debug("Created connection " + conn.getRealHashCode() + ".");
>             }
>         } else {
>             PooledConnection oldestActiveConnection = (PooledConnection)this.state.activeConnections.get(0);
>             long longestCheckoutTime = oldestActiveConnection.getCheckoutTime();
>             if (longestCheckoutTime > (long)this.poolMaximumCheckoutTime) {
>                 ++this.state.claimedOverdueConnectionCount;
>                 var10000 = this.state;
>                 var10000.accumulatedCheckoutTimeOfOverdueConnections += longestCheckoutTime;
>                 var10000 = this.state;
>                 var10000.accumulatedCheckoutTime += longestCheckoutTime;
>                 this.state.activeConnections.remove(oldestActiveConnection);
>                 if (!oldestActiveConnection.getRealConnection().getAutoCommit()) {
>                     try {
>                         oldestActiveConnection.getRealConnection().rollback();
>                     } catch (SQLException var16) {
>                         log.debug("Bad connection. Could not roll back");
>                     }
>                 }
> 
>                 conn = new PooledConnection(oldestActiveConnection.getRealConnection(), this);
>                 conn.setCreatedTimestamp(oldestActiveConnection.getCreatedTimestamp());
>                 conn.setLastUsedTimestamp(oldestActiveConnection.getLastUsedTimestamp());
>                 oldestActiveConnection.invalidate();
>                 if (log.isDebugEnabled()) {
>                     log.debug("Claimed overdue connection " + conn.getRealHashCode() + ".");
>                 }
>             } else {
>                 try {
>                     if (!countedWait) {
>                         ++this.state.hadToWaitCount;
>                         countedWait = true;
>                     }
> 
>                     if (log.isDebugEnabled()) {
>                         log.debug("Waiting as long as " + this.poolTimeToWait + " milliseconds for connection.");
>                     }
> 
>                     long wt = System.currentTimeMillis();
>                     this.state.wait((long)this.poolTimeToWait);
>                     var10000 = this.state;
>                     var10000.accumulatedWaitTime += System.currentTimeMillis() - wt;
>                 } catch (InterruptedException var17) {
>                     break;
>                 }
>             }
>         }
> ```

- **如果想要修改 Mybatis 使用的数据源，那么就可以在 Mybatis 配置文件中修改：**（这里 `type` 属性的取值就是为`POOLED、UNPOOLED、JNDI` 。）

```
<!-- 配置数据源（连接池） -->
<dataSource type="POOLED">
    <property name="driver" value="${jdbc.driver}"/>
    <property name="url" value="${jdbc.url}"/>
    <property name="username" value="${jdbc.username}"/>
    <property name="password" value="${jdbc.password}"/>
</dataSource>

```

**查看 `POOLED` 的实现 `PooledDataSource` ，可以看出获取连接时采用了池的思想，大概流程如下图（只是一个简单介绍，不全面）**

```java
protected DataSource createDataSource() throws SQLException {
    if (this.closed) {
        throw new SQLException("Data source is closed");
    } else if (this.dataSource != null) {
        return this.dataSource;
    } else {
        synchronized(this) {// 线程安全
            if (this.dataSource != null) {
                return this.dataSource;
            } else {
                this.jmxRegister();
                ConnectionFactory driverConnectionFactory = this.createConnectionFactory();// 线创建数据库的连接工厂
                boolean success = false;
 
                PoolableConnectionFactory poolableConnectionFactory;
                try {
                    poolableConnectionFactory = this.createPoolableConnectionFactory(driverConnectionFactory);
                    poolableConnectionFactory.setPoolStatements(this.poolPreparedStatements);
                    poolableConnectionFactory.setMaxOpenPreparedStatements(this.maxOpenPreparedStatements);
                    success = true;
                } catch (SQLException var21) {
                    throw var21;
                } catch (RuntimeException var22) {
                    throw var22;
                } catch (Exception var23) {
                    throw new SQLException("Error creating connection factory", var23);
                }
 
                if (success) {
                    this.createConnectionPool(poolableConnectionFactory);// 真正的创建连接池的部分
                }
 
                success = false;
 
                DataSource newDataSource;
                try {
                    newDataSource = this.createDataSourceInstance();// 再创建数据源
                    newDataSource.setLogWriter(this.logWriter);
                    success = true;
                } catch (SQLException var18) {
                    throw var18;
                } catch (RuntimeException var19) {
                    throw var19;
                } catch (Exception var20) {
                    throw new SQLException("Error creating datasource", var20);
                } finally {
                    if (!success) {
                        this.closeConnectionPool();// 创建失败，回滚
                    }
                }
 
                try {
                    for(int i = 0; i < this.initialSize; ++i) {
                        this.connectionPool.addObject();// 根据配置，初始化一定数量的数据库连接
                    }
                } catch (Exception var25) {
                    this.closeConnectionPool();
                    throw new SQLException("Error preloading the connection pool", var25);
                }
 
                this.startPoolMaintenance();
                this.dataSource = newDataSource;
                return this.dataSource;
            }
        }
    }
}
```
**查看 `UNPOOLED` 的实现 `UnpooledDataSource` ，可以看出每次获取连接时都会注册驱动并创建新连接，大概流程如下图（只是一个简单介绍，不全面）**

![UnpooledDataSource](https://img-blog.csdnimg.cn/202002031056597.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ExMDkyODgyNTgw,size_16,color_FFFFFF,t_70#pic_center)

## sqlsession和connection

* 一个sqlsession一般对应一个connection，并且mybatis默认每次获取session都会开启一个事务，且不自动提交事务。如果更新操作完成后不手动commit，则在连接断开时会将更新操作回滚，一个sqlSession(一个transaction)中可以多次commit，commit后cache和statement刷新（一般一个事务（transaction）只对应一个sqlseesion，也即一个connection，分布式一个事务可以对应多个connection），只有close后同时关闭sqlsession和transaction（transaction可以由factory关闭）并返回connection。mybatis的transaction基本没添加什么功能，大部分仅状态判断后交由connection（再由jdbc）执行。可能是为了给spring等框架容易组转自己的事务管理机制。
* mybatis一般由一个sqlsession经过getmapper完成对dao的多次调用，之后再commit，这也是一级缓存能运行的原因

#### sqlsession与transaction

**（第一个JdbcTransactionFactory、第二个defaultsqlsessonfactory）：**

用transactionFactory创建相应的transaction，用创建出的tx来创建executor，再用executor和配置信息来创建defaulSqlsession

```

public class JdbcTransactionFactory implements TransactionFactory {
    public JdbcTransactionFactory() {
    }

    public void setProperties(Properties props) {
    }

    public Transaction newTransaction(Connection conn) {
        return new JdbcTransaction(conn);
    }

    public Transaction newTransaction(DataSource ds, TransactionIsolationLevel level, boolean autoCommit) {
        return new JdbcTransaction(ds, level, autoCommit);
    }
}

```

```
public class DefaultSqlSessionFactory implements SqlSessionFactory {

public SqlSession openSession(ExecutorType execType, Connection connection) {
    return this.openSessionFromConnection(execType, connection);
}
 private SqlSession openSessionFromConnection(ExecutorType execType, Connection connection) {//分为两种创建（dataSource、fromConnection），与Spring整合时transactionFactory的实例是SpringtransactionFactory
        DefaultSqlSession var8;
        try {
            boolean autoCommit;
            try {
                autoCommit = connection.getAutoCommit();
            } catch (SQLException var13) {
                autoCommit = true;
            }

            Environment environment = this.configuration.getEnvironment();
            TransactionFactory transactionFactory = this.getTransactionFactoryFromEnvironment(environment);
            Transaction tx = transactionFactory.newTransaction(connection);
            Executor executor = this.configuration.newExecutor(tx, execType);
            var8 = new DefaultSqlSession(this.configuration, executor, autoCommit);
        } catch (Exception var14) {
            throw ExceptionFactory.wrapException("Error opening session.  Cause: " + var14, var14);
        } finally {
            ErrorContext.instance().reset();
        }

        return var8;
    }
 private SqlSession openSessionFromConnection(ExecutorType execType, Connection connection) {
        DefaultSqlSession var8;
        try {
            boolean autoCommit;
            try {
                autoCommit = connection.getAutoCommit();
            } catch (SQLException var13) {
                autoCommit = true;
            }

            Environment environment = this.configuration.getEnvironment();
            TransactionFactory transactionFactory = this.getTransactionFactoryFromEnvironment(environment);
            Transaction tx = transactionFactory.newTransaction(connection);
            Executor executor = this.configuration.newExecutor(tx, execType);
            var8 = new DefaultSqlSession(this.configuration, executor, autoCommit);
        } catch (Exception var14) {
            throw ExceptionFactory.wrapException("Error opening session.  Cause: " + var14, var14);
        } finally {
            ErrorContext.instance().reset();
        }

        return var8;
    }
    
```

#### sqlsession对executor的调用

**（defaultSqlsession）**

  ```
 public class DefaultSqlSession implements SqlSession {

 public <E> List<E> selectList(String statement, Object parameter, RowBounds rowBounds) {
      List var5;
      try {
          MappedStatement ms = this.configuration.getMappedStatement(statement);
          var5 = this.executor.query(ms, this.wrapCollection(parameter), rowBounds, Executor.NO_RESULT_HANDLER);
      } catch (Exception var9) {
          throw ExceptionFactory.wrapException("Error querying database.  Cause: " + var9, var9);
      } finally {
          ErrorContext.instance().reset();
      }
  
      return var5;
  }
  public int update(String statement, Object parameter) {
          int var4;
          try {
              this.dirty = true;
              MappedStatement ms = this.configuration.getMappedStatement(statement);
              var4 = this.executor.update(ms, this.wrapCollection(parameter));
          } catch (Exception var8) {
              throw ExceptionFactory.wrapException("Error updating database.  Cause: " + var8, var8);
          } finally {
              ErrorContext.instance().reset();
          }
  
          return var4;
      }
   public void commit(boolean force) {
          try {
              this.executor.commit(this.isCommitOrRollbackRequired(force));
              this.dirty = false;
          } catch (Exception var6) {
              throw ExceptionFactory.wrapException("Error committing transaction.  Cause: " + var6, var6);
          } finally {
              ErrorContext.instance().reset();
          }
  
      }
      
       public void rollback(boolean force) {
          try {
              this.executor.rollback(this.isCommitOrRollbackRequired(force));
              this.dirty = false;
          } catch (Exception var6) {
              throw ExceptionFactory.wrapException("Error rolling back transaction.  Cause: " + var6, var6);
          } finally {
              ErrorContext.instance().reset();
          }
  
      }
  ```

* sqlsession无论执行sql语句还是对事物的管理，都会转由executor执行

#### executor对sql的执行

* （simpleExecutor  extends baseExecutor）  sqlsession->executor->connection
* 由configuration以及参数生成语句处理对象  handler，再调用preaparement方法对handler进行connection的获取与连接（当然也通过了transaction，用的是父类baseEexcutor的方法）
* 之后将操作都交给了handler，经过实现了statementhandler接口的RoutingStatementHandler->PreparedStatementHandler(extends BaseStatementHandler)之后的preparedstatement等statement后，便是jdbc的excute操作与result的封装
* 一级缓存的发生也在处理后发生

```
public class SimpleExecutor extends BaseExecutor {
    public SimpleExecutor(Configuration configuration, Transaction transaction) {
        super(configuration, transaction);
    }

    public int doUpdate(MappedStatement ms, Object parameter) throws SQLException {
        Statement stmt = null;

        int var6;
        try {
            Configuration configuration = ms.getConfiguration();
            StatementHandler handler = configuration.newStatementHandler(this, ms, parameter, RowBounds.DEFAULT, (ResultHandler)null, (BoundSql)null);
            stmt = this.prepareStatement(handler, ms.getStatementLog());
            var6 = handler.update(stmt);
        } finally {
            this.closeStatement(stmt);
        }

        return var6;
    }

    public <E> List<E> doQuery(MappedStatement ms, Object parameter, RowBounds rowBounds, ResultHandler resultHandler, BoundSql boundSql) throws SQLException {
        Statement stmt = null;

        List var9;
        try {
            Configuration configuration = ms.getConfiguration();
            StatementHandler handler = configuration.newStatementHandler(this.wrapper, ms, parameter, rowBounds, resultHandler, boundSql);
            stmt = this.prepareStatement(handler, ms.getStatementLog());
            var9 = handler.query(stmt, resultHandler);
        } finally {
            this.closeStatement(stmt);
        }

        return var9;
    }
    
     private Statement prepareStatement(StatementHandler handler, Log statementLog) throws SQLException {
        Connection connection = this.getConnection(statementLog);
        Statement stmt = handler.prepare(connection, this.transaction.getTimeout());
        handler.parameterize(stmt);
        return stmt;
    }
```

```
public abstract class BaseExecutor implements Executor {

protected Connection getConnection(Log statementLog) throws SQLException {
    Connection connection = this.transaction.getConnection();
    return statementLog.isDebugEnabled() ? ConnectionLogger.newInstance(connection, statementLog, this.queryStack) : connection;
}
}
```

#### executor对事务的处理

* 由simpleExecutor对象处理，但用的方法都继承自baseExecutor
* sqlsession(带着在sqlsessionfactory创建的transaction)->executor(transaction)->transaction.commit/close·····
* executor 将事务管理交给了 transaction，commit/rollback等对cache和statement的清空（clearLocalCache，flushStatements）也在这里开始进行。
* **在sqlsession的close前，对sql语句的执行都会用getConnection创建的连接进行sql语句的执行，commit也并不会说新建一个事务（transaction），而是清空cache和statement并提交jdbc执行后的结果到mysql。此时仍可以用commit后的sqlsession和transaction进行getMapper代理并调用接口方法，只有close后两者才会消失**

```
public class SimpleExecutor extends BaseExecutor {

public Transaction getTransaction() {
    if (this.closed) {
        throw new ExecutorException("Executor was closed.");
    } else {
        return this.transaction;
    }
}

public void close(boolean forceRollback) {
    try {
        try {
            this.rollback(forceRollback);
        } finally {
            if (this.transaction != null) {
                this.transaction.close();
            }

        }
    } catch (SQLException var11) {
        log.warn("Unexpected exception on closing transaction.  Cause: " + var11);
    } finally {
        this.transaction = null;
        this.deferredLoads = null;
        this.localCache = null;
        this.localOutputParameterCache = null;
        this.closed = true;
    }

}
public void commit(boolean required) throws SQLException {
        if (this.closed) {
            throw new ExecutorException("Cannot commit, transaction is already closed");
        } else {
            this.clearLocalCache();
            this.flushStatements();
            if (required) {
                this.transaction.commit();
            }

        }
    }
 public void rollback(boolean required) throws SQLException {
        if (!this.closed) {
            try {
                this.clearLocalCache();
                this.flushStatements(true);
            } finally {
                if (required) {
                    this.transaction.rollback();
                }

            }
        }

    }

public void clearLocalCache() {
        if (!this.closed) {
            this.localCache.clear();
            this.localOutputParameterCache.clear();
        }

    }
```

#### transaction到connection

* JdbcTransaction
* 由transaction对与connection 的commit/colse进行管理。

```java
public class JdbcTransaction implements Transaction {


public JdbcTransaction(Connection connection) {
    this.connection = connection;
}

public Connection getConnection() throws SQLException {
    if (this.connection == null) {
        this.openConnection();
    }

    return this.connection;
}

public void commit() throws SQLException {
    if (this.connection != null && !this.connection.getAutoCommit()) {
        if (log.isDebugEnabled()) {
            log.debug("Committing JDBC Connection [" + this.connection + "]");
        }

        this.connection.commit();
    }

}

public void rollback() throws SQLException {
    if (this.connection != null && !this.connection.getAutoCommit()) {
        if (log.isDebugEnabled()) {
            log.debug("Rolling back JDBC Connection [" + this.connection + "]");
        }

        this.connection.rollback();
    }

}

public void close() throws SQLException {
    if (this.connection != null) {
        this.resetAutoCommit();
        if (log.isDebugEnabled()) {
            log.debug("Closing JDBC Connection [" + this.connection + "]");
        }

        this.connection.close();
    }

}
}
```

#### connection

* connectionImp类
* 从这开始便是com.mysql.jdbc包的东西了，就是与数据库直接进行交接的部分了

```java
public class ConnectionImpl extends ConnectionPropertiesImpl implements Connection {

public void commit() throws SQLException {
    synchronized(this.getMutex()) {
        this.checkClosed();

        try {
            if (this.connectionLifecycleInterceptors != null) {
                IterateBlock iter = new IterateBlock(this.connectionLifecycleInterceptors.iterator()) {
                    void forEach(Object each) throws SQLException {
                        if (!((ConnectionLifecycleInterceptor)each).commit()) {
                            this.stopIterating = true;
                        }

                    }
                };
                iter.doForAll();
                if (!iter.fullIteration()) {
                    return;
                }
            }

            if (this.autoCommit && !this.getRelaxAutoCommit()) {
                throw SQLError.createSQLException("Can't call commit when autocommit=true");
            }

            if (this.transactionsSupported) {
                if (this.getUseLocalSessionState() && this.versionMeetsMinimum(5, 0, 0) && !this.io.inTransactionOnServer()) {
                    return;
                }

                this.execSQL((StatementImpl)null, "commit", -1, (Buffer)null, 1003, 1007, false, this.database, (Field[])null, false);
            }
        } catch (SQLException var8) {
            if ("08S01".equals(var8.getSQLState())) {
                throw SQLError.createSQLException("Communications link failure during commit(). Transaction resolution unknown.", "08007");
            }

            throw var8;
        } finally {
            this.needsPing = this.getReconnectAtTxEnd();
        }

    }
}
}
```








![在这里插入图片描述](https://img-blog.csdnimg.cn/20200621235803927.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjQ5MDM4Mw==,size_16,color_FFFFFF,t_70)

## 多表查询

1. 新建一个接收多表消息的实体类

2. 一对一：建立两者的表，在**一**中的实体类添加**一**的类为成员变量，使用resultmap映射。

   一对多： 建立两者的表，实体类上转换成相应的一对多的关系，在**一**中的实体类添加**多**的类为成员变量，使用resultmap映射。
   
   多对多： 建立两者的表并建立中间表，实体类(与一对多一致)转换成相应的一对多的关系，在**一**中的实体类添加**多**的类为成员变量，使用resultmap映射(与一对多一致)，中间表被sql语句中用来与其他sql关键字和函数进行表的拼接
   
   

###### 一对一

**首先编写 SQL 语句，如果想要达到上述效果，那么 SQL 语句可以这样写：**

```
SELECT U.*, a.id AS aid, a.uid, a.money from account a, user u WHERE a.uid = u.id;
```

- **为了可以接收这条语句的查询结果，那么我们应该对账户实体类 `Account` 进行修改，为其增加成员变量 `User` 来接收查询出的用户信息（从表实体应该包含一个主表实体的对象引用）：**

```
public class Account implements Serializable {
    // 其他成员不展示...
    
    /**
     * 账户对应的用户信息,从表实体应该包含一个主表实体的对象引用
     */
    private User user;

    public User getUser() {
        return user;
    }

    public void setUser(User user) {
        this.user = user;
    }
    
    // 其他setter()/getter()不展示...
    
    @Override
    public String toString() {
        return "Account{" +
                "id=" + id +
                ", uid=" + uid +
                ", money=" + money +
                ", user=" + user +
                '}';
    }
```
* 因为此时账户实体类 Account 已经发生了变化，所以我们应该在映射文件定义用于封装带有 User 信息的 Account 的映射集合resultMap

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="cn.ykf.mapper.AccountMapper">
    <!-- 定义可以封装带有User的Account的 resultMap -->
    <resultMap id="AccountWithUserMap" type="cn.ykf.pojo.Account">
        <id property="id" column="aid"/>
        <result property="uid" column="uid"/>
        <result property="money" column="money"/>
        <!-- 关联 User 对象 -->
        <association property="user" javaType="cn.ykf.pojo.User">
            <id property="id" column="id"/>
            <result property="username" column="username"/>
            <result property="sex" column="sex"/>
            <result property="birthday" column="birthday"/>
            <result property="address" column="address"/>
        </association>
    </resultMap>
    <!-- 配置查询所有账户，带有用户信息 -->
    <select id="listAllAccounts" resultMap="AccountWithUserMap">
        SELECT U.*, a.id AS aid, a.uid, a.money from account a, user u WHERE a.uid = u.id
    </select>
</mapper>
<association></association> 用于一对一映射，其中的 property 属性表示要关联的属性，javaType 表示待关联的实体类的全限定类名。
注意，因为 SQL 语句中为 account 表的 id 字段起了别名 aid ，所以在定义 resultMap 的时候，记得主字段写 column="aid"，而不是 column="id"。
```

###### 一对多

- **我们应该采用外连接的方式，使得没有账户的用户也可以查询出来：**

```
SELECT u.*, a.id AS aid, a.uid, a.money FROM user u LEFT OUTER JOIN account a ON u.id = a.uid;
```

- **为了可以接收这条语句的查询结果，那么我们应该对用户实体类 `User` 进行修改，为其增加成员变量 `accounts` 来接收查询出的账户信息（主表实体应该包含从表实体的集合引用）：**

```java
public class User implements Serializable {
    // 其他成员省略不展示...

    /**
     * 一对多关系映射：主表实体应该包含从表实体的集合引用
     */
    private List<Account> accounts;
    
    public List<Account> getAccounts() {
        return accounts;
    }

    public void setAccounts(List<Account> accounts) {
        this.accounts = accounts;
    }

	// 其他setter()/getter()省略不展示...

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", username='" + username + '\'' +
                ", birthday=" + birthday +
                ", sex='" + sex + '\'' +
                ", address='" + address + '\'' +
                ", accounts=" + accounts +
                '}';
    }
```

- **修改映射文件**

```
<mapper namespace="cn.ykf.mapper.UserMapper">
    <!-- 配置 resultMap ，完成实体类与数据库表的映射 -->
    <resultMap id="UserWithAccountsMap" type="cn.ykf.pojo.User">
        <id property="id" column="id"/>
        <result property="username" column="username"/>
        <result property="sex" column="sex"/>
        <result property="birthday" column="birthday"/>
        <result property="address" column="address"/>
        <!-- 配置user对象中accounts集合的映射 -->
        <collection property="accounts" ofType="cn.ykf.pojo.Account">
            <id property="id" column="aid"/>
            <result property="uid" column="uid"/>
            <result property="money" column="money"/>
        </collection>
    </resultMap>
    <!-- 配置查询所有用户，并且带有账户信息 -->
    <select id="listAllUsers" resultMap="UserWithAccountsMap">
        SELECT u.*, a.id AS aid, a.uid, a.money FROM user u LEFT OUTER JOIN account a ON u.id = a.uid;
    </select>
</mapper>
<collection ></collection > 用于一对多映射，其中的 property 属性表示要关联的集合，ofType 表示集合中的实体类的全限定类名。

```

###### 多对多

- **修改用户实体类 `User` ，并添加角色实体类 `Role`，为了能体现出多对多的关系，两个实体类都必须包含对方的一个集合引用（多对多关系其实我们看成是双向的一对多关系）**

```java
public class User implements Serializable {
    /**
     * 多对多关系映射：包含对方的集合引用
     */
    private List<Role> roles;

    public List<Role> getRoles() {
        return roles;
    }

    public void setRoles(List<Role> roles) {
        this.roles = roles;
    }
    
    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", username='" + username + '\'' +
                ", birthday=" + birthday +
                ", sex='" + sex + '\'' +
                ", address='" + address + '\'' +
                ", accounts=" + accounts +
                ", roles=" + roles +
                '}';
       }
```

```
public class Role implements Serializable {
    private Integer roleId;
    private String roleName;
    private String roleDesc;
    /**
     * 多对多关系映射：持有对方集合引用
     */
    private List<User> users;

    public List<User> getUsers() {
        return users;
    }

    public void setUsers(List<User> users) {
        this.users = users;
    }

    public Integer getRoleId() {
        return roleId;
    }

    public void setRoleId(Integer roleId) {
        this.roleId = roleId;
    }

    public String getRoleName() {
        return roleName;
    }

    public void setRoleName(String roleName) {
        this.roleName = roleName;
    }

    public String getRoleDesc() {
        return roleDesc;
    }

    public void setRoleDesc(String roleDesc) {
        this.roleDesc = roleDesc;
    }

    @Override
    public String toString() {
        return "Role{" +
                "roleId=" + roleId +
                ", roleName='" + roleName + '\'' +
                ", roleDesc='" + roleDesc + '\'' +
                ", users=" + users +
                '}';
    }
}
```

- **为了确保所有用户（无论是否具有角色）都可以被查询出来，应该使用左连接**

```SELECT u.*, r.id as rid, r.role_name, r.role_desc FROM user u
SELECT u.*, r.id as rid, r.role_name, r.role_desc FROM user u
	LEFT OUTER JOIN user_role ur ON u.id = ur.uid
	LEFT OUTER JOIN role r ON ur.rid = r.id;

```

- **开始编写接口层和映射文件**

```
/**
 * 查询所有用户信息，包含用户所拥有的角色信息
 * @return
 */
List<User> listUsersWithRoles();
```

```
<mapper namespace="cn.ykf.mapper.UserMapper">
    <resultMap id="UserWithRolesMap" type="cn.ykf.pojo.User">
        <id property="id" column="id"/>
        <result property="username" column="username"/>
        <result property="sex" column="sex"/>
        <result property="birthday" column="birthday"/>
        <result property="address" column="address"/>
        <!-- 配置user对象中roles集合的映射 -->
        <collection property="roles" ofType="cn.ykf.pojo.Role">
            <id property="roleId" column="rid"/>
            <result property="roleName" column="role_name"/>
            <result property="roleDesc" column="role_desc"/>
        </collection>
    </resultMap>
    <!-- 查询所有用户，并且带有角色信息 -->
    <select id="listUsersWithRoles" resultMap="UserWithRolesMap">
        SELECT u.*, r.id as rid, r.role_name, r.role_desc FROM user u
            LEFT OUTER JOIN user_role ur ON u.id = ur.uid
            LEFT OUTER JOIN role r ON ur.rid = r.id
    </select>
</mapper>
```

## 加载

###  延迟加载和立即加载

延迟加载针对级联使用的，延迟加载的目的是减少内存的浪费和减轻系统负担。



**延迟加载需要的配置：**

| 设置参数               | 描述                                                         | 有效值                 | 默认值                         |
| ---------------------- | ------------------------------------------------------------ | ---------------------- | ------------------------------ |
| lazyLoadingEnabled     | 延迟加载的全局开关。当开启时，所有关联对象都会延迟加载。 特定关联关系中可通过设置fetchType属性来覆盖该项的开关状态。 | true、false            | false                          |
| aggressiveLazyLoading  | 当开启时，任何方法的调用都会加载该对象的所有属性。否则，每个属性会按需加载（参考lazyLoadTriggerMethods). | true、false            | false (true in ≤3.4.1)         |
| lazyLoadTriggerMethods | 指定哪个对象的方法触发一次全部属性加载。                     | 用逗号分隔的方法列表。 | equals,clone,hashCode,toString |

```xml
<configuration>
    <settings>
        <!-- 开启延迟加载 -->
        <setting name="lazyLoadingEnabled" value="true" />
        <setting name="aggressiveLazyLoading" value="false" />
        <setting name="lazyLoadTriggerMethods" value="equals,clone,hashCode,toString" />
      </settings>
</configuration>

```

**开启懒加载一定要两个属性都配置吗？**
不是的，真正开启懒加载标签的只是lazyLoadingEnabled：true表示开启，false表示关闭，默认为false。
**如果我不想将全部的级联都设计懒加载怎么办？**
association和collection有个fetchType属性可以覆盖全局的懒加载状态：eager表示这个级联不使用懒加载要立即加载，lazy表示使用懒加载。

**既然fetchType可以控制懒加载那么我仅仅配置fetchType不配置全局的可以吗？**
可以的。
**aggressiveLazyLoading是做什么么的？**
它是控制具有懒加载特性的对象的属性的加载情况的。(懒加载对象在被调用方法或属性时，)
true表示如果对具有懒加载特性的对象的任意调用会导致这个**对象的完整加载**，false表示每种属性按照需要加载。

**如果我仅仅使用aggressiveLazyLoading不使用lazyLoadingEnabled还有能按需加载吗？即调用getName不执行role的sql语句，就像第一种情况那样**
如果不开启懒加载，在执行user的sql语句时也将执行role的sql语句。因此不能实现需求。

```xml
<mapper namespace="org.apache.ibatis.submitted.lazy_properties.Mapper">
  
  <resultMap type="org.apache.ibatis.submitted.lazy_properties.User"
    id="user">
    <id property="id" column="id" />
    <result property="name" column="name" />
  </resultMap>
  <!-- 结果对象 -->
  <resultMap type="org.apache.ibatis.submitted.lazy_properties.User" id="userWithLazyProperties" extends="user">
    <!-- 延迟加载对象lazy1 -->
    <association property="lazy1" column="id" select="getLazy1" fetchType="lazy" />
    <!-- 延迟加载对象lazy2 -->
    <association property="lazy2" column="id" select="getLazy2" fetchType="lazy" />
    <!-- 延迟加载集合lazy3 --> 
    <collection property="lazy3" column="id" select="getLazy3" fetchType="lazy" />
  </resultMap>
  <!-- 执行的查询 -->
  <select id="getUser" resultMap="userWithLazyProperties">
    select * from users where id = #{id}
  </select>
</mapper>
```

> 1. 调用getUser查询数据，从查询结果集解析数据到User对象，当数据解析到lazy1，lazy2，lazy3判断需要执行关联查询
> 2. lazyLoadingEnabled=true，将创建lazy1，lazy2，lazy3对应的Proxy延迟执行对象lazyLoader，并保存
> 3. 当逻辑触发lazyLoadTriggerMethods 对应的方法（equals,clone,hashCode,toString）则执行全部属性加载
> 4. 如果aggressiveLazyLoading=true，只要触发到对象任何的方法，就会立即加载所有属性的加载

 ### 实现延迟加载(一对一、一对多)

- 实现以下需求：
  - 当查询账户信息时使用延迟加载。也就是说，如果不需要使用用户信息的话，那么只查询账户信息；只有当需要使用用户信息时，才去关联查询
- dao：

```java
package com.itheima.dao;

import com.itheima.doamin.Account;
import com.itheima.doamin.User;

import java.util.List;

public interface IAccountDao {
    List<Account> findAll();

    List<Account> selectUserById (Integer uid);
}
```

```java
package com.itheima.dao;

import com.itheima.doamin.User;
import sun.nio.cs.US_ASCII;

import javax.jws.soap.SOAPBinding;
import java.util.List;

public interface IUserDao {
    List<User> findAll();

    User findById(Integer userId);


}
```

- domain:

  ```java
  package com.itheima.doamin;
  
  import javax.jws.soap.SOAPBinding;
  import java.io.Serializable;
  
  public class Account implements Serializable {
      private Integer id;
      private Integer uid;
      private Double money;
  
      private User user;
  
      public User getUser() {
          return user;
      }
  
      public void setUser(User user) {
          this.user = user;
      }
  
      public Integer getId() {
          return id;
      }
  
      public void setId(Integer id) {
          this.id = id;
      }
  
      public Integer getUid() {
          return uid;
      }
  
      public void setUid(Integer uid) {
          this.uid = uid;
      }
  
      public Double getMoney() {
          return money;
      }
  
      public void setMoney(Double money) {
          this.money = money;
      }
  
      @Override
      public String toString() {
          return "Account{" +
                  "id=" + id +
                  ", uid=" + uid +
                  ", money=" + money +
                  '}';
      }
  }
  ```

  ```java
  package com.itheima.doamin;
  
  import java.io.Serializable;
  import java.util.Date;
  import java.util.List;
  
  public class User implements Serializable {
      private Integer id;
      private String username;
      private String address;
      private String sex;
      private Date birthday;
  
      private List<Account>  accounts;
  
      public List<Account> getAccounts() {
          return accounts;
      }
  
      public void setAccounts(List<Account> accounts) {
          this.accounts = accounts;
      }
  
      public Integer getId() {
          return id;
      }
  
      public String getUsername() {
          return username;
      }
  
      public String getAddress() {
          return address;
      }
  
      public String getSex() {
          return sex;
      }
  
      public Date getBirthday() {
          return birthday;
      }
  
      public void setId(Integer id) {
          this.id = id;
      }
  
      public void setUsername(String username) {
          this.username = username;
      }
  
      public void setAddress(String address) {
          this.address = address;
      }
  
      public void setSex(String sex) {
          this.sex = sex;
      }
  
      public void setBirthday(Date birthday) {
          this.birthday = birthday;
      }
  
      @Override
      public String toString() {
          return "User{" +
                  "id=" + id +
                  ", username='" + username + '\'' +
                  ", address='" + address + '\'' +
                  ", sex='" + sex + '\'' +
                  ", birthday=" + birthday +
                  '}';
      }
  }
  ```

- 配置xml：

  IAccountDao.xml：

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE mapper
          PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
  <mapper namespace="com.itheima.dao.IAccountDao">
      <!-- 定义可以封装带有User的Account的 resultMap -->
      <resultMap id="AccountWithUserMap" type="Account">
          <id property="id" column="aid"/>
          <result property="uid" column="uid"/>
          <result property="money" column="money"/>
          <!-- 关联 User 对象 -->
          <association property="user"  javaType="User" column="uid" select="com.itheima.dao.IUserDao.findById">
          </association>
      </resultMap>
      <!-- 配置查询所有账户，带有用户信息 -->
      <select id="findAll" resultMap="AccountWithUserMap">
          SELECT * from account;
      </select>
  
      <select id="selectUserById" parameterType="int" resultType="com.itheima.doamin.Account">
          SELECT * from account where uid =  #{uid};
      </select>
  </mapper>
  ```
  IAccountDao.xml：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.itheima.dao.IUserDao">
    <resultMap id="userAccountMap" type="user">
        <id property="id" column="id"></id>
        <result property="username" column="username"></result>
        <result property="address" column="address"></result>
        <result property="sex" column="sex"></result>
        <result property="birthday" column="birthday"></result>
        <collection property="accounts" ofType="Account" column="id" select="com.itheima.dao.IAccountDao.selectUserById">  <!--JavaType和ofType都是用来指定对象类型的，但是JavaType是用来指定pojo中属性的类型，而ofType指定的是映射到list集合属性中pojo的类型。-->
        </collection>

    </resultMap>
    <select id="findAll" resultMap="userAccountMap">
        SELECT  * from user;
    </select>
      
    <select id="findById" parameterType="int"  resultType="User">
        select * from user where  id = #{id}
    </select>



</mapper>
```

test:

```java
package com.itheima.test;

import com.itheima.dao.IAccountDao;
import com.itheima.dao.IUserDao;
import com.itheima.doamin.Account;
import com.itheima.doamin.User;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.After;
import org.junit.Before;
import org.junit.Test;


import java.io.InputStream;
import java.util.List;

public class AccountTest {

    private InputStream in;
    private SqlSession sqlSession;
    private IAccountDao accountDao;

    @Before
    public void init()throws  Exception{
        in = Resources.getResourceAsStream("SqlMapConfig.xml");
        SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(in);
        sqlSession = factory.openSession();
        accountDao = sqlSession.getMapper(IAccountDao.class);
    }

    @After
    public void destory() throws  Exception{
        sqlSession.commit();
        sqlSession.close();
        in.close();
    }

    @Test
    public void testFindAll(){
        List<Account> accounts = accountDao.findAll();
//        for(Account account:accounts){
//
//            System.out.println(account);
//            System.out.println(account.getUser());
//        }
    }
}
```

```java
package com.itheima.test;

import com.itheima.dao.IAccountDao;
import com.itheima.dao.IUserDao;
import com.itheima.doamin.Account;
import com.itheima.doamin.User;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.After;
import org.junit.Before;
import org.junit.Test;

import java.io.InputStream;
import java.util.List;

public class UserTest {
    private InputStream in;
    private SqlSession sqlSession;
    private IUserDao userDao;

    @Before
    public void init()throws  Exception{
        in = Resources.getResourceAsStream("SqlMapConfig.xml");
        SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(in);
        sqlSession = factory.openSession();
        userDao = sqlSession.getMapper(IUserDao.class);
    }

    @After
    public void destory() throws  Exception{
        sqlSession.commit();
        sqlSession.close();
        in.close();
    }

    @Test
    public void testFindAll(){
        List<User> users = userDao.findAll();
        for(User user:users){
            System.out.println(user);
            System.out.println(user.getAccounts());
        }
    }

}
```

### 延迟加载原理

Mybatis 仅支持 association 关联对象和 collection 关联集合对象的延迟加载，association 指的就是一对一，collection 指的就是一对多查询。在 Mybatis配置文件中，可以配置是否启用延迟加载 lazyLoadingEnabled=true|false。它的原理是，使用 CGLIB 创建目标对象的代理对象，当调用目标方法时，进入拦截器方法，比如调用 a.getB().getName()，拦截器 invoke()方法发现 a.getB()是null 值，那么就会单独发送事先保存好的查询关联 B 对象的 sql，把 B 查询上来，然后调用 a.setB(b)，于是 a 的对象 b 属性就有值了，接着完成 a.getB().getName()方法的调用。这就是延迟加载的基本原理。当然了，不光是 Mybatis，几乎所有的包括 Hibernate，支持延迟加载的原理都是一样的。

![image-20201116085012615](F:\Typora数据储存\数据库\mybatis.assets\image-20201116085012615.png)

###  延迟加载源码解析

#### Setting 配置加载：

```Java
public class Configuration {
   /** aggressiveLazyLoading：
   * 当开启时，任何方法的调用都会加载该对象的所有属性。否则，每个属性会按需加载（参考lazyLoadTriggerMethods).
   * 默认为true
   * */
  protected boolean aggressiveLazyLoading;
  /**
   * 延迟加载触发方法
   */
  protected Set<String> lazyLoadTriggerMethods = new HashSet<String>(Arrays.asList(new String[] { "equals", "clone", "hashCode", "toString" }));
  /** 是否开启延迟加载 */
  protected boolean lazyLoadingEnabled = false;
  
  /**
   * 默认使用Javassist代理工厂
   * @param proxyFactory
   */
  public void setProxyFactory(ProxyFactory proxyFactory) {
    if (proxyFactory == null) {
      proxyFactory = new JavassistProxyFactory();
    }
    this.proxyFactory = proxyFactory;
  }
  
  //省略...
}
```

#### 延迟加载代理对象创建

DefaultResultSetHandler

```Java
//#mark 创建结果对象
  private Object createResultObject(ResultSetWrapper rsw, ResultMap resultMap, ResultLoaderMap lazyLoader, String columnPrefix) throws SQLException {
    this.useConstructorMappings = false; // reset previous mapping result
    final List<Class<?>> constructorArgTypes = new ArrayList<Class<?>>();
    final List<Object> constructorArgs = new ArrayList<Object>();
    //#mark 创建返回的结果映射的真实对象
    Object resultObject = createResultObject(rsw, resultMap, constructorArgTypes, constructorArgs, columnPrefix);
    if (resultObject != null && !hasTypeHandlerForResultObject(rsw, resultMap.getType())) {
      final List<ResultMapping> propertyMappings = resultMap.getPropertyResultMappings();
      for (ResultMapping propertyMapping : propertyMappings) {
        // issue gcode #109 && issue #149 判断属性有没配置嵌套查询，如果有就创建代理对象
        if (propertyMapping.getNestedQueryId() != null && propertyMapping.isLazy()) {
          //#mark 创建延迟加载代理对象
          resultObject = configuration.getProxyFactory().createProxy(resultObject, lazyLoader, configuration, objectFactory, constructorArgTypes, constructorArgs);
          break;
        }
      }
    }
    this.useConstructorMappings = resultObject != null && !constructorArgTypes.isEmpty(); // set current mapping result
    return resultObject;
  }
```

#### 代理功能实现

> 由于Javasisst和Cglib的代理实现基本相同，这里主要介绍Javasisst

ProxyFactory接口定义

```Java
public interface ProxyFactory {

  void setProperties(Properties properties);

  /**
   * 创建代理
   * @param target 目标结果对象
   * @param lazyLoader 延迟加载对象
   * @param configuration 配置
   * @param objectFactory 对象工厂
   * @param constructorArgTypes 构造参数类型
   * @param constructorArgs 构造参数值
   * @return
   */
  Object createProxy(Object target, ResultLoaderMap lazyLoader, Configuration configuration, ObjectFactory objectFactory, List<Class<?>> constructorArgTypes, List<Object> constructorArgs);
}
```

JavasisstProxyFactory实现

```Java
public class JavassistProxyFactory implements org.apache.ibatis.executor.loader.ProxyFactory {

    
    /**
   * 接口实现
   * @param target 目标结果对象
   * @param lazyLoader 延迟加载对象
   * @param configuration 配置
   * @param objectFactory 对象工厂
   * @param constructorArgTypes 构造参数类型
   * @param constructorArgs 构造参数值
   * @return
   */
  @Override
  public Object createProxy(Object target, ResultLoaderMap lazyLoader, Configuration configuration, ObjectFactory objectFactory, List<Class<?>> constructorArgTypes, List<Object> constructorArgs) {
    return EnhancedResultObjectProxyImpl.createProxy(target, lazyLoader, configuration, objectFactory, constructorArgTypes, constructorArgs);
  }

    //省略...
    
  /**
   * 代理对象实现，核心逻辑执行
   */
  private static class EnhancedResultObjectProxyImpl implements MethodHandler {
  
    /**
   * 创建代理对象
   * @param type
   * @param callback
   * @param constructorArgTypes
   * @param constructorArgs
   * @return
   */
  static Object crateProxy(Class<?> type, MethodHandler callback, List<Class<?>> constructorArgTypes, List<Object> constructorArgs) {

    ProxyFactory enhancer = new ProxyFactory();
    enhancer.setSuperclass(type);

    try {
      //通过获取对象方法，判断是否存在该方法
      type.getDeclaredMethod(WRITE_REPLACE_METHOD);
      // ObjectOutputStream will call writeReplace of objects returned by writeReplace
      if (log.isDebugEnabled()) {
        log.debug(WRITE_REPLACE_METHOD + " method was found on bean " + type + ", make sure it returns this");
      }
    } catch (NoSuchMethodException e) {
      //没找到该方法，实现接口
      enhancer.setInterfaces(new Class[]{WriteReplaceInterface.class});
    } catch (SecurityException e) {
      // nothing to do here
    }

    Object enhanced;
    Class<?>[] typesArray = constructorArgTypes.toArray(new Class[constructorArgTypes.size()]);
    Object[] valuesArray = constructorArgs.toArray(new Object[constructorArgs.size()]);
    try {
      //创建新的代理对象
      enhanced = enhancer.create(typesArray, valuesArray);
    } catch (Exception e) {
      throw new ExecutorException("Error creating lazy proxy.  Cause: " + e, e);
    }
    //设置代理执行器
    ((Proxy) enhanced).setHandler(callback);
    return enhanced;
  }
  
  
     /**
     * 代理对象执行
     * @param enhanced 原对象
     * @param method 原对象方法
     * @param methodProxy 代理方法
     * @param args 方法参数
     * @return
     * @throws Throwable
     */
    @Override
    public Object invoke(Object enhanced, Method method, Method methodProxy, Object[] args) throws Throwable {
      final String methodName = method.getName();
      try {
        synchronized (lazyLoader) {
          if (WRITE_REPLACE_METHOD.equals(methodName)) { 
            //忽略暂未找到具体作用
            Object original;
            if (constructorArgTypes.isEmpty()) {
              original = objectFactory.create(type);
            } else {
              original = objectFactory.create(type, constructorArgTypes, constructorArgs);
            }
            PropertyCopier.copyBeanProperties(type, enhanced, original);
            if (lazyLoader.size() > 0) {
              return new JavassistSerialStateHolder(original, lazyLoader.getProperties(), objectFactory, constructorArgTypes, constructorArgs);
            } else {
              return original;
            }
          } else {
              //延迟加载数量大于0
            if (lazyLoader.size() > 0 && !FINALIZE_METHOD.equals(methodName)) {
                //aggressive 一次加载性所有需要要延迟加载属性或者包含触发延迟对象全部加载方法
              if (aggressive || lazyLoadTriggerMethods.contains(methodName)) {
                log.debug("==> laze lod trigger method:" + methodName + ",proxy method:" + methodProxy.getName() + " class:" + enhanced.getClass());
                //一次全部加载
                lazyLoader.loadAll();
              } else if (PropertyNamer.isSetter(methodName)) {
                //判断是否为set方法，set方法不需要延迟加载
                final String property = PropertyNamer.methodToProperty(methodName);
                lazyLoader.remove(property);
              } else if (PropertyNamer.isGetter(methodName)) {
                final String property = PropertyNamer.methodToProperty(methodName);
                if (lazyLoader.hasLoader(property)) {
                  //延迟加载单个属性
                  lazyLoader.load(property);
                  log.debug("load one :" + methodName);
                }
              }
            }
          }
        }
        return methodProxy.invoke(enhanced, args);
      } catch (Throwable t) {
        throw ExceptionUtil.unwrapThrowable(t);
      }
    }
  }
```

## 缓存

* 什么是缓存？
  缓存就是存在于内存中的临时数据。
* 为什么要使用缓存？
  为了减少和数据库交互的次数，提高执行效率。
* 适用于缓存的数据
  经常查询并且不经常改变的数据。
  数据的正确与否对最终结果影响不大的。
* 不适用于缓存的数据
  经常改变的数据。
  数据的正确与否对最终结果影响很大的。例如：商品的库存、银行的汇率、股市的牌价等。

#### 一级缓存

一级缓存是 **SqlSession** 级别的缓存，只要 SqlSession 没有 **flush** 或 **close**，它就会存在。当调用 SqlSession 的**修改、添加、删除、commit()、close()、clearCache()** 等方法时，就会清空一级缓存。

一级缓存流程如下图：

![一级缓存分析](https://img-blog.csdnimg.cn/20200206103422757.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ExMDkyODgyNTgw,size_16,color_FFFFFF,t_70#pic_center)

* 第一次发起查询用户 id 为 1 的用户信息，Mybatis 会先去找缓存中是否有 id 为 1 的用户信息，如果没有，从数据库查询用户信息。
  得到用户信息，将用户信息存储到一级缓存中。
* 如果 sqlSession 去执行 commit 操作（执行插入、更新、删除），那么 Mybatis 就会清空 SqlSession 中的一级缓存，这样做的目的为了让缓存中存储的是最新的信息，避免脏读。
* 第二次发起查询用户 id 为 1 的用户信息，先去找缓存中是否有 id 为 1 的用户信息，缓存中有，直接从缓存中获取用户信息。

> Mybatis 默认就是使用一次缓存的，不需要配置。
> **一级缓存中存放的是对象。**（一级缓存其实就是 Map 结构，直接存放对象） 
>
> mybatis一级缓存，缓存在SqlSession中。
>
> 一级缓存是默认的，不需要配置，localCache中没有缓存时，才去执行queryFromDatabase方法，去查询数据库，并将结果缓存到localCache中。
>
> MyBatis的一级缓存最大范围是SqlSession内部，有多个SqlSession或者分布式的环境下，数据库写操作会引起脏数据，建议设定缓存级别为Statement

###### 源码

```java
public abstract class BaseExecutor implements Executor {

public <E> List<E> query(MappedStatement ms, Object parameter, RowBounds rowBounds, ResultHandler resultHandler, CacheKey key, BoundSql boundSql) throws SQLException {
    ErrorContext.instance().resource(ms.getResource()).activity("executing a query").object(ms.getId());
    if (this.closed) {
        throw new ExecutorException("Executor was closed.");
    } else {
        if (this.queryStack == 0 && ms.isFlushCacheRequired()) {
            this.clearLocalCache();
        }

        List list;
        try {
            ++this.queryStack;
            
            //在缓存中查找是否有
            list = resultHandler == null ? (List)this.localCache.getObject(key) : null;
            if (list != null) {
                this.handleLocallyCachedOutputParameters(ms, key, parameter, boundSql);
            } else {
                list = this.queryFromDatabase(ms, parameter, rowBounds, resultHandler, key, boundSql);
            }
        } finally {
            --this.queryStack;
        }

        if (this.queryStack == 0) {
            Iterator var8 = this.deferredLoads.iterator();

            while(var8.hasNext()) {
                BaseExecutor.DeferredLoad deferredLoad = (BaseExecutor.DeferredLoad)var8.next();
                deferredLoad.load();
            }

            this.deferredLoads.clear();
            if (this.configuration.getLocalCacheScope() == LocalCacheScope.STATEMENT) {
                this.clearLocalCache();
            }
        }

        return list;
    }
}
```

#### 二级缓存

* 二级缓存是 **Mapper** 映射级别的缓存，多个 SqlSession 去操作同一个 Mapper 映射的 SQL 语句，多个SqlSession 可以共用二级缓存，**二级缓存是跨 SqlSession 的**。

二级缓存流程如下图：

![img](https://img-blog.csdnimg.cn/20200206103408586.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ExMDkyODgyNTgw,size_16,color_FFFFFF,t_70#pic_center)

![img](https://img2018.cnblogs.com/blog/1383365/201906/1383365-20190628180149776-546074458.png)

* 当 sqlSession1 去查询用户信息的时候，Mybatis 会将查询数据存储到二级缓存中。
* 如果 sqlSession3 去执行相同 Mapper 映射下的 SQL 语句，并且执行 commit 提交，那么 Mybatis 将会清空该 Mapper 映射下的二级缓存区域的数据。
* sqlSession2 去查询与 sqlSession1 相同的用户信息，Mybatis 首先会去缓存中找是否存在数据，如果存在直接从缓存中取出数据。
* 如果想使用 Mybatis 的二级缓存，那么应该做以下配置
  - 首先在 Mybatis 配置文件中添加配置 **（这一步其实可以忽略，因为默认值为 true）**

```
<settings>
    <!-- 开启缓存 -->
    <setting name="cacheEnabled" value="true"/>
</settings> 
```

- 接着在映射文件中配置

```
<mapper namespace="cn.ykf.mapper.UserMapper">
    <!-- 使用缓存 -->
    <cache/>
</mapper>
```

* 最后在需要使用二级缓存的操作上配置 **（针对每次查询都需要最新数据的操作，要设置成 `useCache="false"`，禁用二级缓存）**

```<select id="listAllUsers" resultMap="UserWithAccountsMap" useCache="true">
<select id="listAllUsers" resultMap="UserWithAccountsMap" useCache="true">
    SELECT * FROM user
</select>
```

> - 当我们使用二级缓存的时候，所缓存的类一定要**实现 `java.io.Serializable` 接口**，这样才可以使用序列化的方式来保存对象。
> - 由于是序列化保存对象，所以二级缓存中存放的是数据，而不是整个对象。

###### 源码

* 首先，excutor是在sqlsessionfactory创建sqlsession时创建的，用以作为sqlsession的参数传入

```java
public class DefaultSqlSessionFactory implements SqlSessionFactory {

private SqlSession openSessionFromDataSource(ExecutorType execType, TransactionIsolationLevel level, boolean autoCommit) {
    Transaction tx = null;

    DefaultSqlSession var8;
    try {
        Environment environment = this.configuration.getEnvironment();
        TransactionFactory transactionFactory = this.getTransactionFactoryFromEnvironment(environment);
        tx = transactionFactory.newTransaction(environment.getDataSource(), level, autoCommit);
        Executor executor = this.configuration.newExecutor(tx, execType);
        var8 = new DefaultSqlSession(this.configuration, executor, autoCommit);
    } catch (Exception var12) {
        this.closeTransaction(tx);
        throw ExceptionFactory.wrapException("Error opening session.  Cause: " + var12, var12);
    } finally {
        ErrorContext.instance().reset();
    }

    return var8;
}
```

* 接着看下excutor有什么类型

```java
public class Configuration {
 public Executor newExecutor(Transaction transaction, ExecutorType executorType) {
        executorType = executorType == null ? this.defaultExecutorType : executorType;
        executorType = executorType == null ? ExecutorType.SIMPLE : executorType;
        Object executor;
        if (ExecutorType.BATCH == executorType) {
            executor = new BatchExecutor(this, transaction);
        } else if (ExecutorType.REUSE == executorType) {
            executor = new ReuseExecutor(this, transaction);
        } else {
            executor = new SimpleExecutor(this, transaction);
        }

        if (this.cacheEnabled) {
            executor = new CachingExecutor((Executor)executor);//装饰前面的三个executor
        }

        Executor executor = (Executor)this.interceptorChain.pluginAll(executor);
        return executor;
    }
}
```

* 可以看出如果开启二级缓存，cachingExecutor会被作为装饰类，为什么说是装饰类呢

```java
public class CachingExecutor implements Executor {
    private final Executor delegate;
    private final TransactionalCacheManager tcm = new TransactionalCacheManager();

    public CachingExecutor(Executor delegate) {
        this.delegate = delegate;
        delegate.setExecutorWrapper(this);
    }

    public Transaction getTransaction() {
        return this.delegate.getTransaction();
    }

    public void close(boolean forceRollback) {
        try {
            if (forceRollback) {
                this.tcm.rollback();
            } else {
                this.tcm.commit();
            }
        } finally {
            this.delegate.close(forceRollback);
        }

    }

    public boolean isClosed() {
        return this.delegate.isClosed();
    }

    public int update(MappedStatement ms, Object parameterObject) throws SQLException {
        this.flushCacheIfRequired(ms);//刷新二级缓存
        return this.delegate.update(ms, parameterObject);
    }

    public <E> List<E> query(MappedStatement ms, Object parameterObject, RowBounds rowBounds, ResultHandler resultHandler) throws SQLException {
        BoundSql boundSql = ms.getBoundSql(parameterObject);
        CacheKey key = this.createCacheKey(ms, parameterObject, rowBounds, boundSql);
        return this.query(ms, parameterObject, rowBounds, resultHandler, key, boundSql);
    }

    public <E> List<E> query(MappedStatement ms, Object parameterObject, RowBounds rowBounds, ResultHandler resultHandler, CacheKey key, BoundSql boundSql) throws SQLException {
        Cache cache = ms.getCache();
        if (cache != null) {
            this.flushCacheIfRequired(ms);
            if (ms.isUseCache() && resultHandler == null) {
                this.ensureNoOutParams(ms, parameterObject, boundSql);
                List<E> list = (List)this.tcm.getObject(cache, key);二级缓存中存在则在二级缓存中读取
                if (list == null) {
                //若是二级缓存没有，则到一级缓存中查询
                    list = this.delegate.query(ms, parameterObject, rowBounds, resultHandler, key, boundSql);
                    this.tcm.putObject(cache, key, list);//保存到二级缓存中
                }

                return list;//返回
            }
        }

        return this.delegate.query(ms, parameterObject, rowBounds, resultHandler, key, boundSql);
    }

    public List<BatchResult> flushStatements() throws SQLException {
        return this.delegate.flushStatements();
    }

    public void commit(boolean required) throws SQLException {
        this.delegate.commit(required);
        this.tcm.commit();
    }

    public void rollback(boolean required) throws SQLException {
        try {
            this.delegate.rollback(required);
        } finally {
            if (required) {
                this.tcm.rollback();
            }

        }

    }

}
```

* 可以从commit等事务可以看出来是由传入的excutor（delegate）实现的，并且与TransactionalCacheManager tcm辅助完成功能，只有query自己实现了功能，即二级缓存，从中可以看出他是先读取二级缓存有无内容，没有再到一级缓存中查看，没有再去数据库查询，再保存到一级缓存（若commit或），最后返还给用户。

* 因为一个二级缓存对应多个一级缓存，所以在其中一个sqlsession需要刷新导致二级缓存刷新时，其他sqlsession的与该sqlsession无关联的数据可以在一级缓存中获取就行，但是如果是相关联的数据，此时又用其他的sqlsession来查找，其他sqlsession的一级缓存数据是否存在过期现象。（不同命名空间？）**这就涉及到一级缓存什么时候提交数据到二级缓存中的问题了**：

  **只有会话关闭或提交后，一级缓存中的数据才会转移到二级缓存中,并且只有是同一个namespace所以可以获取到数据；因为mybatis的缓存会被一个transactioncache类包装住，所有的cache.putObject全部都会被暂时存到一个map里，等会话commit/close以后，这个map里的缓存对象才会被真正的cache类执行putObject操作。这么设计的原因是为了防止事务执行过程中出异常导致回滚，如果get到object后直接put进缓存，万一发生回滚，就很容易导致mybatis缓存被脏读**

  * 这就保证了select就不会到达同的其他的sqlsession的cache中了，若是update成功，则commit成功，数据上传到二级缓存中，对该sqlsession的数据访问会直接在二级内存读取，若是失败，则不会commit，则不会上传到二级缓存中，数据因为失败仍应是原来的数据才是对的，二级缓存数据没有收到commit或close后传来的数据而不变，继续等待相应的sqlsession 进行commit/close

* UserMapper有一个二级缓存区域（按namespace分，如果namespace相同则使用同一个相同的二级缓存区），其它mapper也有自己的二级缓存区域（按namespace分）

* 每一个namespace的mapper都有一个二缓存区域，两个mapper的namespace如果相同，这两个mapper执行sql查询到数据将存在相同的二级缓存区域中。

* mybatis二级缓存的局限性：

  > 细粒度缓存就是，针对某个商品，如果要修改其信息，只修改该商品的缓存数据，缓存区的其他数据不动（不会因为某个商品信息的修改就直接清空整个缓存区）

  mybatis二级缓存对细粒度级别的缓存实现不好，比如如下需求：
  对商品信息进行缓存， 由于商品信息查询访问量大，但是要求用户每次都能查询到最新的商品信息，此时如果使用mybatis的二级缓存，就无法实现当一个商品信息变化时，只刷新该商品的缓存信息，而不刷新其它商品的信息。这是因为mybatis的二级缓存区域以mapper为单位进行划分，当一个商品信息变化时（使用这个mapper的任意一个sqlSession执行commt操作），会将所有商品信息的缓存数据全部清空。解决此问题，需要在业务层根据需求对数据进行针对性的缓存（三级缓存）。



## 注解开发

Mybatis 中的注解开发
在 Mybatis 的注解开发中，常用的注解如下表所示：
注解	作用
@Intsert	实现新增
@Update	实现更新
@Delete	实现删除
@Select	实现查询
@Results	实现结果集封装
@ResultMap	实现引用 @Results 定义的封装
@One	实现一对一结果集封装
@Many	实现一对多结果集封装
@SelectProvider	实现动态 SQL 映射

### Mybatis 使用注解实现单表 CURD

- 用户实体类接口层 `UserMapper` 代码如下

```
public interface UserMapper {
    /**
     * 查询所有用户
     *
     * @return
     */
    @Select("SELECT * FROM user")
    List<User> listAllUsers();

    /**
     * 添加用户
     *
     * @param user
     * @return 成功返回1，失败返回0
     */
    @Insert("INSERT INTO user(username,birthday,sex,address) VALUES(#{username},#{birthday},#{sex},#{address})")
    @SelectKey(keyProperty = "id", keyColumn = "id", statement = "SELECT LAST_INSERT_ID()", resultType = Integer.class, before = false)
    int saveUser(User user);

    /**
     * 根据id删除用户
     *
     * @param userId
     * @return 成功返回1，失败返回0
     */
    @Delete("DELETE FROM user WHERE id = #{id}")
    int removeUserById(Integer userId);

    /**
     * 修改用户
     *
     * @param user
     * @return 成功返回1，失败返回0
     */
    @Update("UPDATE user SET username = #{username}, birthday = #{birthday}, sex = #{sex}, address = #{address} WHERE id = #{id}")
    int updateUser(User user);

    /**
     * 根据id查询单个用户
     *
     * @param userId
     * @return
     */
    @Select("SELECT * FROM user WHERE id = #{id}")
    User getUserById(Integer userId);

    /**
     * 根据姓名模糊查询多个用户
     *
     * @param username
     * @return
     */
    @Select("SELECT * FROM user WHERE username LIKE CONCAT('%',#{username},'%')")
    List<User> listUsersByName(String username);

    /**
     * 查询用户总数
     *
     * @return
     */
    @Select("SELECT COUNT(id) FROM user")
    int countUser();
}
```

- 注意，如果此时**实体类的属性与数据库表列名不一致**，那么我们应该使用`@Results、@Result、@ResultMap` 等注解，如下

```
public interface UserMapper {
    /**
     * 查询所有用户
     *
     * @return
     */
    @Select("SELECT * FROM user")
    @Results(id = "UserMap",value = {
            @Result(id = true,property = "userId",column = "id"),
            @Result(property = "userName",column = "username"),
            @Result(property = "userBirthday",column = "birthday"),
            @Result(property = "userSex",column = "sex"),
            @Result(property = "userAddress",column = "address"),
    })
    List<User> listAllUsers();

    /**
     * 添加用户
     *
     * @param user
     * @return 成功返回1，失败返回0
     */
    @Insert("INSERT INTO user(username,birthday,sex,address) VALUES(#{username},#{birthday},#{sex},#{address})")
    @ResultMap("UserMap")
    int saveUser(User user);
```

> @Results 注解用于定义映射结果集，相当于标签 <resultMap></resultMap>。
> 其中，id 属性为唯一标识。
> value 属性用于接收 @Result[] 注解类型的数组。
> @Result 注解用于定义映射关系，相当于标签 <id /> 和 <result />
> 其中，id 属性指定主键。
> property 属性指定实体类的属性名，column 属性指定数据库表中对应的列。
> @ResultMap 注解用于引用 @Results 定义的映射结果集，避免了重复定义映射结果集。

### Mybatis 使用注解实现多对一（一对一）

还是以上一篇笔记的用户和账户为例子。一个用户可以有多个账户，而一个账户只能对应一个用户。
在 Mybatis 中，多对一是作为一对一来进行处理的。也就是说，虽然多个账户可以属于同一个用户（多对一），但是在实体类中，我们是在账户类中添加一个用户类的对象引用，以此来表明所属用户。(此时就相当于一对一)
账户实体类和用户实体类见上篇笔记，接口层代码如下

```
public interface AccountMapper {
	/**
     * 查询所有账户，并查询所属用户，采用立即加载
     *
     * @return
     */
    @Select("SELECT * FROM account")
    @Results(id = "AccountMap", value = {
            @Result(id = true, property = "id", column = "id"),
            @Result(property = "uid", column = "uid"),
            @Result(property = "user", column = "uid",
                    one = @One(select = "cn.ykf.mapper.UserMapper.getUserById", fetchType = FetchType.EAGER))
    })
    List<Account> listAllAccounts();
}
```

```
public interface UserMapper {
    /**
     * 根据id查询单个用户
     *
     * @param userId
     * @return
     */
    @Select("SELECT * FROM user WHERE id = #{id}")
    User getUserById(Integer userId);
}
@One 注解相当于标签 <association></association>，是多表查询的关键，在注解中用来指定子查询返回单一对象。
其中，select 属性指定用于查询的接口方法，fetchType 属性用于指定立即加载或延迟加载，分别对应 FetchType.EAGER 和 FetchType.LAZY
在包含 @one 注解的 @Result 中，column 属性用于指定将要作为参数进行查询的数据库表列。
```

### Mybatis 使用注解实现一对多

- 为用户实体类添加包含账户实体类的集合引用，具体代码见上篇笔记。接口层代码如下

```
public interface UserMapper {
    /**
     * 查询所有用户，并且查询拥有账户，采用延迟加载
     *
     * @return
     */
    @Select("SELECT * FROM user")
    @Results(id = "UserMap", value = {
            @Result(id = true, property = "userId", column = "id"),
            @Result(property = "userName", column = "username"),
            @Result(property = "userBirthday", column = "birthday"),
            @Result(property = "userSex", column = "sex"),
            @Result(property = "userAddress", column = "address"),
            @Result(property = "accounts", column = "id",
                    many = @Many(select = "cn.ykf.mapper.AccountMapper.getAccountByUid", fetchType = FetchType.LAZY))
    })
    List<User> listAllUsers();
```

```
public interface AccountMapper {
    /**
     * 根据用户id查询账户列表
     *
     * @param uid
     * @return
     */
    @Select("SELECT * FROM account WHERE uid = #{uid}")
    List<Account> listAccountsByUid(Integer uid);
}
@Many 注解相当于标签 <collection></collection>，是多表查询的关键，在注解中用来指定子查询返回对象集合。
其中，select 属性指定用于查询的接口方法，fetchType 属性用于指定立即加载或延迟加载，分别对应 FetchType.EAGER 和 FetchType.LAZY
在包含 @Many 注解的 @Result 中，column 属性用于指定将要作为参数进行查询的数据库表列。
```

###  Mybatis 使用注解实现二级缓存

- 如果使用注解时想开启二级缓存，那么首先应该在 Mybatis 配置文件中开启全局配置

```
 Mybatis 使用注解实现二级缓存
如果使用注解时想开启二级缓存，那么首先应该在 Mybatis 配置文件中开启全局配置<settings>
    <!-- 开启缓存 -->
    <setting name="cacheEnabled" value="true"/>
</settings>
```

```
@CacheNamespace(blocking = true)
public interface UserMapper {
	// .....
}
```

## Mybatis分页功能

https://blog.csdn.net/chenbaige/article/details/70846902

**一.借助数组进行分页（Service层的框架实现）**

原理：进行数据库查询操作时，获取到数据库中所有满足条件的记录，保存在应用的临时数组中，再通过List的subList方法，获取到满足条件的所有记录。

缺点：数据库查询并返回所有的数据，而我们需要的只是极少数符合要求的数据。当数据量少时，还可以接受。当数据库数据量过大时，每次查询对数据库和程序的性能都会产生极大的影响。

**二.借助Sql语句进行分页(Dao层的sql limit实现)**

实现：通过sql语句实现分页也是非常简单的，只是需要改变我们查询的语句就能实现了，即在sql语句后面添加limit分页语句。

在xxxMapper.xml文件中编写sql语句通过limit关键字进行分页：

```xml
 <select id="queryStudentsBySql" parameterType="map" resultMap="studentmapper">
        select * from student limit #{currIndex} , #{pageSize}
</select>
```

**三.拦截器分页(PageHelper原理也是如此，其中startPage只是借助localThread先保存分页Page信息，并在Intercept方法中辅以dialect对象进行检测与执行相关操作，sqlsession下拦截器的limit实现)**

PageHelepr的Intecepter在dao层下面的sqlsession.selectlist下面对statemnehandler等拦截对象进行拦截生成代理对象的，可以说是对dao层（sql语句execute前）的拦截增强

自定义拦截器实现了拦截所有StatementHandler的prepare方法处理的xml中id以ByPage结尾的语句，并且利用获取到的分页相关参数统一在sql语句后面加上limit分页的相关语句

> 如何使用Interceptor呢？
>
> 实例化后的Interceptor会在ApplicationContext初始化时读取实现了Inteceptor接口的实例按实例顺序自动添加到责任链中
>
> Interceptor会在何时起作用？
>
> 我们需要先通过@Interceptor判断我们的Interceptor是对于在sql的Executor、StatementHandler、ParameterHandler、ResultSetHandler四个处理阶段中进行拦截的与拦截了四个类中的什么方法，以便更好的理解Interceptor在哪一步将Page信息往下传递。（因为在创建sqlSession 的时候就会同步创建Executor，并在query执行sql语句时，新建statementHandler并同时创建ParameterHandle和ResultSetHandler，所以Interceptor可以随心在哪个阶段进行拦截并顺序处理，也因此是分页插件可以实现）
>
> https://www.cnblogs.com/tanghaorong/p/14094521.html
>
> 分页需要在Dao层进行哪些标记使得Inteceptor能判断哪些方法需要分页呢？
>
> 不同的Interceptor有不同的拦截逻辑，会使用不同的逻辑判断动态增强DAO层，
>
> 在拦截到标记的拦截类的拦截方法时，在intercept方法中对Dao方法封装成的statement进行相应条件的判断
>
> 如PageHelper会在执行Dao层时判断ThreadLocal上的查看有没有Page 的属性存储，中间会进行判断该sql 的类型是查询，还是修改操作。若是查询有则动态增强，分页处理
>
> PaginationInterceptor则会判断Dao层的第一个参数中有没有实现IPage接口的如Page类，也会判断是否为查询操作，若是且有则读取Page类中存储的属性，动态增强，进行分页处理。

```java
package com.cbg.interceptor;

import org.apache.ibatis.executor.Executor;
import org.apache.ibatis.executor.parameter.ParameterHandler;
import org.apache.ibatis.executor.resultset.ResultSetHandler;
import org.apache.ibatis.executor.statement.StatementHandler;
import org.apache.ibatis.mapping.MappedStatement;
import org.apache.ibatis.plugin.*;
import org.apache.ibatis.reflection.MetaObject;
import org.apache.ibatis.reflection.SystemMetaObject;

import java.sql.Connection;
import java.util.Map;
import java.util.Properties;

/**
 * Created by chenboge on 2017/5/7.
 * <p>
 * Email:baigegechen@gmail.com
 * <p>
 * description:
 */

/**
 * @Intercepts 说明是一个拦截器
 * @Signature 拦截器的签名
 * type 拦截的类型 四大对象之一( Executor,ResultSetHandler,ParameterHandler,StatementHandler)
 * method 拦截的方法
 * args 参数
 */
@Intercepts({@Signature(type = StatementHandler.class, method = "prepare", args = {Connection.class, Integer.class})})
public class MyPageInterceptor implements Interceptor {

//每页显示的条目数
    private int pageSize;
//当前现实的页数
    private int currPage;

    private String dbType;


    @Override
    public Object intercept(Invocation invocation) throws Throwable {
        //获取StatementHandler，默认是RoutingStatementHandler
        StatementHandler statementHandler = (StatementHandler) invocation.getTarget();
        //获取statementHandler包装类
        MetaObject MetaObjectHandler = SystemMetaObject.forObject(statementHandler);

        //分离代理对象链
        while (MetaObjectHandler.hasGetter("h")) {
            Object obj = MetaObjectHandler.getValue("h");
            MetaObjectHandler = SystemMetaObject.forObject(obj);
        }

        while (MetaObjectHandler.hasGetter("target")) {
            Object obj = MetaObjectHandler.getValue("target");
            MetaObjectHandler = SystemMetaObject.forObject(obj);
        }

        //获取连接对象
        //Connection connection = (Connection) invocation.getArgs()[0];


        //object.getValue("delegate");  获取StatementHandler的实现类

        //获取查询接口映射的相关信息
        MappedStatement mappedStatement = (MappedStatement) MetaObjectHandler.getValue("delegate.mappedStatement");
        String mapId = mappedStatement.getId();

        //statementHandler.getBoundSql().getParameterObject();

        //拦截以.ByPage结尾的请求，分页功能的统一实现
        if (mapId.matches(".+ByPage$")) {
            //获取进行数据库操作时管理参数的handler
            ParameterHandler parameterHandler = (ParameterHandler) MetaObjectHandler.getValue("delegate.parameterHandler");
            //获取请求时的参数
            Map<String, Object> paraObject = (Map<String, Object>) parameterHandler.getParameterObject();
            //也可以这样获取
            //paraObject = (Map<String, Object>) statementHandler.getBoundSql().getParameterObject();

            //参数名称和在service中设置到map中的名称一致
            currPage = (int) paraObject.get("currPage");
            pageSize = (int) paraObject.get("pageSize");

            String sql = (String) MetaObjectHandler.getValue("delegate.boundSql.sql");
            //也可以通过statementHandler直接获取
            //sql = statementHandler.getBoundSql().getSql();

            //构建分页功能的sql语句
            String limitSql;
            sql = sql.trim();
            limitSql = sql + " limit " + (currPage - 1) * pageSize + "," + pageSize;

            //将构建完成的分页sql语句赋值个体'delegate.boundSql.sql'，偷天换日
            MetaObjectHandler.setValue("delegate.boundSql.sql", limitSql);
        }
//调用原对象的方法，进入责任链的下一级
        return invocation.proceed();
    }


    //获取代理对象
    @Override
    public Object plugin(Object o) {
    //生成object对象的动态代理对象
        return Plugin.wrap(o, this);
    }

    //设置代理对象的参数
    @Override
    public void setProperties(Properties properties) {
//如果项目中分页的pageSize是统一的，也可以在这里统一配置和获取，这样就不用每次请求都传递pageSize参数了。参数是在配置拦截器时配置的。
        String limit1 = properties.getProperty("limit", "10");
        this.pageSize = Integer.valueOf(limit1);
        this.dbType = properties.getProperty("dbType", "mysql");
    }
}

```

编写好拦截器后，需要注册到项目中，才能发挥它的作用。在mybatis的配置文件中，添加如下代码：

```xml
    <plugins>
        <plugin interceptor="com.cbg.interceptor.MyPageInterceptor">
            <property name="limit" value="10"/>
            <property name="dbType" value="mysql"/>
        </plugin>
    </plugins>
```

若是自定义的interceptor，则需要用setProperties方法处理我们property标签的参数，PageHelper等已经设置好相应的properties，我们只需在property中写入相应的name和value即可

```java
 //读取配置的代理对象的参数
    @Override
    public void setProperties(Properties properties) {
        String limit1 = properties.getProperty("limit", "10");
        this.pageSize = Integer.valueOf(limit1);
        this.dbType = properties.getProperty("dbType", "mysql");
    }
```



**四.RowBounds实现分页（sqlsession的selectList方法重载后在框架中分页，sqlsession的框架实现）**

原理：通过RowBounds实现分页和通过数组方式分页原理差不多，都是一次获取所有符合条件的数据，然后在内存中对大数据进行操作，实现分页效果。只是数组分页需要我们自己去实现分页逻辑，这里更加简化而已。

存在问题：一次性从数据库获取的数据可能会很多，对内存的消耗很大，可能导师性能变差，甚至引发内存溢出。

实现：在Dao层中需要分页的方法的SelectList方法依据Page等Service层传递的实参，转换成RowBounds对象或者DAO接口写成List<xxx> xxx(RowBounds page)，这种情况下，mybatis也会完成分页查询。(但是Dao接口的形参为Page等自定义类型，却没有实现将Page转换成RowBounds交给selectList方法，则不会实现分页功能)

## spring与MyBatis 事务管理

https://coderbee.net/index.php/framework/20191025/2002

#### 1. 运行环境 Enviroment

 当 MyBatis 与不同的应用结合时，需要不同的事务管理机制。与 Spring 结合时，由 Spring 来管理事务；单独使用时需要自行管理事务，在容器里运行时可能由容器进行管理。

MyBatis 用 Enviroment 来表示运行环境，其封装了三个属性：

```java
public class Configuration {
    // 一个 MyBatis 的配置只对应一个环境
    protected Environment environment;
    // 其他属性 .....
}

public final class Environment {
    private final String id;
    private final TransactionFactory transactionFactory;
    private final DataSource dataSource;
}
```

#### 2. 事务抽象

MyBatis 把事务管理抽象出 `Transaction` 接口，由 `TransactionFactory` 接口的实现类负责创建。

```
public interface Transaction {
    Connection getConnection() throws SQLException;
    void commit() throws SQLException;
    void rollback() throws SQLException;
    void close() throws SQLException;
    Integer getTimeout() throws SQLException;
}

public interface TransactionFactory {
    void setProperties(Properties props);
    Transaction newTransaction(Connection conn);
    Transaction newTransaction(DataSource dataSource, TransactionIsolationLevel level, boolean autoCommit);
}
```

Executor 的实现持有一个 SqlSession 实现，事务控制是委托给 SqlSession 的方法来实现的。

```java
public abstract class BaseExecutor implements Executor {
    protected Transaction transaction;

    public void commit(boolean required) throws SQLException {
        if (closed) {
            throw new ExecutorException("Cannot commit, transaction is already closed");
        }
        clearLocalCache();
        flushStatements();
        if (required) {
            transaction.commit();
        }
    }

    public void rollback(boolean required) throws SQLException {
        if (!closed) {
            try {
                clearLocalCache();
                flushStatements(true);
            } finally {
                if (required) {
                    transaction.rollback();
                }
            }
        }
    }

    // 省略其他方法、属性
}
```



#### 3. 与 Spring 集成的事务管理

##### 3.1 配置 TransactionFactory

与 Spring 集成时，通过 SqlSessionFactoryBean 来初始化 MyBatis 。

```java
protected SqlSessionFactory buildSqlSessionFactory() throws IOException {
    Configuration configuration;

    XMLConfigBuilder xmlConfigBuilder = null;
    if (this.configuration != null) {
        configuration = this.configuration;
        if (configuration.getVariables() == null) {
            configuration.setVariables(this.configurationProperties);
        } else if (this.configurationProperties != null) {
            configuration.getVariables().putAll(this.configurationProperties);
        }
    } else if (this.configLocation != null) {
        xmlConfigBuilder = new XMLConfigBuilder(this.configLocation.getInputStream(), null, this.configurationProperties);
        configuration = xmlConfigBuilder.getConfiguration();
    } else {
        configuration = new Configuration();
        configuration.setVariables(this.configurationProperties);
    }

    if (this.objectFactory != null) {
        configuration.setObjectFactory(this.objectFactory);
    }

    if (this.objectWrapperFactory != null) {
        configuration.setObjectWrapperFactory(this.objectWrapperFactory);
    }

    if (this.vfs != null) {
        configuration.setVfsImpl(this.vfs);
    }

    if (hasLength(this.typeAliasesPackage)) {
        String[] typeAliasPackageArray = tokenizeToStringArray(this.typeAliasesPackage,
        ConfigurableApplicationContext.CONFIG_LOCATION_DELIMITERS);
        for (String packageToScan : typeAliasPackageArray) {
            configuration.getTypeAliasRegistry().registerAliases(packageToScan,
            typeAliasesSuperType == null ? Object.class : typeAliasesSuperType);
        }
    }

    if (!isEmpty(this.typeAliases)) {
        for (Class<?> typeAlias : this.typeAliases) {
            configuration.getTypeAliasRegistry().registerAlias(typeAlias);
        }
    }

    if (!isEmpty(this.plugins)) {
        for (Interceptor plugin : this.plugins) {
            configuration.addInterceptor(plugin);
        }
    }

    if (hasLength(this.typeHandlersPackage)) {
        String[] typeHandlersPackageArray = tokenizeToStringArray(this.typeHandlersPackage,
        ConfigurableApplicationContext.CONFIG_LOCATION_DELIMITERS);
        for (String packageToScan : typeHandlersPackageArray) {
            configuration.getTypeHandlerRegistry().register(packageToScan);
        }
    }

    if (!isEmpty(this.typeHandlers)) {
        for (TypeHandler<?> typeHandler : this.typeHandlers) {
            configuration.getTypeHandlerRegistry().register(typeHandler);
        }
    }

    if (this.databaseIdProvider != null) {//fix #64 set databaseId before parse mapper xmls
        try {
            configuration.setDatabaseId(this.databaseIdProvider.getDatabaseId(this.dataSource));
        } catch (SQLException e) {
            throw new NestedIOException("Failed getting a databaseId", e);
        }
    }

    if (this.cache != null) {
        configuration.addCache(this.cache);
    }

    if (xmlConfigBuilder != null) {
        try {
            xmlConfigBuilder.parse();
        } catch (Exception ex) {
            throw new NestedIOException("Failed to parse config resource: " + this.configLocation, ex);
        } finally {
            ErrorContext.instance().reset();
        }
    }

    // 创建 SpringManagedTransactionFactory
    if (this.transactionFactory == null) {
        this.transactionFactory = new SpringManagedTransactionFactory();
    }

    // 封装成 Environment
    configuration.setEnvironment(new Environment(this.environment, this.transactionFactory, this.dataSource));

    if (!isEmpty(this.mapperLocations)) {
        for (Resource mapperLocation : this.mapperLocations) {
            if (mapperLocation == null) {
                continue;
            }

            try {
                XMLMapperBuilder xmlMapperBuilder = new XMLMapperBuilder(mapperLocation.getInputStream(),
                configuration, mapperLocation.toString(), configuration.getSqlFragments());
                xmlMapperBuilder.parse();
            } catch (Exception e) {
                throw new NestedIOException("Failed to parse mapping resource: '" + mapperLocation + "'", e);
            } finally {
                ErrorContext.instance().reset();
            }
        }
    } else {
    }

    return this.sqlSessionFactoryBuilder.build(configuration);
}
public class SqlSessionFactoryBuilder {
    public SqlSessionFactory build(Configuration config) {
        return new DefaultSqlSessionFactory(config);
    }
}
```

重点是在构建 MyBatis Configuration 对象时，把 transactionFactory 配置成 SpringManagedTransactionFactory，再封装成 Environment 对象。

##### 3.2 运行时事务管理

Mapper 的代理对象持有的是 `SqlSessionTemplate`，其实现了 `SqlSession` 接口。

`SqlSessionTemplate` 的方法并不直接调用具体的 `SqlSession` 的方法，而是委托给一个动态代理，通过代理 `SqlSessionInterceptor` 对方法调用进行拦截。

`SqlSessionInterceptor` 负责获取真实的与数据库关联的 `SqlSession` 实现，并在方法执行完后决定提交或回滚事务、关闭会话。

```java
public class SqlSessionTemplate implements SqlSession, DisposableBean {
    private final SqlSessionFactory sqlSessionFactory;
    private final ExecutorType executorType;
    private final SqlSession sqlSessionProxy;
    private final PersistenceExceptionTranslator exceptionTranslator;

    public SqlSessionTemplate(SqlSessionFactory sqlSessionFactory, ExecutorType executorType,
        PersistenceExceptionTranslator exceptionTranslator) {

        notNull(sqlSessionFactory, "Property 'sqlSessionFactory' is required");
        notNull(executorType, "Property 'executorType' is required");

        this.sqlSessionFactory = sqlSessionFactory;
        this.executorType = executorType;
        this.exceptionTranslator = exceptionTranslator;

        // 因为 SqlSession 接口声明的方法也不少，
        // 在每个方法里添加事务相关的拦截比较麻烦，
        // 不如创建一个内部的代理对象进行统一处理。
        this.sqlSessionProxy = (SqlSession) newProxyInstance(
            SqlSessionFactory.class.getClassLoader(),
            new Class[] { SqlSession.class },
            new SqlSessionInterceptor());
    }

    public int update(String statement) {
        // 在代理对象上执行方法调用
        return this.sqlSessionProxy.update(statement);
    }

    // 对方法调用进行拦截，加入事务控制逻辑
    private class SqlSessionInterceptor implements InvocationHandler {
        public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
            // 获取与数据库关联的会话
            SqlSession sqlSession = SqlSessionUtils.getSqlSession(
                SqlSessionTemplate.this.sqlSessionFactory,
                SqlSessionTemplate.this.executorType,
                SqlSessionTemplate.this.exceptionTranslator);
            try {
                // 执行 SQL 操作
                Object result = method.invoke(sqlSession, args);
                if (!SqlSessionUtils.isSqlSessionTransactional(sqlSession, SqlSessionTemplate.this.sqlSessionFactory)) {
                    // 如果 sqlSession 不是 Spring 管理的，则要自行提交事务
                    sqlSession.commit(true);
                }
                return result;
            } catch (Throwable t) {
                Throwable unwrapped = unwrapThrowable(t);
                if (SqlSessionTemplate.this.exceptionTranslator != null && unwrapped instanceof PersistenceException) {

                    SqlSessionUtils.closeSqlSession(sqlSession, SqlSessionTemplate.this.sqlSessionFactory);
                    sqlSession = null;
                    Throwable translated = SqlSessionTemplate.this.exceptionTranslator.translateExceptionIfPossible((PersistenceException) unwrapped);
                    if (translated != null) {
                        unwrapped = translated;
                    }
                }
                throw unwrapped;
            } finally {
                if (sqlSession != null) {
                    SqlSessionUtils.closeSqlSession(sqlSession, SqlSessionTemplate.this.sqlSessionFactory);
                }
            }
        }
    }
}
```

`SqlSessionUtils` 封装了对 Spring 事务管理机制的访问。

```java
// SqlSessionUtils
public static SqlSession getSqlSession(SqlSessionFactory sessionFactory, ExecutorType executorType, PersistenceExceptionTranslator exceptionTranslator) {
    // 从 Spring 的事务管理机制那里获取当前事务关联的会话
    SqlSessionHolder holder = (SqlSessionHolder) TransactionSynchronizationManager.getResource(sessionFactory);

    SqlSession session = sessionHolder(executorType, holder);
    if (session != null) {
        // 已经有一个会话则复用
        return session;
    }

    // 创建新的 会话
    session = sessionFactory.openSession(executorType);

    // 注册到 Spring 的事务管理机制里
    registerSessionHolder(sessionFactory, executorType, exceptionTranslator, session);

    return session;
}

private static void registerSessionHolder(SqlSessionFactory sessionFactory, ExecutorType executorType,
        PersistenceExceptionTranslator exceptionTranslator, SqlSession session) {
    SqlSessionHolder holder;
    if (TransactionSynchronizationManager.isSynchronizationActive()) {
        Environment environment = sessionFactory.getConfiguration().getEnvironment();

        if (environment.getTransactionFactory() instanceof SpringManagedTransactionFactory) {
            holder = new SqlSessionHolder(session, executorType, exceptionTranslator);
            TransactionSynchronizationManager.bindResource(sessionFactory, holder);

            // 重点：注册会话管理的回调钩子，真正的关闭动作是在回调里完成的。
            TransactionSynchronizationManager.registerSynchronization(new SqlSessionSynchronization(holder, sessionFactory));
            holder.setSynchronizedWithTransaction(true);

            // 维护会话的引用计数
            holder.requested();
        } else {
            if (TransactionSynchronizationManager.getResource(environment.getDataSource()) == null) {
            } else {
                throw new TransientDataAccessResourceException(
                    "SqlSessionFactory must be using a SpringManagedTransactionFactory in order to use Spring transaction synchronization");
            }
        }
    } else {
    }
}

public static void closeSqlSession(SqlSession session, SqlSessionFactory sessionFactory) {
    // 从线程本地变量里获取 Spring 管理的会话
    SqlSessionHolder holder = (SqlSessionHolder) TransactionSynchronizationManager.getResource(sessionFactory);
    if ((holder != null) && (holder.getSqlSession() == session)) {
        // Spring 管理的不直接关闭，由回调钩子来关闭
        holder.released();
    } else {
        // 非 Spring 管理的直接关闭
        session.close();
    }
}
```

`SqlSessionSynchronization` 是 `SqlSessionUtils` 的内部私有类，用于作为回调钩子与 Spring 的事务管理机制协调工作，`TransactionSynchronizationManager` 在适当的时候回调其方法。

```java
private static final class SqlSessionSynchronization extends TransactionSynchronizationAdapter {
    private final SqlSessionHolder holder;
    private final SqlSessionFactory sessionFactory;
    private boolean holderActive = true;

    public SqlSessionSynchronization(SqlSessionHolder holder, SqlSessionFactory sessionFactory) {
        this.holder = holder;
        this.sessionFactory = sessionFactory;
    }

    public int getOrder() {
        return DataSourceUtils.CONNECTION_SYNCHRONIZATION_ORDER - 1;
    }

    public void suspend() {
        if (this.holderActive) {
            TransactionSynchronizationManager.unbindResource(this.sessionFactory);
        }
    }

    public void resume() {
        if (this.holderActive) {
            TransactionSynchronizationManager.bindResource(this.sessionFactory, this.holder);
        }
    }

    public void beforeCommit(boolean readOnly) {
        if (TransactionSynchronizationManager.isActualTransactionActive()) {
            try {
                this.holder.getSqlSession().commit();
            } catch (PersistenceException p) {
                if (this.holder.getPersistenceExceptionTranslator() != null) {
                    DataAccessException translated = this.holder
                        .getPersistenceExceptionTranslator()
                        .translateExceptionIfPossible(p);
                    if (translated != null) {
                        throw translated;
                    }
                }
                throw p;
            }
        }
    }

    public void beforeCompletion() {
        if (!this.holder.isOpen()) {
            TransactionSynchronizationManager.unbindResource(sessionFactory);
            this.holderActive = false;

            // 真正关闭数据库会话
            this.holder.getSqlSession().close();
        }
    }

    public void afterCompletion(int status) {
        if (this.holderActive) {
            TransactionSynchronizationManager.unbindResourceIfPossible(sessionFactory);
            this.holderActive = false;

            // 真正关闭数据库会话
            this.holder.getSqlSession().close();
        }
        this.holder.reset();
    }
}
```

##### 3.3 创建新会话

```java
// DefaultSqlSessionFactory
private SqlSession openSessionFromDataSource(ExecutorType execType, TransactionIsolationLevel level, boolean autoCommit) {
    Transaction tx = null;
    try {
        final Environment environment = configuration.getEnvironment();

        // 获取事务工厂实现
        final TransactionFactory transactionFactory = getTransactionFactoryFromEnvironment(environment);

        tx = transactionFactory.newTransaction(environment.getDataSource(), level, autoCommit);

        final Executor executor = configuration.newExecutor(tx, execType);
        return new DefaultSqlSession(configuration, executor, autoCommit);
    } catch (Exception e) {
        closeTransaction(tx); // may have fetched a connection so lets call close()
        throw ExceptionFactory.wrapException("Error opening session.  Cause: " + e, e);
    } finally {
        ErrorContext.instance().reset();
    }
}

private TransactionFactory getTransactionFactoryFromEnvironment(Environment environment) {
    if (environment == null || environment.getTransactionFactory() == null) {
        return new ManagedTransactionFactory();
    }
    return environment.getTransactionFactory();
}
```

#### 4. 小结

1. MyBatis 的核心组件 Executor 通过 Transaction 接口来进行事务控制。
2. 与 Spring 集成时，初始化 Configuration 时会把 transactionFactory 设置为 SpringManagedTransactionFactory 的实例。
3. 每个 Mapper 代理里注入的 SqlSession 是 SqlSessionTemplate 的实例，其实现了 SqlSession 接口；
4. SqlSessionTemplate 把对 SqlSession 接口里声明的方法调用委托给内部的一个动态代理，该代理的方法处理器为内部类 SqlSessionInterceptor 。
5. SqlSessionInterceptor 接收到方法调用时，通过 SqlSessionUtil 访问 Spring 的事务设施，如果有与 Spring 当前事务关联的 SqlSession 则复用；没有则创建一个。
6. SqlSessionInterceptor 根据 Spring 当前事务的状态来决定是否提交或回滚事务。会话的真正关闭是通过注册在 TransactionSynchronizationManager 上的回调钩子实现的。

![img](https://coderbee.net/wp-content/uploads/2019/10/mybatis-mapper-call.png)

如上图：

1. 步骤 1 是 Spring AOP 添加的切面的执行，事务是其中一个切面。
2. 步骤 2 是 Spring 事务管理机制的部分。
3.  步骤 3 是业务代码，调用 Mapper 代理上的方法。
4.  步骤 4 是代理上的方法调用被 MapperProxy.invoke 拦截。
5.  步骤 5、6 是因为 MapperProxy 持有的 sqlSession 是 SqlSessionTemplate，调用到 template 上的方法，又被转发给内部类 SqlSessionInterceptor ，该类获得 Spring 事务管理持有的数据库连接，用以创建 Executor h和 DefaultSqlSession。
6.  步骤 7 用 DefaultSqlSession 发起数据库调用。

## Spring 与 MyBatis

#### 简要概述

* 在配置文件中对service层的AOP实现，使得SpringMVC等servlet调用容器中的Service对象时，会用TransactionInterceptor对其进行动态处理，为其调用DataSourceTransactionManager为其创建transaction并将connection绑定到ThreadLocal（关键字为datasource，之后用到该datasource的就用该connectionholder，就可以用来判断是否存在事务Spring的事务控制）中
* **（service对象一经从容器中获取就执行，使得commit等交易transaction管理，sqlsession保存在线程中，可重复使用，否则sqlsession是由每个单独的MapperFactoryBean调用的，每个执行完便提交，再次调用会获取不同的connection，一级缓存失效；就算你不自动提交（没开启事务一般是自动commit），不开启事务也不能保证同一个sqlsession调用的是同一个connection，可能出现更大的问题如脏读等，若是绑定同一个则又是事务的原理了）**
* 对service中的方法进行调用时，就需要调用到dao层的方法与mysql进行交互，而dao层的经过MapperScannerConfigurer的自动扫描以及将方法封装成各个MapperFactoryBean
* MapperFactoryBean创建时若没有指定特定的注入对象，在初始化时Spring会自动注入相应的sqlsessionTemplate等sqlsession对象（实现了sqlsession接口，但都不是实际负责sql语句执行的）
* 若是实现了事务管理，则会在TransactionSynchronizationManager寻找真正执行sql语句的且在同一事务中的DefaultSqlsession这类sqlsession真正的实现类，通过SpringManageTransaction调用datasourceUtils，从TransactionSynchronizationManager获取唯一的connection去执行相应的sql语句。
* 若是声明式事务，则在service方法执行完后自动commit；若编程式则从status获取transaction进行commit

```
<context:component-scan base-package="cn.itcast">//扫描的包
<context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
</context:component-scan>

<bean id="comboPooledDataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">//数据源
<property name="driverClass" value="com.mysql.jdbc.Driver"/>
<property name="jdbcUrl" value="jdbc:mysql://localhost:3306/ssm"/>
<property name="user" value="root"/>
<property name="password" value="123456"/>
</bean>

//创建特定的SqlSessionFactory
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
<property name="dataSource" ref="comboPooledDataSource"/>
</bean>

// 查 找 类 路 径 下 的 映 射 器 并自 动 将 它 们 创 建 成 MapperFactoryBean
<bean id="mapperScannerConfigurer" class="org.mybatis.spring.mapper.MapperScannerConfigurer">
<property name="basePackage" value="cn.itcast.dao"/>
</bean>

//事务管理
<bean id="dataSourceTransactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
<property name="dataSource" ref="comboPooledDataSource"></property>
</bean>

<tx:advice id="txAdvice" transaction-manager="dataSourceTransactionManager">
<tx:attributes>
<tx:method name="find*" read-only="true"/>
<tx:method name="*" isolation="DEFAULT"/>
</tx:attributes>
</tx:advice>

<aop:config>
<aop:advisor advice-ref="txAdvice" pointcut="execution(* cn.itcast.service.impl.*ServiceImpl.*(..))"/>
</aop:config>
```

---------



#### MapperScannerConfigurer与MapperFactoryBean

*  MapperScannerConfigurer查 找 类 路 径 下 的 映 射 器 并自 动 将 它 们 创 建 成 MapperFactoryBean

> basePackage 属性是让你为映射器接口文件设置基本的包路径。 你可以使用分号或逗号 作为分隔符设置多于一个的包路径。每个映射器将会在指定的包路径中递归地被搜索到。
>
> 如果不是有特殊需求，不需要 去 指 定 SqlSessionFactory 或 SqlSessionTemplate , 因 为 MapperScannerConfigurer 将会创建 MapperFactoryBean,之后自动装配。但是,如果你使 用了一个 以上的 DataSource ,那 么自动 装配可 能会失效 。这种 情况下 ,你可 以使用 **sqlSessionFactoryBeanName 或 sqlSessionTemplateBeanName** 属性来设置正确的 bean 名 称来使用。
>
> ```
> <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory" />
> ```

```
public class MapperScannerConfigurer implements BeanDefinitionRegistryPostProcessor, InitializingBean, ApplicationContextAware, BeanNameAware {

	//接口包
	
    private String basePackage;
    private boolean addToConfig = true;
    private SqlSessionFactory sqlSessionFactory;
    private SqlSessionTemplate sqlSessionTemplate;
    private String sqlSessionFactoryBeanName;
    private String sqlSessionTemplateBeanName;
    private Class<? extends Annotation> annotationClass;
    private Class<?> markerInterface;
    private ApplicationContext applicationContext;
    private String beanName;
    private boolean processPropertyPlaceHolders;
    private BeanNameGenerator nameGenerator;
     ···
     get/set方法用以注入
     ···
}
private void processPropertyPlaceHolders() {
        Map<String, PropertyResourceConfigurer> prcs = this.applicationContext.getBeansOfType(PropertyResourceConfigurer.class);
        if (!prcs.isEmpty() && this.applicationContext instanceof GenericApplicationContext) {
            BeanDefinition mapperScannerBean = ((GenericApplicationContext)this.applicationContext).getBeanFactory().getBeanDefinition(this.beanName);
            DefaultListableBeanFactory factory = new DefaultListableBeanFactory();
            factory.registerBeanDefinition(this.beanName, mapperScannerBean);
            Iterator var4 = prcs.values().iterator();

            while(var4.hasNext()) {
                PropertyResourceConfigurer prc = (PropertyResourceConfigurer)var4.next();
                prc.postProcessBeanFactory(factory);
            }

            PropertyValues values = mapperScannerBean.getPropertyValues();
            this.basePackage = this.updatePropertyValue("basePackage", values);
            this.sqlSessionFactoryBeanName = this.updatePropertyValue("sqlSessionFactoryBeanName", values);
            this.sqlSessionTemplateBeanName = this.updatePropertyValue("sqlSessionTemplateBeanName", values);
        }

    }
```

* MapperFactoryBean

> 数据映射器接口可以按照如下做法加入到 Spring 中:
>
> ```
> <bean id="userMapper" class="org.mybatis.spring.mapper.MapperFactoryBean">
> <property name="mapperInterface" value="org.mybatis.spring.sample.mapper.UserMapper" />
> <property name="sqlSessionFactory" ref="sqlSessionFactory" />
> </bean>
> ```
>
> MapperFactoryBean 创建的代理类实现了 UserMapper 接口,并且注入到应用程序中。 因为代理创建在运行时环境中 ,那么指定的映射器必须是一个接口,而 不是一个具体的实现类。
>
> 如果 UserMapper 有一个对应的 MyBatis 的 XML 映射器文件, 如果 XML 文件在类路径的 位置和映射器类相同时, 它会被 MapperFactoryBean 自动解析。 没有必要在 MyBatis 配置文 件 中 去 指 定 映 射 器 , 除 非 映 射 器 的 XML 文 件 在 不 同 的 类 路 径 下 。
>
> 当 MapperFactoryBean 需要 SqlSessionFactory 或 SqlSessionTemplate 时。 这些可以通过各自的 SqlSessionFactory 或 SqlSessionTemplate 属性来设置, 或者可以由 Spring 来自动装配。如果两个属性都设置了,那么 SqlSessionFactory 就会被忽略,因为 SqlSessionTemplate 是需要有一个 session 工厂的设置; 那个工厂会由 MapperFactoryBean. 来使用。

* 由于使用spring管理bean，当我们在代码中需要使用这个bean的时候，会首先去容器中找，第一次需要调用MapperFactoryBean的getObject方法获取一个bean，并保存到容器中。（这里生成的对象最终也是用sqlssion的getMapper生成的，与mybatis一致）

  MapperFactoryBean的getObject方法如下：

> ```
> public class MapperFactoryBean<T> extends SqlSessionDaoSupport implements FactoryBean<T> {
> public T getObject() throws Exception {
>     return this.getSqlSession().getMapper(this.mapperInterface);
> }
> }
> ```

* 又到SqlSessionDaoSupport中去获取

> ```
> public abstract class SqlSessionDaoSupport extends DaoSupport {
> 	private SqlSession sqlSession;
>     private boolean externalSqlSession;
> 
> public SqlSession getSqlSession() {
>     return this.sqlSession;
> }
> 
> // 在MapperScannerConfigurer自动扫描生成/自己创建MapperFactoryBean时，由传入的工厂类型/Spring自动配置的工厂类型
> 时，就创建了SqlSessionTemplate（实现了sqlsession接口），因此调用方法时会通过SqlSessionTemplate的proxysqlsession（动态代理生成的代理类）去TransactionSynchronizationManager寻找真正的sqlsession或创建
> 
>  public void setSqlSessionFactory(SqlSessionFactory sqlSessionFactory) {
>         if (!this.externalSqlSession) {
>             this.sqlSession = new SqlSessionTemplate(sqlSessionFactory);
>         }
> 
>     }
> }
> 
> //spring整合mybatis后，非事务环境下，每个执行完便提交，每次操作数据库都使用新的sqlSession对象，获取不同的connection。因此mybatis的一级缓存无法使用，若有则取TransactionSynchronizationManager中的sqlsession则一级缓存生效（一级缓存针对同一个sqlsession有效）
> ```

------------



#### TransactionDefinition与TransactionAttributes

* **获取事务属性对象(TransactionAttributes若无设置attributes则默认为DefaultTransactionDefinition)，放置在容器中，transactionManager调用getTransaction时作为参数传入**

  事务属性对象持有事务的相关配置，比如事务的隔离级别，传播行为，是否只读等。我们开启spring事务管理时，通常都会在配置文件里加入这样一段配置。

  ```
  <tx:advice id="txAdvice" transaction-manager="transactionManager">
      <tx:attributes>
          <tx:method name="get*" read-only="true"/>
          <tx:method name="*"/>
      </tx:attributes>
  </tx:advice>
  ```

  > * TransactionDefinition主要定义了有哪些事务属性可以指定：
  >
  >   * 事务的隔离级别（Isolation）
  >
  >   * 事务的传播行为（Propagation Behavior）
  >
  >     >  默认为PROPAGATION_REQUIRED ,若存在事务则加入当前事务，若没有则自己新建一个事务。
  >     >
  >     >  Propagation Behavior注意PROPAGATION_REQUIRES_NEW和PROPAGATION_NESTED的区别，前者将当前事务挂起，创建新的事务执行；后者，则是在当前事务种的一个嵌套事务中执行
  >
  >   * 事务的超时时间（Timeout）
  >
  >   * 是否为只读事务(ReadOnly)
  >
  >   * SQL标准定义了4类隔离级别，包括了一些具体规则，用来限定事务内外的哪些改变是可见的，哪些是不可见的。低级别的隔离级一般支持更高的并发处理，并拥有更低的系统开销。
  >
  >   * ISOLATION_DEFAULT 数据库的默认级别：mysql为**Repeatable Read（可重读）**，其他的一般为**Read Committed（读取提交内容）**
  >
  >     
  >
  >   * **Read Uncommitted（读取未提交内容）** ISOLATION_READ_UNCOMITTED
  >
  >     
  >
  >     在该隔离级别，所有事务都可以看到其他未提交事务的执行结果。本隔离级别很少用于实际应用，因为它的性能也不比其他级别好多少。**读取未提交的数据，也被称之为脏读（Dirty Read）。**
  >
  >      
  >
  >    * **Read Committed（读取提交内容）**ISOLATION_READ_COMITTED
  >
  >      
  >
  >   ​		这是大多数数据库系统的默认隔离级别（但不是MySQL默认的）。它满足了隔离的简单定义：一个事务只能看见已经提交事务所做的改变。这种隔离级别 也支持所谓的**不可重复读（Nonrepeatable Read），如果一个用户在一个事务中多次读取一条数据，而另外一个用户则同时更新啦这条数据，造成第一个用户多次读取数据不一致。****
  >
  >   
  >
  >   * **Repeatable Read（可重读）**ISOLATION_REPEATABLE_READ
  >
  >    
  >
  >   这是MySQL的默认事务隔离级别，它确保同一事务的多个实例在并发读取数据时，会看到同样的数据行。不过理论上，这会导致另一个棘手的问题：**幻读 （Phantom Read）**。简单的说，幻读指第一个事务读取一个结果集后，第二个事务，对这个结果集经行增删操作（第一个事务暂时没用到，没添加行锁），然而第一个事务中再次对这个结果集进行查询时，数据发现丢失或新增，出现了“幻影”。**通过加表级锁解决，如间隙锁**InnoDB和Falcon存储引擎通过多版本并发控制（MVCC，Multiversion Concurrency Control）机制解决了该问题。
  >
  >    
  >
  >   **Serializable（可串行化）**ISOLATION_SERIALIZABLE
  >
  >   这是最高的隔离级别，它通过强制事务排序，使之不可能相互冲突，从而解决幻读问题。简言之，它是在每个读的数据行上加上共享锁。在这个级别，可能导致大量的超时现象和锁竞争。
  >
  >   
  >
  > * TransactionAttribute在TransactionDefinition的基础上添加了rollbackon方法，可以通过声明的方式指定业务方法在抛出的哪些的异常的情况下可以回滚事务。（该接口主要面向使用Spring AOP进行声明式事务管理的场合）
  >
  >    
  >
  > ![image-20200728132052202](F:\Typora数据储存\数据库\mybatis.assets\image-20200728132052202.png)

  ----

  

#### 编程式的事务管理与声明式事务处理

* spring提供**编程式的事务管理（自己创建事务管理器，由自己调用管理器的方法创建事务或处理事务，可以使用动态代理减少代理类的编写，但是还是要编写很多事务的代码在业务代码的前后，不是很方便管理，对于事务管理这种特性基本一致的操作用声明反而更利于维护）和声明式事务处理（声明后由容器创建，由声明来调用相应的方法，声明式事务管理用AOP避免了我们为每一个需要事务管理的类创建一个代理类）。**

* 若是声明式事务管理，我们会发现，我们没有自己操作commit的空间，他会在你事务声明的方法结束时就commit，你可以把很多业务代码放在一个事务service方法中实现同一事务，但若是不想则可以编码式事务控制。

  > 声明式事务管理中我们用到org.springframework.transaction.interceptor.TransactionInterceptor帮我们拦截业务方法，使得我们在springMVc中调用serviece中的方法时（我们从容器拿到的Service是经过AOP（也就是动态代理）处理的了），需要先经过它。
  >
  > 需要一个容器去放置用不同ORM框架时，在TransactionInterceptor帮我们拦截后，将service增强成我们想要的模板，加入到像JdbcTempalte/SqlsessionTemplate这样不同的模板中，以便在调用service的方法时，自动去调用相应的数据库框架执行sql语句时需要的流程，如SqlsessionTemplate（它的原理跟sqlsession.getMapper相似）会自动对sqlsession进行创建，再进行该框架对sql语句执行流程，这个容器可以是ProxyFactory（ProxyFactoryBean）或者其他的interceptor的实现类
  >
  > 而sqlsessionTemplate所需要的springMapConfig已经用SqlSessionFactoryBean创建的SqlSessionFactory中了，sqlsessionTemplate会将SqlSessionFactory注入其中，便可以拥有需要的Dao层信息和需要的sql语句等了
  >
  > > 在spring 1.x到2.x中我们可以使用4种配置的方式在IoC容器的配置文件种指定事务需要的元数据
  > >
  > > 1. 使用ProxyFactory（ProxyFactoryBean）+TransactionIntercepter
  > >
  > >    > ```
  > >    > <!-- TransactionInterceptor -->//拦截我们需要的service方法
  > >    > <bean id="transactionInterceptor"
  > >    >   class="org.springframework.transaction.interceptor.TransactionInterceptor">
  > >    >   <property name="transactionManager">
  > >    >       <ref bean="transactionManager" />
  > >    >   </property>
  > >    >   <property name="transactionAttributeSource">
  > >    >       <value>
  > >    >           org.springframework.prospring.ticket.service.PaymentService.transfer=PROPAGATION_REQUIRED,ISOLATION_SERIALIZABLE,timeout_30,-Exception
  > >    >       </value>
  > >    >   </property>
  > >    > </bean>
  > >    > <!-- Business Object -->
  > >    > <bean id="paymentServiceTarget"
  > >    >   class="org.springframework.prospring.ticket.service.PaymentServiceImpl">
  > >    >   <property name="paymentDao">
  > >    >       <ref local="paymentDao" />
  > >    >   </property>
  > >    > </bean>
  > >    > 
  > >    > // 将需要的框架Template/相应的dao实现，与Service层接口以及transactionInterceptor集成一个Bean方便Client调用
  > >    > 
  > >    > <!-- Transactional proxy for the primary business object -->
  > >    > <bean id="paymentService" class="org.springframework.aop.framework.ProxyFactoryBean">
  > >    >   <property name="target">
  > >    >       <ref local="paymentServiceTarget" />
  > >    >   </property>
  > >    >   <property name="proxyInterfaces">
  > >    >       <value>org.springframework.prospring.ticket.service.PaymentService
  > >    >       </value>
  > >    >   </property>
  > >    >   <property name="interceptorNames">
  > >    >     <list>
  > >    >       <value>transactionInterceptor</value>
  > >    >     </list>
  > >    >   </property>
  > >    > </bean>
  > >    > 
  > >    > 
  > >    > ```
  > >
  > > 2. 使用“一站式”的TransactionProxyFactoryBean
  > >
  > > 3. 使用BeanNameAutoProxyCreater
  > >
  > > 4. 使用Spring2.x的声明事务配置方式（我们使用的）
  > >
  > > ```
  > > <context:component-scan base-package="cn.itcast">
  > >     <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
  > > </context:component-scan>
  > > 
  > > <bean id="comboPooledDataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
  > >     <property name="driverClass" value="com.mysql.jdbc.Driver"/>
  > >     <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/ssm"/>
  > >     <property name="user" value="root"/>
  > >     <property name="password" value="123456"/>
  > > </bean>
  > > 
  > > <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
  > >     <property name="dataSource" ref="comboPooledDataSource"/>
  > > </bean>
  > > 
  > > <bean id="mapperScannerConfigurer" class="org.mybatis.spring.mapper.MapperScannerConfigurer">
  > >     <property name="basePackage" value="cn.itcast.dao"/>
  > > </bean>
  > > 
  > > <bean id="dataSourceTransactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
  > >     <property name="dataSource" ref="comboPooledDataSource"></property>
  > > </bean>
  > > 
  > > // <tx:advice>专门为声明事务Advice而设置的配置元素，底层还是TransactionInterceptor
  > > 		若不指定<tx:attributes>，则采用DefaultTransactionDefinition
  > > 
  > > <tx:advice id="txAdvice" transaction-manager="dataSourceTransactionManager">
  > >     <tx:attributes>
  > >         <tx:method name="find*" read-only="true"/>
  > >         <tx:method name="*" isolation="DEFAULT"/>
  > >     </tx:attributes>
  > > </tx:advice>
  > > 
  > > 
  > > // 作用如同ProxyFactoryBean一直，不过使用了aop实现，只需指定ServiceTarget的class，之后的便依靠动态代理实现
  > > 
  > > <aop:config>
  > >     <aop:advisor advice-ref="txAdvice" pointcut="execution(* cn.itcast.service.impl.*ServiceImpl.*(..))"/>
  > > </aop:config>
  > > 
  > > //这个实现使得我们在MVC层调用的service对象已经是经过代理实现了的，我们可以直接用其中的方法，底层都会依据流程自己完成，我们会发现，我们没有自己操作commit的空间，他会在你事务声明的方法结束时就commit，你可以把很多业务代码放在一个事务service方法中实现同一事务，但若是不想则可以编码式事务控制。
  > > ```

----------



#### Spring的事务处理

* 我们一般通过TransactionTemplate来对PlatformTransactionManager的事务进行封装，使得出现异常需要callback时，将其依据callback接口的实现可以在TransactionTemplate类的excute方法中便处理成功，我们就只需要**注意call back接口的设计与实现**，就能将PlatformTransactionManager的callback都由该模板实现，**减少重复代码的编写**。当然小型测试直接用也可以。



* Spring的事务处理中，通用的事务处理流程是由抽象事务管理器AbstractPlatformTransactionManager来提供的，而具体的底层事务处理实现，由PlatformTransactionManager的具体实现类来实现，如 DataSourceTransactionManager 、JtaTransactionManager和 HibernateTransactionManager等。（**我们通常使用的是DataSourceTransactionManager来与mybatis集成**）

* spring事务处理的一个关键是保证在整个事务的生命周期里所有执行sql的jdbc connection和处理事务的jdbc connection始终是同一个（不然事务通过不同connection commit的时候可能会相互覆盖，顺序也难以确定）。然后执行sql的业务代码一般都分散在程序的不同地方，如何让它们共享一个jdbc connection呢？

  

* 这里spring做了一个前提假设：即一个事务的操作一定是在一个thread中执行，在事务未结束前该事务一直存在于该thread中，且一个thread中如果有多个事务在不同的jdbc connection的话，他们必须顺序执行（在上一个结束的情况下），**不能同时存在**，此时若是将connection也绑定到线程中，那（这个假设在绝大多数情况下都是成立的，mybatis自身的事务处理中，sqlsession也可以多次执行commit，connection的获取是在sqlsession执行sql时进行的，因此sqlsession也可有多个不同jdbc connection生成的事务，必须顺序执行）。

  

* 基于这个假设，spring在transaction创建时，会用ThreadLocal把创建这个事务的jdbc connection绑定到当前thread，接下来在事务的整个生命周期中都会从ThreadLocal中获取同一个jdbc connection（只要没有主动断开connection）。

  

* Spring本身的事务流程（配合JdbcTemplate）


![143421_Bmpa_1452390.png](https://static.oschina.net/uploads/space/2016/1110/143421_Bmpa_1452390.png)

* **从上面可以看出，Spring事务管理一共可分为三个步骤，分别是初始化事务、提交事务、回滚事务，spring事务工作流相当于为用户屏蔽了具体orm框架的底层处理逻辑，基于spring开发的程序，即便更换了orm框架也是跟换了调用的sqlTemplate，我们的事务管理器更换成适合于他的便可，基本不用改变其他的实现。这是Spring的优点**

  

* Spring控制datasourcetransaction ，mybaitis用springManagedTransaction对数据库进行sql操作，但commit等都是最后由datasourceTransaction 进行.

  
  
###### SqlSessionFactoryBean

* 再看这个容器中的参数，**无论如何我们都需要SqlSessionFactoryBean（无论是否有用Spring的事务管理都需要）和DataSourceTransactionManager**

  

* 由SqlSessionFactoryBean创建工厂，此工厂将**生成SpringManagedTransactionFactory**（可能有人会好奇为什么要大费周章重新实现一个TransactionFactory，到下面进入sqlsession的部分就可以知道答案了），再封装成 Environment 对象。最后封装成configuration对象来调用sqlSessionFactoryBuilder.build(configuration);

> 需要注意的是 SqlSessionFactoryBean 实现了 Spring 的 FactoryBean 接口。这意味着由 Spring 最终创建的 bean 并不是 SqlSessionFactoryBean 本身，而是工厂类（SqlSessionFactoryBean）的 getObject() 方法的返回结果。这种情况下，Spring 将会在应用启动时为你创建 SqlSessionFactory，并使用 sqlSessionFactory 这个名字存储起来。因为是spring容器生成的，所以xml解析时对实现这个接口的对象会自动调用getObject() 方法
>
> 等效的 Java 代码如下：
>
> ```java
> @Bean
> public SqlSessionFactory sqlSessionFactory() {
>   SqlSessionFactoryBean factoryBean = new SqlSessionFactoryBean();
>   factoryBean.setDataSource(dataSource());
>   return factoryBean.getObject();
> }
> ```

* 生成sqlsqlSessionFactory，由XMLConfigBuilder读取parse成一个sqlsqlSessionFactory，**交由SqlsessionTemplate使用**（其实现了 `SqlSession` 接口，并不直接调用具体的 `SqlSession` 的方法，而是委托给一个动态代理，通过代理 `SqlSessionInterceptor` 对方法调用进行拦截，用调用接口的方法时，会去寻找是否有了停留在resources中的未关闭的sqlsession）

  >  若是编程式事务控制的话我们应该先创建SqlSessionFactoryBean创建sqlsqlSessionFactory，再将其注入自己创建的SqlsessionTemplate对象中，再利用SqlsessionTemplate进行增删查改

  ```
  public class SqlSessionFactoryBean implements FactoryBean<SqlSessionFactory>, InitializingBean, ApplicationListener<ApplicationEvent> {
  
  protected SqlSessionFactory buildSqlSessionFactory() throws IOException {
      XMLConfigBuilder xmlConfigBuilder = null;
      Configuration configuration;
      ···
      各种configuration的参数判断
      ····
       //生成SpringManagedTransactionFactory
      if (this.transactionFactory == null) {
          this.transactionFactory = new SpringManagedTransactionFactory();
         
      }
      
  	//封装成 Environment 对象
  	//封装成configuration对象
      configuration.setEnvironment(new Environment(this.environment, this.transactionFactory, this.dataSource));
  	···
  	参数检查
  	···
  
  	//调用sqlSessionFactoryBuilder.build(configuration);生成sqlsqlSessionFactory，
      return this.sqlSessionFactoryBuilder.build(configuration);
  }
  }
  ```



###### DataSourceTransactionManager

* 若要使用spring的事务，使用sqlsessionTemplate前，我们要分析DataSourceTransactionManager整个流程，我们需要一步一步来，从最顶层开始



* Spring的PlatformTransactionManager是事务管理的顶层接口，其中定义的三个方法对应的就是初始化事务、提交事务、回滚事务，，然后AbstractPlatformTransactionManager抽象类（作为模板）给出了三个步骤的具体实现。



* **但对诸如doGetTransaction()之类的和doBegin等还是弄成了抽象方法由DataSourceTransactionManager等实现，以便自己决定对 TransactionSynchronization接口的实现类进行调用，自己实现线程安全、获得ConnectionHolder和创建新事务时按何种方式dobgin，连接方式和注入各种参数。**

> ```
> public abstract class AbstractPlatformTransactionManager implements PlatformTransactionManager, Serializable {
> 
> public final TransactionzhuangtStatus getTransaction(@Nullable TransactionDefinition definition) throws TransactionException {
> 
> //在DataSourceTransactionManager中重写了的类，查询当前线程中是否有传入的datasource对应的ConnectionHolder，有则依据他创建transaction
> 
> Object transaction = this.doGetTransaction();
> boolean debugEnabled = this.logger.isDebugEnabled();
> if (definition == null) {
>   definition = new DefaultTransactionDefinition();
> }
> 
> if (this.isExistingTransaction(transaction)) {
>   return this.handleExistingTransaction((TransactionDefinition)definition, transaction, debugEnabled);
> }
> ···
> 各种条件判断
> ···
>   try {
>       boolean newSynchronization = this.getTransactionSynchronization() != 2;
>       
>       //被创建的事务状态对象类型是DefaultTransactionStatus，它持有上述创建的事务对象。事务状态对象主要用于获取当前事务对象的状态，比如事务是否被标记了回滚，是否是一个新事务等等。
>       DefaultTransactionStatus status = this.newTransactionStatus((TransactionDefinition)definition, transaction, true, newSynchronization, debugEnabled, suspendedResources);
>       
>       //事务开始处理，进行促使化，连接获取
>       this.doBegin(transaction, (TransactionDefinition)definition);
>       
>       //对象都交由TransactionSynchronizationManager管理，TransactionSynchronizationManager把这些对象都保存在ThreadLocal中。在该transaction未commit/关闭前，该线程就会与该connection一直绑定在一起，通过只能同时绑定一个connection的原理，实现事务管理。
>       
>       this.prepareSynchronization(status, (TransactionDefinition)definition);
>       
>       返回事务状态对象：（主要进行：查询事务状态、通过setRollbackOnly（）方法标记当前事务使其回滚，根据事务参数创建内嵌事务），statu的意思是方便先对事务进行判断再获取transaction进行操作。
>       return status;
>   } catch (Error | RuntimeException var7) {
>       this.resume((Object)null, suspendedResources);
>       throw var7;
>   }
> }
> }
> }
> ```

* DataSourceTransactionManager（继承了AbstractPlatformTransactionManager）的主要作用是创建transaction和对transaction的初始化

> ```
> public class DataSourceTransactionManager extends AbstractPlatformTransactionManager
> 		implements ResourceTransactionManager, InitializingBean {
> 
> @Override//主要增加了对TransactionSynchronizationManager中当前线程是否有connectionHolder
> 	protected Object doGetTransaction() {
> 	
> 		// 创建事务对象
> 		
> 		DataSourceTransactionObject txObject = new DataSourceTransactionObject();
> 		txObject.setSavepointAllowed(isNestedTransactionAllowed());
> 		ConnectionHolder conHolder =
> 				(ConnectionHolder) TransactionSynchronizationManager.getResource(obtainDataSource());
> 		//ConnectionHolder赋值给事务对象txObject
> 		txObject.setConnectionHolder(conHolder, false);
> 		return txObject;		
> 		}
> }
> 
> //处理事务开始的方法  
>     protected void doBegin(Object transaction, TransactionDefinition definition) {  
>         DataSourceTransactionObject txObject = (DataSourceTransactionObject) transaction;  
>         Connection con = null;  
>         try {  
>             //如果数据源事务对象的ConnectionHolder为null或者是事务同步的  
>             if (txObject.getConnectionHolder() == null ||  
>         txObject.getConnectionHolder().isSynchronizedWithTransaction()) {  
>                 //获取当前数据源的数据库连接  
>                 Connection newCon = this.dataSource.getConnection();  
>                 if (logger.isDebugEnabled()) {  
>                     logger.debug("Acquired Connection [" + newCon + "] for JDBC transaction");  
>                 }  
>                 //为数据源事务对象设置ConnectionHolder  
>                 txObject.setConnectionHolder(new ConnectionHolder(newCon), true);  
>             }  
>     //设置数据源事务对象的事务同步    txObject.getConnectionHolder().setSynchronizedWithTransaction(true);  
>             //获取数据源事务对象的数据库连接  
>             con = txObject.getConnectionHolder().getConnection();  
>             //根据数据连接和事务属性，获取数据库连接的事务隔离级别  
>             Integer previousIsolationLevel = DataSourceUtils.prepareConnectionForTransaction(con, definition);  
>     //为数据源事务对象设置事务隔离级别  
>     txObject.setPreviousIsolationLevel(previousIsolationLevel);  
>             //如果数据库连接设置了自动事务提交属性，则关闭自动提交  
>             if (con.getAutoCommit()) {  
>                 //保存数据库连接设置的自动连接到数据源事务对象中  
>                 txObject.setMustRestoreAutoCommit(true);  
>                 if (logger.isDebugEnabled()) {  
>                     logger.debug("Switching JDBC Connection [" + con + "] to manual commit");  
>                 }  
>                 //设置数据库连接自动事务提交属性为false，即禁止自动事务提交  
>                 con.setAutoCommit(false);  
>             }  
>             //激活当前数据源事务对象的事务配置  
>             txObject.getConnectionHolder().setTransactionActive(true);  
>             //获取事务配置的超时时长  
> int timeout = determineTimeout(definition);  
> //如果事务配置的超时时长不等于事务的默认超时时长  
>             if (timeout != TransactionDefinition.TIMEOUT_DEFAULT) {  
>         //数据源事务对象设置超时时长  
>         txObject.getConnectionHolder().setTimeoutInSeconds(timeout);  
>             }  
>             //把当前数据库Connection和线程ThreadLocal绑定（key为DataSource，value为getConnectionHolder）spring事务管理的关键一步
>             if (txObject.isNewConnectionHolder()) {  
>         TransactionSynchronizationManager.bindResource(getDataSource(), txObject.getConnectionHolder());  
>             }  
>         }  
>         catch (Exception ex) {  
>             DataSourceUtils.releaseConnection(con, this.dataSource);  
>             throw new CannotCreateTransactionException("Could not open JDBC Connection for transaction", ex);  
>         }  
>     }  
> ```

###### TransactionSynchronizationManager

* 主要从线程中根据键值获取资源

```
public abstract class TransactionSynchronizationManager {
    private static final Log logger = LogFactory.getLog(TransactionSynchronizationManager.class);
    private static final ThreadLocal<Map<Object, Object>> resources = new NamedThreadLocal("Transactional resources");
    private static final ThreadLocal<Set<TransactionSynchronization>> synchronizations = new NamedThreadLocal("Transaction synchronizations");
    private static final ThreadLocal<String> currentTransactionName = new NamedThreadLocal("Current transaction name");
    private static final ThreadLocal<Boolean> currentTransactionReadOnly = new NamedThreadLocal("Current transaction read-only status");
    private static final ThreadLocal<Integer> currentTransactionIsolationLevel = new NamedThreadLocal("Current transaction isolation level");
    private static final ThreadLocal<Boolean> actualTransactionActive = new NamedThreadLocal("Actual transaction active");
    
     //获取相应的资源，键值先unwrapResourceIfNecessary处理，交由doget获取
     public static Object getResource(Object key) {
        Object actualKey = TransactionSynchronizationUtils.unwrapResourceIfNecessary(key);
        Object value = doGetResource(actualKey);
        if (value != null && logger.isTraceEnabled()) {
            logger.trace("Retrieved value [" + value + "] for key [" + actualKey + "] bound to thread [" + Thread.currentThread().getName() + "]");
        }

        return value;
    }
	
	//从保存在线程的resource获取
    @Nullable
    private static Object doGetResource(Object actualKey) {
        Map<Object, Object> map = (Map)resources.get();
        if (map == null) {
            return null;
        } else {
            Object value = map.get(actualKey);
            if (value instanceof ResourceHolder && ((ResourceHolder)value).isVoid()) {
                map.remove(actualKey);
                if (map.isEmpty()) {
                    resources.remove();
                }

                value = null;
            }

            return value;
        }
    }

    public static void bindResource(Object key, Object value) throws IllegalStateException {
        Object actualKey = TransactionSynchronizationUtils.unwrapResourceIfNecessary(key);
        Assert.notNull(value, "Value must not be null");
        Map<Object, Object> map = (Map)resources.get();
        if (map == null) {
            map = new HashMap();
            resources.set(map);
        }

        Object oldValue = ((Map)map).put(actualKey, value);
        if (oldValue instanceof ResourceHolder && ((ResourceHolder)oldValue).isVoid()) {
            oldValue = null;
        }

        if (oldValue != null) {
            throw new IllegalStateException("Already value [" + oldValue + "] for key [" + actualKey + "] bound to thread [" + Thread.currentThread().getName() + "]");
        } else {
            if (logger.isTraceEnabled()) {
                logger.trace("Bound value [" + value + "] for key [" + actualKey + "] to thread [" + Thread.currentThread().getName() + "]");
            }

        }
    }
```

* **此时若是注释运行的便需要把PlatformTransactionManager，他生成的TransactionStatus、TransactionAttribute等封装到TransactionInfo中以便注解能自己调用**

----------

#### Spring+mybatis

* 完成了对事务的创建，并将其用TransactionSynchronizationManager绑定到了thread local上，接着便是对sqlsessionTemplate的调用了即对mybatis框架的sqlsession的整合了

* Spring+mybatis的事务控制流程

![img](http://static.oschina.net/uploads/space/2016/1110/143554_iORI_1452390.png)



#### SqlsessionTemplate

* SqlsessionTemplate的使用并不是直接用sqlsession进行增删查改（实际上是sqlsession的一个代理类，其实现了 `SqlSession` 接口，并不直接调用具体的 `SqlSession` 的方法，而是委托给一个动态代理，通过代理 `SqlSessionInterceptor` 对方法调用进行拦截，用调用接口的方法时，会去寻找是否有了停留在resources中的未关闭的sqlsession）

  

```
public class SqlSessionTemplate implements SqlSession, DisposableBean {
public SqlSessionTemplate(SqlSessionFactory sqlSessionFactory, ExecutorType executorType, PersistenceExceptionTranslator exceptionTranslator) {
    Assert.notNull(sqlSessionFactory, "Property 'sqlSessionFactory' is required");
    Assert.notNull(executorType, "Property 'executorType' is required");
    this.sqlSessionFactory = sqlSessionFactory;
    this.executorType = executorType;
    this.exceptionTranslator = exceptionTranslator;
    this.sqlSessionProxy = (SqlSession)Proxy.newProxyInstance(SqlSessionFactory.class.getClassLoader(), new Class[]{SqlSession.class}, new SqlSessionTemplate.SqlSessionInterceptor());
}



// 动态代理中的 InvocationHandler类，真正对方法的前后进行通知的类，代理类对象只是提供一个目标对象的封装以调用而已
 private class SqlSessionInterceptor implements InvocationHandler {
        private SqlSessionInterceptor() {
        }

        public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        
            //查看是否有为关闭的闲置的同个配置的sqlsession，有则调用，这也是你用sqlsessionTemplate代理执行增删查改一直是同一个sqlsession完成事务管理的原因，没有则生成。我们可以看到是由SqlSessionUtils实现的
            
    SqlSession sqlSession = SqlSessionUtils.getSqlSession(SqlSessionTemplate.this.sqlSessionFactory, SqlSessionTemplate.this.executorType, SqlSessionTemplate.this.exceptionTranslator);

            Object unwrapped;
            try {
                Object result = method.invoke(sqlSession, args);
                if (!SqlSessionUtils.isSqlSessionTransactional(sqlSession, SqlSessionTemplate.this.sqlSessionFactory)) {
                //查询该sqlsession是否在Spring事务中，若是则步不自动提交
                //若是自动则为一条sql一次commit，是为新手设置的，一般手动提交。
                    sqlSession.commit(true);
                }

                unwrapped = result;
            } catch (Throwable var11) {
                unwrapped = ExceptionUtil.unwrapThrowable(var11);
                if (SqlSessionTemplate.this.exceptionTranslator != null && unwrapped instanceof PersistenceException) {
                    SqlSessionUtils.closeSqlSession(sqlSession, SqlSessionTemplate.this.sqlSessionFactory);
                    sqlSession = null;
                    Throwable translated = SqlSessionTemplate.this.exceptionTranslator.translateExceptionIfPossible((PersistenceException)unwrapped);
                    if (translated != null) {
                        unwrapped = translated;
                    }
                }

                throw (Throwable)unwrapped;
            } finally {
            
            //关闭sqlsession，同一个事务可以有多个sqlsession
            
                if (sqlSession != null) {
                    SqlSessionUtils.closeSqlSession(sqlSession, SqlSessionTemplate.this.sqlSessionFactory);
                }

            }

            return unwrapped;
        }
    }
}
```



#### SqlSessionUtils

* 我们来看下SqlSessionUtils的实现，这里的sqlsession的创建需要注意**transactionFactory是SpringManageTransactionFactory。**

  

```
public final class SqlSessionUtils {
	    public static SqlSession getSqlSession(SqlSessionFactory sessionFactory, ExecutorType executorType, PersistenceExceptionTranslator exceptionTranslator) {
        Assert.notNull(sessionFactory, "No SqlSessionFactory specified");
        Assert.notNull(executorType, "No ExecutorType specified");
        
        //有相应的sqlsessionHolder则取出里面的SqlSession
        
        SqlSessionHolder holder = (SqlSessionHolder)TransactionSynchronizationManager.getResource(sessionFactory);
        SqlSession session = sessionHolder(executorType, holder);
        if (session != null) {
            return session;
        } else {
            if (LOGGER.isDebugEnabled()) {
                LOGGER.debug("Creating a new SqlSession");
            }
	
			//没有则用工厂创建sqlsession，注意：此时的sqlsessionfactory是sqlsessionfactoryBean创建的，即他的configuration中的Enviroment中的transactionFactory是SpringManageTransactionFactory。
			
            session = sessionFactory.openSession(executorType);
            
           // 注册sqlsession到TransactionSyncronizedMager，
           
           
            registerSessionHolder(sessionFactory, executorType, exceptionTranslator, session);
            return session;
        }
    }
}
```



* 关于SqlSessionUtils中的registerSessionHolder方法，注册sqlsession到TransactionSyncronizedMager中，

* **包含两个方法**

  > **bindResource(sessionFactory, holder);
  >  registerSynchronization(new SqlSessionUtils.SqlSessionSynchronization(holder, sessionFactory))；**

```
private static void registerSessionHolder(SqlSessionFactory sessionFactory, ExecutorType executorType, PersistenceExceptionTranslator exceptionTranslator, SqlSession session) {
    if (TransactionSynchronizationManager.isSynchronizationActive()) {
    
    	//从sessionFactory中获取Environment
    	
        Environment environment = sessionFactory.getConfiguration().getEnvironment();
        
        //判断environment中封装的TransactionFactorySpringManagedTransactionFactory，是否用到了Spring的事务管理服务，sqlsession中对TransactionSyncronizedMager的应用只有与Spring集成时才用到，mybatis自身的事务管理并不会用到。
        
        if (environment.getTransactionFactory() instanceof SpringManagedTransactionFactory) {
            if (LOGGER.isDebugEnabled()) {
                LOGGER.debug("Registering transaction synchronization for SqlSession [" + session + "]");
            }

            SqlSessionHolder holder = new SqlSessionHolder(session, executorType, exceptionTranslator);
            
            //绑定、注册到TransactionSynchronizationManager
            TransactionSynchronizationManager.bindResource(sessionFactory, holder);
            TransactionSynchronizationManager.registerSynchronization(new SqlSessionUtils.SqlSessionSynchronization(holder, sessionFactory));
            
            holder.setSynchronizedWithTransaction(true);
            holder.requested();
        } else {
            if (TransactionSynchronizationManager.getResource(environment.getDataSource()) != null) {
                throw new TransientDataAccessResourceException("SqlSessionFactory must be using a SpringManagedTransactionFactory in order to use Spring transaction synchronization");
            }

            if (LOGGER.isDebugEnabled()) {
                LOGGER.debug("SqlSession [" + session + "] was not registered for synchronization because DataSource is not transactional");
            }
        }
    } else if (LOGGER.isDebugEnabled()) {
        LOGGER.debug("SqlSession [" + session + "] was not registered for synchronization because synchronization is not active");
    }

}
```

#### SqlSessionSynchronization

* 其中SqlSessionSynchronization是一个事务生命周期的callback接口，mybatis-spring通过SqlSessionSynchronization在事务提交和回滚前分别调用DefaultSqlSession.commit()和DefaultSqlSession.rollback()

  ```
private static final class SqlSessionSynchronization extends TransactionSynchronizationAdapter {
  public void beforeCommit(boolean readOnly) {
              if (TransactionSynchronizationManager.isActualTransactionActive()) {
                  try {
                      if (SqlSessionUtils.LOGGER.isDebugEnabled()) {
                          SqlSessionUtils.LOGGER.debug("Transaction synchronization committing SqlSession [" + this.holder.getSqlSession() + "]");
                      }
  
                      this.holder.getSqlSession().commit();
                  } catch (PersistenceException var4) {
                      if (this.holder.getPersistenceExceptionTranslator() != null) {
                          DataAccessException translated = this.holder.getPersistenceExceptionTranslator().translateExceptionIfPossible(var4);
                          if (translated != null) {
                              throw translated;
                          }
                      }
                      throw var4;
                  }
              }
  
          }
  
          public void beforeCompletion() {
              if (!this.holder.isOpen()) {
                  if (SqlSessionUtils.LOGGER.isDebugEnabled()) {
                      SqlSessionUtils.LOGGER.debug("Transaction synchronization deregistering SqlSession [" + this.holder.getSqlSession() + "]");
                  }
  
                  TransactionSynchronizationManager.unbindResource(this.sessionFactory);
                  this.holderActive = false;
                  if (SqlSessionUtils.LOGGER.isDebugEnabled()) {
                      SqlSessionUtils.LOGGER.debug("Transaction synchronization closing SqlSession [" + this.holder.getSqlSession() + "]");
                  }
  
                  this.holder.getSqlSession().close();
              }
  
          }
  
          public void afterCompletion(int status) {
              if (this.holderActive) {
                  if (SqlSessionUtils.LOGGER.isDebugEnabled()) {
                      SqlSessionUtils.LOGGER.debug("Transaction synchronization deregistering SqlSession [" + this.holder.getSqlSession() + "]");
                  }
  
                  TransactionSynchronizationManager.unbindResourceIfPossible(this.sessionFactory);
                  this.holderActive = false;
                  if (SqlSessionUtils.LOGGER.isDebugEnabled()) {
                      SqlSessionUtils.LOGGER.debug("Transaction synchronization closing SqlSession [" + this.holder.getSqlSession() + "]");
                  }
  
                  this.holder.getSqlSession().close();
              }
  
              this.holder.reset();
          }
  ```

####  datasourceUtils和SpringManagedTransaction

* 一番周折终于拿到了sqlsession到了代理类SqlsessionTemplate中，接着便是目标代码本身(sql语句)的执行了
* 在此之前我们谈及为何要专门用SpringManagedTransaction，现在就要揭晓了，之前我们创建了sqlsession到了SpringTemplate，但是还未获取connection，所以我们执行语句前就要获取connection，我们用mybatis的Executor执行语句，会在prepareStatement中获得connection

```
protected Connection getConnection(Log statementLog) throws SQLException {
    Connection connection = this.transaction.getConnection();
    return statementLog.isDebugEnabled() ? ConnectionLogger.newInstance(connection, statementLog, this.queryStack) : connection;
}
```

* 看到上面的transsaction便可以知道我们需要一个transaction来实现连接的获取，但是我们的连接是之前绑定到了ThreadLocal中了，我们Spring事务管理的关键要让此时Thread中的connection用上，这时候我们就需要用到DataSourceUtils来获取了	

  

* **datasourceUtils:也是从TransactionSynchronizationManager获取connection**

  

* SqlSessionUtils从TransactionSynchronizationManager获取ConnectionHolder交给SpringManagedTransaction，并作为参数封装进Executor，最后封装进DefaultSqlSession，**将Spring的事务管理与它的数据访问框架是紧密结合的**

* **SpringManagedTransaction中保留由datasource等信息，而且它特别实现了对SqlSessionUtils的调用（其他的jdbcTransaction和managedTransaction都没有对SqlSessionUtils调用的方法，这两者只适用于mybaitis自身事务情况，不适用于集成）**

* **并且可以用来给datasourceUtils提供参数，让其可以直接从TransactionSynchronizationManager中调用相应的connection，因为是同一线程且事务没结束所以能保证是同一个了**



* SpringManagedTransaction的意义便在此，我们的问题也就解决了，最后便是jdbc.connection的excute操作了

> ```
> public abstract class DataSourceUtils {
> public static Connection doGetConnection(DataSource dataSource) throws SQLException {
> Assert.notNull(dataSource, "No DataSource specified");
> 
> ConnectionHolder conHolder = (ConnectionHolder) TransactionSynchronizationManager.getResource(dataSource);
> if (conHolder != null && (conHolder.hasConnection() || conHolder.isSynchronizedWithTransaction())) {
>    conHolder.requested();
>    if (!conHolder.hasConnection()) {
>       logger.debug("Fetching resumed JDBC Connection from DataSource");
>       conHolder.setConnection(fetchConnection(dataSource));
>    }
>    return conHolder.getConnection();
> }
> // Else we either got no holder or an empty thread-bound holder here.
> 
> logger.debug("Fetching JDBC Connection from DataSource");
> Connection con = fetchConnection(dataSource);
> 
> if (TransactionSynchronizationManager.isSynchronizationActive()) {
>    logger.debug("Registering transaction synchronization for JDBC Connection");
>    // Use same Connection for further JDBC actions within the transaction.
>    // Thread-bound object will get removed by synchronization at transaction completion.
>    ConnectionHolder holderToUse = conHolder;
>    if (holderToUse == null) {
>       holderToUse = new ConnectionHolder(con);
>    }
>    else {
>       holderToUse.setConnection(con);
>    }
>    holderToUse.requested();
>    TransactionSynchronizationManager.registerSynchronization(
>          new ConnectionSynchronization(holderToUse, dataSource));
>    holderToUse.setSynchronizedWithTransaction(true);
>    if (holderToUse != conHolder) {
>       TransactionSynchronizationManager.bindResource(dataSource, holderToUse);
>    }
> }
> return con;
> }
> ```



* 可以看到**mybatis-spring处理事务的主要流程和spring jdbc处理事务并没有什么区别，**都是通过DataSourceTransactionManager的getTransaction(), rollback(), commit()完成事务的生命周期管理，而且jdbc connection的创建也是通过DataSourceTransactionManager.getTransaction()完成，mybatis并没有参与其中，mybatis只是在执行sql时通过DataSourceUtils.getConnection()获得当前thread的jdbc connection，然后在其上执行sql。

#### commit

* **最后写一下commit的流程**（rollback实现差不多，回到保存的回调点而已）



* 调用DataSourceTransactionManager的父类AbstractPlatformTransactionManager的commit

```
public abstract class AbstractPlatformTransactionManager implements PlatformTransactionManager, Serializable {
 
 public final void commit(TransactionStatus status) throws TransactionException {
     ···
     status 是否已完成或是否需要rollback
    ···    
        //正常流程会调用processCommit         
            this.processCommit(defStatus);
        }   
        }
        }
  }
​```


```

```
private void processCommit(DefaultTransactionStatus status) throws TransactionException {
    try {
        boolean beforeCompletionInvoked = false;

        try {
            boolean unexpectedRollback = false;         
            //commit准备      
            this.prepareForCommit(status);
            
            //BeforeCommit：commit前对springTransaciton、sqlsession的commit，大部分未实现这个的功能
            
            this.triggerBeforeCommit(status);
            
            //BeforeCompletion：Complete前对springTransaciton、sqlsession的commit
            
            this.triggerBeforeCompletion(status);
            beforeCompletionInvoked = true;
            ···
            是否有记录回调点/是否需要回调
            ···
            // 在DataSourceTransactionManager中实现对connection的commit
            this.doCommit(status);

            ···          
           异常处理
            ···
    
        try {
        	
        	//对TransactionSynchronizationManager中的synchronizations用Iterator计数器遍历删除
            this.triggerAfterCommit(status);
            
        } finally {
        
        // 若上一步没清理完成，则再次清理一次，用synchronizations用Iterator计数器遍历删除
            this.triggerAfterCompletion(status, 0);
            
        }
    } finally {
    
        // 清空TransactionSynchronizationManager，若有挂起的事务，则恢复执行
        this.cleanupAfterCompletion(status);
    }

}
```

```
// 清空TransactionSynchronizationManager，若有挂起的事务，则恢复执行，这和definiton设置的事务级别有关
private void cleanupAfterCompletion(DefaultTransactionStatus status) {
        status.setCompleted();
        if (status.isNewSynchronization()) {
            TransactionSynchronizationManager.clear();
        }

        if (status.isNewTransaction()) {
            this.doCleanupAfterCompletion(status.getTransaction());
        }

        if (status.getSuspendedResources() != null) {
            if (status.isDebug()) {
                this.logger.debug("Resuming suspended transaction after completion of inner transaction");
            }

            Object transaction = status.hasTransaction() ? status.getTransaction() : null;
            
            //SuspendedResourcesHolder用静态方法放置在JVM的方法区，可以直接调用
            
            this.resume(transaction, (AbstractPlatformTransactionManager.SuspendedResourcesHolder)status.getSuspendedResources());
        }
```

* 这便是整个spring与mybatis集成的事务控制了。