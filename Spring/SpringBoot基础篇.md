

## 简介

#### 优点

- 快速创建独立运行的Spring项目以及与主流框架集成
- **使用嵌入式的Servlet容器，应用无需打成WAR包**
- starters自动依赖与版本控制
- 大量的自动配置，简化开发，也可修改默认值
- 无需配置XML，无代码生成，开箱即用
- 准生产环境的运行时应用监控
- 与云计算的天然集成

#### 集群、微服务、分布式

###### 概念

- 分布式：一个业务分拆多个子业务，部署在不同的服务器上
- 集群：同一个业务，部署在多个服务器上
- **微服务是一种架构风格，一个大型复杂软件应用由一个或多个微服务组成。系统中的各个微服务可被独立部署，各个微服务之间是松耦合的。每个微服务仅关注于完成一件任务并很好地完成该任务。在所有情况下，每个任务代表着一个小的业务能力。**(https://martinfowler.com/articles/microservices.html#MicroservicesAndSoa)

> 去饭店吃饭就是一个完整的业务，饭店的厨师、配菜师、传菜员、服务员就是分布式；厨师、配菜师、传菜员和服务员都不止一个人，这就是集群；分布式就是微服务的一种表现形式，分布式是部署层面，微服务是设计层面。

1：**分布式是指将不同的业务分布在不同的地方。而集群指的是将几台服务器集中在一起，实现同一业务。**

分布式中的每一个节点，都可以做集群。而集群并不一定就是分布式的。

举例：就比如新浪网，访问的人多了，他可以做一个群集，前面放一个响应服务器，后面几台服务器完成同一业务，如果有业务访问的时候，响应服务器看哪台服务器的负载不是很重，就将给哪一台去完成。

而分布式，从窄意上理解，也跟集群差不多，但是它的组织比较松散，不像集群，有一个组织性，**一台服务器垮了，其它的服务器可以顶上来。**



2：简单说，**分布式是以缩短单个任务的执行时间来提升效率的，而集群则是通过提高单位时间内执行的任务数来提升效率。**

例如：如果一个任务由 10 个子任务组成，每个子任务单独执行需 1 小时，则在一台服务器上执行该任务需 10 小时。

采用分布式方案，提供 10 台服务器，每台服务器只负责处理一个子任务，不考虑子任务间的依赖关系，执行完这个任务只需一个小时。(这种工作模式的一个典型代表就是 Hadoop 的 Map/Reduce 分布式计算模型）

而采用集群方案，同样提供 10 台服务器，每台服务器都能独立处理这个任务。假设有 10 个任务同时到达，10 个服务器将同时工作，1 小时后，10 个任务同时完成，这样，整身来看，还是 1 小时内完成一个任务！

###### 区别

* 分布式:每一个功能元素最终都是一个可独立替换和独立升级的软件单元；**分布式的每一个节点，都完成不同的业务，一个节点垮了，那这个业务就不可访问了。**

  ![img](https://img-blog.csdn.net/20170213134043567)

分布式将一个大的系统划分为多个业务模块，业务模块分别部署到不同的机器上，各个业务模块之间通过接口进行数据交互。区别分布式的方式是根据不同机器不同业务。

**上面：service A、B、C、D 分别是业务组件，通过API Geteway（HTTP）进行业务访问。**

注：分布式需要做好事务管理。

* 集群



![img](https://img-blog.csdn.net/20170213132417777)

集群模式是不同服务器部署同一套服务对外访问，实现服务的负载均衡。区别集群的方式是根据部署多台服务器业务是否相同。

注：集群模式需要做好session共享，确保在不同服务器切换的过程中不会因为没有获取到session而中止退出服务。

一般配置Nginx*的负载容器实现：静态资源缓存、Session共享可以附带实现，Nginx支持5000个并发量。

* 微服务

![img](https://img-blog.csdn.net/20170213133425286)

微服务的设计是为了不因为某个模块的升级和BUG影响现有的系统业务。微服务与分布式的细微差别是，微服务的应用不一定是分散在多个服务器上，他也可以是同一个服务器。

## Springboot POM

#### 版本仲裁中心

* spring-boot-starter-parent

```xml
<parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.1.RELEASE</version>
        <relativePath/>
</parent>
```

* spring-boot-starter-parent是spring-boot-starter的父项目，描述了构建过程中所导入的插件和对插件的执行方式，并引用了spring-boot-starter-parent的父文件spring-boot-dependencies(版本仲裁中心)

```xml
<?xml version="1.0" encoding="utf-8"?><project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-dependencies</artifactId>
    <version>2.2.6.RELEASE</version>
    <relativePath>../../spring-boot-dependencies</relativePath>
  </parent>
  <artifactId>spring-boot-starter-parent</artifactId>
  <packaging>pom</packaging>
  <name>Spring Boot Starter Parent</name>
  <description>Parent pom providing dependency and plugin management for applications
      built with Maven</description>
  <url>https://projects.spring.io/spring-boot/#/spring-boot-starter-parent</url>
  <properties>
    <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
    <java.version>1.8</java.version>
    <resource.delimiter>@</resource.delimiter>
    <maven.compiler.source>${java.version}</maven.compiler.source>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.target>${java.version}</maven.compiler.target>
  </properties>
  <build>
      ····
      ····
      ····
  </build>
   
```

* spring-boot-dependencies：描写了该文件的缘由和springboot文件的常用jar包和依赖等的版本

```xml
<?xml version="1.0" encoding="utf-8"?><project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-dependencies</artifactId>
  <version>2.2.6.RELEASE</version>
  <packaging>pom</packaging>
  <name>Spring Boot Dependencies</name>
  <description>Spring Boot Dependencies</description>
  <url>https://projects.spring.io/spring-boot/#</url>
  <licenses>
    <license>
      <name>Apache License, Version 2.0</name>
      <url>https://www.apache.org/licenses/LICENSE-2.0</url>
    </license>
  </licenses>
  <developers>
    <developer>
      <name>Pivotal</name>
      <email>info@pivotal.io</email>
      <organization>Pivotal Software, Inc.</organization>
      <organizationUrl>https://www.spring.io</organizationUrl>
    </developer>
  </developers>
  <scm>
    <url>https://github.com/spring-projects/spring-boot</url>
  </scm>
  <properties>
    <activemq.version>5.15.12</activemq.version>
    <antlr2.version>2.7.7</antlr2.version>
    <appengine-sdk.version>1.9.79</appengine-sdk.version>
    <artemis.version>2.10.1</artemis.version>
    <aspectj.version>1.9.5</aspectj.version>
    <assertj.version>3.13.2</assertj.version>
    <atomikos.version>4.0.6</atomikos.version>
    <awaitility.version>4.0.2</awaitility.version>
      ····
 </properties>
<dependencyManagement>
 <dependencies>
     <dependencies>
      <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot</artifactId>
        <version>2.2.6.RELEASE</version>
      </dependency>
      <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-test</artifactId>
        <version>2.2.6.RELEASE</version>
      </dependency>
         ····
         ···
         ··
  </dependencies>
</dependencyManagement>
```

#### 主程序/主入口类

```java
@SpringBootApplication
public class HelloWorldMainApplication {

    public static void main(String[] args) {
        //启动
        SpringApplication.run(HelloWorldMainApplication.class, args);
    }
}
```

`@SpringBootApplication`: Spring Boot应用标注在某个类上说明这个类是SpringBoot的主配置类，SpringBoot就应该运行这个类的main方法来启动SpringBoot应用；

看一下`@SpringBootApplication`这个注解类的源码

```java
@Target({ElementType.TYPE})    //可以给一个类型进行注解，比如类、接口、枚举
@Retention(RetentionPolicy.RUNTIME)    //可以保留到程序运行的时候，它会被加载进入到 JVM 中
@Documented    //将注解中的元素包含到 Javadoc 中去。
@Inherited    //继承，比如A类上有该注解，B类继承A类，B类就也拥有该注解

@SpringBootConfiguration

@EnableAutoConfiguration

/*
*创建一个配置类，在配置类上添加 @ComponentScan 注解。
*该注解默认会扫描该类所在的包下所有的配置类，相当于之前的 <context:component-scan>。
*/
@ComponentScan(
    excludeFilters = {@Filter(
    type = FilterType.CUSTOM,
    classes = {TypeExcludeFilter.class}
), @Filter(
    type = FilterType.CUSTOM,
    classes = {AutoConfigurationExcludeFilter.class}
)}
)
public @interface SpringBootApplication
```

- `@SpringBootConfiguration`：Spring Boot的配置类；标注在某个类上，表示这是一个Spring Boot的配置类；

  ```java
   @Target({ElementType.TYPE})
   @Retention(RetentionPolicy.RUNTIME)
   @Documented
   @Configuration
   public @interface SpringBootConfiguration
  ```

  - `@Configuration`：配置类上来标注这个注解；

    配置类 ----- 配置文件；配置类也是容器中的一个组件；@Component

    ```java
    @Target({ElementType.TYPE})
    @Retention(RetentionPolicy.RUNTIME)
    @Documented
    @Component
    public @interface Configuration 
    ```

- `@EnableAutoConfiguration`：开启自动配置功能；

  以前我们需要配置的东西，Spring Boot帮我们自动配置；@**EnableAutoConfiguration**告诉SpringBoot开启自动配置功能；这样自动配置才能生效；

  ```
   @Target({ElementType.TYPE})
   @Retention(RetentionPolicy.RUNTIME)
   @Documented
   @Inherited
   @AutoConfigurationPackage
   @Import({AutoConfigurationImportSelector.class})
   public @interface EnableAutoConfiguration
  ```

  - `@AutoConfigurationPackage`：自动配置包

    ```java
    @Target({ElementType.TYPE})
    @Retention(RetentionPolicy.RUNTIME)
    @Documented
    @Inherited
    @Import({Registrar.class})
    public @interface AutoConfigurationPackage
    ```

    - `@Import`：Spring的底层注解@Import，给容器中导入一个组件

      导入的组件由`org.springframework.boot.autoconfigure.AutoConfigurationPackages.Registrar`将主配置类（@SpringBootApplication标注的类）的所在包及下面所有子包里面的所有组件扫描到Spring容器；

      ![DEBUG](https://cdn.static.note.zzrfdsn.cn/images/springboot/assets/1573637120233.png)

      这里controller包是在主程序所在的包下，所以会被扫描到，我们在springboot包下创建一个test包，把主程序放在test包下，这样启动就只会去扫描test包下的内容而controller包就不会被扫描到，再访问开始的hello就是404

      ![DEBUG](https://cdn.static.note.zzrfdsn.cn/images/springboot/assets/1573637728857.png)

  - `@Import({AutoConfigurationImportSelector.class})`

    `AutoConfigurationImportSelector.class`将所有需要导入的组件以全类名的方式返回；这些组件就会被添加到容器中；会给容器中导入非常多的自动配置类（xxxAutoConfiguration）；就是给容器中导入这个场景需要的所有组件，并配置好这些组件；

    有了自动配置类，免去了我们手动编写配置注入功能组件等的工作；

    ![Configuration](https://cdn.static.note.zzrfdsn.cn/images/springboot/assets/1573638685562.png)

有了自动配置类，免去了我们手动编写配置注入功能组件等的工作；

 SpringFactoriesLoader.loadFactoryNames(EnableAutoConfiguration.class,classLoader)；

==Spring Boot在启动的时候从类路径下的META-INF/spring.factories中获取EnableAutoConfiguration指定的值，将这些值作为自动配置类导入到容器中，自动配置类就生效，帮我们进行自动配置工作；==以前我们需要自己配置的东西，自动配置类都帮我们；

J2EE的整体整合解决方案和自动配置都在spring-boot-autoconfigure-1.5.9.RELEASE.jar；

* 启用的服务都会到sprng-boot和springboot-autoconfigure两个jar包中读取，其他starterjar包中并没有类，他们只是起到一个配置和通知的作用，本身并不存储相关的实现类，使得spring-autoconfigure jar包中的Bean被自动加载

#### 使用Spring Initializer快速创建Spring Boot项目

IDE都支持使用Spring的项目创建向导快速创建一个Spring Boot项目；

选择我们需要的模块；

向导会联网创建Spring Boot项目；（联网才能加载我们勾选的组件）

## 配置文件

SpringBoot使用一个全局的配置文件，配置文件名`application`是固定的；

作用：修改SpringBoot自动配置的默认值；SpringBoot在底层都给我们自动配置好；

- application.properties
- application.yml
- application.yaml

#### YAML

YAML（YAML Ain't Markup Language）

 YAML A Markup Language：是一个标记语言

 YAML isn't Markup Language：不是一个标记语言；

标记语言：

 以前的配置文件；大多都使用的是 **xxxx.xml**文件；

 YAML：**以数据为中心**，比json、xml等更适合做配置文件；

#### YAML 语法

k:（空格） V

YAML 以 **空格** 控制 **层级**关系，只要是左对齐的一列数据，都是同一层级的

此等级的前面是空格，不能使用制表符（tab）

冒号之后有值，**那么冒号和值之间至少有一个空格**

#### 值的写法

* 字面值：普通的值（数字、字符串、布尔）

```yaml
k: v
```

字符串默认不用加上单引号或者双引号；

`""`：双引号；不会转义字符串里面的特殊字符；特殊字符会作为本身想表示的意思

*eg：* name: "zhangsan \n lisi"：输出；zhangsan 换行 lisi

`''`：单引号；会转义特殊字符，特殊字符最终只是一个普通的字符串数据

*eg：* name: ‘zhangsan \n lisi’：输出；zhangsan \n lisi



* 对象、Map（属性和值）

1. ` k: v`在下一行来写对象的属性和值的关系；注意缩进

```yaml
person:
  name: 张三
  gender: 男
  age: 22
```

2.  行内写法

```yaml
person: {name: 张三,gender: 男,age: 22}
```



* 数组（List、Set）

1. 用`-` 表明组内元素（值和-间仍需要一个空格）

```yaml
fruits: 
  - 苹果
  - 桃子
  - 香蕉
```

2. 行内写法

```yaml
fruits: [苹果,桃子,香蕉]
```

#### 配置文件值注入

###### yaml配置文件注入

==配置类会调用类中的set方法为其赋值==

1.  配置文件处理器

```java
  <!--导入配置文件处理器，配置文件进行绑定就会有提示-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-configuration-processor</artifactId>
            <optional>true</optional>
        </dependency>
```

2. 编写**配置文件：**

   ```yaml
   person:
     name: 张三
     gender: 男
     age: 36
     boss: true
     birth: 1982/10/1
     maps: {k1: v1,k2: v2}
     lists:
       - apple
       - peach
       - banana
     pet:
       name: 小狗
       age: 12
   ```

3. 相应的注入类上用注释加入容器与用配置文件注入

   ```
   @Component
   @ConfigurationProperties(prefix = "yaml类名")
   ```

4. 测试（测试类用@RunWith(SpringRunner.class)，SpringRunner可以使得测试期间也能自动注入）

###### properties

上面yaml对应的properties配置文件写法

```properties
person.name=李四
person.age=34
person.birth=1986/09/12
person.boss=true
person.gender=女
person.lists=cat,dog,pig
person.maps.k1=v1
person.maps.k2=v2
person.pet.name="小黑"
person.pet.age=10
```

测试，发现中文会乱码，而且char类型还会抛出Failed to bind properties under 'person.gender' to java.lang.Character异常

###### [中文乱码解决方法]

在设置中找到`File Encodings`，将配置文件字符集改为`UTF-8`，并勾选：

-  `Transparent native-to-ascii conversion`

yaml和properties配置文件同时存在，properties配置文件的内容会覆盖yaml配置文件的内容

#### @ConfigurationProperties与@value

| @ConfigurationProperties | @Value                   |            |
| ------------------------ | ------------------------ | ---------- |
| 功能                     | 批量注入配置文件中的属性 | 一个个指定 |
| 松散绑定（松散语法）     | 支持                     | 不支持     |
| SpEL                     | 不支持                   | 支持       |
| JSR303数据校验           | 支持                     | 不支持     |
| 复杂类型封装             | 支持                     | 不支持     |

**松散绑定**：例如Person中有`lastName`属性，在配置文件中可以写成

`lastName`或`lastname`或`last-name`或`last_name`等等

**复杂类型封装**

@value`注解无法注入map等对象的复杂类型，但`list、数组可以

**SpEL**：

```
##　properties配置文件
persion.age=#{2019-1986+1}

# Person类
#--------------------使用@ConfigurationProperties注解，会抛出异常--------------------
@Component
@ConfigurationProperties(prefix = "person")
public class Person {
    private Integer age;


#--------------------使用@value注解 OK--------------------
@Component
public class Person {
    @Value("${person.age}")
    private Integer age;
```

**JSR303数据校验**

`@ConfigurationProperties`支持校验，如果校验不通过，会抛出异常

![数据校验](https://cdn.static.note.zzrfdsn.cn/images/springboot/assets/1573716216690.png)

`@value`注解不支持数据校验

![数据校验](https://cdn.static.note.zzrfdsn.cn/images/springboot/assets/1573716427494.png)



#### @PropertySource

`@PropertySource`注解的作用是加载指定的配置文件，值可以是数组，也就是可以加载多个配置文件

springboot**默认加载的配置文件名是`application`**，如果配置文件名不是这个是不会被容器加载的，所以这里Person并没有被注入任何属性值。

>  使用这个注解加载配置文件就需要配置类使用@component等注解而不是等待@EnableConfigurationProperties激活m，而且不支持yaml，只能是properties

![1573718679208](https://cdn.static.note.zzrfdsn.cn/images/springboot/assets/1573718679208.png)





#### @ImportResource和@Configuration

* `@ImportResource`注解用于导入Spring的配置文件，让配置文件里面的内容生效；(就是以前写的springmvc.xml、applicationContext.xml)
* Spring Boot里面没有Spring的配置文件，我们自己编写的配置文件，也不能自动识别；

* 想让Spring的配置文件生效，加载进来；@**ImportResource**标注在一个配置类上

> 注意！这个注解是放在主入口函数的类上，而不是测试类上

![1573720006428](https://cdn.static.note.zzrfdsn.cn/images/springboot/assets/1573720006428.png)





* SpringBoot推荐给容器中添加组件的方式；推荐使用全注解的方式
* 配置类**@Configuration**

* 用@Bean 代替了<bean> </bean>标签，可以用在方法上（返回值为bean对象，默认id为方法名），也可以用在属性上，q



#### 参数占位符

* 随机数

![image-20200807231556123](F:\Typora数据储存\Spring\SpringBoot.assets\image-20200807231556123.png)

* 可以引用在配置文件中配置的其他属性的值，如果使用一个没有在配置文件中的属性，则会原样输出
* 可以使用`:`指定默认值



#### Profile

1. **配置文件方式**

* 多profile文件形式

文件名格式：application-{profile}.properties/yml，例如：

application-dev.properties

application-prod.properties

![1573723830627](https://cdn.static.note.zzrfdsn.cn/images/springboot/assets/1573723830627.png)

程序启动时会默认加载`application.properties`，启动的端口就是8080

可以在主配置文件中指定激活哪个配置文件

![1573724084979](https://cdn.static.note.zzrfdsn.cn/images/springboot/assets/1573724084979.png)



* **yml支持多文档块方式**

```yaml
server:
  port: 8080
spring:
  profiles:
    active: prod //指定激活文档块
---
server:
  port: 8081
spring:
  profiles: dev
---
server:
  port: 8082
spring:
  profiles: prod
```



2. **项目打包后在命令行启动**

* 在IDEA 启动配置环境中编写

![image-20200807234028751](F:\Typora数据储存\Spring\SpringBoot.assets\image-20200807234028751.png)

* 用 cmd 启动jar包时 添加选项

![1573724952868](https://cdn.static.note.zzrfdsn.cn/images/springboot/assets/1573724952868.png)

3. **虚拟机配置**

```java
-Dspring.profiles.active=dev
```

![image-20200807234307728](F:\Typora数据储存\Spring\SpringBoot.assets\image-20200807234307728.png)



#### 配置文件加载位置

springboot 启动会扫描以下位置的application.properties或者application.yml文件作为Spring boot的默认配置文件

```
file: ./config/

    file: ./

        classpath: /config/

            classpath: /        -->first load ↑
```

优先级由高到底，高优先级的配置会覆盖低优先级的配置（优先级低的先加载）；

SpringBoot会从这四个位置全部加载主配置文件；**互补配置**（各个配置文件中互相没有的都会加载）；

![1573728449451](https://cdn.static.note.zzrfdsn.cn/images/springboot/assets/1573728449451.png)

> 这里项目根路径下的配置文件maven编译时不会打包过去，需要修改pom
>
> ```xml
> <resources>
>          <resource>
>              <directory>.</directory>
>              <filtering>true</filtering>
>              <includes>
>                  <include>**/*.properties</include>
>                  <include>**/*.yaml</include>
>              </includes>
>          </resource>
> </resources>
> ```

我们还可以通过`spring.config.location`来改变默认的配置文件位置

**项目打包好以后，我们可以使用命令行参数的形式，启动项目的时候来指定配置文件的新位置；指定配置文件和默认加载的这些配置文件共同起作用形成互补配置；**(运维时可以用相同的jar包改变参数部署，避免服务因为修改打包而暂停)

```
java -jar xxx.jar --spring.config.location=/home/cloudlandboy/application.yaml
```



#### 配置文件加载顺序

**SpringBoot也可以从以下位置加载配置； 优先级从高到低；高优先级的配置覆盖低优先级的配置，所有的配置会形成互补配置**

1. **命令行参数**

   所有的配置都可以在命令行上进行指定

   ```
   java -jar xxx.jar --server.port=8087  --server.context-path=/abc
   ```

   多个配置用空格分开； --配置项=值

2. 来自java:comp/env的JNDI属性 ⤴️

3. Java系统属性（System.getProperties()） ⤴️

4. 操作系统环境变量 ⤴️

5. RandomValuePropertySource配置的random.*属性值 ⤴️

**由jar包外向jar包内进行寻找；**

**优先加载带profile**

6. **jar包外部的`application-{profile}.properties`或`application.yml`(带spring.profile)配置文件** ⤴️

7. **jar包内部的`application-{profile}.properties`或`application.yml`(带spring.profile)配置文件** ⤴️

**再来加载不带profile**

8. **jar包外部的`application.properties`或`application.yml`(不带spring.profile)配置文件** ⤴️

9. **jar包内部的`application.properties`或`application.yml`(不带spring.profile)配置文件** ⤴️

10. @Configuration注解类上的@PropertySource ⤴️

11. 通过SpringApplication.setDefaultProperties指定的默认属性 ⤴️

所有支持的配置加载来源：

[参考官方文档](https://docs.spring.io/spring-boot/docs/2.2.1.RELEASE/reference/htmlsingle/#boot-features-external-config)

#### 自动配置的执行流程

* 需要先创建SpringApplication，再依据Environment创建出ApplicaitonContext，即创建好容器后，在依据一定的注解优先级顺序实例化bean时，才会调用AutoConfigurationImportSelector的selectImports方法，读取spring.factories中的AutoConfiguration标签的自动配置类进行实例化加入容器中

* @SpringBootApplication

```java
@SpringBootApplication // 由启动类的@SpringBootApplication开启自动配置
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration //标至该类是一个配置类，与@Configuration作用一致
@EnableAutoConfiguration //启动
@ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {
}
     
```

> 从Spring3.0，@Configuration用于定义配置类，可替换xml配置文件，被注解的类内部包含有一个或多个被@Bean注解的方法，这些方法将会被AnnotationConfigApplicationContext或AnnotationConfigWebApplicationContext类进行扫描，并用于构建bean定义，初始化Spring容器。
>
> **注意**：@Configuration注解的配置类有如下要求：
>
> 1. @Configuration不可以是final类型；
> 2. @Configuration不可以是匿名类；
> 3. 嵌套的configuration必须是静态类。
>
> 一、用@Configuration加载spring
> 1.1、@Configuration配置spring并启动spring容器
> 1.2、@Configuration启动容器+@Bean注册Bean
> 1.3、@Configuration启动容器+@Component注册Bean
> 1.4、**使用 AnnotationConfigApplicationContext 注册 AppContext 类的两种方法**
>
> 1.5、**配置Web应用程序(web.xml中配置AnnotationConfigApplicationContext)**
>
> 二、组合多个配置类
> 2.1、在@configuration中引入spring的xml配置文件
> 2.2、在@configuration中引入其它注解配置
> 2.3、@configuration嵌套（嵌套的Configuration必须是静态类）
> 三、@EnableXXX注解
> 四、@Profile逻辑组配置
> 五、使用外部变量

* EnableAutoConfiguration

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@AutoConfigurationPackage //当SpringBoot应用启动时默认会将启动类所在的package作为自动配置的package。
@Import(AutoConfigurationImportSelector.class) //@EnableAutoConfiguration注解是Spring Boot中配置自动装载的总开关。
public @interface EnableAutoConfiguration {
}

```

> boot.autoconfigure.EnableAutoConfiguration注解
>
> -> @Import了一个AutoConfigurationImportSelector实例
>
> -> AutoConfigurationImportSelector类（implement ImportSelector），实现了selectImports() 方法，用来筛选被@Import的Configuration类（减去exclude等）

* AutoConfigurationImportSelector.class

```java
public class AutoConfigurationImportSelector implementsDeferredImportSelector, BeanClassLoaderAware,
    ResourceLoaderAware, BeanFactoryAware, EnvironmentAware, Ordered {
  //......
  @Override
  publicString[] selectImports(AnnotationMetadata annotationMetadata) {
    // 如果AutoConfiguration没开,返回{}
    if(!isEnabled(annotationMetadata)) {
      returnNO_IMPORTS;
    }
    // 将spring-autoconfigure-metadata.properties的键值对配置载入到PropertiesAutoConfigurationMetadata对象中并返回
    AutoConfigurationMetadata autoConfigurationMetadata = AutoConfigurationMetadataLoader
        .loadMetadata(this.beanClassLoader);
    // 基于各种配置计算需要import的configuration和exclusion
    AutoConfigurationEntry autoConfigurationEntry = getAutoConfigurationEntry(autoConfigurationMetadata,
        annotationMetadata);
    returnStringUtils.toStringArray(autoConfigurationEntry.getConfigurations());
  }
      
  // 判断AudoConfiguration是否开启
  protectedbooleanisEnabled(AnnotationMetadata metadata) {
    if(getClass() == AutoConfigurationImportSelector.class) {
      // 如果配置文件中有"spring.boot.enableautoconfiguration",返回该字段的值;否则返回true
      returngetEnvironment().getProperty(EnableAutoConfiguration.ENABLED_OVERRIDE_PROPERTY, Boolean.class, true);
    }
    returntrue;
  }
      
  protectedAutoConfigurationEntry getAutoConfigurationEntry(AutoConfigurationMetadata autoConfigurationMetadata,
      AnnotationMetadata annotationMetadata) {
    if(!isEnabled(annotationMetadata)) {
      returnEMPTY_ENTRY;
    }
    // 获取注解的属性值
    AnnotationAttributes attributes = getAttributes(annotationMetadata);
    // 从META-INF/spring.factories文件中获取EnableAutoConfiguration所对应的configurations，但并不实例化，还要筛选
    List<String> configurations = getCandidateConfigurations(annotationMetadata, attributes);
    // 去重,List转Set再转List
    configurations = removeDuplicates(configurations);
    // 从注解的exclude/excludeName属性中获取排除项
    Set<String> exclusions = getExclusions(annotationMetadata, attributes);
    // 对于不属于AutoConfiguration的exclude报错
    checkExcludedClasses(configurations, exclusions);
    // 从configurations去除exclusions
    configurations.removeAll(exclusions);
    // 所有AutoConfigurationImportFilter类实例化，并再进行一次筛选
    configurations = filter(configurations, autoConfigurationMetadata);
    // 实例化剩下configuration中的类，并把AutoConfigurationImportEvent绑定在所有AutoConfigurationImportListener子类实例上，当fireAutoConfigurationImportEvents事件被触发时，打印出已经注册到spring上下文中的@Configuration注解的类,打印出被阻止注册到spring
    fireAutoConfigurationImportEvents(configurations, exclusions);
    // 返回(configurations, exclusions)组
    return newAutoConfigurationEntry(configurations, exclusions);
  }
  // ......
}

protected List<String> getCandidateConfigurations(AnnotationMetadata metadata, AnnotationAttributes attributes) {
    
   		// getSpringFactoriesLoaderFactoryClass()返回的是EnableAutoConfiguration.class;

		// getBeanClassLoader()这里使用的是AppClassLoader。

		List<String> configurations = SpringFactoriesLoader.loadFactoryNames(getSpringFactoriesLoaderFactoryClass()  		//getSpringFactoriesLoaderFactoryClass()获取需要配置的类即EnableAutoConfiguration.class
				getBeanClassLoader()); 
		Assert.notEmpty(configurations, "No auto configuration classes found in META-INF/spring.factories. If you "
				+ "are using a custom packaging, make sure that file is correct.");
		return configurations;
	}

```

* getCandidateConfigurations方法中，SpringFactoriesLoader.loadFactoryNames(),扫描所有jar包类路径下 `META-INF/spring.factories`，并对相应的key值进行筛选，这里使用的key值为org.springframework.boot.autoconfigure.EnableAutoConfiguration。
* 把扫描到的这些文件的内容包装成properties对象，以 **Properties** 类型(即 **key-value** 形式)配置，就可以将相应的实现类注入 **Spirng** 容器中（key为factory类型）。从properties中获取到EnableAutoConfiguration.class（类名）对应的值，然后把它们添加在容器中

```java
public static List<String> loadFactoryNames(Class<?> factoryType, @Nullable ClassLoader classLoader) {
		String factoryTypeName = factoryType.getName();
  		// loadSpringFactories方法是获取所有的springFactories
		// getOrDefault是通过key，获取到对应的类的集合。因为value是通过逗号相隔的，可以有多个，所以是list
		// getOrDefault如果存在就返回，如果不存在那么就返回给的默认值
		return loadSpringFactories(classLoader).getOrDefault(factoryTypeName, Collections.emptyList());
	}


private static Map<String, List<String>> loadSpringFactories(@Nullable ClassLoader classLoader) {
   
    MultiValueMap<String, String> result = cache.get(classLoader);
   if (result != null) {
      return result;
   }

   try {
       // 三目表达式，判断参数classLoader是否为空，如果不为空，那么直接使用传入的classLoader获取META-INF/spring.factories
			// 如果为空，那么就使用系统的classLoader来获取META-INF/spring.factories
			// 总之健壮性比较强，
      Enumeration<URL> urls = (classLoader != null ?
            classLoader.getResources(FACTORIES_RESOURCE_LOCATION) :
            ClassLoader.getSystemResources(FACTORIES_RESOURCE_LOCATION));
      result = new LinkedMultiValueMap<>();
      while (urls.hasMoreElements()) {
          // 通过循环遍历所有的META-INF/spring.factories
         URL url = urls.nextElement();
         UrlResource resource = new UrlResource(url);
    // 找到的每个 META-INF/spring.factories 文件都是一个 Properties 文件，将其内容加载到一个 Properties 对象然后处理其中的每个属性
         Properties properties = PropertiesLoaderUtils.loadProperties(resource);
         for (Map.Entry<?, ?> entry : properties.entrySet()) {        
             // 获取工厂类名称（接口或者抽象类的全限定名）
             String factoryTypeName = ((String) entry.getKey()).trim();
               // 将逗号分割的属性值逐个取出，然后放到 结果result 中去
            for (String factoryImplementationName : StringUtils.commaDelimitedListToStringArray((String) entry.getValue())) {
               result.add(factoryTypeName, factoryImplementationName.trim());
            }
         }
      }
       
     // 筛选出的结果集Map放入内存中，
      cache.put(classLoader, result);
      return result;
   }
   catch (IOException ex) {
      throw new IllegalArgumentException("Unable to load factories from location [" +
            FACTORIES_RESOURCE_LOCATION + "]", ex);
   }
```

* 在getConfigurationClassFilter与fireAutoConfigurationImportEvents方法中将其通过SpringFactoriesLoader 中的loadFactories反射对所有的配置进行筛选，实例化,并绑定到AutoConfigurationImportListener子类实例上

```java
protected List<AutoConfigurationImportListener> getAutoConfigurationImportListeners() {
		return SpringFactoriesLoader.loadFactories(AutoConfigurationImportListener.class, this.beanClassLoader);
	}

protected List<AutoConfigurationImportFilter> getAutoConfigurationImportFilters() {
		return SpringFactoriesLoader.loadFactories(AutoConfigurationImportFilter.class, this.beanClassLoader);
	}

/**
	 * 通过classLoader从各个jar包的classpath下面的META-INF/spring.factories加载并解析其key-value值，然后创建其给定类型的工厂实现
	 *
	 * 返回的工厂通过AnnotationAwareOrderComparator进行排序过的。
	 * AnnotationAwareOrderComparator就是通过@Order注解上面的值进行排序的，值越高，则排的越靠后
	 *
	 * 如果需要自定义实例化策略，请使用loadFactoryNames方法获取所有注册工厂名称。
	 *
	 * @param  factoryType 接口或者抽象类的Class对象
	 * @param  classLoader 用于加载的类加载器（可以是null，如果是null，则使用默认值）
	 * @throws IllegalArgumentException 如果无法加载任何工厂实现类，或者在实例化任何工厂时发生错误，则会抛出IllegalArgumentException异常
	 */
	public static <T> List<T> loadFactories(Class<T> factoryType, @Nullable ClassLoader classLoader) {
		// 首先断言，传入的接口或者抽象类的Class对象不能为空
		Assert.notNull(factoryType, "'factoryType' must not be null");
		// 将传入的classLoader赋值给classLoaderToUse
		// 判断classLoaderToUse是否为空，如果为空，则使用默认的SpringFactoriesLoader的classLoader
		ClassLoader classLoaderToUse = classLoader;
		if (classLoaderToUse == null) {
			classLoaderToUse = SpringFactoriesLoader.class.getClassLoader();
		}
		// 加载所有的META-INF/spring.factories并解析，获取其配置的factoryNames
		List<String> factoryImplementationNames = loadFactoryNames(factoryType, classLoaderToUse);
		if (logger.isTraceEnabled()) {
			logger.trace("Loaded [" + factoryType.getName() + "] names: " + factoryImplementationNames);
		}

		List<T> result = new ArrayList<>(factoryImplementationNames.size());
		// 通过反射对所有的配置进行实例化。
		for (String factoryImplementationName : factoryImplementationNames) {
			result.add(instantiateFactory(factoryImplementationName, factoryType, classLoaderToUse));
		}
		AnnotationAwareOrderComparator.sort(result);
		return result;
	}

// 实例化工厂，根据
	@SuppressWarnings("unchecked")
	private static <T> T instantiateFactory(String factoryImplementationName, Class<T> factoryType, ClassLoader classLoader) {
		try {
			// 根据全限定类名通过反射获取Class对象
			Class<?> factoryImplementationClass = ClassUtils.forName(factoryImplementationName, classLoader);
			// 判断获取的Class对象是否从factoryType里面来，
			// 说具体点就是判断我们配置的spring.factories中的权限定类名所对应的类是否是对应的子类或者实现类
			// 比如
			// org.springframework.context.ApplicationContextInitializer=\
			// org.springframework.boot.context.ConfigurationWarningsApplicationContextInitializer,\
			// org.springframework.boot.context.ContextIdApplicationContextInitializer,
			// 那么他就会验证ConfigurationWarningsApplicationContextInitializer和ContextIdApplicationContextInitializer是否是ApplicationContextInitializer的子类
//			isAssignableFrom()方法与instanceof关键字的区别总结为以下两个点：
//			isAssignableFrom()方法是从类继承的角度去判断，instanceof关键字是从实例继承的角度去判断。
//			isAssignableFrom()方法是判断是否为某个类的父类，instanceof关键字是判断是否某个类的子类。
			// 如果不是，则会保存
			if (!factoryType.isAssignableFrom(factoryImplementationClass)) {
				throw new IllegalArgumentException(
						"Class [" + factoryImplementationName + "] is not assignable to factory type [" + factoryType.getName() + "]");
			}
			// 通过反射的有参构造函数进行实例化：如果直接newInstance的话，那么只能通过空参构造函数进行实例化。
			// 通过这种方式可以通过不同参数的构造函数进行创建实例，但是这里并没有传入参数，所以调用的是默认空惨构造函数
			return (T) ReflectionUtils.accessibleConstructor(factoryImplementationClass).newInstance();
		}
		catch (Throwable ex) {
			throw new IllegalArgumentException(
				"Unable to instantiate factory class [" + factoryImplementationName + "] for factory type [" + factoryType.getName() + "]",
				ex);
		}
	}
```

* 可见selectImports()是AutoConfigurationImportSelector的核心函数，其核心功能就是获取spring.factories中EnableAutoConfiguration所对应的Configuration类列表，由@EnableAutoConfiguration注解中的exclude/excludeName参数筛选一遍，再由AutoConfigurationImportFilter类所有实例筛选一遍，得到最终的用于Import的configuration和exclusion。

* 该函数是被谁调用的呢？在org.springframework.context.annotation.ConfigurationClassParser类中被processImports()调用，而processImports()函数被doProcessConfigurationClass()调用。下面从doProcessConfigurationClass() 看起。

* 上面为@Import（）的Spring的注释的底层实现

* 接着看我们EnableAutoConfiguration.class（类名）对应的值：
* 每一个这样的 `xxxAutoConfiguration`类都是容器中的一个组件，都加入到容器中；用他们来做自动配置；
* 每一个自动配置类进行自动配置功能；
* eg :HttpEncodingAutoConfiguration

```java
package org.springframework.boot.autoconfigure.web.servlet;

......

//表示这是一个配置类，以前编写的配置文件一样，也可以给容器中添加组件
@Configuration(
    proxyBeanMethods = false
)

/**
 * 启动指定类的ConfigurationProperties功能；
 * 将配置文件中对应的值和HttpProperties绑定起来；
 * 并把HttpProperties加入到ioc容器中
 */
@EnableConfigurationProperties({HttpProperties.class})

/**
 * Spring底层@Conditional注解
 * 根据不同的条件，如果满足指定的条件，整个配置类里面的配置就会生效；
 * 判断当前应用是否是web应用，如果是，当前配置类生效
 */
@ConditionalOnWebApplication(
    type = Type.SERVLET
)

//判断当前项目有没有这个类
@ConditionalOnClass({CharacterEncodingFilter.class})

/**
 * 判断配置文件中是否存在某个配置  spring.http.encoding.enabled；
 * 如果不存在，判断也是成立的
 * 即使我们配置文件中不配置pring.http.encoding.enabled=true，也是默认生效的；
 */
@ConditionalOnProperty(
    prefix = "spring.http.encoding",
    value = {"enabled"},
    matchIfMissing = true
)
public class HttpEncodingAutoConfiguration {

    //它已经和SpringBoot的配置文件映射了
    private final Encoding properties;

    //只有一个有参构造器的情况下，参数的值就会从容器中拿
    public HttpEncodingAutoConfiguration(HttpProperties properties) {
        this.properties = properties.getEncoding();
    }

    @Bean     //给容器中添加一个组件，这个组件的某些值需要从properties中获取
    @ConditionalOnMissingBean    //判断容器有没有这个组件？（容器中没有才会添加这个组件）
    public CharacterEncodingFilter characterEncodingFilter() {
        CharacterEncodingFilter filter = new OrderedCharacterEncodingFilter();
        filter.setEncoding(this.properties.getCharset().name());
        filter.setForceRequestEncoding(this.properties.shouldForce(org.springframework.boot.autoconfigure.http.HttpProperties.Encoding.Type.REQUEST));
        filter.setForceResponseEncoding(this.properties.shouldForce(org.springframework.boot.autoconfigure.http.HttpProperties.Encoding.Type.RESPONSE));
        return filter;
    }

    ......
```

1. 根据当前不同的条件判断，决定这个配置类是否生效
2. 一但这个配置类生效；这个配置类就会给容器中添加各种组件；这些组件的属性是从对应的properties类中获取的，这些类里面的每一个属性又是和配置文件绑定的；
3. 相应的配置类在@EnableConfigurationProperties中指定，其会在读取我们写的properties并将值注入类中，由`xxxAutoConfiguration` 读取 实例化的xxxxProperties类，根据属性生成相应的bean给ApplicationContext调用。

**所有在配置文件中能配置的属性都是在`xxxxProperties`类中封装着；配置文件能配置什么就可以参照某个功能对应的这个属性类**

```java
@ConfigurationProperties(
    prefix = "spring.http"
)
public class HttpProperties {
    private boolean logRequestDetails;
    private final HttpProperties.Encoding encoding = new HttpProperties.Encoding();
```

> 我们配置时的流程：
>
> - SpringBoot启动会加载大量的自动配置类
>
> - 我们看我们需要的功能有没有SpringBoot默认写好的自动配置类
>
> - 再来看这个自动配置类中到底配置了哪些组件；（只要我们要用的组件有，我们就不需要再来配置了）
>
> - 给容器中自动配置类添加组件的时候，会从properties类中获取某些属性。我们就可以在配置文件中指定这些属性的值
>
> - `xxxxAutoConfigurartion`：自动配置类；
>
>   `xxxxProperties`:封装配置文件中相关属性；



#### @Conditional派生注解

作用：必须是@Conditional指定的条件成立，才给容器中添加组件，配置配里面的所有内容才生效；

| @Conditional扩展注解            | 作用（判断是否满足当前指定条件）                 |
| ------------------------------- | ------------------------------------------------ |
| @ConditionalOnJava              | 系统的java版本是否符合要求                       |
| @ConditionalOnBean              | 容器中存在指定Bean；                             |
| @ConditionalOnMissingBean       | 容器中不存在指定Bean；                           |
| @ConditionalOnExpression        | 满足SpEL表达式指定                               |
| @ConditionalOnClass             | 系统中有指定的类                                 |
| @ConditionalOnMissingClass      | 系统中没有指定的类                               |
| @ConditionalOnSingleCandidate   | 容器中只有一个指定的Bean，或者这个Bean是首选Bean |
| @ConditionalOnProperty          | 系统中指定的属性是否有指定的值                   |
| @ConditionalOnResource          | 类路径下是否存在指定资源文件                     |
| @ConditionalOnWebApplication    | 当前是web环境                                    |
| @ConditionalOnNotWebApplication | 当前不是web环境                                  |
| @ConditionalOnJndi              | JNDI存在指定项                                   |

* 自动配置类必须在一定的条件下才能生效；

  我们怎么知道哪些自动配置类生效了；

  我们可以通过配置文件启用 `debug=true`属性；来让控制台打印自动配置报告，这样我们就可以很方便的知道哪些自动配置类生效；

  - `Positive matches` ：（自动配置类启用的）
  - `Negative matches`：（没有启动，没有匹配成功的自动配置类）

## 日志 

`JUL`、`JCL`、`Jboss-logging`、`logback`、`log4j`、`log4j2`、`slf4j`....

| 日志门面 （日志的抽象层）                                    | 日志实现                                          |
| ------------------------------------------------------------ | ------------------------------------------------- |
| JCL（Jakarta Commons Logging）**SLF4j（Simple Logging Facade for Java）** jboss-loggi | JUL（java.util.logging） Log4j Log4j2 **Logback** |

左边选一个门面（抽象层）、右边来选一个实现；

例：SLF4j-->Logback

SpringBoot选用 `SLF4j`和`logback`



#### SLF4j使用

如何在系统中使用SLF4j ：[https://www.slf4j.org](https://www.slf4j.org/)

以后开发的时候，日志记录方法的调用，不应该来直接调用日志的实现类，而是调用日志抽象层里面的方法；

![日志](http://www.slf4j.org/images/concrete-bindings.png)

每一个日志的实现框架都有自己的配置文件。如果需要则用一个适配层调用使得slf4j的接口调用转换为log412的调用，使得可以用实现log412API的实现类

#### 遗留问题

项目中依赖的框架可能使用不同的日志：

Spring（commons-logging）、Hibernate（jboss-logging）、MyBatis、xxxx

当项目是使用多种日志API时，可以统一适配到SLF4J，中间使用SLF4J或者第三方提供的日志适配器适配到SLF4J，SLF4J在底层用开发者想用的一个日志框架来进行日志系统的实现，从而达到了多种日志的统一实现。

![统一日志](http://www.slf4j.org/images/legacy.png)

* 跟上面不同实现类调用同一个SLF4J API相反，用多个API转换为对同一个SLF4J API，再调用相同的实现类

#### 如何让系统中所有的日志都统一到slf4j

1. 将系统中其他日志框架先排除出去；
2. 用中间包来替换原有的日志框架（适配器的类名和包名与替换的被日志框架一致）；
3. 我们导入slf4j其他的实现



```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter</artifactId>
</dependency>
```

SpringBoot使用它来做日志功能；

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-logging</artifactId>
</dependency>
```

底层依赖关系

总结：

1. SpringBoot底层也是使用slf4j+logback的方式进行日志记录
2. SpringBoot也把其他的日志都替换成了slf4j；
3. 中间替换包？

```java
@SuppressWarnings("rawtypes")
public abstract class LogFactory {

    static String UNSUPPORTED_OPERATION_IN_JCL_OVER_SLF4J = "http://www.slf4j.org/codes.html#unsupported_operation_in_jcl_over_slf4j";

    static LogFactory logFactory = new SLF4JLogFactory();
```

如果我们要引入其他框架？一定要把这个框架的默认日志依赖移除掉？

Spring框架用的是commons-logging；

```xml
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-core</artifactId>
            <exclusions>
                <exclusion>
                    <groupId>commons-logging</groupId>
                    <artifactId>commons-logging</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
```

**SpringBoot能自动适配所有的日志，而且底层使用slf4j+logback的方式记录日志，引入其他框架的时候，只需要把这个框架依赖的日志框架排除掉即可；**



#### 日志使用

###### 默认配置

```java
 //记录器
    Logger logger = LoggerFactory.getLogger(getClass());
    @Test
    public void contextLoads() {
        //System.out.println();

        //日志的级别；
        //由低到高   trace<debug<info<warn<error
        //可以调整输出的日志级别；日志就只会在这个级别以以后的高级别生效
        logger.trace("这是trace日志...");
        logger.debug("这是debug日志...");
        //SpringBoot默认给我们使用的是info级别的，没有指定级别的就用SpringBoot默认规定的级别；root级别
        logger.info("这是info日志...");
        logger.warn("这是warn日志...");
        logger.error("这是error日志...");


    }
```

> 日志输出格式：      
>
> %d表示日期时间，      
>
> %thread表示线程名，     
>
> %-5level：级别从左显示5个字符宽度   
>
> %logger{50} 表示logger名字最长50个字符，否则按照句点分割。 
>
> %msg：日志消息，       
>
> %n是换行符    
>
> -->  
>
> %d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n

###### SpringBoot修改日志的配置

```properties
# 也可以指定一个包路径 logging.level.com.xxx=error
logging.level.root=error


#logging.path=
# 不指定路径在当前项目下生成springboot.log日志
# 可以指定完整的路径；
#logging.file=G:/springboot.log

# 在当前磁盘的根路径下创建spring文件夹和里面的log文件夹；使用 spring.log 作为默认文件
logging.path=/spring/log

#  在控制台输出的日志的格式
logging.pattern.console=%d{yyyy-MM-dd} [%thread] %-5level %logger{50} - %msg%n
# 指定文件中日志输出的格式
logging.pattern.file=%d{yyyy-MM-dd} === [%thread] === %-5level === %logger{50} ==== %msg%n
```

| logging.file | logging.path | Example  | Description                        |
| ------------ | ------------ | -------- | ---------------------------------- |
| (none)       | (none)       |          | 只在控制台输出                     |
| 指定文件名   | (none)       | my.log   | 输出日志到my.log文件               |
| (none)       | 指定目录     | /var/log | 输出到指定目录的 spring.log 文件中 |

###### 指定配置

给类路径下放上每个日志框架自己的配置文件即可；SpringBoot就不使用他默认配置的了

| Logging System          | Customization                                                |
| ----------------------- | ------------------------------------------------------------ |
| Logback                 | `logback-spring.xml`, `logback-spring.groovy`, `logback.xml` or `logback.groovy` |
| Log4j2                  | `log4j2-spring.xml` or `log4j2.xml`                          |
| JDK (Java Util Logging) | `logging.properties`                                         |

logback.xml：直接就被日志框架识别了；

**logback-spring.xml**：日志框架就不直接加载日志的配置项，由SpringBoot解析日志配置，可以使用SpringBoot的高级Profile功能

```xml
<springProfile name="staging">
    <!-- configuration to be enabled when the "staging" profile is active -->
      可以指定某段配置只在某个环境下生效
</springProfile>
```

如：

```xml
<appender name="stdout" class="ch.qos.logback.core.ConsoleAppender">
        <!--
        日志输出格式：
            %d表示日期时间，
            %thread表示线程名，
            %-5level：级别从左显示5个字符宽度
            %logger{50} 表示logger名字最长50个字符，否则按照句点分割。 
            %msg：日志消息，
            %n是换行符
        -->
        <layout class="ch.qos.logback.classic.PatternLayout">
            <springProfile name="dev">
                <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} ----> [%thread] ---> %-5level %logger{50} - %msg%n</pattern>
            </springProfile>
            <springProfile name="!dev">
                <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} ==== [%thread] ==== %-5level %logger{50} - %msg%n</pattern>
            </springProfile>
        </layout>
    </appender>
```

**如果使用logback.xml作为日志配置文件，还要使用profile功能**，会有以下**错误**

```
no applicable action for [springProfile]
```

#### 切换日志框架

**可以按照上面官网的日志适配图，进行相关的切换；**

slf4j+log4j的方式；

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-web</artifactId>
  <exclusions>
    <exclusion>
      <artifactId>logback-classic</artifactId>
      <groupId>ch.qos.logback</groupId>
    </exclusion>
    <exclusion>
      <artifactId>log4j-over-slf4j</artifactId>
      <groupId>org.slf4j</groupId>
    </exclusion>
  </exclusions>
</dependency>

<dependency>
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-log4j12</artifactId>
</dependency>
```

切换为log4j2（log4j2 是指log4j的2代版本，）

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <exclusions>
        <exclusion>
            <artifactId>spring-boot-starter-logging</artifactId>
            <groupId>org.springframework.boot</groupId>
        </exclusion>
    </exclusions>
</dependency>

<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-log4j2</artifactId>
</dependency>
```

##  web 开发

1. 创建SpringBoot应用，选中我们需要的模块
2. SpringBoot已经默认将这些场景配置好了，只需要在配置文件中指定少量配置就可以运行起来
3. 自己编写业务代码

#### 静态资源的映射规则

* 以往的SpringMVC我们将其写在WebApp文件夹下，编译时会自动放入classes
* Springboot用资源映射`WebMvcAutoConfiguration`类的`addResourceHandlers`方法：（添加资源映射)

**webjars方式**

```java
 public void addResourceHandlers(ResourceHandlerRegistry registry) {
            if (!this.resourceProperties.isAddMappings()) {
                logger.debug("Default resource handling disabled");
            } else {
                Duration cachePeriod = this.resourceProperties.getCache().getPeriod();
                CacheControl cacheControl = this.resourceProperties.getCache().getCachecontrol().toHttpCacheControl();
                if (!registry.hasMappingForPattern("/webjars/**")) {
                    this.customizeResourceHandlerRegistration(registry.addResourceHandler(new String[]{"/webjars/**"}).addResourceLocations(new String[]{"classpath:/META-INF/resources/webjars/"}).setCachePeriod(this.getSeconds(cachePeriod)).setCacheControl(cacheControl));
                }

                String staticPathPattern = this.mvcProperties.getStaticPathPattern();
                if (!registry.hasMappingForPattern(staticPathPattern)) {
                    this.customizeResourceHandlerRegistration(registry.addResourceHandler(new String[]{staticPathPattern}).addResourceLocations(WebMvcAutoConfiguration.getResourceLocations(this.resourceProperties.getStaticLocations())).setCachePeriod(this.getSeconds(cachePeriod)).setCacheControl(cacheControl));
                }

            }
        }
```

所有 `/webjars/**` ，都去 `classpath:/META-INF/resources/webjars/` 找资源

`webjars`：以jar包的方式引入静态资源；

[webjars官网](https://www.webjars.org/)

![1573815091111](https://cdn.static.note.zzrfdsn.cn/images/springboot/assets/1573815091111.png)

例如：添加jquery的webjars

```xml
        <dependency>
            <groupId>org.webjars</groupId>
            <artifactId>jquery</artifactId>
            <version>3.4.1</version>
        </dependency>
```

![1573815506777](https://cdn.static.note.zzrfdsn.cn/images/springboot/assets/1573815506777.png)

访问地址对应就是：http://localhost:8080/webjars/jquery/3.4.1/jquery.js



**非webjars方式（添加到自动扫描的静态文件夹）**

**资源配置类：**

```java
@ConfigurationProperties(    //webjars和静态文件夹都用的是该ResourceProperties，说明可以在配置文件中配置相关参数
    prefix = "spring.resources",
    ignoreUnknownFields = false
)
public class ResourceProperties {
    private static final String[] CLASSPATH_RESOURCE_LOCATIONS = new String[]{"classpath:/META-INF/resources/", "classpath:/resources/", "classpath:/static/", "classpath:/public/"};
    private String[] staticLocations;
    private boolean addMappings;
    private final ResourceProperties.Chain chain;
    private final ResourceProperties.Cache cache;

    public ResourceProperties() {
        this.staticLocations = CLASSPATH_RESOURCE_LOCATIONS;
        this.addMappings = true;
        this.chain = new ResourceProperties.Chain();
        this.cache = new ResourceProperties.Cache();
    }
```

![1573817274649](https://cdn.static.note.zzrfdsn.cn/images/springboot/assets/1573817274649.png)

上图中添加的映射访问路径`staticPathPattern`值是`/**`，对应的资源文件夹就是上面配置类`ResourceProperties`中的`CLASSPATH_RESOURCE_LOCATIONS`数组中的文件夹：

| 数组中的值                     | 在项目中的位置                         |
| ------------------------------ | -------------------------------------- |
| classpath:/META-INF/resources/ | src/main/resources/META-INF/resources/ |
| classpath:/resources/          | src/main/resources/resources/          |
| classpath:/static/             | src/main/resources/static/             |
| classpath:/public/             | src/main/resources/public/             |

localhost:8080/abc ---> 去静态资源文件夹里面找abc



###### 欢迎页映射

```java
@Bean
		public WelcomePageHandlerMapping welcomePageHandlerMapping(ApplicationContext applicationContext,
				FormattingConversionService mvcConversionService, ResourceUrlProvider mvcResourceUrlProvider) {
            
            // getWelcomePage()为上面扫描的静态资源文件夹搜索的地方
            //getStaticPathPattern（）为映射访问路径/**
			WelcomePageHandlerMapping welcomePageHandlerMapping = new WelcomePageHandlerMapping(
					new TemplateAvailabilityProviders(applicationContext), applicationContext, getWelcomePage(),
					this.mvcProperties.getStaticPathPattern());
			welcomePageHandlerMapping.setInterceptors(getInterceptors(mvcConversionService, mvcResourceUrlProvider));
			welcomePageHandlerMapping.setCorsConfigurations(getCorsConfigurations());
			return welcomePageHandlerMapping;
		}

```

其中会在读取的静态资源路径中匹配index.html返回

```java
private Optional<Resource> getWelcomePage() {
			String[] locations = getResourceLocations(this.resourceProperties.getStaticLocations());
			return Arrays.stream(locations).map(this::getIndexHtml).filter(this::isReadable).findFirst();
		}

		private Resource getIndexHtml(String location) {
			return this.resourceLoader.getResource(location + "index.html");
		}

```



###### 网站图标映射（favicon.ico）

加载网页所需要的 favicon.ico 都是在静态资源文件下找，原理与index.html一样

#### 模板引擎

###### 引入Thymeleaf

```xml
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>

<properties>

        <thymeleaf.version>X.X.X.RELEASE</thymeleaf.version>

        <!-- 布局功能的支持程序  thymeleaf3主程序  layout2以上版本 -->
        <!-- thymeleaf2   layout1-->
        <thymeleaf-layout-dialect.version>2.2.2</thymeleaf-layout-dialect.version>

</properties>
```



###### Thymeleaf使用

```java
package org.springframework.boot.autoconfigure.thymeleaf;

......

@ConfigurationProperties(
    prefix = "spring.thymeleaf"
)
public class ThymeleafProperties {
    private static final Charset DEFAULT_ENCODING;
    public static final String DEFAULT_PREFIX = "classpath:/templates/";
    public static final String DEFAULT_SUFFIX = ".html";
    private boolean checkTemplate = true;
    private boolean checkTemplateLocation = true;
    private String prefix = "classpath:/templates/";
    private String suffix = ".html";
    private String mode = "HTML";
}

```

默认只要我们把HTML页面放在`classpath:/templates/`，thymeleaf就能自动渲染；

1. 创建模板文件`t1.html`，并导入thymeleaf的名称空间

   ```html
   <html lang="en" xmlns:th="http://www.thymeleaf.org">
   ```

   ```html
   <!DOCTYPE html>
   <html lang="en" xmlns:th="http://www.thymeleaf.org">
   <head>
       <meta charset="UTF-8">
       <title>Title</title>
   </head>
   <body>
   
   </body>
   </html>
   ```

2. 使用模板

   ```html
   <!DOCTYPE html>
   <html lang="en" xmlns:th="http://www.thymeleaf.org">
   <head>
       <meta charset="UTF-8">
       <title>[[${title}]]</title>
   </head>
   <body>
   <h1 th:text="${title}"></h1>
   <div th:text="${info}">这里的文本之后将会被覆盖</div>
   </body>
   </html>
   ```

3. 在controller中准备数据

   ```java
   @Controller
   public class HelloT {
   
       @RequestMapping("/ht")
       public String ht(Model model) {
           model.addAttribute("title","hello Thymeleaf")
                .addAttribute("info","this is first thymeleaf test");
           return "t1";
       }
   }
   ```

###### 语法规则



`th:text` --> 改变当前元素里面的文本内容；

`th：任意html属性` --> 来替换原生属性的值

​										==描述==                       																				==属性==

![thymeleaf](https://cdn.static.note.zzrfdsn.cn/images/springboot/assets/2018-02-04_123955.png)



转义特殊字符：**即特殊字符全变为普通字符**

表达式expression

```xml
Simple expressions:	（表达式语法）
    Variable Expressions: ${...}
		1. 	获取对象的属性、方法
		2.  使用内置的缓存对象
            #ctx : the context object.
            #vars: the context variables.
            #locale : the context locale.
            #request : (only in Web Contexts) the HttpServletRequest object.
            #response : (only in Web Contexts) the HttpServletResponse object.
            #session : (only in Web Contexts) the HttpSession object.
            #servletContext : (only in Web Contexts) the ServletContext object
		3. 内置的一些工具对象
    Selection Variable Expressions: *{...}
    Message Expressions: #{...}
    Link URL Expressions: @{...}
    Fragment Expressions: ~{...}
Literals：（字面量）
    Text literals: 'one text' , 'Another one!' ,…
    Number literals: 0 , 34 , 3.0 , 12.3 ,…
    Boolean literals: true , false
    Null literal: null
    Literal tokens: one , sometext , main ,…
Text operations:(文本操作)
    String concatenation: +
    Literal substitutions: |The name is ${name}|
Arithmetic operations:（数学运算）
    Binary operators: + , - , * , / , %
    Minus sign (unary operator): -
Boolean operations:（布尔运算）
    Binary operators: and , or
    Boolean negation (unary operator): ! , not
    Comparisons and equality:
    Comparators: > , < , >= , <= ( gt , lt , ge , le )
    Equality operators: == , != ( eq , ne )
Conditional operators:（条件运算）
    If-then: (if) ? (then)
    If-then-else: (if) ? (then) : (else)
    Default: (value) ?: (defaultvalue)
Special tokens:
    No-Operation: _
```

###### Expression inlining

Although the Standard Dialect allows us to do almost everything using tag attributes, there are situations in which we could prefer writing expressions directly into our HTML texts. For example, we could prefer writing this:

```html
Hello, [[${session.user.name}]]!
```

…instead of this:

```html
Hello, Sebastian!
```

==Expressions between [[...]] or [(...)] are considered inlined expressions== in Thymeleaf, and inside them we can use any kind of expression that would also be valid in a ==th:text or th:utext== attribute.

 Note that, while [[...]] corresponds to th:text (i.e. result will be HTML-escaped), [(...)] corresponds to th:utext and will not perform any HTML-escaping. So with a variable such as msg = 'This is **great!**' , given this fragment:

```html
The message is "[(${msg})]"
```

The result will be HTML-escaped:

```html
<p>The message is "This is <b>great!</b>"</p>
```

Whereas if escaped like:

```html
<p>The message is "[[${msg}]]"</p>
```

The result will be HTML-escaped:

```html
<p>The message is "This is &lt;b&gt;great!&lt;/b&gt;"</p>
```

Note that text inlining is active by default ==in the body of every tag in our markup –- not the tags themselves== -–, so there is nothing we need to do to enable it.

###### 抽取公共片段

~{templatename::selector}：模板名::选择器

~{templatename::fragmentname}:模板名::片段名

**若文件的路径为：/WEBINF/templates/ div.html** 

```html
片段名:
/*公共代码片段*/
<div th:fragment="copy">
    &copy; 2011 The Good Thymes Virtual Grocery
</div>

/*引用代码片段*/
<div th:insert="~{ div :: copy}"></div>

/*（〜{...}包围是完全可选的，所以上⾯的代码 将等价于：*/
<div th:insert=" div :: copy"></div>

选择器:    
<div id="copy">
    &copy; 2011 The Good Thymes Virtual Grocery
</div>
<div th:insert=" div :: #copy"></div>
```

> ==若是用`th:insert `  、 `th:replace`、`th:include`引入公用片段，可省略~{}，但若是用Expression inlining则一定要加。==

三种引入公共片段的th属性：

- `th:insert`：将公共片段整个插入到声明引入的元素中
- `th:replace`：将声明引入的元素替换为公共片段
- `th:include`：将被引入的片段的内容包含进这个标签中

```html
/*公共片段*/
< div th:fragment="copy">
&copy; 2011 The Good Thymes Virtual Grocery
</ div>

/*引入方式*/
<div th:insert=" div :: copy"></div>
<div th:replace=" div :: copy"></div>
<div th:include=" div :: copy"></div>


/*效果*/
<div>
    < div>
    &copy; 2011 The Good Thymes Virtual Grocery
    </ div>
</div>

< div>
&copy; 2011 The Good Thymes Virtual Grocery
</ div>

<div>
&copy; 2011 The Good Thymes Virtual Grocery
</div>
```

###### 公共片段传参

* Parameterizable fragment signatures 

In order to create a more function-like mechanism for template fragments, fragments defined with th:fragment can specify a set of parameters:

```html
<div th:fragment="frag (onevar,twovar)">
<p th:text="${onevar} + ' - ' + ${twovar}">...</p>
</div>
```

This requires the use of one of these two syntaxes to call the fragment from th:insert or th:replace :

```html
<div th:replace="footer::frag (${value1},${value2})">...</div>
<div th:replace="footer::frag (onevar=${value1},twovar=${value2})">...</div> <!--这样写时前后关系无所谓-->
```

Even if fragments are defined without arguments like this:

```html
<div th:fragment="frag">
...
</div>
```

We could use the second syntax specified above to call them (and only the second one):

```html
<div th:replace="footer::frag (onevar=${value1},twovar=${value2})">
```

This would be equivalent to a combination of th:replace and th:with :

```html
<div th:replace="footer::frag" th:with="onevar=${value1},twovar=${value2}">
```



#### SpringMVC配置

Spring Boot为Spring MVC提供了自动配置，可与大多数应用程序完美配合。

以下是SpringBoot对SpringMVC的默认配置

**`org.springframework.boot.autoconfigure.web.servlet.WebMvcAutoConfiguration`**

自动配置在Spring的默认值之上添加了以下功能：

- 包含`ContentNegotiatingViewResolver`和`BeanNameViewResolver`。--> 视图解析器
- 支持服务静态资源，包括对WebJars的支持（[官方文档中有介绍](https://docs.spring.io/spring-boot/docs/2.2.1.RELEASE/reference/html/spring-boot-features.html#boot-features-spring-mvc-static-content)）。--> 静态资源文件夹路径
- 自动注册`Converter`，`GenericConverter`和`Formatter`beans。--> 转换器，格式化器
- 支持`HttpMessageConverters`（[官方文档中有介绍](https://docs.spring.io/spring-boot/docs/2.2.1.RELEASE/reference/html/spring-boot-features.html#boot-features-spring-mvc-message-converters)）。--> SpringMVC用来转换Http请求和响应的；User---Json；
- 自动注册`MessageCodesResolver`（[官方文档中有介绍](https://docs.spring.io/spring-boot/docs/2.2.1.RELEASE/reference/html/spring-boot-features.html#boot-features-spring-message-codes)）。--> 定义错误代码生成规则
- 静态`index.html`支持。--> 静态首页访问
- 定制`Favicon`支持（[官方文档中有介绍](https://docs.spring.io/spring-boot/docs/2.2.1.RELEASE/reference/html/spring-boot-features.html#boot-features-spring-mvc-favicon)）。--> 网站图标
- 自动使用`ConfigurableWebBindingInitializer`bean（[官方文档中有介绍](https://docs.spring.io/spring-boot/docs/2.2.1.RELEASE/reference/html/spring-boot-features.html#boot-features-spring-mvc-web-binding-initializer)）。

如果您想保留 Spring Boot MVC 的功能，并且需要添加其他 [MVC 配置](https://docs.spring.io/spring/docs/5.1.3.RELEASE/spring-framework-reference/web.html#mvc)（拦截器，格式化程序和视图控制器等），可以添加自己的 `WebMvcConfigurer` 类型的 `@Configuration` 类，但**不能**带 `@EnableWebMvc` 注解。如果您想自定义 `RequestMappingHandlerMapping`、`RequestMappingHandlerAdapter` 或者 `ExceptionHandlerExceptionResolver` 实例，可以声明一个 `WebMvcRegistrationsAdapter` 实例来提供这些组件。

如果您想完全掌控 Spring MVC，可以添加自定义注解了 `@EnableWebMvc` 的 @Configuration 配置类。

###### 视图解析器

视图解析器：根据方法的返回值得到视图对象（View），视图对象决定如何渲染（转发？重定向？）

- 自动配置了ViewResolver
- ContentNegotiatingViewResolver：组合所有的视图解析器的；

视图解析器从哪里来的？

![1573874365778](https://cdn.static.note.zzrfdsn.cn/images/springboot/assets/1573874365778.png)

**所以我们可以自己给容器中添加一个视图解析器；自动的将其组合进来**

```java
@Component
public class MyViewResolver implements ViewResolver {

    @Override
    public View resolveViewName(String s, Locale locale) throws Exception {
        return null;
    }
}
```

![1573875409759](https://cdn.static.note.zzrfdsn.cn/images/springboot/assets/1573875409759.png)

###### 转换器、格式化器

- `Converter`：转换器； public String hello(User user)：类型转换使用Converter（表单数据转为user）
- `Formatter` 格式化器； 2017.12.17===Date；

```java
        @Bean
        //在配置文件中配置日期格式化的规则
        @ConditionalOnProperty(prefix = "spring.mvc", name = "date-format")
        public Formatter<Date> dateFormatter() {
            return new DateFormatter(this.mvcProperties.getDateFormat());//日期格式化组件
        }
```

**自己添加的格式化器转换器，我们只需要放在容器中即可**

###### HttpMessageConverters

- `HttpMessageConverter`：SpringMVC用来转换Http请求和响应的；User---Json；
- `HttpMessageConverters` 是从容器中确定；获取所有的HttpMessageConverter；

**自己给容器中添加HttpMessageConverter，只需要将自己的组件注册容器中（@Bean,@Component）**

###### MessageCodesResolver

**我们可以配置一个ConfigurableWebBindingInitializer来替换默认的；（添加到容器）**

###### 扩展SpringMVC

以前的配置文件中的配置

```xml
<mvc:view-controller path="/hello" view-name="success"/>
```

**现在，编写一个配置类（@Configuration），是WebMvcConfigurer类型；不能标注@EnableWebMvc**

```java
@Configuration
public class MyMvcConfig implements WebMvcConfigurer {

    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/hi").setViewName("success");
    }
}
```

访问：http://localhost:8080/hi

**原理：**

我们知道`WebMvcAutoConfiguration`是SpringMVC的自动配置类

下面这个类是`WebMvcAutoConfiguration`中的一个内部类

![1573891167026](https://cdn.static.note.zzrfdsn.cn/images/springboot/assets/1573891167026.png)

看一下`@Import({WebMvcAutoConfiguration.EnableWebMvcConfiguration.class})`中的这个类，

这个类依旧是`WebMvcAutoConfiguration`中的一个内部类

![1573891478014](https://cdn.static.note.zzrfdsn.cn/images/springboot/assets/1573891478014.png)

重点看一下这个类继承的父类`DelegatingWebMvcConfiguration`

```java
public class DelegatingWebMvcConfiguration extends WebMvcConfigurationSupport {
    private final WebMvcConfigurerComposite configurers = new WebMvcConfigurerComposite();

    public DelegatingWebMvcConfiguration() {
    }

    //自动注入，从容器中获取所有的WebMvcConfigurer
    @Autowired(
        required = false
    )
    public void setConfigurers(List<WebMvcConfigurer> configurers) {
        if (!CollectionUtils.isEmpty(configurers)) {
            this.configurers.addWebMvcConfigurers(configurers);
        }

    }

    ......

    /**
     * 查看其中一个方法
      * this.configurers：也是WebMvcConfigurer接口的一个实现类
      * 看一下调用的configureViewResolvers方法 ↓
      */
    protected void configureViewResolvers(ViewResolverRegistry registry) {
        this.configurers.configureViewResolvers(registry);
    }
    public void configureViewResolvers(ViewResolverRegistry registry) {
        Iterator var2 = this.delegates.iterator();

        while(var2.hasNext()) {
            WebMvcConfigurer delegate = (WebMvcConfigurer)var2.next();
            //将所有的WebMvcConfigurer相关配置都来一起调用；  
            delegate.configureViewResolvers(registry);
        }

    }
```

容器中所有的WebMvcConfigurer都会一起起作用；

我们的配置类也会被调用；

效果：SpringMVC的自动配置和我们的扩展配置都会起作用；

![1573892805539](https://cdn.static.note.zzrfdsn.cn/images/springboot/assets/1573892805539.png)

###### 全面接管SpringMVC

SpringBoot对SpringMVC的自动配置不需要了，所有都是由我们自己来配置；所有的SpringMVC的自动配置都失效了

**我们只需要在配置类中添加`@EnableWebMvc`即可；**

```java
@Configuration
@EnableWebMvc
public class MyMvcConfig implements WebMvcConfigurer
```

![1573892899452](https://cdn.static.note.zzrfdsn.cn/images/springboot/assets/1573892899452.png)

原理：

为什么@EnableWebMvc自动配置就失效了；

我们看一下EnableWebMvc注解类

```java
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.TYPE})
@Documented
@Import({DelegatingWebMvcConfiguration.class})
public @interface EnableWebMvc {
}
```

重点在于`@Import({DelegatingWebMvcConfiguration.class})`

`DelegatingWebMvcConfiguration`是`WebMvcConfigurationSupport`的子类

我们再来看一下springmvc的自动配置类`WebMvcAutoConfiguration`

```java
@Configuration(
    proxyBeanMethods = false
)
@ConditionalOnWebApplication(
    type = Type.SERVLET
)
@ConditionalOnClass({Servlet.class, DispatcherServlet.class, WebMvcConfigurer.class})

//重点是这个注解，只有当容器中没有这个类型组件的时候该配置类才会生效
@ConditionalOnMissingBean({WebMvcConfigurationSupport.class})

@AutoConfigureOrder(-2147483638)
@AutoConfigureAfter({DispatcherServletAutoConfiguration.class, TaskExecutionAutoConfiguration.class, ValidationAutoConfiguration.class})
public class WebMvcAutoConfiguration 
```

1. @EnableWebMvc将WebMvcConfigurationSupport组件导入进来；
2. 导入的WebMvcConfigurationSupport只是SpringMVC最基本的功能；

###### 如何修改SpringBoot的默认配置

SpringBoot在自动配置很多组件的时候，先看容器中有没有用户自己配置的（@Bean、@Component）如果有就用用户配置的，如果没有，才自动配置；如果有些组件可以有多个（ViewResolver）将用户配置的和自己默认的组合起来；

- 在SpringBoot中会有非常多的xxxConfigurer帮助我们进行扩展配置
- 在SpringBoot中会有很多的xxxCustomizer帮助我们进行定制配置

## RestfulCRUD

静态资源文件：https://www.lanzous.com/i7eenib

1. 将静态资源(css,img,js)添加到项目中，放到springboot[默认的静态资源文件夹下](https://note.clboy.cn/#/backend/springboot/helloweb?id=非webjars，自己的静态资源怎么访问)
2. 将模板文件(html)放到[template文件夹下](https://note.clboy.cn/#/backend/springboot/templateengine?id=thymeleaf使用)

如果你的静态资源明明放到了静态资源文件夹下却无法访问，请检查一下是不是在自定义的配置类上加了**@EnableWebMvc注解**

#### 更改映射规则

template文件加不是静态资源文件夹，默认是无法直接访问的，所以要添加视图映射

```java
package cn.clboy.hellospringbootweb.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.ViewControllerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

/**
 * @Author cloudlandboy
 * @Date 2019/11/16 下午3:32
 * @Since 1.0.0
 */

@Configuration
public class MyMvcConfig implements WebMvcConfigurer {

    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/").setViewName("login");
        registry.addViewController("/index").setViewName("login");
        registry.addViewController("/index.html").setViewName("login");
    }
}
```

访问：http://localhost:8080/

#### 国际化

SpringMVC:

1. 编写国际化配置文件（properties）
2. 使用ResourceBundleMessageSource：管理国际化资源文件
3. 在页面使用fmt：message取出国际化文件



Springboot：

1. 编写国际化配置文件，抽取页面需要显示的国际化消息

创建i18n文件夹存放配置文件，文件名格式为`基础名(login)`+`语言代码(zh)`+`国家代码(CN)`

![1573900332686](https://cdn.static.note.zzrfdsn.cn/images/springboot/assets/1573900332686.png)

2. SpringBoot自动配置好了管理国际化资源文件的组件（MessageSourceAutoConfiguration）

```java
@Configuration(
    proxyBeanMethods = false
)
@ConditionalOnMissingBean( //若是没有自己编写国际化配置文件并加入bean中
    name = {"messageSource"},
    search = SearchStrategy.CURRENT
)
@AutoConfigureOrder(-2147483648)
@Conditional({MessageSourceAutoConfiguration.ResourceBundleCondition.class})
@EnableConfigurationProperties
public class MessageSourceAutoConfiguration {
    private static final Resource[] NO_RESOURCES = new Resource[0];

    public MessageSourceAutoConfiguration() {
    }

    @Bean
    @ConfigurationProperties(
        prefix = "spring.messages"   //默认basename = "messages"，可以在properties中修改配置文件的位置，不然默认为根目录下的messages文件
    )
    public MessageSourceProperties messageSourceProperties() {
        return new MessageSourceProperties();
    }

    @Bean
    public MessageSource messageSource(MessageSourceProperties properties) {
        ResourceBundleMessageSource messageSource = new ResourceBundleMessageSource();//SpringMVC中的ResourceBundleMessageSource组件
        if (StringUtils.hasText(properties.getBasename())) {
            messageSource.setBasenames(StringUtils.commaDelimitedListToStringArray(StringUtils.trimAllWhitespace(properties.getBasename())));
        }

        if (properties.getEncoding() != null) {
            messageSource.setDefaultEncoding(properties.getEncoding().name());
        }

        messageSource.setFallbackToSystemLocale(properties.isFallbackToSystemLocale());
        Duration cacheDuration = properties.getCacheDuration();
        if (cacheDuration != null) {
            messageSource.setCacheMillis(cacheDuration.toMillis());
        }

        messageSource.setAlwaysUseMessageFormat(properties.isAlwaysUseMessageFormat());
        messageSource.setUseCodeAsDefaultMessage(properties.isUseCodeAsDefaultMessage());
        return messageSource;
    }
```

3. 将页面文字改为获取国际化配置，格式`#{key}`

 ```html
       <body class="text-center">
           <form class="form-signin" action="dashboard.html">
               <img class="mb-4" src="asserts/img/bootstrap-solid.svg" alt="" width="72" height="72">
               <h1 class="h3 mb-3 font-weight-normal" th:text="#{login.tip}">Please sign in</h1>
               <label class="sr-only">Username</label>
               <input type="text" class="form-control" th:placeholder="#{login.username}" placeholder="Username" required="" autofocus="">
               <label class="sr-only">Password</label>
               <input type="password" class="form-control" th:placeholder="#{login.password}" placeholder="Password" required="">
               <div class="checkbox mb-3">
                   <label>
             <input type="checkbox" value="remember-me"> [[#{login.remember}]]
           </label>
               </div>
               <button class="btn btn-lg btn-primary btn-block" type="submit" th:text="#{login.btn}">Sign in</button>
               <p class="mt-5 mb-3 text-muted">© 2017-2018</p>
               <a class="btn btn-sm">中文</a>
               <a class="btn btn-sm">English</a>
           </form>
   
       </body>
 ```

 然后就可以更改浏览器语言，页面就会使用对应的国际化配置文件

   ![1573900071209](https://cdn.static.note.zzrfdsn.cn/images/springboot/assets/1573900071209.png)

 原理

   国际化Locale（区域信息对象）；

   LocaleResolver（获取区域信息对象的组件）；==会根据参数如（zh_CN）等生成local对象，local对象中的属性会被上述的ResourceBundleMessageSource对象调用作为判断条件来引用不同的properties==

   在springmvc配置类`WebMvcAutoConfiguration`中注册了该组件

   ```java
@Bean
/**
    *前提是容器中不存在这个组件，
   　*所以使用自己的对象就要配置@Bean让这个条件不成立（实现LocaleResolver 即可）
   　*/
@ConditionalOnMissingBean

/**
	* 如果在application.properties中有配置国际化就用配置文件的
    * 没有配置就用AcceptHeaderLocaleResolver 默认request中获取
    */
@ConditionalOnProperty(
    prefix = "spring.mvc",
    name = {"locale"}
)
public LocaleResolver localeResolver() {
    if (this.mvcProperties.getLocaleResolver() == org.springframework.boot.autoconfigure.web.servlet.WebMvcProperties.LocaleResolver.FIXED) //若是在配置文件中配置了LocalResolver的类型，则依据类型的创建该类型的LocaleResolver
    {
        return new FixedLocaleResolver(this.mvcProperties.getLocale()); 
    } else {
        AcceptHeaderLocaleResolver localeResolver = new AcceptHeaderLocaleResolver();//否则调用浏览器加上的htpp头部生成的locale对象生成localeResolver
        localeResolver.setDefaultLocale(this.mvcProperties.getLocale());
        return localeResolver;
    }
}
   ```

   默认的就是==根据请求头带来的区域信息==获取Locale进行国际化

   ```java
public class AcceptHeaderLocaleResolver implements LocaleResolver {

public Locale resolveLocale(HttpServletRequest request) {
    Locale defaultLocale = this.getDefaultLocale();//查看有没有在配置文件中设置local对象的属性，有则配置
    if (defaultLocale != null && request.getHeader("Accept-Language") == null) {
        return defaultLocale;
    } else {
        Locale requestLocale = request.getLocale();//否则获取请求头中的Locale属性
        List<Locale> supportedLocales = this.getSupportedLocales();
        if (!supportedLocales.isEmpty() && !supportedLocales.contains(requestLocale)) {
            Locale supportedLocale = this.findSupportedLocale(request, supportedLocales);
            if (supportedLocale != null) {
                return supportedLocale;
            } else {
                return defaultLocale != null ? defaultLocale : requestLocale;
            }
        } else {
            return requestLocale;
        }
    }
}
}
   ```



==配置自己的`LocaleResolver`来实现自己的Locale对象生成==

**实现点击连接切换语言，而不是更改浏览器**

- 修改页面，点击连接携带语言参数

  ```html
              <a class="btn btn-sm" th:href="@{/index.html(l='zh_CN')}">中文</a>
  			<a class="btn btn-sm" th:href="@{/index.html(l='en_US')}">English</a>
  ```

- 自己实现区域信息解析器

  ```java
  public class MyLocaleResolver implements LocaleResolver {
  
      @Override
      public Locale resolveLocale(HttpServletRequest httpServletRequest) {
          //获取请求参数中的语言
          String language = httpServletRequest.getParameter("l");
          //没带区域信息参数就用系统默认的
          Locale locale = Locale.getDefault();
          if (!StringUtils.isEmpty(language)) {
              //提交的参数是zh_CN （语言代码_国家代码）
              String[] s = language.split("_");
  
              locale = new Locale(s[0], s[1]);
  
          }
  
          return locale;
      }
  
      @Override
      public void setLocale(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Locale locale) {
  
      }
  }
  ```

- 在配置类中将其注册到容器中

  ```java
  @Configuration
  public class MyMvcConfig implements WebMvcConfigurer {
  
      @Override
      public void addViewControllers(ViewControllerRegistry registry) {
          registry.addViewController("/").setViewName("login");
          registry.addViewController("/index").setViewName("login");
          registry.addViewController("/index.html").setViewName("login");
      }
  
      @Bean
      public LocaleResolver localeResolver() {
          return new MyLocaleResolver();
      }
  
  }
  ```

#### 登陆

1. 提供登录的controller

```java
@Controller
public class UserController {

    @PostMapping("/user/login")
    public String login(@RequestParam String username, @RequestParam String password, HttpSession session, Model model) {
        if (!StringUtils.isEmpty(username) && "123456".equals(password)) {

            //登录成功，把用户信息方法哦session中，防止表单重复提交，重定向到后台页面
            session.setAttribute("loginUser", username);
            return "redirect:/main.html";
        }
        //登录失败,返回到登录页面
        model.addAttribute("msg", "用户名或密码错误！");
        return "login";
    }
}
```

2. 修改表单提交地址，输入框添加name值与参数名称对应

```html
        <form class="form-signin" action="dashboard.html" th:action="@{/user/login}" method="post">
            <img class="mb-4" src="asserts/img/bootstrap-solid.svg" alt="" width="72" height="72">
            <h1 class="h3 mb-3 font-weight-normal" th:text="#{login.tip}">Please sign in</h1>
            <label class="sr-only">Username</label>
            <input type="text" name="username" class="form-control" th:placeholder="#{login.username}" placeholder="Username" autofocus="">
            <label class="sr-only">Password</label>
            <input type="password" name="password" class="form-control" th:placeholder="#{login.password}" placeholder="Password" required="">
            <div class="checkbox mb-3">
                <label>
          <input type="checkbox" value="remember-me"> [[#{login.remember}]]
        </label>
            </div>
            <button class="btn btn-lg btn-primary btn-block" type="submit" th:text="#{login.btn}">Sign in</button>
            <p class="mt-5 mb-3 text-muted">© 2017-2018</p>
            <a class="btn btn-sm" href="?l=zh_CN">中文</a>
            <a class="btn btn-sm" href="?l=en_US">English</a>
        </form>
```

3. 由于登录失败/登陆成功是转发，所以==html的静态资源(css.js)请求路径==会不正确，使用模板引擎语法替换

```java
<link  href="asserts/css/bootstrap.min.css" th:href="@{/asserts/css/bootstrap.min.css}" rel="stylesheet">
<!-- Custom styles for this template -->
<link href="asserts/css/signin.css" th:href="@{/asserts/css/signin.css}" rel="stylesheet">
    
<link href="asserts/css/bootstrap.min.css" th:href="@{/asserts/css/bootstrap.min.css}" rel="stylesheet">

		<!-- Custom styles for this template -->
<link href="asserts/css/dashboard.css" th:href="@{/asserts/css/bootstrap.min.css}" rel="stylesheet">
```

4. 添加登录失败页面显示

```html
<h1 class="h3 mb-3 font-weight-normal" th:text="#{login.tip}">Please sign in</h1>
<!--msg存在才显示该p标签-->
<p th:text="${msg}" th:if="${not #strings.isEmpty(msg)}" style="color: red"></p>
```

> ==编码时页面重编译即使生效而不是重启服务器==
>
> 1. \# 禁用缓存 spring.thymeleaf.cache=false
> 2. 在页面修改完成以后按快捷键`ctrl+f9`，重新编译；

5. 为了防止表单重复提交（用的仍是之前的url，需要一个Post表单，网页的刷新功能可能是会把当前request重新发送一次，上一次传入的POST表单仍在当前的request中被重新传入当前的url），需要增加映射，将路径更换为相应资源的路径

```java
 @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/").setViewName("login");
        registry.addViewController("/index").setViewName("login");
        registry.addViewController("/index.html").setViewName("login");
        registry.addViewController("/main.html").setViewName("dashboard");
    }
```

> 跳转：
>
> 1. request.getRequestDispatcher().forward(request,response)：
>
>    1、属于转发，也是服务器跳转，相当于方法调用，在执行当前文件的过程中转向执行目标文件，两个文件(当前文件和目标文件)属于同一次请求，前后页共用一个request，可以通过此来传递一些数据或者session信息，request.setAttribute()和request.getAttribute()。
>
>    2、在前后两次执行后，地址栏不变，仍是当前文件的地址。
>
>    3、不能转向到本web应用之外的页面和网站，所以转向的速度要快。
>
>    4、URL中所包含的“/”表示应用程序(项目)的路径。
>
> 2. response.sendRedirect()：
>
>    1、属于重定向，也是客户端跳转，相当于客户端向服务端发送请求之后，服务器返回一个响应，客户端接收到响应之后又向服务端发送一次请求，一共是2次请求，前后页不共用一个request，不能读取转向前通过request.setAttribute()设置的属性值。
>
>    2、在前后两次执行后，地址栏发生改变，是目标文件的地址。
>
>    3、可以转向到本web应用之外的页面和网站，所以转向的速度相对要慢。
>
>    4、URL种所包含的"/"表示根目录的路径。
>
>  特殊的应用：对数据进行修改、删除、添加操作的时候，应该用response.sendRedirect()。如果是采用了request.getRequestDispatcher().forward(request,response)，那么操作前后的地址栏都不会发生改变，仍然是修改的控制器，如果此时再对当前页面刷新的话，就会重新发送一次请求对数据进行修改，这也就是有的人在刷新一次页面就增加一条数据的原因。
>
> 
>
> 如何采用第二种方式传递数据：
>
> 1、可以选择session，但要在第二个文件中删除；
>
> 2、可以在请求的url中带上参数，如"add.htm?id=122"

#### 拦截器检查登陆

1. 实现拦截器

   ```
   package cn.clboy.hellospringbootweb.interceptor;
   
   import org.springframework.web.servlet.HandlerInterceptor;
   import org.springframework.web.servlet.ModelAndView;
   
   import javax.servlet.http.HttpServletRequest;
   import javax.servlet.http.HttpServletResponse;
   
   
   public class LoginHandlerInterceptor implements HandlerInterceptor {
       @Override
       public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
   
           Object loginUser = request.getSession().getAttribute("loginUser");
           if (loginUser == null) {
               //未登录，拦截，并转发到登录页面
               request.setAttribute("msg", "您还没有登录，请先登录！");
               //转发时将同一个request指向另一个url，仍用该request 
               request.getRequestDispatcher("/index").forward(request, response);
               return false;
           }
           return true;
       }
   
       @Override
       public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
   
       }
   
       @Override
       public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
   
       }
   }
   
   ```

2. 注册拦截器

```java
package com.atguigu.springboot04.config;


import com.atguigu.springboot04.component.LoginHandlerInterceptor;
import com.atguigu.springboot04.component.MyLocaleResolver;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.LocaleContextResolver;
import org.springframework.web.servlet.LocaleResolver;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.ViewControllerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class MyMvcConfig implements WebMvcConfigurer {

    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/").setViewName("login");
        registry.addViewController("/index").setViewName("login");
        registry.addViewController("/index.html").setViewName("login");
        registry.addViewController("/main.html").setViewName("dashboard");
    }

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
         //添加不拦截的路径，以前添加全部路径的拦截器需要再次过滤静态资源SpringBoot已经做好了静态资源映射，所以我们不用管
        registry.addInterceptor(new LoginHandlerInterceptor()).addPathPatterns("/**").excludePathPatterns("/","/index.html","/index",
                "/user/login");
    }

    @Bean
    public LocaleResolver localeResolver(){
        return new MyLocaleResolver();
    }
}

```

#### CRUD员工列表

==**以往SSM传统的CRUD：用各自的URL分别执行CRUD操作**==

**==Restful风格：用URL+http请求类型（GET、POST、PUT、DELETE） 标识==**

**使用rest风格**

| 实验功能                             | 请求URI | 请求方式 |
| ------------------------------------ | ------- | -------- |
| 查询所有员工                         | emps    | GET      |
| 查询某个员工(来到修改页面)           | emp/1   | GET      |
| 来到添加页面                         | emp     | GET      |
| 添加员工                             | emp     | POST     |
| 来到修改页面（查出员工进行信息回显） | emp/1   | GET      |
| 修改员工                             | emp     | PUT      |
| 删除员工                             | emp/1   | DELETE   |

1. 为了页面结构清晰，在template文件夹下新建emp文件夹，将list.html移动到emp文件夹下

2. 将dao层和实体层[java代码](https://www.lanzous.com/i7eenib)复制到项目中`dao`，`entities`

3. 添加员工controller，实现查询员工列表的方法

   ```java
   @Controller
   public class EmpController {
   
       @Autowired
       private EmployeeDao employeeDao;
   
       @GetMapping("/emps")
       public String emps(Model model) {
           Collection<Employee> empList = employeeDao.getAll();
           model.addAttribute("emps", empList);
           return "emp/list";
       }
   
   }
   ```

4. 修改后台页面，更改左侧侧边栏，将`customer`改为`员工列表`，并修改请求路径

   ```html
   <li class="nav-item">
       <a class="nav-link" th:href="@{/emps}">
           <svg .....>
               ......
           </svg>
           员工列表
       </a>
   </li>
   ```

5. 同样emp/list页面的左边侧边栏是和后台页面一模一样的，每个都要修改很麻烦，接下来，抽取公共片段

###### 提取公共模块

1. 将后台主页中的顶部导航栏作为片段，在list页面引入

   **dashboard.html：**

   ```html
           <nav th:fragment="topbar" class="navbar navbar-dark sticky-top bg-dark flex-md-nowrap p-0">
               <a class="navbar-brand col-sm-3 col-md-2 mr-0" href="http://getbootstrap.com/docs/4.0/examples/dashboard/#">Company name</a>
               <input class="form-control form-control-dark w-100" type="text" placeholder="Search" aria-label="Search">
               <ul class="navbar-nav px-3">
                   <li class="nav-item text-nowrap">
                       <a class="nav-link" href="http://getbootstrap.com/docs/4.0/examples/dashboard/#">Sign out</a>
                   </li>
               </ul>
           </nav>
   ```

   **list.html：**

   ```html
   <body>
   
   <div th:replace="dashboard::topbar"></div>
   
   ......
   ```

2. 使用选择器的方式 抽取左侧边栏代码

   **dashboard.html：**

   ```html
   <div class="container-fluid">
       <div class="row">
           <nav id="sidebar" class="col-md-2 d-none d-md-block bg-light sidebar" ......
   ```

   **list.html：**

   ```html
   <div class="container-fluid">
       <div class="row">
           <div th:replace="dashboard::#sidebar"></div>
           ......
   ```

###### 传递参数实现高亮

* 用传递参数实现侧边栏当前页面所属标签高亮

将`dashboard.html`中的公共代码块抽出为单独的html文件，放到commos文件夹下

然后在`dashboard.html`和`list.html`中引入:

```html
<body>
<div th:replace="commons/topbar::topbar"></div>
<div class="container-fluid">
    <div class="row">
        <div th:replace="commons/sidebar::#sidebar(currentURI='main.html')"></div>
        ......
```

```html
<body>
<div th:replace="commons/topbar::topbar"></div>

<div class="container-fluid">
    <div class="row">
        <div th:replace="commons/sidebar::#sidebar(currentURI='emps')"></div>
        ......
```



######  员工添加

1. 添加员工添加按钮与更改员工信息显示

```html
        <main role="main" class="col-md-9 ml-sm-auto col-lg-10 pt-3 px-4">
            <h2>
                <button class="btn btn-sm btn-success">添加员工</button>
            </h2>
            <div class="table-responsive">
                <table class="table table-striped table-sm">
                    <thead>
                    <tr>
                        <th>员工号</th>
                        <th>姓名</th>
                        <th>邮箱</th>
                        <th>性别</th>
                        <th>部门</th>
                        <th>生日</th>
                        <th>操作</th>
                    </tr>
                    </thead>
                    <tbody>
                    <tr th:each="emp:${emps}">
                        <td th:text="${emp.id}"></td>
                        <td th:text="${emp.lastName}"></td>
                        <td th:text="${emp.email}"></td>
                        <td th:text="${emp.gender}==1?'男':'女'"></td>
                        <td th:text="${emp.department.departmentName}"></td>
                        <td th:text="${#dates.format(emp.birth,'yyyy-MM-dd')}"></td>
                        <td>
                            <button class="btn btn-sm btn-primary">修改</button>
                            <button class="btn btn-sm btn-danger">删除</button>
                        </td>
                    </tr>
                    </tbody>
                </table>
            </div>
        </main>
```

2. 创建员工添加页面`add.html`（添加时form表单转换成Json格数数据，接收时再由相应bean转换成controller参数类型，==因此input标签的name需要为相应实体类的属性名==）

```html
......
<body>
<div th:replace="commons/bar::topbar"></div>

<div class="container-fluid">
    <div class="row">
        <div th:replace="commons/sidebar::#sidebar(currentURI='emps')"></div>
        <main role="main" class="col-md-9 ml-sm-auto col-lg-10 pt-3 px-4">
            <form>
                <div class="form-group">
                    <label>LastName</label>
                    <input name="lastName" type="text" class="form-control" placeholder="zhangsan">
                </div>
                <div class="form-group">
                    <label>Email</label>
                    <input  name="email" type="email" class="form-control" placeholder="zhangsan@atguigu.com">
                </div>
                <div class="form-group">
                    <label>Gender</label><br/>
                    <div class="form-check form-check-inline">
                        <input class="form-check-input" type="radio" name="gender" value="1">
                        <label class="form-check-label">男</label>
                    </div>
                    <div class="form-check form-check-inline">
                        <input class="form-check-input" type="radio" name="gender" value="0">
                        <label class="form-check-label">女</label>
                    </div>
                </div>
                <div class="form-group">
                    <label>department</label>
                    <select name="department.id" class="form-control">
                        <option th:each="dept:${departments}" th:text="${dept.departmentName}" th:value="${dept.id}"></option>
                    </select>
                </div>
                <div class="form-group">
                    <label>Birth</label>
                    <input name="birth" type="text" class="form-control" placeholder="zhangsan">
                </div>
                <button type="submit" class="btn btn-primary">添加</button>
            </form>
        </main>
    </div>
</div>
......
```

3. 点击链接(list页面的button)跳转到添加页面

   ```html
   <a href="/emp" th:href="@{/emp}" class="btn btn-sm btn-success">添加员工</a>
   ```

4. `EmpController`添加映射方法

   ```java
       @Autowired
       private DepartmentDao departmentDao;
   
       @GetMapping("/emp")
       public String toAddPage(Model model) {
           //准备部门下拉框数据
           Collection<Department> departments = departmentDao.getDepartments();
           model.addAttribute("departments",departments);
           return "emp/add";
       }
   
   ```

5. 修改页面遍历添加下拉选项

```html
<select name="department.id" class="form-control"><!--注意需要提交的value一般是ID>
<option th:each="dept:${departments}" th:text="${dept.departmentName}" th:value="${dept.id}"></option>
</select>
```

6. 表单提交，添加员工

```html
<form th:action="@{/emp}" method="post">
```

```java
    @PostMapping("/emp")
    public String add(Employee employee) {
        System.out.println(employee);
        //模拟添加到数据库
        employeeDao.save(employee);
        //添加成功重定向到列表页面
        return "redirect:/emps";
    }
```

> form表单提交的数据转换为的date对象默认格式由容器中的Formatter自动转换为xxxx/xx/xx格式，修改需要再properties中设置spring.mvc.date-format=yyyy-MM-dd

#### 员工修改

1. 修改list编辑按钮跳转到编辑页面

   ```html
    <a th:href="@{/emp/}+${emp.id}" class="btn btn-sm btn-primary">修改</a>
   ```

2. Controller转发到编辑页面，回显员工信息，并跳转到修改页面（这里修改和添加用同一个页面）

```java
@GetMapping("/emp/{id}")
    public String toEditPage(@PathVariable Integer id, Model model) {
        Employee employee = employeeDao.get(id);
        //准备部门下拉框数据
        Collection<Department> departments = departmentDao.getDepartments();
        model.addAttribute("emp", employee).addAttribute("departments", departments);
        return "emp/add";
    }
```

3. 修改add页面中若是修改操作时的显示（通过回显的employee）

   * 判断emp是否为空，决定input是否显示当前员工的信息在上面
   * 若是则为emp属性（option用th:selected; 普通input用th:value；radio类型用th:checked），若不是则显示提示信息placeholder
   * 使用Hidden类型的input添加name为“_method”的数据项，通过bean自动识别将post类型form表单类型更改为put（默认为get和post）

   > 1. SpringMVC 中配置HiddenHttpMethodFilter；（springboot自动添加）
   > 2. 创建一个post表单
   > 3. 创建一个hidden类型input标签，name=_method，value为我们想要的表单类型
   > 4. HiddenHttpMethodFilter自动读取更换

   * 注意id是否有在表单信息中（没有是为了安全），没有也要通过input等标签生成数据发送回controller，否则若是id为修改保存的关键字则会为新加

```html
	<body>
		<div th:replace="/commons/bar::topbar"></div>

		<div class="container-fluid">
			<div class="row">
				<div th:replace="commons/bar::#sidebar(currentURI='emps')"></div>
				<main role="main" class="col-md-9 ml-sm-auto col-lg-10 pt-3 px-4">
					<form th:action="@{/emp}" method="post" >
						<input type="hidden" name="_method" value="put" th:if="${emp!=null}">
						<input type="hidden" name="id" th:if="${emp!=null}" th:value="${emp.id}">
						<div class="form-group">
							<label>LastName</label>
							<input name="lastName" type="text" class="form-control" placeholder="zhangsan" th:value="${emp!=null}?${emp.lastName}">
						</div>
						<div class="form-group">
							<label>Email</label>
							<input  name="email" type="email" class="form-control" placeholder="zhangsan@atguigu.com" th:value="${emp!=null}?${emp.email}">
						</div>
						<div class="form-group">
							<label>Gender</label><br/>
							<div class="form-check form-check-inline">
								<input class="form-check-input" type="radio" name="gender" value="1" th:checked="${emp!=null}?${emp.gender}==1">
								<label class="form-check-label">男</label>
							</div>
							<div class="form-check form-check-inline">
								<input class="form-check-input" type="radio" name="gender" value="0" th:checked="${emp!=null}?${emp.gender}==0">
								<label class="form-check-label">女</label>
							</div>
						</div>
						<div class="form-group">
							<label>department</label>
							<select name="department.id" class="form-control">
								<option th:selected="${emp!=null}?${dept.id == emp.department.id}" th:each="dept:${departments}" th:text="${dept.departmentName}" th:value="${dept.id}"></option>
							</select>
						</div>
						<div class="form-group">
							<label>Birth</label>
							<input name="birth" type="text" class="form-control" placeholder="zhangsan" th:value="${emp!=null}?${#dates.format(emp.birth,'yyyy/MM/dd')}">
						</div>
						<button type="submit" class="btn btn-primary" th:text="${emp!=null}?'修改':'添加'">添加</button>
					</form>
				</main>
			</div>
		</div>

```

#### 员工删除

1. 一个删除按钮一个form表单

> list页面按钮 返回 hidden类型的form表单 ，提交delete类型的http请求

2. 用script 获取 button的一个class为点击事件

* 添加点击事件

```html
<script>
			$(".deleteBtn").click(function(){
				$("#deleteEmpForm").attr("action",$(this).attr("del_uri")).submit();
				return false;
			})
		</script>
```

* 获取一个form表单

```html
<form id="deleteEmpForm" method="post">
					<input type="hidden" name="_method" value="delete">
</form>
```

* 为每一个button添加唯一标识

```html
<button  th:attr="del_uri=@{/emp/}+${emp.id}" class="btn btn-sm btn-danger deleteBtn">删除</button>
```

* controller接收请求

```java
 @DeleteMapping("/emp/{id}")
    public String delete(@PathVariable String id){
        employeeDao.delete(id);
        return "redirect:/emps";
    }

//如果提示不支持POST请求，在确保代码无误的情况下查看是否配置启动HiddenHttpMethodFilter
```



## 错误处理

当访问一个不存在的页面，或者程序抛出异常时

**默认效果：**

- 浏览器返回一个默认的错误页面， 注意看浏览器发送请求的`请求头`：

  ![1573993746920](https://cdn.static.note.zzrfdsn.cn/images/springboot/assets/1573993746920.png)

- 其他客户端返回json数据，注意看`请求头`

  ![1573996455049](https://cdn.static.note.zzrfdsn.cn/images/springboot/assets/1573996455049.png)

查看`org.springframework.boot.autoconfigure.web.servlet.error.ErrorMvcAutoConfiguration`源码，

这里是springboot错误处理的自动配置信息

**主要给日容器中注册了以下组件：**

- ErrorPageCustomizer 系统出现错误以后来到error请求进行处理；相当于（web.xml注册的错误页面规则）
- BasicErrorController 处理/error请求
- DefaultErrorViewResolver 默认的错误视图解析器
- DefaultErrorAttributes 错误信息
- defaultErrorView 默认错误视图

#### ErrorPageCustomizer

* 错误页面定制器：定制错误页面到basicErrorController 

* 作用便是有Exception时转发到默认请求路径为：获取到的是"/error"或者是error.path/server.error.path，这三个的路径都会由BasicErrorController接收，并加入到errorPageRegistry中给dispatcher选择；

```java
    @Bean //若用了模板解析器
    public ErrorMvcAutoConfiguration.ErrorPageCustomizer errorPageCustomizer(DispatcherServletPath dispatcherServletPath) {
        return new ErrorMvcAutoConfiguration.ErrorPageCustomizer(this.serverProperties, dispatcherServletPath);
    }
```

```java
    private static class ErrorPageCustomizer implements ErrorPageRegistrar, Ordered {
        private final ServerProperties properties;
        private final DispatcherServletPath dispatcherServletPath;

        protected ErrorPageCustomizer(ServerProperties properties, DispatcherServletPath dispatcherServletPath) {
            this.properties = properties;
            this.dispatcherServletPath = dispatcherServletPath;
        }

        //注册错误页面
        public void registerErrorPages(ErrorPageRegistry errorPageRegistry) {
            //getPath()获取到的是"/error"或者是error.path/server.error.path，这三个的路径都会由BasicErrorController接收
            ErrorPage errorPage = new ErrorPage(this.dispatcherServletPath.getRelativePath(this.properties.getError().getPath()));
            //将当前项目路径与/error路径拼接放到errorPageRegistry，出现错误时dispatcher会查找errorPageRegistry找到/error，由BasicErrorController接收
            errorPageRegistry.addErrorPages(new ErrorPage[]{errorPage});
        }

        public int getOrder() {
            return 0;
        }
    }
```

#### basicErrorController

```java
    @Bean
    @ConditionalOnMissingBean(
        value = {ErrorController.class},
        search = SearchStrategy.CURRENT
    )
    public BasicErrorController basicErrorController(ErrorAttributes errorAttributes, ObjectProvider<ErrorViewResolver> errorViewResolvers) {
        return new BasicErrorController(errorAttributes, this.serverProperties.getError(), (List)errorViewResolvers.orderedStream().collect(Collectors.toList()));
    }
```

* BasicErrorController

```java
@Controller
/**
  * 查看server.error.path或error.path在dispatcher错误转发的请求是否为/error（默认），是则调用该controller
  * 使用配置文件中server.error.path配置
  * 如果server.error.path没有配置error.path
  * 如果error.path也没有配置就的/error
  */
@RequestMapping({"${server.error.path:${error.path:/error}}"})
public class BasicErrorController extends AbstractErrorController
```

```java
@RequestMapping(produces = MediaType.TEXT_HTML_VALUE) //查看请求头是否为text/html 数据类型
public ModelAndView errorHtml(HttpServletRequest request, HttpServletResponse response) {
   HttpStatus status = getStatus(request);
   Map<String, Object> model = Collections
         .unmodifiableMap(getErrorAttributes(request, getErrorAttributeOptions(request, MediaType.TEXT_HTML)));
   response.setStatus(status.value());
    // modelAndView存储到哪个页面的页面地址和页面内容数据,而他由resolveErrorView返回
   ModelAndView modelAndView = resolveErrorView(request, response, status, model);
   return (modelAndView != null) ? modelAndView : new ModelAndView("error", model);
}

@RequestMapping//除了text/html类型其他的全在这里转化为JSON格式
public ResponseEntity<Map<String, Object>> error(HttpServletRequest request) {
   HttpStatus status = getStatus(request);
   if (status == HttpStatus.NO_CONTENT) {
      return new ResponseEntity<>(status);
   }
   Map<String, Object> body = getErrorAttributes(request, getErrorAttributeOptions(request, MediaType.ALL));
   return new ResponseEntity<>(body, status);
}
```

* 一个用于浏览器请求响应html页面，一个用于其他客户端请求响应json数据 

```java
  protected ModelAndView resolveErrorView(HttpServletRequest request, HttpServletResponse response, HttpStatus status,
			Map<String, Object> model) {
		for (ErrorViewResolver resolver : this.errorViewResolvers) {
             //从所有的ErrorViewResolver得到ModelAndView，而其在ErrorMvcAutoConfiguration已被注册进容器
			ModelAndView modelAndView = resolver.resolveErrorView(request, status, model);
			if (modelAndView != null) {
				return modelAndView;
			}
		}
		return null;
	}
```



#### DefaultErrorViewResolver

```java
    @Configuration(
        proxyBeanMethods = false
    )
    static class DefaultErrorViewResolverConfiguration {
        private final ApplicationContext applicationContext;
        private final ResourceProperties resourceProperties;

        DefaultErrorViewResolverConfiguration(ApplicationContext applicationContext, ResourceProperties resourceProperties) {
            this.applicationContext = applicationContext;
            this.resourceProperties = resourceProperties;
        }

        //注册默认错误视图解析器
        @Bean
        @ConditionalOnBean({DispatcherServlet.class})
        @ConditionalOnMissingBean({ErrorViewResolver.class})
        DefaultErrorViewResolver conventionErrorViewResolver() {
            return new DefaultErrorViewResolver(this.applicationContext, this.resourceProperties);
        }
    }
```

然后调用ErrorViewResolver的`resolveErrorView()`方法

```java
    public ModelAndView resolveErrorView(HttpServletRequest request, HttpStatus status, Map<String, Object> model) {
        //把状态码和model传过去获取视图
        ModelAndView modelAndView = this.resolve(String.valueOf(status.value()), model);

        //上面没有获取到视图就使用把状态吗替换再再找，以4开头的替换为4xx，5开头替换为5xx，见下文（如果定制错误响应）
        if (modelAndView == null && SERIES_VIEWS.containsKey(status.series())) {
            modelAndView = this.resolve((String)SERIES_VIEWS.get(status.series()), model);
        }

        return modelAndView;
    }

    private ModelAndView resolve(String viewName, Map<String, Object> model) {
        //viewName传过来的是状态码，例：/error/404
        String errorViewName = "error/" + viewName;
        TemplateAvailabilityProvider provider = this.templateAvailabilityProviders.getProvider(errorViewName, this.applicationContext);
        //模板引擎可以解析这个页面地址就用模板引擎解析，弱国没有模板引擎则调用resolveResource方法获取视图,getProvider会在template路径下有没有erroor与相应的viewName
        return provider != null ? new ModelAndView(errorViewName, model) : this.resolveResource(errorViewName, model);
    }
```

如果模板引擎不可用，就调用resolveResource方法获取视图，按静态资源等级调用相应的文件

```java
    private ModelAndView resolveResource(String viewName, Map<String, Object> model) {
        //获取的是静态资源文件夹
        String[] var3 = this.resourceProperties.getStaticLocations();
        int var4 = var3.length;

        for(int var5 = 0; var5 < var4; ++var5) {
            String location = var3[var5];

            try {
                Resource resource = this.applicationContext.getResource(location);
                //例：static/error.html
                resource = resource.createRelative(viewName + ".html");
                //存在则返回视图
                if (resource.exists()) {
                    return new ModelAndView(new DefaultErrorViewResolver.HtmlResourceView(resource), model);
                }
            } catch (Exception var8) {
            }
        }

        return null;
    }

```

#### DefaultErrorAttributes

我们找到了路径，根据信息头知道要加载的html文件是哪个，那么template解析器做了什么呢？答案就是basicErrorControlle

创建的model是由DefaultErrorAttributes中将消息头中的信息addattribute到了model后返回的，因此，resovler解析的html中可以调用model的参数用thymeleaf解析器解析

* 新建的BasicErrorController会引用ErrorAttributes

```java
public BasicErrorController(ErrorAttributes errorAttributes, ErrorProperties errorProperties,
			List<ErrorViewResolver> errorViewResolvers) {
		super(errorAttributes, errorViewResolvers);
		Assert.notNull(errorProperties, "ErrorProperties must not be null");
		this.errorProperties = errorProperties;
	}
```

* 再看一下BasicErrorController的errorHtml方法

```java
    public ModelAndView errorHtml(HttpServletRequest request, HttpServletResponse response) {
        HttpStatus status = this.getStatus(request);

        //model的数据
        Map<String, Object> model = Collections.unmodifiableMap(this.getErrorAttributes(request, this.isIncludeStackTrace(request, MediaType.TEXT_HTML)));
        response.setStatus(status.value());
        ModelAndView modelAndView = this.resolveErrorView(request, response, status, model);
        return modelAndView != null ? modelAndView : new ModelAndView("error", model);
    }
```

* 看一下调用的this.getErrorAttributes()方法

  ```java
      public Map<String, Object> getErrorAttributes(WebRequest webRequest, boolean includeStackTrace) {
          Map<String, Object> errorAttributes = new LinkedHashMap();
          errorAttributes.put("timestamp", new Date());
          this.addStatus(errorAttributes, webRequest);
          this.addErrorDetails(errorAttributes, webRequest, includeStackTrace);
          this.addPath(errorAttributes, webRequest);
          return errorAttributes;
      }
  ```

* 再看 this.errorAttributes.getErrorAttributes()方法， this.errorAttributes是接口类型ErrorAttributes，实现类就一个`DefaultErrorAttributes`，看一下`DefaultErrorAttributes`的 getErrorAttributes()方法

```java
    // getErrorAttributes有很多重载方法
public Map<String, Object> getErrorAttributes(WebRequest webRequest, boolean includeStackTrace) {
        Map<String, Object> errorAttributes = new LinkedHashMap();
        errorAttributes.put("timestamp", new Date());
        this.addStatus(errorAttributes, webRequest);
        this.addErrorDetails(errorAttributes, webRequest, includeStackTrace);
        this.addPath(errorAttributes, webRequest);
        return errorAttributes;
    }
```

- timestamp：时间戳
- status：状态码
- error：错误提示
- exception：异常对象
- message：异常消息
- errors：JSR303数据校验的错误都在这里

2.0以后默认是不显示exception的，需要在配置文件中开启

```java
server.error.include-exception=true
```

原因：

![1574001983704](https://cdn.static.note.zzrfdsn.cn/images/springboot/assets/1574001983704.png)

在注册时

![1574002101183](https://cdn.static.note.zzrfdsn.cn/images/springboot/assets/1574002101183.png)

- 没有视图解析器或者如果template和静态资源文件夹下都没有找到错误页面，就是默认来到SpringBoot默认的错误提示页面；

#### defaultErrorView

```java
	@RequestMapping(produces = MediaType.TEXT_HTML_VALUE)
	public ModelAndView errorHtml(HttpServletRequest request, HttpServletResponse response) {
		HttpStatus status = getStatus(request);
		Map<String, Object> model = Collections
				.unmodifiableMap(getErrorAttributes(request, getErrorAttributeOptions(request, MediaType.TEXT_HTML)));
		response.setStatus(status.value());
		ModelAndView modelAndView = resolveErrorView(request, response, status, model);
        //如果template和静态资源文件夹下都没有找到错误页面，就是默认来到SpringBoot默认的错误提示页面
		return (modelAndView != null) ? modelAndView : new ModelAndView("error", model);
	}

```

* ==modelAndView是根据 viewName来查看使用哪个视图对象的==，因此默认的错误提示页面也需要注册一个自己的name为“error”的View对象()

```java
@Bean(name = "error")
		@ConditionalOnMissingBean(name = "error")
		public View defaultErrorView() {
			return this.defaultErrorView;
		}

```

* 他是用java代码生成一个页面的

```java
private static class StaticView implements View {

   private static final MediaType TEXT_HTML_UTF8 = new MediaType("text", "html", StandardCharsets.UTF_8);

   private static final Log logger = LogFactory.getLog(StaticView.class);

   @Override
   public void render(Map<String, ?> model, HttpServletRequest request, HttpServletResponse response)
         throws Exception {
      if (response.isCommitted()) {
         String message = getMessage(model);
         logger.error(message);
         return;
      }
      response.setContentType(TEXT_HTML_UTF8.toString());
      StringBuilder builder = new StringBuilder();
      Date timestamp = (Date) model.get("timestamp");
      Object message = model.get("message");
      Object trace = model.get("trace");
      if (response.getContentType() == null) {
         response.setContentType(getContentType());
      }
      builder.append("<html><body><h1>Whitelabel Error Page</h1>").append(
            "<p>This application has no explicit mapping for /error, so you are seeing this as a fallback.</p>")
            .append("<div id='created'>").append(timestamp).append("</div>")
            .append("<div>There was an unexpected error (type=").append(htmlEscape(model.get("error")))
            .append(", status=").append(htmlEscape(model.get("status"))).append(").</div>");
      if (message != null) {
         builder.append("<div>").append(htmlEscape(message)).append("</div>");
      }
      if (trace != null) {
         builder.append("<div style='white-space:pre-wrap;'>").append(htmlEscape(trace)).append("</div>");
      }
      builder.append("</body></html>");
      response.getWriter().append(builder.toString());
   }
```

#### 错误响应页面

通过/erroe请求响应

由basicErrorController生成model和调用视图解析器生成view

有视图解析器的情况下：

有模板引擎的情况下；将错误页面命名为 `错误状态码.html` 放在模板引擎文件夹里面的 error文件夹下发生此状态码的错误就会来到这里找对应的页面；

比如我们在template文件夹下创建error/404.html当浏览器请求是404错误，就会使用我们创建的404.html页面响应，如果是其他状态码错误，还是使用默认的视图

但是如果404.html没有找到就会替换成4XX.html再查找一次

若没有模板引擎则用静态文件夹中的error/中的`错误状态码.html`

没有视图解析器的情况下：

调用自动配置生成的errorVIEW

VIEW与model生成modelAndView对象，再交还basicErrorController，返回给dispatcher交由Mark渲染视图给用户

* ==浏览器读取的都是由getErrorAttributeOptions(request, MediaType.TEXT_HTML)加入model中再由页面决定读取哪些==

* ==客户端直接将全部getErrorAttributeOptions读取到的写到Body中==

  ```java
  Map<String, Object> body = getErrorAttributes(request, getErrorAttributeOptions(request, MediaType.ALL));
  ```

#### 自定义错误数据进容器

* 自此，前面我们有了根据request中信息的model，但是我们还不能更改成自己想要的样式
* 我们现在只能做如更改异常抛出的消息内容这类已经由defaultAttribute设置的model中的数据

###### 更改已有的错误信息类型的信息内容



* 若是更改的是Exception的内容

1. 自定义新的异常类，异常时抛出新的异常类，调用默认的ExceptionHandler

* 自定义一个Exception类

```java
public class UserNotExistException extends RuntimeException{
    public UserNotExistException() {
        super("用户不存在");
    }
}
```

* 在controller中捕捉异常并抛出上面的类
* 传入/error请求的exception将为上面的自定义exception

2. 自定义一个ExceptionHandler

> 由@ExceptionHandler(ExceptionName.class)注解捕获
>
> @ControllerAdvice 这是一个增强的 Controller。使用这个 Controller ，可以实现三个方面的功能：（与其他ControllerAdvice 互补协调处理）
>
> 1. 全局异常处理
> 2. 全局数据绑定
> 3. 全局数据预处理
>
> #### 全局异常处理
>
> 使用 @ControllerAdvice 实现全局异常处理，只需要定义类，添加该注解即可定义方式如下：
>
> ```java
> @ControllerAdvice
> public class MyGlobalExceptionHandler {
>     @ExceptionHandler(Exception.class)
>     public ModelAndView customException(Exception e) {
>         ModelAndView mv = new ModelAndView();
>         mv.addObject("message", e.getMessage());
>         mv.setViewName("myerror");
>         return mv;
>     }
> }
> ```
>
> 在该类中，可以定义多个方法，不同的方法处理不同的异常，例如专门处理空指针的方法、专门处理数组越界的方法...，也可以直接向上面代码一样，在一个方法中处理所有的异常信息。
>
> @ExceptionHandler 注解用来指明异常的处理类型，即如果这里指定为 NullpointerException，则数组越界异常就不会进到这个方法中来。
>
> #### 全局数据绑定
>
> 全局数据绑定功能可以用来做一些初始化的数据操作，我们可以将一些公共的数据定义在添加了 @ControllerAdvice 注解的类中，这样，在每一个 Controller 的接口中，就都能够访问导致这些数据。
>
> 使用步骤，首先定义全局数据，如下：
>
> ```java
> @ControllerAdvice
> public class MyGlobalExceptionHandler {
>     @ModelAttribute(name = "md")
>     public Map<String,Object> mydata() {
>         HashMap<String, Object> map = new HashMap<>();
>         map.put("age", 99);
>         map.put("gender", "男");
>         return map;
>     }
> }
> ```
>
> 使用 @ModelAttribute 注解标记该方法的返回数据是一个全局数据，默认情况下，这个全局数据的 key 就是返回的变量名，value 就是方法返回值，当然开发者可以通过 @ModelAttribute 注解的 name 属性去重新指定 key。
>
> 定义完成后，在任何一个Controller 的接口中，都可以获取到这里定义的数据：
>
> ```java
> @RestController
> public class HelloController {
>     @GetMapping("/hello")
>     public String hello(Model model) {
>         Map<String, Object> map = model.asMap();
>         System.out.println(map);
>         int i = 1 / 0;
>         return "hello controller advice";
>     }
> }
> ```
>
> #### 全局数据预处理
>
> 考虑我有两个实体类，Book 和 Author，分别定义如下：
>
> ```java
> public class Book {
>     private String name;
>     private Long price;
>     //getter/setter
> }
> public class Author {
>     private String name;
>     private Integer age;
>     //getter/setter
> }
> ```
>
> 此时，如果我定义一个数据添加接口，如下：
>
> ```java
> @PostMapping("/book")
> public void addBook(Book book, Author author) {
>     System.out.println(book);
>     System.out.println(author);
> }
> ```
>
> 这个时候，添加操作就会有问题，因为两个实体类都有一个 name 属性，从前端传递时 ，无法区分。此时，通过 @ControllerAdvice 的全局数据预处理可以解决这个问题
>
> 解决步骤如下:
>
> 1.给接口中的变量取别名
>
> ```java
> @PostMapping("/book")
> public void addBook(@ModelAttribute("b") Book book, @ModelAttribute("a") Author author) {
>     System.out.println(book);
>     System.out.println(author);
> }
> ```
>
> 2.进行请求数据预处理
> 在 @ControllerAdvice 标记的类中添加如下代码:
>
> ```java
> @InitBinder("b")
> public void b(WebDataBinder binder) {
>     binder.setFieldDefaultPrefix("b.");
> }
> @InitBinder("a")
> public void a(WebDataBinder binder) {
>     binder.setFieldDefaultPrefix("a.");
> }
> ```
>
> @InitBinder("b") 注解表示该方法用来处理和Book和相关的参数,在方法中,给参数添加一个 b 前缀,即请求参数要有b前缀.
>
> 3.发送请求
>
> 请求发送时,通过给不同对象的参数添加不同的前缀,可以实现参数的区分.
>
> ![img](https://www.javaboy.org/images/boot/5-1.png)



###### 自定义model样式

* 由于model是在basicErrorController中创建的，若是不打算重写basicErrorController，且想要分为浏览器与客户端两种，则需要转发到/error
* ==若是打算重写ErrorController，也不需要ErrorPageCustomizer来定制需要的路径了，但是他还是会加载以应对出现未捕获异常能跳转到/error加载springboot的默认error页面==

**第一种方法，定义全局异常处理器类注入到容器中，捕获到异常返回json格式的数据**

```java
@ControllerAdvice
public class MyExceptionHandler {
 
    @ResponseBody
    @ExceptionHandler(Exception.class)
    public Map<String, Object> handleException(Exception e) {
        Map<String, Object> map = new HashMap(2);
        map.put("code", "100011");
        map.put("msg", e.getMessage());
        return map;
    }
}
```

```java
@ResponseBody
@RequestMapping("/hello")//id的时候用/{id}接收是为了与add页面区分，若编辑的url用？或者（）拼接则两个方法无法区分
public String hello(@RequestParam("user") String user){
    if(user.equals("aaa")){
        throw new UserNotExistException();
    }

    return "Hello world";
}
```

这样的话，不管是浏览器访问还是客户端访问都是响应json数据，就没有了自适应效果

**第二种方法，捕获到异常后转发到/error**

```java
@ExceptionHandler(UserNotExistException.class)
public String handleException(Exception e, HttpServletRequest request) {
    Map<String, Object> map = new HashMap(2);
    map.put("code", "100011");
    map.put("msg", e.getMessage());
    //此时request被上一个controller接收到，requerst的状态码会被dispatcher更改为200 若未被处理则会默认是200
    request.setAttribute("javax.servlet.error.status_code", 500);
    return "forward:/error";
}
```

* 但是因为我们之前直接用map发送的，未经过分类处理，便用`@ResponseBody`转换成JSON发送回了浏览器/客户端

* 但此时用了`BasicErrorController`，而其model是引用`defaultErrorAttribute`的，其又是从request中获取的
* 因此我们需要绑定数据到request中再自定义一个ErrorAtributes 提取到model中，如果不想重写真个类直接继承defaultErrorAttribut，并重写getErrorAttributes即可

```java
@ControllerAdvice
public class MyExceptionHandler {

    @ExceptionHandler(UserNotExistException.class)
    public String handleException(Exception e, HttpServletRequest request) {
        Map<String, Object> map = new HashMap<>();
        map.put("code", "100011");
        map.put("msg", e.getMessage());
        //此时request被上一个controller接收到，requerst的状态码会被dispatcher更改为200 若未被处理则会默认是200
        request.setAttribute("javax.servlet.error.status_code", 400);
        request.setAttribute("ext", map);
        return "forward:/error";
    }
}
```

```java
@Component
public class MyErrorAttributes extends DefaultErrorAttributes {
    @Override

    public Map<String, Object> getErrorAttributes(WebRequest webRequest, ErrorAttributeOptions options) {
        Map<String, Object> map = super.getErrorAttributes(webRequest, options);
        map.put("company","你猜啊");
        Map<String, Object>  ext = (Map<String, Object>)webRequest.getAttribute("ext", 0);
        map.put("ext",ext);
        return map;
    }
}
```

## 配置嵌入式Servlet容器

#### 定制和修改Servlet容器的server相关配置

1. 修改和server有关的配置

   ```properties
   server.port=8081
   server.context-path=/crud
   
   server.tomcat.uri-encoding=UTF-8
   
   //通用的Servlet容器设置
   server.xxx
   //Tomcat的设置
   server.tomcat.xxx
   ```

2. 编写一个~~EmbeddedServletContainerCustomizer~~，2.0以后改为`WebServerFactoryCustomizer`：嵌入式的Servlet容器的定制器；来修改Servlet容器的配置，==代码方式的配置会覆盖配置文件的配置==（和其他Customizer一起代理生成）

   ```java
   @Configuration
   public class MyMvcConfig implements WebMvcConfigurer {
       @Bean
       public WebServerFactoryCustomizer webServerFactoryCustomizer() {
           return new WebServerFactoryCustomizer<ConfigurableWebServerFactory>() {
               @Override
               public void customize(ConfigurableWebServerFactory factory) {
                   factory.setPort(8088);
               }
           };
       }
   ......
   ```

#### 修改server配置原理

`ServletWebServerFactoryAutoConfiguration`在向容器中添加web容器时还添加了一个组件

![1574235580031](https://cdn.static.note.zzrfdsn.cn/images/springboot/assets/1574235580031.png)

`BeanPostProcessorsRegistrar`：后置处理器注册器(实现了`BeanPostProcessor`，动态代理后后置处理生成一些Bean)

```java
    public static class BeanPostProcessorsRegistrar implements ImportBeanDefinitionRegistrar, BeanFactoryAware {
        private ConfigurableListableBeanFactory beanFactory;

        public BeanPostProcessorsRegistrar() {...}

        public void setBeanFactory(BeanFactory beanFactory) throws BeansException {...}

        public void registerBeanDefinitions(AnnotationMetadata importingClassMetadata, BeanDefinitionRegistry registry) {
            if (this.beanFactory != null) {
                //注册了下面的组件
              registerSyntheticBeanIfMissing(registry, "webServerFactoryCustomizerBeanPostProcessor",
					WebServerFactoryCustomizerBeanPostProcessor.class);
            }
        }

        private void registerSyntheticBeanIfMissing(BeanDefinitionRegistry registry, String name, Class<?> beanClass) {...}
    }



public class WebServerFactoryCustomizerBeanPostProcessor implements BeanPostProcessor, BeanFactoryAware {

    ......

    //在Bean初始化之前
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        //判断添加的Bean是不是WebServerFactory类型的
        if (bean instanceof WebServerFactory) {
            this.postProcessBeforeInitialization((WebServerFactory)bean);
        }

        return bean;
    }

    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        return bean;
    }

    private void postProcessBeforeInitialization(WebServerFactory webServerFactory) {
        //获取所有的定制器，调用每一个定制器的customize方法来给Servlet容器进行属性赋值；
        ((Callbacks)LambdaSafe.callbacks(WebServerFactoryCustomizer.class, this.getCustomizers(), webServerFactory, new Object[0]).withLogger(WebServerFactoryCustomizerBeanPostProcessor.class)).invoke((customizer) -> {
            customizer.customize(webServerFactory);
        });
    }
```

> #### 1. BeanPostProcessor简介
>
> BeanPostProcessor是Spring IOC容器给我们提供的一个扩展接口。接口声明如下：
>
> 
>
> ```java
> public interface BeanPostProcessor {
>     //bean初始化方法调用前被调用
>     Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException;
>     //bean初始化方法调用后被调用
>     Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException;
> }
> ```
>
> **运行顺序**
>
> ==Spring IOC容器实例化Bean==
>  ==调用BeanPostProcessor的postProcessBeforeInitialization方法==
>  ==调用bean实例的初始化方法==
>  ==调用BeanPostProcessor的postProcessAfterInitialization方法==
>
> ![img](https:////upload-images.jianshu.io/upload_images/13150128-bb5c9389cd0acc6c.png?imageMogr2/auto-orient/strip|imageView2/2/w/844/format/webp)
>
> image.png
>
> #### 2. BeanPostProcessor实例
>
> ```dart
> /**
>  * 后置处理器：初始化前后进行处理工作
>  * 将后置处理器加入到容器中
>  */
> @Component
> public class MyBeanPostProcessor implements BeanPostProcessor {
> 
>     @Override
>     public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
>         // TODO Auto-generated method stub
>         System.out.println("postProcessBeforeInitialization..."+beanName+"=>"+bean);
>         return bean;
>     }
> 
>     @Override
>     public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
>         // TODO Auto-generated method stub
>         System.out.println("postProcessAfterInitialization..."+beanName+"=>"+bean);
>         return bean;
>     }
> 
> }
> ```
>
> #### 3. BeanFactoryPostProcessor简介
>
> bean工厂的bean属性处理容器，说通俗一些就是可以管理我们的bean工厂内所有的beandefinition（未实例化）数据，可以随心所欲的修改属性。
>
> #### 4. BeanFactoryPostProcessor实例
>
> ```java
> @Component
> public class MyBeanFactoryPostProcessor implements BeanFactoryPostProcessor {
> 
>     @Override
>     public void postProcessBeanFactory(ConfigurableListableBeanFactory beanFactory) throws BeansException {
>         System.out.println("MyBeanFactoryPostProcessor...postProcessBeanFactory...");
>         int count = beanFactory.getBeanDefinitionCount();
>         String[] names = beanFactory.getBeanDefinitionNames();
>         System.out.println("当前BeanFactory中有"+count+" 个Bean");
>         System.out.println(Arrays.asList(names));
>     }
> 
> }
> ```
>
> **区别：**
>
> 注册BeanFactoryPostProcessor的实例，需要重载
>
> void postProcessBeanFactory(ConfigurableListableBeanFactory beanFactory) throws BeansException;
>
> 通过beanFactory可以获取bean的示例或定义等。同时可以修改bean的属性，这是和BeanPostProcessor最大的区别。

关于==如何AutoConfiguration中Customizer配置文件是如何设置的==，参考`EmbeddedWebServerFactoryCustomizerAutoConfiguration`类，最后若是常用设置，还是使用propertis配置更为方便。

```java
@Configuration(proxyBeanMethods = false)
@ConditionalOnWebApplication
@EnableConfigurationProperties(ServerProperties.class)
public class EmbeddedWebServerFactoryCustomizerAutoConfiguration {

	/**
	 * Nested configuration if Tomcat is being used.
	 */
	@Configuration(proxyBeanMethods = false)
    // 若嵌入式Servlet容器导入的为Tomcat的依赖
	@ConditionalOnClass({ Tomcat.class, UpgradeProtocol.class })
	public static class TomcatWebServerFactoryCustomizerConfiguration {

		@Bean
		public TomcatWebServerFactoryCustomizer tomcatWebServerFactoryCustomizer(Environment environment,
				ServerProperties serverProperties) {
			return new TomcatWebServerFactoryCustomizer(environment, serverProperties);
		}
}

```

**该类也实现了WebServerFactoryCustomizer，只是customize方法调用的参数配置不一样，最终都由BeanPostProcessorsRegistrar在创建容器前动态代理与其他WebServerFactoryCustomizer互补配置生成代理类。**

```java
public class TomcatWebServerFactoryCustomizer
		implements WebServerFactoryCustomizer，<ConfigurableTomcatWebServerFactory>, Ordered {

	private final Environment environment;

	private final ServerProperties serverProperties;

	public TomcatWebServerFactoryCustomizer(Environment environment, ServerProperties serverProperties) {
		this.environment = environment;
		this.serverProperties = serverProperties;
	}
    @Override
	public void customize(ConfigurableTomcatWebServerFactory factory) {
        ·····
    }
}

```

总结：

1. SpringBoot根据导入的依赖情况，给容器中添加相应的`XXX`ServletWebServerFactory

2. 容器中某个组件要创建对象就会惊动后置处理器 `webServerFactoryCustomizerBeanPostProcessor`

   只要是嵌入式的是Servlet容器工厂，后置处理器就会工作；

3. 后置处理器，从容器中获取所有的`WebServerFactoryCustomizer`，调用定制器的定制方法给工厂添加配置

#### 注册servlet三大组件

由于SpringBoot默认是以jar包的方式启动嵌入式的Servlet容器来启动SpringBoot的web应用，没有web.xml文件。

###### Servlet

向容器中添加ServletRegistrationBean

```java
@Configuration
public class MyMvcConfig implements WebMvcConfigurer {

    @Bean
    public ServletRegistrationBean myServlet() {
        ServletRegistrationBean register = new ServletRegistrationBean(new MyServlet(), "/myServlet");
        register.setLoadOnStartup(1);
        return register;
    }
    ......
```

* myservlet

```java
public class MyServlet extends HttpServlet {

    @Override
    public void init() throws ServletException {
        System.out.println("servlet初始化");
    }

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        this.doPost(req, resp);
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.getWriter().write("this is MyServlet");
    }

}
```

###### Filter

```java
@Configuration
public class MyMvcConfig implements WebMvcConfigurer {


    @Bean
    public FilterRegistrationBean myFilter() {
        FilterRegistrationBean register = new FilterRegistrationBean(new MyFilter());
        register.setUrlPatterns(Arrays.asList("/myServlet","/"));
        return register;
    }

    ......
```

```java
public class MyFilter extends HttpFilter {
    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
        response.getWriter().write("请求被拦截......");
    }
}
```

###### Listener

向容器中注入ServletListenerRegistrationBean

```java
@Configuration
public class MyMvcConfig implements WebMvcConfigurer {

    @Bean
    public ServletListenerRegistrationBean myServletContextListener(){
        return new ServletListenerRegistrationBean(new MyServletContextListener());
    }

    ......

```

```java
public class MyServletContextListener implements ServletContextListener {
    @Override
    public void contextInitialized(ServletContextEvent sce) {
        System.out.println("web容器   启动......");
    }

    @Override
    public void contextDestroyed(ServletContextEvent sce) {
        System.out.println("web容器   销毁......");
    }
}
```

* 三个组件都只是简单的将容器注入进去，原理应该也是扫描所有相同ServletxxxRegistrationBean类型的Bean，在ServletAutoConfiguration中被扫描生成，依次加入ServletContext中。

#### 替换为其他嵌入式web容器

![1574170320042](https://cdn.static.note.zzrfdsn.cn/images/springboot/assets/1574170320042.png)

如果要换成其他的就把Tomcat的依赖排除掉，然后引入其他嵌入式Servlet容器的以来，如`Jetty`，`Undertow`

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <exclusions>
                <exclusion>
                    <artifactId>spring-boot-starter-tomcat</artifactId>
                    <groupId>org.springframework.boot</groupId>
                </exclusion>
            </exclusions>
        </dependency>

        <dependency>
            <artifactId>spring-boot-starter-jetty</artifactId>
            <groupId>org.springframework.boot</groupId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <exclusions>
                <exclusion>
                    <artifactId>spring-boot-starter-tomcat</artifactId>
                    <groupId>org.springframework.boot</groupId>
                </exclusion>
            </exclusions>
        </dependency>

        <dependency>
            <artifactId>spring-boot-starter-undertow</artifactId>
            <groupId>org.springframework.boot</groupId>
        </dependency>
```

#### 替换原理

**查看web容器自动配置类**

1. ==`ServletWebServerFactoryAutoConfiguration`在当是服务端的情况下必然启动的配置，在其中完成2版本之前的AnnotationConfigEmbeddedApplicationContext的Embedded判断==
2. `xxxWebServerFactoryCustomizer`嵌入式web服务器对于自己serverproperties的servler参数的详细设置。
3. @Import`ServletWebServerFactoryConfiguration.EmbeddedTomcat.class`时会判断是否符合条件，他将读取`EmbeddedWebServerFactoryCustomizerAutoConfiguration`中的`TomcatWebServerFactoryCustomizer`和自己配置的`WebServerFactoryCustomizer`来生成factory
4. ==若是不是内嵌的servlet，则将下面的两个生效在外部的servlet上（可能）==（可能在servletContext上区分除了外部和内嵌）
5. `ServletWebServerFactoryCustomizer`读取了配置文件的基本配置，对应于所有内嵌容器中中的server.xml+web.xml中的通用部分提取。
6. `TomcatServletWebServerFactoryCustomizer`定制的若是tomcat服务器，则进行一些server中对于servlet的特殊参数的设置

2.0以下是：~~*EmbeddedServletContainerAutoConfiguration*~~

`ServletWebServerFactoryAutoConfiguration`：嵌入式的web服务器自动配置

```java
@Configuration(
    proxyBeanMethods = false
)
@AutoConfigureOrder(-2147483648)
@ConditionalOnClass({ServletRequest.class})
@ConditionalOnWebApplication(
    type = Type.SERVLET
)
@EnableConfigurationProperties({ServerProperties.class})

//---看这里---
@Import({ServletWebServerFactoryAutoConfiguration.BeanPostProcessorsRegistrar.class, 							ServletWebServerFactoryConfiguration.EmbeddedTomcat.class,
		ServletWebServerFactoryConfiguration.EmbeddedJetty.class,
		ServletWebServerFactoryConfiguration.EmbeddedUndertow.class})
public class ServletWebServerFactoryAutoConfiguration {
    ···
}
```

`ServletWebServerFactoryConfiguration.EmbeddedTomcat.class`：

```java
@Configuration(proxyBeanMethods = false)
class ServletWebServerFactoryConfiguration {

	@Configuration(proxyBeanMethods = false)
        //判断当前是否引入了Tomcat依赖；
	@ConditionalOnClass({ Servlet.class, Tomcat.class, UpgradeProtocol.class })
    	/**
      *判断当前容器没有用户自己定义ServletWebServerFactory：嵌入式的web服务器工厂；
      *作用：创建嵌入式的web服务器
      */
	@ConditionalOnMissingBean(value = ServletWebServerFactory.class, search = SearchStrategy.CURRENT)
	static class EmbeddedTomcat {

		@Bean
		TomcatServletWebServerFactory tomcatServletWebServerFactory(
				ObjectProvider<TomcatConnectorCustomizer> connectorCustomizers,
				ObjectProvider<TomcatContextCustomizer> contextCustomizers,
				ObjectProvider<TomcatProtocolHandlerCustomizer<?>> protocolHandlerCustomizers) {
			TomcatServletWebServerFactory factory = new TomcatServletWebServerFactory();
			factory.getTomcatConnectorCustomizers()
					.addAll(connectorCustomizers.orderedStream().collect(Collectors.toList()));
			factory.getTomcatContextCustomizers()
					.addAll(contextCustomizers.orderedStream().collect(Collectors.toList()));
			factory.getTomcatProtocolHandlerCustomizers()
					.addAll(protocolHandlerCustomizers.orderedStream().collect(Collectors.toList()));
			return factory;
		}

	}
```

我们可以实现`ServletWebServerFactory`接口来自定义`ServletWebServerFactory`，它默认为我们实现了常用的服务器。

以`TomcatServletWebServerFactory`为例，接口只要求实现一个便是，getWebServer方法，下面是TomcatServletWebServerFactory类

```java
    public WebServer getWebServer(ServletContextInitializer... initializers) {
        if (this.disableMBeanRegistry) {
            Registry.disableRegistry();
        }

        //创建一个Tomcat
        Tomcat tomcat = new Tomcat();

        //配置Tomcat的基本环境，（tomcat的配置都是从本类获取的，tomcat.setXXX）
        File baseDir = this.baseDirectory != null ? this.baseDirectory : this.createTempDir("tomcat");
        tomcat.setBaseDir(baseDir.getAbsolutePath());
        Connector connector = new Connector(this.protocol);
        connector.setThrowOnFailure(true);
        tomcat.getService().addConnector(connector);
        this.customizeConnector(connector);
        tomcat.setConnector(connector);
        tomcat.getHost().setAutoDeploy(false);
        this.configureEngine(tomcat.getEngine());
        Iterator var5 = this.additionalTomcatConnectors.iterator();

        while(var5.hasNext()) {
            Connector additionalConnector = (Connector)var5.next();
            tomcat.getService().addConnector(additionalConnector);
        }

        this.prepareContext(tomcat.getHost(), initializers);

        //将配置好的Tomcat传入进去，返回一个WebServer；并且启动Tomcat服务器
        return this.getTomcatWebServer(tomcat);
    }
```

* 依据不同的服务器类型选择，生成不同的服务器，用多个`WebServerFactoryCustomizer`最后代理生成的`TomcatServletWebServerFactory`（由修改配置原理的2和3配置互补生成同一种类型的factory）
* 可以自己自定义或内嵌的生成相应的xxxServletebServerFactory。

#### 内置Servlet的启动原理

内置与外置的差别在于createApplicationContext()的的结果不同，

1. SpringApplication.run
2. SpringBoot应用启动运行run方法

```java
public ConfigurableApplicationContext run(String... args) {
   StopWatch stopWatch = new StopWatch();
   stopWatch.start();
   ConfigurableApplicationContext context = null;
   Collection<SpringBootExceptionReporter> exceptionReporters = new ArrayList<>();
   configureHeadlessProperty();
   SpringApplicationRunListeners listeners = getRunListeners(args);
   listeners.starting();
   try {
      ApplicationArguments applicationArguments = new DefaultApplicationArguments(args);
      ConfigurableEnvironment environment = prepareEnvironment(listeners, applicationArguments);
      configureIgnoreBeanInfo(environment);
      Banner printedBanner = printBanner(environment);
       
      // 若是默认配置且为web应用怎会调用AnnotationConfigServletWebServerApplicationContext，1.5的版本仍是需要AnnotationConfigEmbeddedApplicationContext
      context = createApplicationContext();
       /*try {
				switch (this.webApplicationType) {
				case SERVLET:
					contextClass = Class.forName(DEFAULT_SERVLET_WEB_CONTEXT_CLASS);
					break;
				case REACTIVE:
					contextClass = Class.forName(DEFAULT_REACTIVE_WEB_CONTEXT_CLASS);
					break;
				default:
					contextClass = Class.forName(DEFAULT_CONTEXT_CLASS);
				}
	 */
      exceptionReporters = getSpringFactoriesInstances(SpringBootExceptionReporter.class,
            new Class[] { ConfigurableApplicationContext.class }, context);
      prepareContext(context, environment, listeners, applicationArguments, printedBanner);
      //最终会调用相应的applicationContext的refresh()方法;
      refreshContext(context);
      afterRefresh(context, applicationArguments);
      stopWatch.stop();
      if (this.logStartupInfo) {
         new StartupInfoLogger(this.mainApplicationClass).logStarted(getApplicationLog(), stopWatch);
      }
      listeners.started(context);
      callRunners(context, applicationArguments);
   }
   catch (Throwable ex) {
      handleRunFailure(context, ex, exceptionReporters, listeners);
      throw new IllegalStateException(ex);
   }

   try {
      listeners.running(context);
   }
   catch (Throwable ex) {
      handleRunFailure(context, ex, exceptionReporters, null);
      throw new IllegalStateException(ex);
   }
   return context;
}
```

3. 查看`ServletWebServerApplicationContext`类的refresh方法

```java
	@Override
	public final void refresh() throws BeansException, IllegalStateException {
		try {
			super.refresh();
		}
		catch (RuntimeException ex) {
			WebServer webServer = this.webServer;
			if (webServer != null) {
				webServer.stop();
			}
			throw ex;
		}
	}
```

4. 调用其父类的refresh方法，其中try中对于bean对象的刷新全都在`ServletWebServerApplicationContext`中重写了，这里提供一个规范与注释，易于维护与理解，

```java
@Override
public void refresh() throws BeansException, IllegalStateException {
   synchronized (this.startupShutdownMonitor) {
      // Prepare this context for refreshing.
      prepareRefresh();

      // Tell the subclass to refresh the internal bean factory.
      ConfigurableListableBeanFactory beanFactory = obtainFreshBeanFactory();

      // Prepare the bean factory for use in this context.
      prepareBeanFactory(beanFactory);

      try {
         // Allows post-processing of the bean factory in context subclasses.
         postProcessBeanFactory(beanFactory);

         // Invoke factory processors registered as beans in the context.
         invokeBeanFactoryPostProcessors(beanFactory);

         // Register bean processors that intercept bean creation.
         registerBeanPostProcessors(beanFactory);

         // Initialize message source for this context.
         initMessageSource();

         // Initialize event multicaster for this context.
         initApplicationEventMulticaster();

         // Initialize other special beans in specific context subclasses.
         onRefresh();

         // Check for listener beans and register them.
         registerListeners();

         // Instantiate all remaining (non-lazy-init) singletons.
         finishBeanFactoryInitialization(beanFactory);

         // Last step: publish corresponding event.
         finishRefresh();
      }

      catch (BeansException ex) {
         if (logger.isWarnEnabled()) {
            logger.warn("Exception encountered during context initialization - " +
                  "cancelling refresh attempt: " + ex);
         }

         // Destroy already created singletons to avoid dangling resources.
         destroyBeans();

         // Reset 'active' flag.
         cancelRefresh(ex);

         // Propagate exception to caller.
         throw ex;
      }

      finally {
         // Reset common introspection caches in Spring's core, since we
         // might not ever need metadata for singleton beans anymore...
         resetCommonCaches();
      }
   }
}
```

6. 对于servlet的启动，重要的是onRefresh()方法

```java
@Override
protected void onRefresh() {
    //调用父类的onRefresh，输出了时间戳
   super.onRefresh();
   try {
       //创建服务器服务
      createWebServer();
   }
   catch (Throwable ex) {
      throw new ApplicationContextException("Unable to start web server", ex);
   }
}
```

7.createWebServer();

```java
private void createWebServer() {
   WebServer webServer = this.webServer;
   ServletContext servletContext = getServletContext();
   if (webServer == null && servletContext == null) {//可能在这里区分出了外部和内嵌的区别
       
       //获取当前系统由配置类自动生成到容器中的factory
      ServletWebServerFactory factory = getWebServerFactory();
       //用该factory生成一个对应类型的服务器,并在xxxWebServer的初始化时会启动xxx服务器。
       // 其中xxxServletWebServerFactory（若是tomcat)则会new tomcat和StandardServer等Tomcat外置服务器中同样 org.apache.catalina包中的类，之后和tomcat一样的启动流程。
      this.webServer = factory.getWebServer(getSelfInitializer());
      getBeanFactory().registerSingleton("webServerGracefulShutdown",
            new WebServerGracefulShutdownLifecycle(this.webServer));
      getBeanFactory().registerSingleton("webServerStartStop",
            new WebServerStartStopLifecycle(this, this.webServer));
   }
   else if (servletContext != null) {
      try {
         getSelfInitializer().onStartup(servletContext);
      }
      catch (ServletException ex) {
         throw new ApplicationContextException("Cannot initialize servlet context", ex);
      }
   }
   initPropertySources();
}
```

* 若获取的是tomcat服务器，则配置后启动

```java
@Override
public WebServer getWebServer(ServletContextInitializer... initializers) {
   if (this.disableMBeanRegistry) {
      Registry.disableRegistry();
   }
   // 给将配置的参数放置在tomcat类中以便初始化何启动
   // tomcat也为org.apache.catalina.startup包中的类，用于内嵌式时自己启动，自己配置server，service等组件，不同于用批处理命令开启读取xml文件创建server的方式
   Tomcat tomcat = new Tomcat();
   File baseDir = (this.baseDirectory != null) ? this.baseDirectory : createTempDir("tomcat");
   tomcat.setBaseDir(baseDir.getAbsolutePath());
   Connector connector = new Connector(this.protocol);
   connector.setThrowOnFailure(true);
   tomcat.getService().addConnector(connector);
   customizeConnector(connector);
   tomcat.setConnector(connector);
   tomcat.getHost().setAutoDeploy(false);
   configureEngine(tomcat.getEngine());
   for (Connector additionalConnector : this.additionalTomcatConnectors) {
      tomcat.getService().addConnector(additionalConnector);
   }
   prepareContext(tomcat.getHost(), initializers);
    //获取TomcatWebServer，设置启动级别，初始化若是1则同时启动
   return getTomcatWebServer(tomcat);
}
```

7. 嵌入式的Servlet容器创建对象并启动Servlet容器；

8. 嵌入式的Servlet容器启动后，再将ioc容器中剩下没有创建出的对象获取出来(Controller,Service等)；

## 使用外置的Servlet容器

1. 将项目的打包方式改为war

2. 编写一个类继承`SpringBootServletInitializer`，并重写configure方法，调用参数的sources方法springboot启动类传过去然后返回

   ```java
   public class ServletInitializer extends SpringBootServletInitializer {
       @Override
       protected SpringApplicationBuilder configure(SpringApplicationBuilder application) {
           return application.sources(HelloSpringBootWebApplication.class);
       }
   }
   ```

3. 然后把tomcat的依赖范围改为provided

   ```xml
       <dependencies>
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-web</artifactId>
           </dependency>
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-tomcat</artifactId>
               <version>2.2.1.RELEASE</version>
               <scope>provided</scope>
           </dependency>
   
           ......
   
       </dependencies>
   ```

4. 最后就可以把项目打包成war放到tomcat中了

5. 在IDEA中可以这样配置

   ![1574247311250](https://cdn.static.note.zzrfdsn.cn/images/springboot/assets/1574247311250.png)

6. 在创建项目时使用Spring Initializr创建选择打包方式为war，1，2，3步骤会自动配置

#### 使用外置Servlet的原理

*TODO* 2019-11-20

1. Servlet3.0标准ServletContainerInitializer扫描所有jar包中METAINF/services/javax.servlet.ServletContainerInitializer文件指定的类并加载

2. 还可以使用@HandlesTypes，在应用启动的时候加载我们感兴趣的类；

3. 在spring-web-xxx.jar包中的METAINF/services下有javax.servlet.ServletContainerInitializer这个文件

   文件中的类是：

   ```
   org.springframework.web.SpringServletContainerInitializer
   ```

   对应的类：

   ```java
   @HandlesTypes({WebApplicationInitializer.class})
   public class SpringServletContainerInitializer implements ServletContainerInitializer {
       public SpringServletContainerInitializer() {
       }
   
       public void onStartup(@Nullable Set<Class<?>> webAppInitializerClasses, ServletContext servletContext) throws ServletException {
   
           ......
   ```

4. SpringServletContainerInitializer将@HandlesTypes(WebApplicationInitializer.class)标注的所有这个类型的类都传入到onStartup方法的`Set<Class<?>>`；为这些WebApplicationInitializer类型的类创建实例；

5. 每一个WebApplicationInitializer都调用自己的onStartup方法；

6. WebApplicationInitializer的实现类

   ![1574256076145](https://cdn.static.note.zzrfdsn.cn/images/springboot/assets/1574256076145.png)

7. 相当于我们的SpringBootServletInitializer的类会被创建对象，并执行onStartup方法

8. SpringBootServletInitializer实例执行onStartup的时候会createRootApplicationContext；创建容器

   ```java
   protected WebApplicationContext createRootApplicationContext(
         ServletContext servletContext) {
       //1、创建SpringApplicationBuilder
      SpringApplicationBuilder builder = createSpringApplicationBuilder();
      StandardServletEnvironment environment = new StandardServletEnvironment();
      environment.initPropertySources(servletContext, null);
      builder.environment(environment);
      builder.main(getClass());
      ApplicationContext parent = getExistingRootWebApplicationContext(servletContext);
      if (parent != null) {
         this.logger.info("Root context already created (using as parent).");
         servletContext.setAttribute(
               WebApplicationContext.ROOT_WEB_APPLICATION_CONTEXT_ATTRIBUTE, null);
         builder.initializers(new ParentContextApplicationContextInitializer(parent));
      }
      builder.initializers(
            new ServletContextApplicationContextInitializer(servletContext));
      builder.contextClass(AnnotationConfigEmbeddedWebApplicationContext.class);
   
       //调用configure方法，子类重写了这个方法，将SpringBoot的主程序类传入了进来
      builder = configure(builder);
   
       //使用builder创建一个Spring应用
      SpringApplication application = builder.build();
      if (application.getSources().isEmpty() && AnnotationUtils
            .findAnnotation(getClass(), Configuration.class) != null) {
         application.getSources().add(getClass());
      }
      Assert.state(!application.getSources().isEmpty(),
            "No SpringApplication sources have been defined. Either override the "
                  + "configure method or add an @Configuration annotation");
      // Ensure error pages are registered
      if (this.registerErrorPageFilter) {
         application.getSources().add(ErrorPageFilterConfiguration.class);
      }
       //启动Spring应用
      return run(application);
   }
   ```

9. Spring的应用就启动并且创建IOC容器

   ```java
   public ConfigurableApplicationContext run(String... args) {
      StopWatch stopWatch = new StopWatch();
      stopWatch.start();
      ConfigurableApplicationContext context = null;
      FailureAnalyzers analyzers = null;
      configureHeadlessProperty();
      SpringApplicationRunListeners listeners = getRunListeners(args);
      listeners.starting();
      try {
         ApplicationArguments applicationArguments = new DefaultApplicationArguments(
               args);
         ConfigurableEnvironment environment = prepareEnvironment(listeners,
               applicationArguments);
         Banner printedBanner = printBanner(environment);
        // 若是web应用则调用AnnotationConfigServletWebServerApplicationContext，1.5的版本仍是需要AnnotationConfigEmbeddedApplicationContext
         context = createApplicationContext();
         analyzers = new FailureAnalyzers(context);
         prepareContext(context, environment, listeners, applicationArguments,
               printedBanner);
   
          //刷新IOC容器
         refreshContext(context);
         afterRefresh(context, applicationArguments);
         listeners.finished(context, null);
         stopWatch.stop();
         if (this.logStartupInfo) {
            new StartupInfoLogger(this.mainApplicationClass)
                  .logStarted(getApplicationLog(), stopWatch);
         }
         return context;
      }
      catch (Throwable ex) {
         handleRunFailure(context, listeners, analyzers, ex);
         throw new IllegalStateException(ex);
      }
   }
   ```

## springboot与数据库连接

* 无论是SQL还是NoSQL，Spring Boot默认采用整合SpringData的方式进行统一处理

* 只需引用xxxTemplate和xxxResposibility就行

==依赖==

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-jdbc</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.mybatis.spring.boot</groupId>
        <artifactId>mybatis-spring-boot-starter</artifactId>
        <version>2.1.1</version>
    </dependency>

    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <scope>runtime</scope>
    </dependency>
</dependencies>
```

==连接配置==

```yaml
spring:
  datasource:
    username: root
    password: root
    url: jdbc:mysql://172.16.145.137:3306/springboot
    driver-class-name: com.mysql.cj.jdbc.Driver
```

测试能否连接上数据库

```hava
@SpringBootTest
class SpringbootJdbcApplicationTests {

    @Autowired
    private DataSource dataSource;

    @Test
    void contextLoads() throws SQLException {
        System.out.println(dataSource.getClass());
        System.out.println(dataSource.getConnection());
    }

}
```

springboot默认是使用`com.zaxxer.hikari.HikariDataSource`作为数据源，2.0以下是用`org.apache.tomcat.jdbc.pool.DataSource`作为数据源；

数据源的相关配置都在DataSourceProperties里面；

#### 数据源自动配置原理

> 数据源：==（1. 底层也依靠JAVA提供的对不同的数据库数据操作提供的不同JDBC 2. 依据不同xxx.jdbc.Driver产生不同的连接池并管理）==
>
> JDBC2.0 提供了javax.sql.DataSource接口，它负责建立与数据库的连接，当在应用程序中访问数据库时
> 不必编写连接数据库的代码，直接引用DataSource获取数据库的连接对象即可。用于获取操作数据Connection对象。
>
> 数据源与数据库连接池：
>
> 数据源建立多个数据库连接，这些数据库连接会保存在数据库连接池中，当需要访问数据库时，只需要从数据库连接池中 获取空闲的数据库连接，当程序访问数据库结束时，数据库连接会放回数据库连接池中。 
>
> ==数据源可以附加监控数据库连接等多种工具==
>
> 传统的JDBC访问数据库技术，每次访问数据库都需要通过数据库驱动器Driver和数据库名称以及密码等等资源建立数据库连接。 这样的连接存在两大问题： 
>
> 1. 频繁的建立数据库连接与断开数据库，这样会消耗大量的资源和时间，降低性能。 
> 2. 数据库的连接需要用户名和密码等等，这些需要一定的内存和CPU一定开销。
>
> 而数据源就是就包括了数据库连接池的建立和对连接的管理，只需将用户信息放置在数据源的bean中并维护他就可以。

jdbc的相关配置都在`org.springframework.boot.autoconfigure.jdbc`包下

参考`DataSourceConfiguration`，根据配置创建数据源，默认使用Hikari连接池；可以使用spring.datasource.type指定自定义的数据源类型；

springboot默认支持的连池：

- org.apache.commons.dbcp2.BasicDataSource
- com.zaxxer.hikari.HikariDataSource
- org.apache.tomcat.jdbc.pool.DataSource

自定义数据源类型：

```java
    @Configuration(
        proxyBeanMethods = false
    )
    @ConditionalOnMissingBean({DataSource.class})
    @ConditionalOnProperty(
        name = {"spring.datasource.type"}
    )
    static class Generic {
        Generic() {
        }

        @Bean
        DataSource dataSource(DataSourceProperties properties) {
             //使用DataSourceBuilder创建数据源，利用反射创建响应type的数据源，并且绑定相关属性
            return properties.initializeDataSourceBuilder().build();
        }
    }
```

#### 数据源初始化自动配置

SpringBoot在创建连接池后还会运行预定义的SQL脚本文件，具体参考`org.springframework.boot.autoconfigure.jdbc.DataSourceInitializationConfiguration`配置类，

在该类中注册了`dataSourceInitializerPostProcessor` ：Configures DataSource initialization，配置数据库的初始化信息

```java
@Import({ DataSourceInitializerInvoker.class, DataSourceInitializationConfiguration.Registrar.class })
class DataSourceInitializationConfiguration {
}
```

其中`DataSourceInitializerInvoker`是一个`ApplicationListener`，当监听到数据库初始化时会调用`DataSourceInitializer`

该类会有项目初始化的具体类，如初始化表

```java
void initSchema() {
    //获取需要执行的脚本
   List<Resource> scripts = getScripts("spring.datasource.data", this.properties.getData(), "data");
   if (!scripts.isEmpty()) {
      if (!isEnabled()) {
         logger.debug("Initialization disabled (not running data scripts)");
         return;
      }
      String username = this.properties.getDataUsername();
      String password = this.properties.getDataPassword();
       //执行脚本
      runScripts(scripts, username, password);
   }
}
```

其中getScripts会获取CreateSchema（schema）和initSchema（data）的fallback参数，分别runScript（`schema-all.sql`和`schema.sql`）或者(data-all.sql和data.sql)

但是我们也可以在配置文件中配置sql文件的位置

```yaml
spring:
  datasource:
    schema:
      - classpath:department.sql
      - 指定位置
```

下面是获取schema脚本文件的方法

```java
List<Resource> scripts = this.getScripts("spring.datasource.schema", this.properties.getSchema(), "schema");
```

![1574409852703](https://cdn.static.note.zzrfdsn.cn/images/springboot/assets/1574409852703.png)

可以看出，如果我们没有在配置文件中配置脚本的具体位置，就会在classpath下找`schema-all.sql`和`schema.sql` platform获取的是all，platform可以在配置文件中修改



**测试：**

在类路径下创建`schema.sql`，运行程序查看数据库是否存在该表

```sql
DROP TABLE IF EXISTS `department`;
CREATE TABLE `department` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `departmentName` varchar(255) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
```

程序启动后发现表并没有被创建，DEBUG查看以下，发现在运行之前会有一个判断

![1574411869052](https://cdn.static.note.zzrfdsn.cn/images/springboot/assets/1574411869052.png)

![1574412098885](https://cdn.static.note.zzrfdsn.cn/images/springboot/assets/1574412098885.png)

上面方法也不知道在干什么，反正就是只要是`NEVER`和`EMBEDDED`就为true，而DataSourceInitializationMode枚举类中除了这两个就剩下`ALWAYS`了，可以在配置文件中配置为ALWAYS

![1574412237660](https://cdn.static.note.zzrfdsn.cn/images/springboot/assets/1574412237660.png)

```yaml
spring:
  datasource:
    username: root
    password: root
    url: jdbc:mysql://172.16.145.137:3306/springboot
    driver-class-name: com.mysql.cj.jdbc.Driver
    initialization-mode: always
```

`schema.sql`：建表语句

`data.sql`：插入数据

当然混合使用也可以，愿意咋来咋来

**注意：**项目每次启动都会执行一次sql

#### 更换Druid数据源

选择哪个数据库源（一般数据源中也有自己的连接池实现）

- DBCP2 是 Appache 基金会下的项目，是最早出现的数据库连接池 DBCP 的第二个版本。
- C3P0 最早出现时是作为 Hibernate 框架的默认数据库连接池而进入市场。
- Druid 是阿里巴巴公司开源的一款数据库连接池，其特点在于有丰富的附加功能。
- HikariCP 相较而言比较新，它最近两年才出现，据称是速度最快的数据库连接池。最近更是被 Spring 设置为默认数据库连接池。

不选择 C3P0 的原因：

- C3P0 的 Connection 是异步释放。这个特性会导致释放的在某些情况下 Connection 实际上 **still in use** ，并未真正释放掉，从而导致连接池中的 Connection 耗完，等待状况。
- Hibernate 现在对所有数据库连接池一视同仁，官方不再指定『默认』数据库连接池。因此 C3P0 就失去了『官方』光环。

不选择 DBCP2 的原因：

- 相较于 Druid 和 HikariCP，DBCP2 没有什么特色功能/卖点。基本上属于 `能用，没毛病` 的情况，地位显得略有尴尬。

1. 在 Spring Boot 项目中加入`druid-spring-boot-starter`依赖 ([点击查询最新版本](https://mvnrepository.com/artifact/com.alibaba/druid-spring-boot-starter))

   ```xml
   <dependency>
       <groupId>com.alibaba</groupId>
       <artifactId>druid-spring-boot-starter</artifactId>
       <version>1.1.20</version>
   </dependency>
   ```

2. 在配置文件中指定数据源类型

   ```yaml
   spring:
     datasource:
       username: root
       password: 123456
       url: jdbc:mysql://192.168.98.128:3306/springboot
       driver-class-name: com.mysql.cj.jdbc.Driver
       initialization-mode: always
       type: com.alibaba.druid.pool.DruidDataSource
   ```

3. 测试类查看使用的数据源

   ```java
   @SpringBootTest
   class SpringbootJdbcApplicationTests {
   
       @Autowired
       private DataSource dataSource;
   
       @Test
       void contextLoads() throws SQLException {
           System.out.println(dataSource.getClass());
           System.out.println(dataSource.getConnection());
       }
   
   }
   ```

在较新的druid-spring-boot-starter-1.1.20.jar!\com\alibaba\druid\spring\boot\autoconfigure\DruidDataSourceWrapper.class中实现了对于@ConfigurationProperties("spring.datasource.druid")的资源映射

使得我们自身的propertis的spring.datasource.druid标签能映射到生成的DruidDataSource中的属性，而statViewServlet和WebStatFilter则由DruidStatProperties映射配置。

因此我们可以直接在原先的properties上修改，不像以前需要新建一个DruidDataSource和statViewServlet等对象再映射或初始化赋值

```yaml
spring:
  datasource:
    username: root
    password: 123456
    url: jdbc:mysql://192.168.98.128:3306/mysql
    driver-class-name: com.mysql.cj.jdbc.Driver
    initialization-mode: always
    type: com.alibaba.druid.pool.DruidDataSource
    druid:
      # 连接池配置
      # 配置初始化大小、最小、最大
      initial-size: 1
      min-idle: 1
      max-active: 20
      # 配置获取连接等待超时的时间
      max-wait: 3000
      validation-query: SELECT 1 FROM DUAL
      test-on-borrow: false
      test-on-return: false
      test-while-idle: true
      pool-prepared-statements: true
      time-between-eviction-runs-millis: 60000
      min-evictable-idle-time-millis: 300000
      filters: stat,wall,slf4j
      # 配置web监控,默认配置也和下面相同(除用户名密码，enabled默认false外)，其他可以不配
      web-stat-filter:
        enabled: true
        url-pattern: /*
        exclusions: "*.js,*.gif,*.jpg,*.png,*.css,*.ico,/druid/*"
      stat-view-servlet:
        enabled: true
        url-pattern: /druid/*
        login-username: admin
        login-password: root
        allow: 127.0.0.1
```

​	

## 整合Mybatis

* ==Mybatis等持久层框架，会从数据源获取连接，该连接已经实现了相应数据库的JDBC的Driver，因此最底层用得到的connection进行JDBC的CRUD能被数据库正确解读==

#### 引入依赖

```xml
<dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>2.1.1</version>
        </dependency>

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid-spring-boot-starter</artifactId>
            <version>1.1.20</version>
        </dependency>
    </dependencies>
```

![1574423628318](https://cdn.static.note.zzrfdsn.cn/images/springboot/assets/1574423628318.png)

#### 项目构建

1. 在resources下创建`department.sql`和`employee.sql`，项目启动时创建表

   ```sql
   DROP TABLE IF EXISTS `department`;
   CREATE TABLE `department` (
     `id` int(11) NOT NULL AUTO_INCREMENT,
     `departmentName` varchar(255) DEFAULT NULL,
     PRIMARY KEY (`id`)
   ) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
   ```

   ```sql
   DROP TABLE IF EXISTS `employee`;
   CREATE TABLE `employee` (
     `id` int(11) NOT NULL AUTO_INCREMENT,
     `lastName` varchar(255) DEFAULT NULL,
     `email` varchar(255) DEFAULT NULL,
     `gender` int(2) DEFAULT NULL,
     `d_id` int(11) DEFAULT NULL,
     PRIMARY KEY (`id`)
   ) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
   ```

2. 实体类

   <details style="-webkit-font-smoothing: antialiased; -webkit-tap-highlight-color: rgba(0, 0, 0, 0); text-size-adjust: none; box-sizing: border-box;"><summary style="-webkit-font-smoothing: antialiased; -webkit-tap-highlight-color: rgba(0, 0, 0, 0); text-size-adjust: none; box-sizing: border-box;">Department</summary></details>

   <details style="-webkit-font-smoothing: antialiased; -webkit-tap-highlight-color: rgba(0, 0, 0, 0); text-size-adjust: none; box-sizing: border-box;"><summary style="-webkit-font-smoothing: antialiased; -webkit-tap-highlight-color: rgba(0, 0, 0, 0); text-size-adjust: none; box-sizing: border-box;">Employee</summary></details>

1. 配置文件

   ```yaml
   spring:
     datasource:
       username: root
       password: root
       url: jdbc:mysql://172.16.145.137:3306/springboot
       driver-class-name: com.mysql.cj.jdbc.Driver
       initialization-mode: always
       type: com.alibaba.druid.pool.DruidDataSource
       druid:
         # 连接池配置
         # 配置初始化大小、最小、最大
         initial-size: 1
         min-idle: 1
         max-active: 20
         # 配置获取连接等待超时的时间
         max-wait: 3000
         validation-query: SELECT 1 FROM DUAL
         test-on-borrow: false
         test-on-return: false
         test-while-idle: true
         pool-prepared-statements: true
         time-between-eviction-runs-millis: 60000
         min-evictable-idle-time-millis: 300000
         filters: stat,wall,slf4j
         # 配置web监控,默认配置也和下面相同(除用户名密码，enabled默认false外)，其他可以不配
         web-stat-filter:
           enabled: true
           url-pattern: /*
           exclusions: "*.js,*.gif,*.jpg,*.png,*.css,*.ico,/druid/*"
         stat-view-servlet:
           enabled: true
           url-pattern: /druid/*
           login-username: admin
           login-password: root
           allow: 127.0.0.1
       schema:
         - classpath:department.sql
         - classpath:employee.sql
   ```

#### Mybatis注解增删查改

1. 创建mapper接口

   ```java
   @Mapper
   public interface DepartmentMapper {
   
      
       @Select("select * from department")
       public List<Department> selectAll();
   
       @Select("select * from department where id = #{id}")
       public Department selectById(Integer id);
   
       @Options(useGeneratedKeys = true, keyProperty = "id")
       @Insert("Insert into department(departmentName) values(#{departmentName})")
       public int save(Department department);
   
       @Update("update department set departmentName=#{departmentName}")
       public int update(Department department);
   
       @Delete("delete from department where id = #{id}")
       public int delete(Integer id);
   }
   ```

2. 创建Controller

   ```java
   @Controller//若是用Controller而下面用Restful风格的Mapping，不用ResponseBody试图解析器解析不了为JSON格式，会出现死循环
   // 因此最好用Restful风格的映射时用@RestfulController
   public class DepartmentController {
   
      @Autowired
       private DepartmentMapper departmentMapper;
   
       @ResponseBody
       @GetMapping("/dept/{id}")
       public Department selectById(@PathVariable("id") Integer id){
           Department department = departmentMapper.selectById(id);
           return  department;
       }
   
       @ResponseBody 
       @GetMapping("/dept")
       public Department insertDep(Department department){
           departmentMapper.save(department);
           return department;
       }
   }
   ```

1. 访问：http://localhost:8080/dep?departmentName=PeppaPig 添加一条数据

   访问：http://localhost:8080/dep/1获取数据

#### Mybaitis 配置

###### 驼峰命名

我们的实体类和表中的列名一致，一点问题也没有

我们把department表的departmentName列名改为department_name看看会发生什么

访问：http://localhost:8080/dep/1获取数据

```
[{"id":1,"departmentName":null}]
```

由于列表和属性名不一致，所以就没有封装进去，我们表中的列名和实体类属性名都是遵循驼峰命名规则的，可以开启mybatis的开启驼峰命名配置

```yaml
mybatis:
  configuration:
    map-underscore-to-camel-case: true
```

然后重启项目，重新插入数据，再查询就发现可以封装进去了

也可以通过向spring容器中注入`org.mybatis.spring.boot.autoconfigure.ConfigurationCustomizer`的方法设置mybatis参数

```JAVA
@Configuration
public class MybatisConfig {

    @Bean
    public ConfigurationCustomizer mybatisConfigurationCustomizer() {
        return new ConfigurationCustomizer() {
            @Override
            public void customize(org.apache.ibatis.session.Configuration configuration) {
                configuration.setMapUnderscoreToCamelCase(true);
            }
        };
    }
}
```

![1574430056512](https://cdn.static.note.zzrfdsn.cn/images/springboot/assets/1574430056512.png)

![1574430280791](https://cdn.static.note.zzrfdsn.cn/images/springboot/assets/1574430280791.png)

###### Mapper扫描

使用`@mapper注解`的类可以被扫描到容器中，但是每个Mapper都要加上这个注解就是一个繁琐的工作，能不能直接扫描某个包下的所有Mapper接口呢，当然可以，在springboot启动类上加上`@MapperScan`

```java
@MapperScan("cn.clboy.springbootmybatis.mapper")
@SpringBootApplication
public class SpringbootMybatisApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringbootMybatisApplication.class, args);
    }

}
```

#### Mybatis 配置文件增删查改

1. 创建mybatis全局配置文件

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">
   <configuration>
       <typeAliases>
           <package name="cn.clboy.springbootmybatis.model"/>
       </typeAliases>
   </configuration>
   ```

2. 创建EmployeeMapper接口

   ```java
   public interface EmployeeMapper {
   
       List<Employee> selectAll();
   
       int save(Employee employee);
   }
   ```

3. 创建EmployeeMapper.xml映射文件

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
   <mapper namespace="cn.clboy.springbootmybatis.mapper.EmployeeMapper">
       <select id="selectAll" resultType="employee">
           SELECT * FROM employee
       </select>
       <insert id="save" parameterType="employee" useGeneratedKeys="true" keyProperty="id">
          INSERT INTO employee(lastName,email,gender,d_id) VALUES (#{lastName},#{email},#{gender},#{d_id})
       </insert>
   </mapper>
   ```

4. 配置文件(application.yaml)中指定配置文件和映射文件的位置

   ```yaml
   mybatis:
     config-location: classpath:mybatis/mybatis-config.xml
     mapper-locations: classpath:mybatis/mapper/*.xml
   ```

5. 给表中插入两个数据，用于测试

   ```sql
    INSERT INTO employee(lastName,email,gender,d_id) VALUES ('张三','123456@qq.com',1,1);
    INSERT INTO employee(lastName,email,gender,d_id) VALUES ('lisi','245612@qq.com',1,1);
   ```

6. 创建EmployeeController

   ```java
   @RestController
   public class EmployeeController {
   
       @Autowired
       private EmployeeMapper employeeMapper;
   
       @RequestMapping("/emp/list")
       public List<Employee> getALl() {
           return employeeMapper.selectAll();
       }
   
       @RequestMapping("/emp/{id}")
       public Employee save(Employee employee) {
           employeeMapper.save(employee);
           return employee;
       }
   }
   ```

   访问：http://localhost:8080/emp/list

## Springboot整合JPA

* SpringDataXXX的作用便和MybatisPlus自己定义的Mapper继承与一个实现了基本CRUD的抽象类一样，用实现了相应接口的抽象类的方法便可以进行CRUD而不用自己编写SQL代码

* SpringData则是SpringDataxxx的一个同一规范，依据他对关系型数据库的Respository和对Nosql的Template则是对SpingDataXXX

  的规范要求，Application只需调用SpringData提供的接口，其在SpringDataXXX会被重写实现。
  
  * Respository是针对数据库的CRUD的一些基本操作的实现，底层帮我们实现了提供的方法的SQL语句
  * Template是对需要类似Client操作的模板he
  
  ![image-20200825103950283](F:\Typora数据储存\Spring\SpringBoot基础篇.assets\image-20200825103950283.png)
  
  ```xml
  <dependencies>
          <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter-data-jpa</artifactId>
          </dependency>
          <dependency>
              <groupId>mysql</groupId>
              <artifactId>mysql-connector-java</artifactId>
              <scope>runtime</scope>
          </dependency>
          <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter-thymeleaf</artifactId>
          </dependency>
          <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter-web</artifactId>
          </dependency>
      </dependencies>Copy to clipboardErrorCopied
  ```
  
  ![1574481366615](https://cdn.static.note.zzrfdsn.cn/images/springboot/assets/1574481366615.png)
  
  #### 实体类 
  
  ```java
  package cn.clboy.springbootjpa.entity;
  
  
  import javax.persistence.*;
  
  
  @Entity //使用JPA注解配置映射关系,告诉JPA这是一个实体类（和数据表映射的类）
  @Table(name = "tbl_user")   //@Table来指定和哪个数据表对应;如果省略默认表名类名小写；
  public class User {
  
      @Id //这是一个主键
      @GeneratedValue(strategy = GenerationType.IDENTITY) //自增主键
      private Integer id;
  
      @Column(name = "last_name",length = 50) //这是和数据表对应的一个列
      private String lastName;
      @Column //省略默认列名就是属性名
      private String email;
  
      public Integer getId() {
          return id;
      }
  
      public void setId(Integer id) {
          this.id = id;
      }
  
      public String getLastName() {
          return lastName;
      }
  
      public void setLastName(String lastName) {
          this.lastName = lastName;
      }
  
      public String getEmail() {
          return email;
      }
  
      public void setEmail(String email) {
          this.email = email;
      }
  }
  Copy to clipboardErrorCopied
  ```
  
  #### DAO
  
  ```java
  package cn.clboy.springbootjpa.repository;
  
  import cn.clboy.springbootjpa.entity.User;
  import org.springframework.data.jpa.repository.JpaRepository;
  
  /**
   * 继承JpaRepository来完成对数据库的操作
   * 泛型是（实体类，主键）
   */
  public interface UserRepository extends JpaRepository<User, Integer> {
  
  }Copy to clipboardErrorCopied
  ```
  
  #### 配置文件
  
  ```yaml
  spring:
    datasource:
      username: root
      password: root
      url: jdbc:mysql://172.16.145.137:3306/springboot
      driver-class-name: com.mysql.cj.jdbc.Driver
    jpa:
      hibernate:
        ddl-auto: update
      show-sql: trueCopy to clipboardErrorCopied
  ```
  
  #### Controller
  
  ```java
  @RestController
  public class UserController{
      
      @Autowired
      UserRespository userRespository
      
      @GetMapping("/user/{id}")
      public User get(@PathVariable("id") Integer id){
         User user = userRespository.findOne(id); 
      }
  }
  ```
  
  添加User，访问：
  
  http://localhost:8080/user?lastName=zhangsan&email=123456@qq.com
  
  http://localhost:8080/user?lastName=lisi&email=78215646@qq.com
  
  查询用户访问：
  
  http://localhost:8080/user/1，然后你就会发现抛出500错误，原因是getOne方法使用的懒加载，获取到的只是代理对象，转换为json时会报错
  
  **解决方法有两种：**
  
  1. 关闭懒加载，在实体类上加`@Proxy(lazy = false)`注解
  
     ```java
     @Entity
     @Table(name = "tbl_user")
     @Proxy(lazy = false)
     public class UserCopy to clipboardErrorCopied
     ```
  
  2. 转json的时候忽略`hibernateLazyInitializer`和`handler`属性
  
     ```java
     @Entity
     @Table(name = "tbl_user")
     @JsonIgnoreProperties(value = {"hibernateLazyInitializer", "handler"})
     public class User 
     ```
  
  ](F:\Typora数据储存\Spring\SpringBoot基础篇.assets\image-20200825103639357.png)



## Springboot启动原理

```java
    public static void main(String[] args) {
        //xxx.class：主配置类，（可以传多个）
        SpringApplication.run(xxx.class, args);
    }
```

#### 1. 从run方法开始，创建SpringApplication，然后再调用run方法

```java
/**
 * ConfigurableApplicationContext(可配置的应用程序上下文)
 */
public static ConfigurableApplicationContext run(Class<?> primarySource, String... args) {
    //调用下面的run方法
    return run(new Class[]{primarySource}, args);
}

public static ConfigurableApplicationContext run(Class<?>[] primarySources, String[] args) {
    //primarySources：主配置类
    return (new SpringApplication(primarySources)).run(args);
}
```

#### 2. 创建SpringApplication

```java
public SpringApplication(Class<?>... primarySources) {
    //调用下面构造方法
    this((ResourceLoader) null, primarySources);
}
```

```java
public SpringApplication(ResourceLoader resourceLoader, Class<?>... primarySources) {
   //获取类加载器
   this.resourceLoader = resourceLoader;
   //查看类加载器是否为空
   Assert.notNull(primarySources, "PrimarySources must not be null");
   // 保存主配置类的信息到一个哈希链表集合中，方便查询调用增删
   this.primarySources = new LinkedHashSet<>(Arrays.asList(primarySources));
   //获取当前应用的类型，是不是web应用，见2.1
   this.webApplicationType = WebApplicationType.deduceFromClasspath();
   //从类路径下找到META‐INF/spring.factories配置的所有ApplicationContextInitializer；然后保存起来，见2.2
   setInitializers((Collection) getSpringFactoriesInstances(ApplicationContextInitializer.class));
   //从类路径下找到META‐INF/spring.factories配置的所有spring.ApplicationListener；然后保存起来,原理同上
   setListeners((Collection) getSpringFactoriesInstances(ApplicationListener.class));
   //从多个SpringApplication中找到有main方法的主SpringApplication(在调run方法的时候是可以传递多个配置类的)
   //只记录有main方法的类名，以便下一步的run
   this.mainApplicationClass = deduceMainApplicationClass();
}
```

#### 2.1 deduceFromClasspath

```java
static WebApplicationType deduceFromClasspath() {
   if (ClassUtils.isPresent(WEBFLUX_INDICATOR_CLASS, null) && !ClassUtils.isPresent(WEBMVC_INDICATOR_CLASS, null)
         && !ClassUtils.isPresent(JERSEY_INDICATOR_CLASS, null)) {
      return WebApplicationType.REACTIVE;
   }
   for (String className : SERVLET_INDICATOR_CLASSES) {
      if (!ClassUtils.isPresent(className, null)) {
         return WebApplicationType.NONE;
      }
   }
   return WebApplicationType.SERVLET;
}
```

#### 2.2 getSpringFactoriesInstances

```java
private <T> Collection<T> getSpringFactoriesInstances(Class<T> type, Class<?>[] parameterTypes, Object... args) {
   ClassLoader classLoader = getClassLoader();
   // Use names and ensure unique to protect against duplicates
   // 配置的所有ApplicationContextInitializer等分别到一个集合中以便查询使用，其中loadFactoryNames方法从类路径下找到META‐INF/spring.factories中传入的parameterTypes
   Set<String> names = new LinkedHashSet<>(SpringFactoriesLoader.loadFactoryNames(type, classLoader));
   // 实例化传入的类
   List<T> instances = createSpringFactoriesInstances(type, parameterTypes, classLoader, args, names);
   // 排序以便提高针对他执行操作的效率
   AnnotationAwareOrderComparator.sort(instances);
   return instances;
}
```

#### 2.3 deduceMainApplicationClass 

```java
private Class<?> deduceMainApplicationClass() {
   try {
       // 查询当前的虚拟机的当前线程的StackTrace信息
      StackTraceElement[] stackTrace = new RuntimeException().getStackTrace();
      for (StackTraceElement stackTraceElement : stackTrace) {
          // 查看当前线程中已加载的类中有没有main方法
         if ("main".equals(stackTraceElement.getMethodName())) {
           //有则返回该类的类名
            return Class.forName(stackTraceElement.getClassName());
         }
      }
   }
   catch (ClassNotFoundException ex) {
      // Swallow and continue
   }
   return null;
}
```

>  StackTrace简述
>
>   1 StackTrace用栈的形式保存了方法的调用信息.
>
>   2 怎么获取这些调用信息呢?
>
> 可用Thread.currentThread().getStackTrace()方法得到当前线程的StackTrace信息.该方法返回的是一个StackTraceElement数组.
>
> 3 该StackTraceElement数组就是StackTrace中的内容.
>
> 4 遍历该StackTraceElement数组.就可以看到方法间的调用流程.
>
> 比如线程中methodA调用了methodB那么methodA先入栈methodB再入栈.
>
>  5 在StackTraceElement数组下标为2的元素中保存了当前方法的所属文件名,当前方法所属的类名,以及该方法的名字
>
> ​	除此以外还可以获取方法调用的行数.
>
> 6 在StackTraceElement数组下标为3的元素中保存了当前方法的调用者的信息和它调用时的代码行数.

#### 3. 调用SpringApplication对象的run方法

> #### Apllication总体流程
>
> SpringApplication的run方法的实现是我们本次旅程的主要线路，该方法的主要流程大体可以归纳如下：
>
> 1） 如果我们使用的是SpringApplication的静态run方法，那么，这个方法里面首先要创建一个SpringApplication对象实例，然后调用这个创建好的SpringApplication的实例方法。在SpringApplication实例初始化的时候，它会提前做几件事情：
>
> - 根据classpath里面是否存在某个特征类（org.springframework.web.context.ConfigurableWebApplicationContext）来决定是否应该创建一个为Web应用使用的ApplicationContext类型。
> - 使用SpringFactoriesLoader在应用的classpath中查找并加载所有可用的ApplicationContextInitializer。
> - 使用SpringFactoriesLoader在应用的classpath中查找并加载所有可用的ApplicationListener。
> - 推断并设置main方法的定义类。
>
> 2） SpringApplication实例初始化完成并且完成设置后，就开始执行run方法的逻辑了，方法执行伊始，首先遍历执行所有通过SpringFactoriesLoader可以查找到并加载的SpringApplicationRunListener。调用它们的started()方法，告诉这些SpringApplicationRunListener，“嘿，SpringBoot应用要开始执行咯！”。
>
> 3） 创建并配置当前Spring Boot应用将要使用的Environment（包括配置要使用的PropertySource以及Profile）。
>
> 4） 遍历调用所有SpringApplicationRunListener的environmentPrepared()的方法，告诉他们：“当前SpringBoot应用使用的Environment准备好了咯！”。
>
> 5） 如果SpringApplication的showBanner属性被设置为true，则打印banner。
>
> 6） 根据用户是否明确设置了applicationContextClass类型以及初始化阶段的推断结果，决定该为当前SpringBoot应用创建什么类型的ApplicationContext并创建完成，然后根据条件决定是否添加ShutdownHook，决定是否使用自定义的BeanNameGenerator，决定是否使用自定义的ResourceLoader，当然，最重要的，将之前准备好的Environment设置给创建好的ApplicationContext使用。
>
> 7） ApplicationContext创建好之后，SpringApplication会再次借助Spring-FactoriesLoader，查找并加载classpath中所有可用的ApplicationContext-Initializer，然后遍历调用这些ApplicationContextInitializer的initialize（applicationContext）方法来对已经创建好的ApplicationContext进行进一步的处理。
>
> 8） 会调用SpringApplication的prepareContext方法，其中会遍历调用所有SpringApplicationRunListener的contextPrepared()方法。
>
> 9）将之前通过@EnableAutoConfiguration获取的所有配置以及其他形式的IoC容器配置加载到已经准备完毕的ApplicationContext。
>
> 10）遍历调用所有SpringApplicationRunListener的contextLoaded()方法。
>
> 11）接着便是SpringApplication的afterRefresh方法，调用ApplicationContext的refresh()方法，完成IoC容器可用的最后一道工序。
>
> 12） 查找当前ApplicationContext中是否注册有CommandLineRunner，如果有，则遍历执行它们。
>
> 13） 正常情况下，遍历执行SpringApplicationRunListener的finished()方法、（如果整个过程出现异常，则依然调用所有SpringApplicationRunListener的finished()方法，只不过这种情况下会将异常信息一并传入处理）

```java
    public ConfigurableApplicationContext run(String... args) {
        //Simple stop watch, allowing for timing of a number of tasks, exposing totalrunning time and running time for each named task.简单来说是创建ioc容器的计时器
        StopWatch stopWatch = new StopWatch();
        stopWatch.start();
        //声明IOC容器
        ConfigurableApplicationContext context = null;
        //异常报告存储列表
        Collection<SpringBootExceptionReporter> exceptionReporters = new ArrayList();
        //加载图形化配置
        this.configureHeadlessProperty();
        //从类路径下META‐INF/spring.factories获取SpringApplicationRunListeners，原理同2中获取ApplicationContextInitializer和ApplicationListener
        SpringApplicationRunListeners listeners = this.getRunListeners(args);
        //遍历上一步获取的所有SpringApplicationRunListener，调用其starting方法
        listeners.starting();

        Collection exceptionReporters;
        try {
            //封装命令行
            ApplicationArguments applicationArguments = new DefaultApplicationArguments(args);
            //准备环境，把上面获取到的SpringApplicationRunListeners传过去，见3.1
            ConfigurableEnvironment environment = this.prepareEnvironment(listeners, applicationArguments);
            
            this.configureIgnoreBeanInfo(environment);
            //打印Banner，就是控制台那个Spring字符画
            Banner printedBanner = this.printBanner(environment);
            //根据当前环境利用反射创建IOC容器，见3.2
            context = this.createApplicationContext();
        //从类路径下META‐INF/spring.factories获取SpringBootExceptionReporter，原理同2中获取ApplicationContextInitializer和ApplicationListener
            exceptionReporters = this.getSpringFactoriesInstances(SpringBootExceptionReporter.class, new Class[]{ConfigurableApplicationContext.class}, context);
            //准备IOC容器，见3.3
            this.prepareContext(context, environment, listeners, applicationArguments, printedBanner);
            //刷新IOC容器，可查看配置嵌入式Servlet容器原理，所有的@Configuration和@AutoConfigutation等Bean对象全在此时加入容器中，并依据不同的选项创建了不同功能如服务器/数据库等组件。见3.4
            //可以说@SpringbootApplication中自动扫描包和Autoconfiguration也是在此时进行的
            this.refreshContext(context);
            //这是一个空方法
            this.afterRefresh(context, applicationArguments);
            //停止计时，打印时间
            stopWatch.stop();
            if (this.logStartupInfo) {
                (new StartupInfoLogger(this.mainApplicationClass)).logStarted(this.getApplicationLog(), stopWatch);
            }
            //调用所有SpringApplicationRunListener的started方法
            listeners.started(context);
            //见3.5 ，从ioc容器中获取所有的ApplicationRunner和CommandLineRunner进行回调ApplicationRunner先回调，CommandLineRunner再回调。
            this.callRunners(context, applicationArguments);
        } catch (Throwable var10) {
            this.handleRunFailure(context, var10, exceptionReporters, listeners);
            throw new IllegalStateException(var10);
        }

        try {
            //调用所有SpringApplicationRunListener的running方法
            listeners.running(context);
            //返回创建好的ioc容器，启动完成
            return context;
        } catch (Throwable var9) {
            this.handleRunFailure(context, var9, exceptionReporters, (SpringApplicationRunListeners)null);
            throw new IllegalStateException(var9);
        }
    }
```

#### 3.1 **prepareEnvironment**

```java
    private ConfigurableEnvironment prepareEnvironment(SpringApplicationRunListeners listeners, ApplicationArguments applicationArguments) {
        //获取或者创建环境，有则获取，无则创建
        ConfigurableEnvironment environment = this.getOrCreateEnvironment();
        //配置环境
        this.configureEnvironment((ConfigurableEnvironment)environment, applicationArguments.getSourceArgs());
        ConfigurationPropertySources.attach((Environment)environment);
        //创建环境完成后，ApplicationContext创建前，调用前面获取的所有SpringApplicationRunListener的environmentPrepared方法，读取配置文件使之生效
        listeners.environmentPrepared((ConfigurableEnvironment)environment);
        // 环境搭建好后，需依据他改变Apllication的参数，将enviroment的信息放置到Binder中，再由Binder依据不同的条件将“spring.main”SpringApplication更改为不同的环境，为后面context的创建搭建环境
        //为什么boot要这样做，其实咱们启动一个boot的时候并不是一定只有一个Application且用main方式启动。这样子我们可以读取souces配置多的Application
        this.bindToSpringApplication((ConfigurableEnvironment)environment);
        if (!this.isCustomEnvironment) {
            environment = (new EnvironmentConverter(this.getClassLoader())).convertEnvironmentIfNecessary((ConfigurableEnvironment)environment, this.deduceEnvironmentClass());
        }

        ConfigurationPropertySources.attach((Environment)environment);        
        //回到3，将创建好的environment返回
        return (ConfigurableEnvironment)environment;
    }
```

#### 3.2 **createApplicationContext**

```java
    protected ConfigurableApplicationContext createApplicationContext() {        
        //获取当前Application中ioc容器类
        Class<?> contextClass = this.applicationContextClass;
        //若没有则依据该应用是否为web应用而创建相应的ioc容器
        //除非为其传入applicationContext，不然Application的默认构造方法并不会创建ioc容器
        if (contextClass == null) {
            try {
                switch(this.webApplicationType) {
                case SERVLET:
                    contextClass = Class.forName("org.springframework.boot.web.servlet.context.AnnotationConfigServletWebServerApplicationContext");
                    break;
                case REACTIVE:
                    contextClass = Class.forName("org.springframework.boot.web.reactive.context.AnnotationConfigReactiveWebServerApplicationContext");
                    break;
                default:
                    contextClass = Class.forName("org.springframework.context.annotation.AnnotationConfigApplicationContext");
                }
            } catch (ClassNotFoundException var3) {
                throw new IllegalStateException("Unable create a default ApplicationContext, please specify an ApplicationContextClass", var3);
            }
        }
		//用bean工具类实例化ioc容器对象并返回。回到3
        return (ConfigurableApplicationContext)BeanUtils.instantiateClass(contextClass);
    }
```

#### 3.3 **prepareContext**

```java
    private void prepareContext(ConfigurableApplicationContext context, ConfigurableEnvironment environment, SpringApplicationRunListeners listeners, ApplicationArguments applicationArguments, Banner printedBanner) {
        //将创建好的环境放到IOC容器中
        context.setEnvironment(environment);
        //处理一些组件，没有实现postProcessA接口
        this.postProcessApplicationContext(context);
        //获取所有的ApplicationContextInitializer调用其initialize方法，这些ApplicationContextInitializer就是在2步骤中获取的，见3.3.1
        this.applyInitializers(context);
        //回调所有的SpringApplicationRunListener的contextPrepared方法，这些SpringApplicationRunListeners是在步骤3中获取的
        listeners.contextPrepared(context);

        //打印日志
        if (this.logStartupInfo) {
            this.logStartupInfo(context.getParent() == null);
            this.logStartupProfileInfo(context);
        }

        ConfigurableListableBeanFactory beanFactory = context.getBeanFactory();
        //将一些applicationArguments注册成容器工厂中的单例对象
        beanFactory.registerSingleton("springApplicationArguments", applicationArguments);
        if (printedBanner != null) {
            beanFactory.registerSingleton("springBootBanner", printedBanner);
        }

        if (beanFactory instanceof DefaultListableBeanFactory) {
            ((DefaultListableBeanFactory)beanFactory).setAllowBeanDefinitionOverriding(this.allowBeanDefinitionOverriding);
        }
		
        //若是延迟加载，则在ioc容器中加入LazyInitializationBeanFactoryPostProcessor，
        if (this.lazyInitialization) {
            context.addBeanFactoryPostProcessor(new LazyInitializationBeanFactoryPostProcessor());
        }

        //获取所有报告primarySources在内的所有source
        Set<Object> sources = this.getAllSources();
        Assert.notEmpty(sources, "Sources must not be empty");
        //加载容器
        this.load(context, sources.toArray(new Object[0]));
        //回调所有的SpringApplicationRunListener的contextLoaded方法
        listeners.contextLoaded(context);
    }
```

#### 3.3.1 **applyInitializers**

```java
    protected void applyInitializers(ConfigurableApplicationContext context) {
        Iterator var2 = this.getInitializers().iterator();

        while(var2.hasNext()) {
            //将之前的所有的ApplicationContextInitializer遍历
            ApplicationContextInitializer initializer = (ApplicationContextInitializer)var2.next();
            //解析查看之前从spring.factories调用的ApplicationContextInitializer是否能被ioc容器调用
            Class<?> requiredType = GenericTypeResolver.resolveTypeArgument(initializer.getClass(), ApplicationContextInitializer.class);
            Assert.isInstanceOf(requiredType, context, "Unable to call initializer.");
            //可以调用则对ioc容器进行初始化（还没加载我们自己配置的bean和AutoConfiguration那些）
            initializer.initialize(context);
        }

    }//返回3.3
```

#### 3.4 **refreshment(context)**

```java
@Override 
//详情见内置Servlet的启动原理,最后是用ApplicationContext的实现类的refresh()方法，若是web Application则为ServletWebServerApplicationContext
public void refresh() throws BeansException, IllegalStateException {
   synchronized (this.startupShutdownMonitor) {
      // Prepare this context for refreshing.
      prepareRefresh();

      // Tell the subclass to refresh the internal bean factory.
      ConfigurableListableBeanFactory beanFactory = obtainFreshBeanFactory();

      // Prepare the bean factory for use in this context.
      prepareBeanFactory(beanFactory);

      try {
         // Allows post-processing of the bean factory in context subclasses.
         postProcessBeanFactory(beanFactory);

         // Invoke factory processors registered as beans in the context.
         invokeBeanFactoryPostProcessors(beanFactory);

         // Register bean processors that intercept bean creation.
         registerBeanPostProcessors(beanFactory);

         // Initialize message source for this context.
         initMessageSource();

         // Initialize event multicaster for this context.
         initApplicationEventMulticaster();

         // Initialize other special beans in specific context subclasses.
         onRefresh();

         // Check for listener beans and register them.
         registerListeners();

         // Instantiate all remaining (non-lazy-init) singletons.
         finishBeanFactoryInitialization(beanFactory);

         // Last step: publish corresponding event.
         finishRefresh();
      }

      catch (BeansException ex) {
         if (logger.isWarnEnabled()) {
            logger.warn("Exception encountered during context initialization - " +
                  "cancelling refresh attempt: " + ex);
         }

         // Destroy already created singletons to avoid dangling resources.
         destroyBeans();

         // Reset 'active' flag.
         cancelRefresh(ex);

         // Propagate exception to caller.
         throw ex;
      }

      finally {
         // Reset common introspection caches in Spring's core, since we
         // might not ever need metadata for singleton beans anymore...
         resetCommonCaches();
      }
   }
}
```

#### 3.5 **callRunners**

```java
    private void callRunners(ApplicationContext context, ApplicationArguments args) {
        List<Object> runners = new ArrayList();
        //将ioc容器中的的ApplicationRunner和CommandLineRunner（），在创建ioc容器时创建的
        runners.addAll(context.getBeansOfType(ApplicationRunner.class).values());
        runners.addAll(context.getBeansOfType(CommandLineRunner.class).values());
        AnnotationAwareOrderComparator.sort(runners);
        Iterator var4 = (new LinkedHashSet(runners)).iterator();
		
        //调用ApplicationRunner和CommandLineRunner的run方法
        while(var4.hasNext()) {
            Object runner = var4.next();
            if (runner instanceof ApplicationRunner) {
                this.callRunner((ApplicationRunner)runner, args);
            }

            if (runner instanceof CommandLineRunner) {
                this.callRunner((CommandLineRunner)runner, args);
            }
        }

    }
```

**配置在META-INF/spring.factories**

- ApplicationContextInitializer（在我们将enviroment绑定到application后可以用来创建不同类型的context）
- SpringApplicationRunListener（在Application 创建/运行/销毁等进行一些我们想要的特殊配置）

**只需要放在ioc容器中**

- ApplicationRunner（加载没有在ApplicationContext中的bean时让bean能加载）
- CommandLineRunner（命令行执行器）

## Application初始化组件测试

1. 创建`ApplicationContextInitializer`和`SpringApplicationRunListener`的实现类，==并在META-INF/spring.factories文件中配置==

   ```java
   public class TestApplicationContextInitializer implements ApplicationContextInitializer {
   
       @Override
       public void initialize(ConfigurableApplicationContext configurableApplicationContext) {
           System.out.println("TestApplicationContextInitializer.initialize");
       }
   }
   ```

   ```java
   public class TestSpringApplicationRunListener implements SpringApplicationRunListener {
       @Override
       public void starting() {
           System.out.println("TestSpringApplicationRunListener.starting");
       }
   
       @Override
       public void environmentPrepared(ConfigurableEnvironment environment) {
           System.out.println("TestSpringApplicationRunListener.environmentPrepared");
       }
   
       @Override
       public void contextPrepared(ConfigurableApplicationContext context) {
           System.out.println("TestSpringApplicationRunListener.contextPrepared");
       }
   
       @Override
       public void contextLoaded(ConfigurableApplicationContext context) {
           System.out.println("TestSpringApplicationRunListener.contextLoaded");
       }
   
       @Override
       public void started(ConfigurableApplicationContext context) {
           System.out.println("TestSpringApplicationRunListener.started");
       }
   
       @Override
       public void running(ConfigurableApplicationContext context) {
           System.out.println("TestSpringApplicationRunListener.running");
       }
   
       @Override
       public void failed(ConfigurableApplicationContext context, Throwable exception) {
           System.out.println("TestSpringApplicationRunListener.failed");
       }
   }
   ```

   ```properties
   org.springframework.context.ApplicationContextInitializer=\
   cn.clboy.springbootprocess.init.TestApplicationContextInitializer
   
   org.springframework.boot.SpringApplicationRunListener=\
   cn.clboy.springbootprocess.init.TestSpringApplicationRunListener
   ```

   启动报错：说是没有找到带org.springframework.boot.SpringApplication和String数组类型参数的构造器，给TestSpringApplicationRunListener添加这样的构造器

   ```java
    public TestSpringApplicationRunListener(SpringApplication application,String[] args) {
       }
   ```
   
2. 创建`ApplicationRunner`实现类和`CommandLineRunner`实现类，因为是从ioc容器完成创建后中提取存放在里面的这两个Runner，因此可以直接添加到容器中，最后callRunner使用

   ```java
   @Component
   public class TestApplicationRunner implements ApplicationRunner {
   
       @Override
       public void run(ApplicationArguments args) throws Exception {
           System.out.println("TestApplicationRunner.run\t--->"+args);
       }
   }
   ```

   ```java
   @Component
   public class TestCommandLineRunn implements CommandLineRunner {
   
       @Override
       public void run(String... args) throws Exception {
           System.out.println("TestCommandLineRunn.runt\t--->"+ Arrays.toString(args));
       }
   }
   ```


**修改Banner**

默认是找类路径下的`banner.txt`，可以在配置文件中修改

```properties
spring.banner.location=xxx.txt
```

生成banner的网站：http://patorjk.com/software/taag

## Springboot Starter

Spring Boot starter 依赖项的特殊之处在于，它们本身通常没有任何库代码，而是间接地引入其他库。这些 starter 依赖提供了三个主要的好处：

- 构建的文件将会小得多，也更容易管理，因为不需要对每一个可能需要的库都声明一个依赖项。
- 可以根据它们提供的功能来考虑需要的依赖关系，而不是根据库名来考虑。如果正在开发一个 web 应用程序，那么将添加 web starter 依赖项，而不是一个编写 web 应用程序的各个库的清单。
- 不用担心 library 版本问题。可以相信的是，对于给定版本的 Spring Boot，可间接地引入的库的版本将是兼容的，只需要考虑使用的是哪个版本的 Spring Boot。

### 自定义Starter

- 启动器只用来做依赖导入，启动器模块是一个空 JAR 文件，仅提供辅助性依赖管理，这些依赖可能用于自动装配或者其他类库
- 专门来写一个自动配置模块；（xxx-spring-boot-starter-autoconfigure）
- 启动器依赖自动配置模块，项目中引入相应的starter就会引入启动器的所有传递依赖,xxx-spring-boot-starter的依赖会导入真正需要的类（如：自己的AutoConfiguration，spring-boot-starter，而spring-boot-starter回会导入spring-boot-autoconfigure等我们所需要的基本包）

> **命名规约**
>
> - 官方命名
>
>   `spring-boot-starter-模块名`
>
>   *eg*：`spring-boot-starter-web`、`spring-boot-starter-jdbc`、`spring-boot-starter-thymeleaf`
>
> - 自定义命名
>
>   `模块名-spring-boot-starter`
>
>   *eg*：`mybatis-spring-boot-start`

#### 如何编写自动配置

```java
@Configuration //指定这个类是一个配置类
@ConditionalOnXXX //在指定条件成立的情况下自动配置类生效
@AutoConfigureAfter //指定自动配置类的顺序
@Bean //给容器中添加组件
@ConfigurationPropertie//结合相关xxxProperties类来绑定相关的配置
@EnableConfigurationProperties //让xxxProperties生效加入到容器中
public class XxxxAutoConfiguration {
```

自动配置类要能加载,需要将启动就加载的自动配置类配置在`META-INF/spring.factories`中

*eg：*

```properties
# Auto Configure
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
org.mybatis.spring.boot.autoconfigure.MybatisLanguageDriverAutoConfiguration,\
org.mybatis.spring.boot.autoconfigure.MybatisAutoConfiguration
```

#### 案例

1. 创建一个自动配置模块，和创建普通springboot项目一样，不需要引入其他starter

2. 删除掉多余的文件和依赖

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
       <modelVersion>4.0.0</modelVersion>
       <parent>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-parent</artifactId>
           <version>2.2.1.RELEASE</version>
           <relativePath/>
       </parent>
       <groupId>cn.clboy.spring.boot</groupId>
       <artifactId>clboy-spring-boot-autoconfigure</artifactId>
       <version>0.0.1-SNAPSHOT</version>
       <name>clboy-spring-boot-autoconfigure</name>
   
       <properties>
           <java.version>1.8</java.version>
       </properties>
   
       <dependencies>
           <!--引入spring‐boot‐starter；所有starter的基本配置-->
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter</artifactId>
           </dependency>
           <!--可以生成配置类提示文件-->
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-configuration-processor</artifactId>
               <optional>true</optional>
           </dependency>
       </dependencies>
   
   </project>
   ```

3. 创建配置类和自动配置类

   ```java
   package cn.clboy.spring.boot.autoconfigure;
   
   import org.springframework.boot.context.properties.ConfigurationProperties;
   
   /*Binding is either performed by calling setters on the annotated class or, if
    * {@link ConstructorBinding @ConstructorBinding} is in use, by binding to the constructor
    * parameters.
    用set或构造器方法注入
   */
   @ConfigurationProperties(prefix = "clboy")
   public class ClboyProperties {
   
       private String prefix;
       private String suffix;
   
       public ClboyProperties() {
           this.prefix = "";
           this.suffix = "";
       }
   
       public String getPrefix() {
           return prefix;
       }
   
       public void setPrefix(String prefix) {
           this.prefix = prefix;
       }
   
       public String getSuffix() {
           return suffix;
       }
   
       public void setSuffix(String suffix) {
           this.suffix = suffix;
       }
   }
   ```

   ```java
   package cn.clboy.spring.boot.autoconfigure;
   
   import org.springframework.boot.autoconfigure.condition.ConditionalOnWebApplication;
   import org.springframework.boot.context.properties.EnableConfigurationProperties;
   import org.springframework.context.annotation.Bean;
   import org.springframework.context.annotation.Configuration;
   
   /**
    * @Author cloudlandboy
    * @Date 2019/11/24 上午10:40
    * @Since 1.0.0
    */
   
   @Configuration
   @ConditionalOnWebApplication //web应用才生效
   @EnableConfigurationProperties(ClboyProperties.class) //让配置类生效，(注入到容器中)
   public class ClboyAutoConfiguration {
   
       private final ClboyProperties clboyProperties;
   
       /**
        * 构造器注入clboyProperties
        *
        * @param clboyProperties
        */
       public ClboyAutoConfiguration(ClboyProperties clboyProperties) {
           this.clboyProperties = clboyProperties;
       }
   
       @Bean
       public HelloService helloService() {
           return new HelloService();
       }
   
       public class HelloService {
   
           public String sayHello(String name) {
               return clboyProperties.getPrefix() + name + clboyProperties.getSuffix();
           }
       }
   }
   ```

1. 在resources文件夹下创建META-INF/spring.factories

   ```properties
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
   cn.clboy.spring.boot.autoconfigure.ClboyAutoConfiguration
   ```
   
2. 创建starter，选择maven工程即可，只是用于管理依赖，添加对AutoConfiguration模块的依赖

   ```xml
<?xml version="1.0" encoding="UTF-8"?>
   <project xmlns="http://maven.apache.org/POM/4.0.0"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
       <modelVersion>4.0.0</modelVersion>
   
       <groupId>cn.clboy.spring.boot</groupId>
       <artifactId>clboy-spring-boot-starter</artifactId>
       <version>1.0-SNAPSHOT</version>
   
       <dependencies>
           <dependency>
               <groupId>cn.clboy.spring.boot</groupId>
               <artifactId>clboy-spring-boot-autoconfigure</artifactId>
               <version>0.0.1-SNAPSHOT</version>
           </dependency>
       </dependencies>
   
   </project>
   ```
   
3. 将starter和autoconfigure安装到本地仓库（使用maven的install 会自动安装到当前maven使用的respository）

4. 创建项目测试，选择添加web场景，因为设置是web场景才生效

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
       <modelVersion>4.0.0</modelVersion>
       <parent>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-parent</artifactId>
           <version>2.2.1.RELEASE</version>
           <relativePath/> <!-- lookup parent from repository -->
       </parent>
       <groupId>cn.clboy</groupId>
       <artifactId>starter-test</artifactId>
       <version>0.0.1-SNAPSHOT</version>
       <name>starter-test</name>
       <description>Demo project for Spring Boot</description>
   
       <properties>
           <java.version>1.8</java.version>
       </properties>
   
       <dependencies>
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-web</artifactId>
           </dependency>
           <dependency>
               <groupId>cn.clboy.spring.boot</groupId>
               <artifactId>clboy-spring-boot-starter</artifactId>
               <version>1.0-SNAPSHOT</version>
           </dependency>
       </dependencies>
   
   </project>
   ```

1. 创建Controller

   ```java
   @RestController
   public class HelloController {
   
       @Autowired
       private HelloService helloService;
   
       @RequestMapping("/hello")
       public String sayHello() {
           String hello = helloService.sayHello("Peppa Pig");
           return hello;
       }
   }
   ```

1. 在配置文件中配置

   ```properties
   clboy.prefix=hello！
   clboy.suffix=，你好啊...
   ```

2. 启动项目访问：http://localhost:8080/hello

注意查看文件夹的命名是否正确，最好是从别的包中复制过去，正确的情况下spring.factories是有小绿叶图标的

