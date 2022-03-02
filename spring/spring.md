# Spring

## Bean的配置方式

### 基于XML配置

**Bean的定义**： 在XML文件中通过元素定义。

**Bean的名称**： 通过的id或name属性定义。

**Bean的注入**： 通过子元素或通过p命名空间的动态属性。

**Bean生命过程方法**：通过的init-method和destory-method属性指定Bean实现类的方法名。最多只能指定一个初始化方法和一个销毁方法。

**Bean作用范围**： 通过的scope属性指定。

**Bean延迟初始化**： 通过的lazy-init属性指定，默认为default，继承于的default-lazy-init设置，该值默认为false。

**应用场景**：

​ 1、Bean实现类来源于第三方类库，如 DataSoure、JdbcTemplate等，因无法在类中标注注解，所以通过XML配置方式比较好。

​ 2、命名空间的配置，如aop、context等，只能采用基于XML的配置。

### 基于注解配置

**Bean的定义**： 在Bean实现类处通过标注@Compoent或衍型类（@Repository、@Service、@Controller）定义Bean。

**Bean的名称**： 通过注解的value属性定义，如@Component("name")。默认名称为小写字母开头的类名（不带包名）name。

**Bean的注入**： 通过通过在成员变更或方法入参处标注@Autowired，按类型匹配自动注入。还可以配合使用@Qualifier按名称匹配方式注入。

**Bean生命过程方法**：通过在目标方法上标注@PostConstruct 和@PreDestroy注解指定初始化或销毁方法，可以定义任意多个。

**Bean作用范围**： 通过在类定义处标注@Scope指定，如@Scope("prototype")

**Bean延迟初始化**： 通过在类定义处标注@Lazy指定，如@Lazy(true)。

**应用场景**：

​ 1、Bean的实现类是当前项目开发的，可以直接在Java类中使用基于注解的配置。

​

### 基于Java类配置

**Bean的定义**： 在标注了@Configuration的Java类中，通过在类方法上标注@Bean定义一个Bean。方法必须提供Bean的实例化逻辑。

**Bean的名称**： 通过@Bean的name属性定义，如@Bean("user")。默认名称为方法名。

**Bean的注入**： 比较灵活，可以在方法处通过@Autowired使方法入参绑定Bean，然后在方法中通过代码进行注入；还可以通过调用配置类@Bean方法进行注入。

> 1. 查询实现对应实现类的Bean，若只有一个则直接匹配
> 2. 若是有多个则依据BeanID/Name进行匹配
> 3. 若无，则报错，若想不报错，则需要配置require=false

**Bean生命过程方法**：通过@Bean的initMethod或destoryMethod指定一个初始化或销毁方法。对于初始化方法来说，可以直接在方法内通过代码的方法灵活定义初始化逻辑。

**Bean作用范围**： 通过在Bean方法定义处标注@Scope指定。

**Bean延迟初始化**： 通过在Bean方法定义处标注@Lazy指定。

**应用场景**：

​ 1、基于JAVA类配置的优势在于可以通过代码方法控制Bean初始化的整体逻辑。如果实例化Bean的逻辑比较复杂，则比较适合基于Java类配置的方式。

​

### 基于Groovy DSL配置

**Bean的定义**： 在Groovy文件中通过DSL定义Bean。

**Bean的名称**： 通过Groovy的DSL定义Bean的名称（Bean的类型，Bean构建函数参数）。

**Bean的注入**： 比较灵活，可以在方法处通过ref()方法进行注入，如：ref("logDao")。

**Bean生命过程方法**：通过bean->bean.initMethod或bean.destoryMethod指定一个初始化或销毁方法。

**Bean作用范围**： 通过bean->bean.scope="prototype"指定。

**Bean延迟初始化**： 通过bean->bean.lazyInit=true指定。

**应用场景**：

​ 1、基于Groovy DSL配置的优势在于可以通过Groovy脚本灵活控制Bean初始化的过程。如果实例化Bean的逻辑比较复杂，则比较适合基于Groovy DSL配置的方式。

![](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200624172023357.png) ![image-20200626100931779](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200626100931779.png)

* 应用工厂方法创建需要的资源，以供APP调用，放入Map类型的bean容器中以便只创建一个对象并只一直对这个对象进行操作

![image-20200624175855384](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200624175855384.png)

![image-20200624175911269](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200624175911269.png)

* 在工厂中创建bean对象的三种方法，默认构造函数若有constructor-arg则可以用带参数的构造函数。

![image-20200624190047128](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200624190047128.png)

![image-20200624195026798](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200624195026798.png)

![image-20200624200708233](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200624200708233.png)

* 如果经常变化的数据，并不适用于注入的方式

![image-20200626105404879](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200626105404879.png)

![image-20200624204112274](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200624204112274.png)

![image-20200626111150208](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200626111150208.png)

![image-20200626111333772](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200626111333772.png)

![image-20200624205352081](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200624205352081.png)

![image-20200626111524993](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200626111524993.png)

* 只关心set方法的方法名

![image-20200624210850659](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200624210850659.png)

* 两种注入方式都可以
* 注解既可以用于创建bean对象（先用contextsacn扫描），又可以在标签的基础上注入依赖（自动:Autowired,+Qualifier可以指定，Value用于指定基本类型），甚至可以通过Configuration注解涉略掉contextscan扫描

![image-20200625101710248](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200625101710248.png)

![image-20200625110311613](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200625110311613.png) ![image-20200625111011079](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200625111011079.png)

![image-20200625145355850](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200625145355850.png)

![image-20200625151610240](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200625151610240.png)

![image-20200625151538155](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200625151538155.png) ![image-20200625151917183](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200625151917183.png)

![image-20200625184727135](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200625184727135.png)

* 数据源也可配置成容器、Runner面对多个Dao对象时的线程安全

![image-20200625202915987](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200625202915987.png)

![image-20200625204425267](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200625204425267.png)

* 需要创建容器的类用于AnnotationConfigApplicationContext是不注解也会自动加入容器

![image-20200625212914836](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200625212914836.png)

![image-20200625213822300](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200625213822300.png)

* 如果是导入的包用xml比较合适，如果是自己写的类，注解方便点

![image-20200625215604223](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200625215604223.png)

* RunWith用Junit的Srpingtest依赖（maven），再用ContextConfiguration标明容器化的位置，再用Autowired的自动注入（扫描注解会自动生成容器）

![image-20200626153808540](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200626153808540.png)

![image-20200626153840701](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200626153840701.png)

![image-20200626154123981](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200626154123981.png)

*   cglib

    ![image-20200626164023501](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200626164023501.png)

![image-20200626164131453](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200626164131453.png)

## AOP

![image-20200626193001665](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200626193001665.png)

通过动态代理实现面向切面的编程，是函数式编程的可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性。

JointPoint：程序运行中的一个被拦截的行为

PointCut：invoke方法中对于Joinpoint的调用的行为

![image-20200626201506651](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200626201506651.png)

* 连接点（所有被代理接口中的方法），切入点：连接点中被增强的连接点

![image-20200626201913615](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200626201913615.png)

![image-20200626202152133](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200626202152133.png)

![image-20200626202743981](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200626202743981.png)

### [aop:aspect](aop:aspect)

![image-20200626205509055](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200626205509055.png)

![image-20200626205443285](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200626205443285.png)

![image-20200626205535029](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200626205535029.png)

![image-20200626211959142](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200626211959142.png)

![image-20200626212051843](https://f/Typora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98/Spring/Spring.assets/image-20200626212051843.png) ![image-20200626212334057](https://f/Typora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98/Spring/Spring.assets/image-20200626212334057.png) ![image-20200626213237753](https://f/Typora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98/Spring/Spring.assets/image-20200626213237753.png) ![image-20200626213257768](https://f/Typora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98/Spring/Spring.assets/image-20200626213257768.png)

* 四个通知类型，配置切入点表达式，在aop：aspect外面的切入点表达式需要在配置通知前面

![image-20200626214046683](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200626214046683.png)

![image-20200626215320009](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200626215320009.png)

![image-20200626215333127](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200626215333127.png)

![image-20200626215402944](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200626215402944.png)

![image-20200626220829370](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200626220829370.png)

* 注解AOP如果不用环绕用别的，会出现顺序问题，如果要用，建议用环绕

### aop:advice

```xml
<aop:config>
    <aop:pointcut id = "pointcut" expression = "execution(* com.gary.spring.Student.*(..))"/>
    <aop:advisor advice-ref = "adviceHandler" pointcut-ref="pointCut"/>
</aop:config>   
```

### 两者的区别

1. 从配置文件上看

> [aop:advisor](aop:advisor) 直接通过advice-ref和pointcut-ref只想advice(切入的方法实现)和point
>
> [aop:aspect](aop:aspect) 需要在内部定义不同的advice类型，比如[aop:around](aop:around)（aop：pointcut是可以写在aop：aspect外部的，但要在配置通知前面）

1. 从代码编写上看

> [aop:aspect](aop:aspect)内部指定了Advice的类型，所以adviceHandler的实现类不需要实现任何借口，直接定义即可
>
> ```java
> public class AdviceHandler  {
>     public Object doAround(ProceedingJoinPoint pjp) throws Throwable {
>         System.out.println("-----doAround().invoke-----");
>         //调用核心逻辑
>         Object retVal = pjp.proceed();
>         System.out.println("-----End of doAround()------");
>         return retVal;
>     }
> }
> ```
>
> 而[aop:advisor](aop:advisor)对应的Advice实现就需要实现Advice接口，比如MethodBeforeAdvice。
>
> ```java
> public class AdviceHandler implements MethodBeforeAdvice, AfterReturningAdvice {
>     @Override
>     public void afterReturning(Object returnValue, Method method, Object[] args, Object target) throws Throwable {
>         System.out.println(" 此处可以做类似于Before Advice的事情");
>     }
>     @Override
>     public void before(Method method, Object[] args, Object target) throws Throwable {
>         System.out.println(" 此处可以做类似于After Advice的事情");
>     }
> }
> ```

1. **从Spring解析过程来看**

https://www.jianshu.com/p/7388398d7019

## 数据访问层封装

![](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200626233906439.png)

*   BeanPropertyRowMapper和RowMapper的区别（bean封装了RowMapper需要自己实现的部分）

    ![image-20200627221326997](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200627221326997.png)
* JdbcDaoSupport的使用以及Dao的两种编写方式
  * 注解时多个Template是无法autowired+其他的改边指定
* ![image-20200627232303922](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200627232303922.png)
  * 灵活

![image-20200627232331122](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200627232331122.png)

![image-20200627235605512](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200627235605512.png)

![image-20200628000302772](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200628000302772.png)

![image-20200628000313928](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200628000313928.png)

![image-20200628000321085](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200628000321085.png)

![image-20200628000338472](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200628000338472.png)

## SPRING事务传播机制

在多个方法的调用中是如何传递的，是重新创建事务还是使用父方法的事务？父方法的回滚对子方法的事务是否有影响？这些都是可以通过事务传播机制来决定的

| 事务传播行为类型                    | 说明                                                          |
| --------------------------- | ----------------------------------------------------------- |
| PROPAGATION\_REQUIRED       | 如果当前没有事务，就新建一个事务，如果已经存在一个事务中，加入到这个事务中。这是最常见的选择。             |
| PROPAGATION\_SUPPORTS       | 支持当前事务，如果当前没有事务，就以非事务方式执行。                                  |
| PROPAGATION\_MANDATORY      | 使用当前的事务，如果当前没有事务，就抛出异常。                                     |
| PROPAGATION\_REQUIRES\_NEW  | 新建事务，如果当前存在事务，把当前事务挂起。                                      |
| PROPAGATION\_NOT\_SUPPORTED | 以非事务方式执行操作，如果当前存在事务，就把当前事务挂起。                               |
| PROPAGATION\_NEVER          | 以非事务方式执行，如果当前存在事务，则抛出异常。                                    |
| PROPAGATION\_NESTED         | 如果当前存在事务，则在嵌套事务内执行。如果当前没有事务，则执行与PROPAGATION\_REQUIRED类似的操作。 |

![image-20200628000514554](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200628000514554.png)

* 基于XML的事务控制管理

![image-20200628114156648](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200628114156648.png)

![image-20200628114223723](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200628114223723.png)

* 基于注解的事务控制管理（任用xml配置datasource和扫描）

![image-20200628132748481](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200628132748481.png)

![image-20200628132818785](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200628132818785.png)

* 纯注解的事务控制管理

![image-20200628134037927](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20200628134037927.png)

## Spring Bean

**由Scope为Singleton时交由Spring容器管理时有如下生命周期流程，若是prototype则交由程序自己控制。**

#### **一、生命周期流程图：**

![image-20201030134546745](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpring.assets%5Cimage-20201030134546745.png)

#### **二、各种接口方法分类**

Bean的完整生命周期经历了各种方法调用，这些方法可以划分为以下几类：

1、Bean自身的方法　　：　　这个包括了Bean本身调用的方法和通过配置文件中的init-method和destroy-method指定的方法

2、Bean级生命周期接口方法　　：　　这个包括了BeanNameAware、BeanFactoryAware、InitializingBean和DiposableBean这些接口的方法

3、容器级生命周期接口方法　　：　　这个包括了InstantiationAwareBeanPostProcessor 和 BeanPostProcessor 这两个接口实现，一般称它们的实现类为“后处理器”。

4、工厂后处理器接口方法　　：　　这个包括了AspectJWeavingEnabler, ConfigurationClassPostProcessor, CustomAutowireConfigurer等等非常有用的工厂后处理器　　接口的方法。工厂后处理器也是容器级的。在应用上下文装配配置文件之后立即调用。

#### **三、演示**

我们用一个简单的Spring Bean来演示一下Spring Bean的生命周期。

1、首先是一个简单的Spring Bean，调用Bean自身的方法和Bean级生命周期接口方法，为了方便演示，它实现了BeanNameAware、BeanFactoryAware、InitializingBean和DiposableBean这4个接口，同时有2个方法，对应配置文件中的init-method和destroy-method。如下：

```java
package springBeanTest;

import org.springframework.beans.BeansException;
import org.springframework.beans.factory.BeanFactory;
import org.springframework.beans.factory.BeanFactoryAware;
import org.springframework.beans.factory.BeanNameAware;
import org.springframework.beans.factory.DisposableBean;
import org.springframework.beans.factory.InitializingBean;

/**
* @author qsk
*/
public class Person implements BeanFactoryAware, BeanNameAware,
       InitializingBean, DisposableBean {

   private String name;
   private String address;
   private int phone;

   private BeanFactory beanFactory;
   private String beanName;

   public Person() {
       System.out.println("【构造器】调用Person的构造器实例化");
   }

   public String getName() {
       return name;
   }

   public void setName(String name) {
       System.out.println("【注入属性】注入属性name");
       this.name = name;
   }

   public String getAddress() {
       return address;
   }

   public void setAddress(String address) {
       System.out.println("【注入属性】注入属性address");
       this.address = address;
   }

   public int getPhone() {
       return phone;
   }

   public void setPhone(int phone) {
       System.out.println("【注入属性】注入属性phone");
       this.phone = phone;
   }

   @Override
   public String toString() {
       return "Person [address=" + address + ", name=" + name + ", phone="
               + phone + "]";
   }

   // 这是BeanFactoryAware接口方法
   @Override
   public void setBeanFactory(BeanFactory arg0) throws BeansException {
       System.out
               .println("【BeanFactoryAware接口】调用BeanFactoryAware.setBeanFactory()");
       this.beanFactory = arg0;
   }

   // 这是BeanNameAware接口方法
   @Override
   public void setBeanName(String arg0) {
       System.out.println("【BeanNameAware接口】调用BeanNameAware.setBeanName()");
       this.beanName = arg0;
   }

   // 这是InitializingBean接口方法
   @Override
   public void afterPropertiesSet() throws Exception {
       System.out
               .println("【InitializingBean接口】调用InitializingBean.afterPropertiesSet()");
   }

   // 这是DiposibleBean接口方法
   @Override
   public void destroy() throws Exception {
       System.out.println("【DiposibleBean接口】调用DiposibleBean.destory()");
   }

   // 通过<bean>的init-method属性指定的初始化方法
   public void myInit() {
       System.out.println("【init-method】调用<bean>的init-method属性指定的初始化方法");
   }

   // 通过<bean>的destroy-method属性指定的初始化方法
   public void myDestory() {
       System.out.println("【destroy-method】调用<bean>的destroy-method属性指定的初始化方法");
   }
}
```

2、接下来是演示BeanPostProcessor接口的方法，如下：

```java
package springBeanTest;

import org.springframework.beans.BeansException;
import org.springframework.beans.factory.config.BeanPostProcessor;

public class MyBeanPostProcessor implements BeanPostProcessor {

    public MyBeanPostProcessor() {
        super();
        System.out.println("这是BeanPostProcessor实现类构造器！！");
        // TODO Auto-generated constructor stub
    }

    @Override
    public Object postProcessAfterInitialization(Object arg0, String arg1)
            throws BeansException {
        System.out
        .println("BeanPostProcessor接口方法postProcessAfterInitialization对属性进行更改！");
        return arg0;
    }

    @Override
    public Object postProcessBeforeInitialization(Object arg0, String arg1)
            throws BeansException {
        System.out
        .println("BeanPostProcessor接口方法postProcessBeforeInitialization对属性进行更改！");
        return arg0;
    }
}
```

如上，BeanPostProcessor接口包括2个方法postProcessAfterInitialization和postProcessBeforeInitialization，这两个方法的第一个参数都是要处理的Bean对象，第二个参数都是Bean的name。返回值也都是要处理的Bean对象。这里要注意。

3、InstantiationAwareBeanPostProcessor 接口本质是BeanPostProcessor的子接口，一般我们继承Spring为其提供的适配器类InstantiationAwareBeanPostProcessor Adapter来使用它，如下：

```java
package springBeanTest;

import java.beans.PropertyDescriptor;

import org.springframework.beans.BeansException;
import org.springframework.beans.PropertyValues;
import org.springframework.beans.factory.config.InstantiationAwareBeanPostProcessorAdapter;

public class MyInstantiationAwareBeanPostProcessor extends
        InstantiationAwareBeanPostProcessorAdapter {

    public MyInstantiationAwareBeanPostProcessor() {
        super();
        System.out
                .println("这是InstantiationAwareBeanPostProcessorAdapter实现类构造器！！");
    }

    // 接口方法、实例化Bean之前调用
    @Override
    public Object postProcessBeforeInstantiation(Class beanClass,
            String beanName) throws BeansException {
        System.out
                .println("InstantiationAwareBeanPostProcessor调用postProcessBeforeInstantiation方法");
        return null;
    }

    // 接口方法、实例化Bean之后调用
    @Override
    public Object postProcessAfterInstantiation(Object bean, String beanName)
            throws BeansException {
        System.out
                .println("InstantiationAwareBeanPostProcessor调用postProcessAfterInstantiation方法");
        return bean;
    }

    // 接口方法、设置某个属性时调用
    @Override
    public PropertyValues postProcessPropertyValues(PropertyValues pvs,
            PropertyDescriptor[] pds, Object bean, String beanName)
            throws BeansException {
        System.out
                .println("InstantiationAwareBeanPostProcessor调用postProcessPropertyValues方法");
        return pvs;
    }
}
```

这个有3个方法，其中第二个方法postProcessAfterInitialization就是重写了BeanPostProcessor的方法。第三个方法postProcessPropertyValues用来操作属性，返回值也应该是PropertyValues对象。

4、演示工厂后处理器接口方法，如下：

```java
package springBeanTest;

import org.springframework.beans.BeansException;
import org.springframework.beans.factory.config.BeanDefinition;
import org.springframework.beans.factory.config.BeanFactoryPostProcessor;
import org.springframework.beans.factory.config.ConfigurableListableBeanFactory;

public class MyBeanFactoryPostProcessor implements BeanFactoryPostProcessor {

    public MyBeanFactoryPostProcessor() {
        super();
        System.out.println("这是BeanFactoryPostProcessor实现类构造器！！");
    }

    @Override
    public void postProcessBeanFactory(ConfigurableListableBeanFactory arg0)
            throws BeansException {
        System.out
                .println("BeanFactoryPostProcessor调用postProcessBeanFactory方法");
        BeanDefinition bd = arg0.getBeanDefinition("person");
        bd.getPropertyValues().addPropertyValue("phone", "110");
    }

}
```

5、配置文件如下beans.xml，很简单，使用ApplicationContext,处理器不用手动注册：

```xml
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:p="http://www.springframework.org/schema/p"
    xmlns:aop="http://www.springframework.org/schema/aop" xmlns:tx="http://www.springframework.org/schema/tx"
    xsi:schemaLocation="
            http://www.springframework.org/schema/beans 
            http://www.springframework.org/schema/beans/spring-beans-3.2.xsd">

    <bean id="beanPostProcessor" class="springBeanTest.MyBeanPostProcessor">
    </bean>

    <bean id="instantiationAwareBeanPostProcessor" class="springBeanTest.MyInstantiationAwareBeanPostProcessor">
    </bean>

    <bean id="beanFactoryPostProcessor" class="springBeanTest.MyBeanFactoryPostProcessor">
    </bean>
    
    <bean id="person" class="springBeanTest.Person" init-method="myInit"
        destroy-method="myDestory" scope="singleton" p:name="张三" p:address="广州"
        p:phone="15900000000" />

</beans>
```

6、下面测试一下：

```java
package springBeanTest;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class BeanLifeCycle {

    public static void main(String[] args) {

        System.out.println("现在开始初始化容器");

        ApplicationContext factory = new ClassPathXmlApplicationContext("springBeanTest/beans.xml");
        System.out.println("容器初始化成功");
        //得到Preson，并使用
        Person person = factory.getBean("person",Person.class);
        System.out.println(person);

        System.out.println("现在开始关闭容器！");
        ((ClassPathXmlApplicationContext)factory).registerShutdownHook();
    }
}
```

我们来看一下结果：

```java
现在开始初始化容器
2014-5-18 15:46:20 org.springframework.context.support.AbstractApplicationContext prepareRefresh
信息: Refreshing org.springframework.context.support.ClassPathXmlApplicationContext@19a0c7c: startup date [Sun May 18 15:46:20 CST 2014]; root of context hierarchy
2014-5-18 15:46:20 org.springframework.beans.factory.xml.XmlBeanDefinitionReader loadBeanDefinitions
信息: Loading XML bean definitions from class path resource [springBeanTest/beans.xml]
这是BeanFactoryPostProcessor实现类构造器！！
BeanFactoryPostProcessor调用postProcessBeanFactory方法
这是BeanPostProcessor实现类构造器！！
这是InstantiationAwareBeanPostProcessorAdapter实现类构造器！！
2014-5-18 15:46:20 org.springframework.beans.factory.support.DefaultListableBeanFactory preInstantiateSingletons
信息: Pre-instantiating singletons in org.springframework.beans.factory.support.DefaultListableBeanFactory@9934d4: defining beans [beanPostProcessor,instantiationAwareBeanPostProcessor,beanFactoryPostProcessor,person]; root of factory hierarchy
InstantiationAwareBeanPostProcessor调用postProcessBeforeInstantiation方法
【构造器】调用Person的构造器实例化
InstantiationAwareBeanPostProcessor调用postProcessAfterInstantiation方法
InstantiationAwareBeanPostProcessor调用postProcessPropertyValues方法
【注入属性】注入属性address
【注入属性】注入属性name
【注入属性】注入属性phone
【BeanNameAware接口】调用BeanNameAware.setBeanName()
【BeanFactoryAware接口】调用BeanFactoryAware.setBeanFactory()
BeanPostProcessor接口方法postProcessBeforeInitialization对属性进行更改！
【InitializingBean接口】调用InitializingBean.afterPropertiesSet()
【init-method】调用<bean>的init-method属性指定的初始化方法
BeanPostProcessor接口方法postProcessAfterInitialization对属性进行更改！
容器初始化成功
Person [address=广州, name=张三, phone=110]
现在开始关闭容器！
【DiposibleBean接口】调用DiposibleBean.destory()
【destroy-method】调用<bean>的destroy-method属性指定的初始化方法
```

## Spring在SingleTon模式下的线程安全

### 有状态和无状态Bean

有状态bean：每个用户有自己特有的一个实例，在用户的生存期内，bean保存了用户的信息，即有状态；一旦用户灭亡（调用结束或实例结束），bean的生命期也告结束。即每个用户最初都会得到一个初始的bean。

无状态bean：bean一旦实例化就被加进会话池中，各个用户都可以共用。即使用户已经消亡，bean的生命期也不一定结束，它可能依然存在于会话池中，供其他用户调用。**没有特定的用户，那么也就不能保持某一用户的状态，所以叫无状态bean。也就是线程中的操作不会对Bean的成员执行查询以外的操作**，但无状态会话bean 并非没有状态，如果它有自己的属性（变量）。

**无状态的Bean适合用不变模式，技术就是单例模式（Singleton），这样可以共享实例，提高性能**。有状态的Bean，多线程环境下不安全，那么适合用Prototype原型模式（解决多线程问题），每次对bean的请求都会创建一个新的bean实例。

**若担心成员变量的线程安全，有三个解决方法：**

1. 方法的参数局部变量（在方法中new）
2. 使用Threadlocal
3. 设置bean的scope=prototype

**Spring根本就没有对bean的多线程安全问题做出任何保证与措施**。对于每个bean的线程安全问题，根本原因是每个bean自身的设计。**不要将有状态的Bean设置成Singleton模式，如果必须将此Bean共享但变量相互独立、线程安全，那么就使用ThreadLocal把变量变为线程私有的，如果有状态bean的实例变量或类变量需要在多个线程之间共享，那么就只能使用synchronized、lock、CAS等这些实现线程同步的方法了。**

### Threadlocal实现单例线程安全

https://juejin.cn/post/6844903509037416455

ThreadLocal是一个为线程提供线程局部变量的工具类。它的思想也十分简单，就是**为线程提供一个线程私有的变量副本**，这样多个线程都可以随意更改自己线程局部的变量，不会影响到其他线程。不过需要注意的是，**ThreadLocal提供的只是一个浅拷贝，如果变量是一个引用类型，那么就要考虑它内部的状态是否会被改变，想要解决这个问题可以通过重写ThreadLocal的initialValue()函数来自己实现深拷贝**，建议在使用ThreadLocal时一开始就重写该函数。

ThreadLocal与像synchronized这样的锁机制是不同的。首先，它们的应用场景与实现思路就不一样，**锁更强调的是如何同步多个线程去正确地共享一个变量，ThreadLocal则是为了解决同一个变量如何不被多个线程共享**。从性能开销的角度上来讲，如果锁机制是用时间换空间的话，那么ThreadLocal就是用空间换时间。

ThreadLocal中含有一个叫做ThreadLocalMap的内部类，该类为一个采用线性探测法实现的HashMap。它的key为ThreadLocal对象而且还使用了WeakReference，ThreadLocalMap正是用来存储变量副本的。

**ThreadLocal是共享的，我们需要ThreadLocal为我们提供方法创建，找到各个线程自己的ThreadLoalMap，并且设置我们不想共享的变量，通过浅拷贝或深拷贝将其传递给各个线程自己的ThreadLoalMap（我们可以通过重写initialValue()来返回我们想要在ThreadLocal中维护的变量），他相当于表单，若有多个ThreadLocal则相当于多个表单，各自有自己数据，因此ThreadLocalMap中的Entry需要以其为Key拷贝保存相应的真正实现value到自己的线程栈中进行使用操作。（若key冲突则线性探测法解决）**

**ThreadLocalMap是每个线程独享的，ThreadLocalMap用一个Entry数组存储我们不想共享的变量，通过ThreadLocal依据不同的Thread找到对应的ThreadLocalMap并提供对Entry的CRUD操作与内存释放（getEntry、Set、remove方法都会检测空的Entry并删除，防止内存泄漏）**

## BeanDefinitionRegistry

`BeanDefinitionRegistry`是一个接口， 实现了`AliasRegistry`接口， 定义了一些对 bean的常用操作。 关于`AliasRegistry`其实已经介绍过了：

它大概有如下功能：

1. 以Map\<String, BeanDefinition>的形式注册bean
2. 根据beanName 删除和获取 beanDefiniation
3. 得到持有的beanDefiniation的数目
4. 根据beanName 判断是否包含beanDefiniation

```java
// 它继承自 AliasRegistry 
public interface BeanDefinitionRegistry extends AliasRegistry {

	// 关键 -> 往注册表中注册一个新的 BeanDefinition 实例 
	void registerBeanDefinition(String beanName, BeanDefinition beanDefinition) throws BeanDefinitionStoreException;
	// 移除注册表中已注册的 BeanDefinition 实例
	void removeBeanDefinition(String beanName) throws NoSuchBeanDefinitionException;
	// 从注册中心取得指定的 BeanDefinition 实例
	BeanDefinition getBeanDefinition(String beanName) throws NoSuchBeanDefinitionException;
	// 判断 BeanDefinition 实例是否在注册表中（是否注册）
	boolean containsBeanDefinition(String beanName);
	
	// 取得注册表中所有 BeanDefinition 实例的 beanName（标识）
	String[] getBeanDefinitionNames();
	// 返回注册表中 BeanDefinition 实例的数量
	int getBeanDefinitionCount();
	// beanName（标识）是否被占用
	boolean isBeanNameInUse(String beanName);
}
```

#### SimpleBeanDefinitionRegistry

是默认的一个实现方式，也是一个非常简单的实现。存储用的是ConcurrentHashMap ，可以保证线程安全

```java
// @since 2.5.2可以看到提供得还是比较晚的
// AliasRegistry和SimpleAliasRegistry都是@since 2.5.2之后才有的~~~
public class SimpleBeanDefinitionRegistry extends SimpleAliasRegistry implements BeanDefinitionRegistry {
	
	// 采用的ConcurrentHashMap来存储注册进来的Bean定义信息~~~~
	/** Map of bean definition objects, keyed by bean name */
	private final Map<String, BeanDefinition> beanDefinitionMap = new ConcurrentHashMap<>(64);
	... // 各种实现都异常的简单，都是操作map
	// 这里只简单说说这两个方法

	// 需要注意的是：如果没有Bean定义，是抛出的异常，而不是返回null这点需要注意
	@Override
	public BeanDefinition getBeanDefinition(String beanName) throws NoSuchBeanDefinitionException {
		BeanDefinition bd = this.beanDefinitionMap.get(beanName);
		if (bd == null) {
			throw new NoSuchBeanDefinitionException(beanName);
		}
		return bd;
	}
	// beanName是个已存在的别名，或者已经包含此Bean定义了，那就证明在使用了嘛
	// 它比单纯的containsBeanDefinition()范围更大些~~~
	@Override
	public boolean isBeanNameInUse(String beanName) {
		return isAlias(beanName) || containsBeanDefinition(beanName);
	}
}
```

#### DefaultListableBeanFactory

该类是 `BeanDefinitionRegistry` 接口的基本实现类，但同时也实现其他了接口的功能，这里只探究下其关于注册 `BeanDefinition` 实例的相关方法。

```java
public class DefaultListableBeanFactory extends AbstractAutowireCapableBeanFactory
		implements ConfigurableListableBeanFactory, BeanDefinitionRegistry, Serializable {
	...
	/** Map of bean definition objects, keyed by bean name */
	private final Map<String, BeanDefinition> beanDefinitionMap = new ConcurrentHashMap<>(256);
	// 保存所有的Bean名称
	/** List of bean definition names, in registration order */
	private volatile List<String> beanDefinitionNames = new ArrayList<>(256);

	// 注册Bean定义信息~~
	@Override
	public void registerBeanDefinition(String beanName, BeanDefinition beanDefinition)
			throws BeanDefinitionStoreException {
		...
		BeanDefinition oldBeanDefinition = this.beanDefinitionMap.get(beanName);
		// 如果不为null，说明这个beanName对应的Bean定义信息已经存在了~~~~~
		if (oldBeanDefinition != null) {
			// 是否允许覆盖（默认是true 表示允许的）
			if (!isAllowBeanDefinitionOverriding()) {
				// 抛异常
			}
			// 若允许覆盖  那还得比较下role  如果新进来的这个Bean的role更大			
			// 比如老的是ROLE_APPLICATION（0）  新的是ROLE_INFRASTRUCTURE(2) 
			// 最终会执行到put，但是此处输出一个warn日志，告知你的bean被覆盖啦~~~~~~~（我们自己覆盖Spring框架内的bean显然就不需要warn提示了）
			else if (oldBeanDefinition.getRole() < beanDefinition.getRole()) {
				// 仅仅输出一个logger.warn
			}
			// 最终会执行put，但是内容还不相同  那就提醒一个info信息吧
			else if (!beanDefinition.equals(oldBeanDefinition)) {
				// 输出一个info信息
			}
			else {
				// 输出一个debug信息
			}
			// 最终添加进去 （哪怕已经存在了~） 
			// 从这里能看出Spring对日志输出的一个优秀处理，方便我们定位问题~~~
			this.beanDefinitionMap.put(beanName, beanDefinition);
			// 请注意：这里beanName并没有再add了，因为已经存在了  没必要了嘛
		}
		else {
			// hasBeanCreationStarted:表示已经存在bean开始创建了（开始getBean()了吧~~~）
			if (hasBeanCreationStarted()) {
				// 注册过程需要synchronized，保证数据的一致性	synchronized (this.beanDefinitionMap) {
					this.beanDefinitionMap.put(beanName, beanDefinition); // 放进去
					List<String> updatedDefinitions = new ArrayList<>(this.beanDefinitionNames.size() + 1);
					updatedDefinitions.addAll(this.beanDefinitionNames);
					updatedDefinitions.add(beanName);
					this.beanDefinitionNames = updatedDefinitions;
			
					// 
					if (this.manualSingletonNames.contains(beanName)) {
						Set<String> updatedSingletons = new LinkedHashSet<>(this.manualSingletonNames);
						updatedSingletons.remove(beanName);
						this.manualSingletonNames = updatedSingletons;
					}
				}
			}
			else {
				// Still in startup registration phase
				// 表示仍然在启动  注册的状态~~~就很好处理了 put仅需，名字add进去
				this.beanDefinitionMap.put(beanName, beanDefinition);
				this.beanDefinitionNames.add(beanName);
				
				// 手动注册的BeanNames里面移除~~~ 因为有Bean定义信息了，所以现在不是手动直接注册的Bean单例~~~~
				this.manualSingletonNames.remove(beanName);
			}

			// 这里的意思是：但凡你新增了一个新的Bean定义信息，之前已经冻结的就清空呗~~~
			this.frozenBeanDefinitionNames = null;
		}
		
		// 最后异步很有意思：老的bean定义信息不为null（beanName已经存在了）,或者这个beanName直接是一个单例Bean了~
		if (oldBeanDefinition != null || containsSingleton(beanName)) {
			// 做清理工作：
			// clearMergedBeanDefinition(beanName)
			// destroySingleton(beanName);  销毁这个单例Bean  因为有了该bean定义信息  最终还是会创建的
			// Reset all bean definitions that have the given bean as parent (recursively).  处理该Bean定义的getParentName  有相同的也得做清楚  所以这里是个递归
			resetBeanDefinition(beanName);
		}
	}

	@Override
	public void removeBeanDefinition(String beanName) throws NoSuchBeanDefinitionException {
		// 移除整体上比较简单：beanDefinitionMap.remove
		// beanDefinitionNames.remove
		// resetBeanDefinition(beanName);

		BeanDefinition bd = this.beanDefinitionMap.remove(beanName);
		// 这里发现移除，若这个Bean定义本来就不存在，事抛异常，而不是返回null 需要注意~~~~
		if (bd == null) {
			throw new NoSuchBeanDefinitionException(beanName);
		}

		if (hasBeanCreationStarted()) {
			synchronized (this.beanDefinitionMap) {
				List<String> updatedDefinitions = new ArrayList<>(this.beanDefinitionNames);
				updatedDefinitions.remove(beanName);
				this.beanDefinitionNames = updatedDefinitions;
			}
		} else {
			this.beanDefinitionNames.remove(beanName);
		}
		this.frozenBeanDefinitionNames = null;
		resetBeanDefinition(beanName);
	}

	// 这个实现非常的简单，直接从map里拿
	@Override
	public BeanDefinition getBeanDefinition(String beanName) throws NoSuchBeanDefinitionException {
		BeanDefinition bd = this.beanDefinitionMap.get(beanName);
		if (bd == null) {
			throw new NoSuchBeanDefinitionException(beanName);
		}
		return bd;
	}

@Override
	public boolean containsBeanDefinition(String beanName) {
		return this.beanDefinitionMap.containsKey(beanName);
	}
	@Override
	public int getBeanDefinitionCount() {
		return this.beanDefinitionMap.size();
	}
	@Override
	public String[] getBeanDefinitionNames() {
		String[] frozenNames = this.frozenBeanDefinitionNames;
		if (frozenNames != null) {
			return frozenNames.clone();
		}
		else {
			return StringUtils.toStringArray(this.beanDefinitionNames);
		}
	}
	
	// ==========这个方法非常有意思：它是间接的实现的===============
	// 因为BeanDefinitionRegistry有这个方法，而它的父类AbstractBeanFactory也有这个方法，所以一步小心，就间接的实现了这个接口方法
	public boolean isBeanNameInUse(String beanName) {
		// 增加了一个hasDependentBean(beanName);  或者这个BeanName是依赖的Bean 也会让位被使用了
		return isAlias(beanName) || containsLocalBean(beanName) || hasDependentBean(beanName);
	}
}
```

#### GenericApplicationContext

```java
public class GenericApplicationContext extends AbstractApplicationContext implements BeanDefinitionRegistry {
    private final DefaultListableBeanFactory beanFactory;
    private ResourceLoader resourceLoader;
    private boolean refreshed = false;
    public GenericApplicationContext() {
        this.beanFactory = new DefaultListableBeanFactory();
    }
        @Override
    public void registerBeanDefinition(String beanName, BeanDefinition beanDefinition)
            throws BeanDefinitionStoreException {

        this.beanFactory.registerBeanDefinition(beanName, beanDefinition);
    }
//其他代码省略
}
```

看到构造方法中的this.beanFactory = new DefaultListableBeanFactory(); 就发现，其实GenericApplicationContext使用的注册bean依然是使用DefaultListableBeanFactory的实现原理。

因为是手动档，对API的使用有一定的门槛，因此我们一般情况下不会直接使用它。但是他有两个子类我们是比较熟悉的

**GenericXmlApplicationContext**

利用XML来配置Bean的定义信息，借助`XmlBeanDefinitionReader`去读取\~\~\~

```java
public class GenericApplicationContext extends AbstractApplicationContext implements BeanDefinitionRegistry {

	// 这个很重要：它持有一个DefaultListableBeanFactory的引用
	private final DefaultListableBeanFactory beanFactory;
	
	// 所以下面所有的 BeanDefinitionRegistry 相关的方法都是委托给beanFactory去做了的，因此省略~~~
}
```

> ```
> AbstractApplicationContext`最终也是委托给`getBeanFactory()`去做这些事。
> 而最终的实现都是它：`DefaultListableBeanFactory`
> 备注：`AnnotationConfigWebApplicationContext`、`XmlWebApplicationContext`都继承自`AbstractRefreshableConfigApplicationContext`，从而也最终继承了`AbstractApplicationContext
> ```

**AnnotationConfigApplicationContext**

注解驱动去扫描Bean的定义信息。先用`ClassPathBeanDefinitionScanner`把文件都扫描进来，然后用`AnnotatedBeanDefinitionReader`去load没有里面的Bean定义信息。

说明：由上面的继承关系可以看出，上文顶层接口`ApplicationContext`是**不提供**直接注册Bean定义信息、删除等一些操作的。因为`ApplicationContext`它并没有实现此接口`BeanDefinitionRegistry`，但是它继承了接口`ListableBeanFactory`，所以它有如下三个能力：

```java
	boolean containsBeanDefinition(String beanName);
	int getBeanDefinitionCount();
	String[] getBeanDefinitionNames();
123
ApplicationContext`体系里只有子体系`GenericApplicationContext`下才能直接操作注册中心。比如常用的：`GenericXmlApplicationContext`和`AnnotationConfigApplicationContext
```

**手动注册BeanDefinition（编程方式注册Bean定义）**

手动注册bean的两种方式：

1. 实现`ImportBeanDefinitionRegistrar`
2. 实现`BeanDefinitionRegistryPostProcessor`

但解析的`BeanDefinition`如何交给`BeanFactory`处理呢？

**各种不同的BeanFactory有不同的`getbean ()`方法，不同的`getbean()`方法会有不同的方式使用beanDefinition，来获得相应的bean对象进入容器中，至于为什么需要beanDefinition而不是直接初始化bean，是因为bean 的实例化与注入属性，各种aware接口的实现，与初始化时会有Listnener等与processor类进行其他的前置和后置处理。**

## @Controller和@RestController的区别?

1.  使用@Controller 注解，在对应的方法上，视图解析器可以解析return 的jsp,html页面，并且跳转到相应页面

    若返回json等内容到页面，则需要加@ResponseBody注解
2. @RestController注解，相当于@Controller+@ResponseBody两个注解的结合，返回json数据不需要在方法前面加@ResponseBody注解了，但使用@RestController这个注解，就不能返回jsp,html页面，视图解析器无法解析jsp,html页面

## ByName与ByType

`byName`就是通过Bean的id或者name，`byType`就是按Bean的Class的类型

## 依赖注入

依赖注入的作用

一个是和IOC（即控制反转）的作用一致，程序运行过程中，如果需要调用另一个对象协助时，无须在代码中创建被调用者，而是依赖于外部的注入。创建被调用者的工作通常由Spring容器来完成，然后注入调用，让bean与bean之间以配置文件组织在一起，而不是以硬编码的方式耦合在一起。

另一个是通过依赖注入的实现，使得基于Spring容器的生成bean基本都能由Spring容器进行管理，使得初始化或释放资源等生命周期管理的功能集中到一个Srping容器中，减轻维护的风险

## 语法糖

语法糖（Syntactic sugar）是一种语法，旨在让代码更适合人类阅读、理解，使得代码更“甜”。

https://blog.csdn.net/weixin\_41712059/article/details/103151238

> **语法糖**
>
> 　　语法糖（Syntactic Sugar），也称糖衣语法，是由英国计算机学家 Peter.J.Landin 发明的一个术语，指在计算机语言中添加的某种语法，这种语法对语言的功能并没有影响，但是更方便程序员使用。简而言之，语法糖让程序更加简洁，有更高的可读性。有意思的是，在编程领域，除了语法糖，还有语法盐和语法糖精的说法，篇幅有限这里不做扩展了。
>
> 　　我们所熟知的编程语言中几乎都有语法糖。作者认为，语法糖的多少是评判一个语言够不够牛逼的标准之一。很多人说Java是一个“低糖语言”，其实从Java 7开始Java语言层面上一直在添加各种糖，主要是在“Project Coin”项目下研发。尽管现在Java有人还是认为现在的Java是低糖，未来还会持续向着“高糖”的方向发展。
>
>
>
> **解语法糖**
>
> 　　前面提到过，语法糖的存在主要是方便开发人员使用。但其实，Java虚拟机并不支持这些语法糖。这些语法糖在编译阶段就会被还原成简单的基础语法结构，这个过程就是解语法糖。说到编译，大家肯定都知道，Java语言中，javac命令可以将后缀名为.java的源文件编译为后缀名为.class的可以运行于Java虚拟机的字节码。如果你去看com.sun.tools.javac.main.JavaCompiler的源码，你会发现在compile()中有一个步骤就是调用desugar()，这个方法就是负责解语法糖的实现的。**属于语法糖部分的代码，在jdk中的javac前端编译器编译成字节码文件时便已经解语法糖，变成了即时编译器JVM支持的基础语法结构**，**Java 中最常用的语法糖主要有泛型、变长参数、条件编译、自动拆装箱、内部类、匿名内部类引用局部变量的final、枚举类等。**
