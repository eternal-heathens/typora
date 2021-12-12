# SpringBoot高级篇

## Spring Boot与缓存

### 一、 JSR107

Java Caching定义了5个核心接口

*   CachingProvider

    定义了创建、配置、获取、管理和控制多个CacheManager。一个应用可 以在运行期访问多个CachingProvider。
*   CacheManager

    定义了创建、配置、获取、管理和控制多个唯一命名的Cache，这些Cache 存在于CacheManager的上下文中。一个CacheManager仅被一个CachingProvider所拥有。
*   Cache

    一个类似**Map**的数据结构并**临时存储以Key为索引**的值。一个Cache仅被一个 CacheManager所拥有。
*   Entry

    一个存储在Cache中的key-value对。
*   Expiry

    每一个存储在Cache中的条目有一个定义的有效期。一旦超过这个时间，条目为过期的状态。一旦过期，条目将不可访问、更新和删除。缓存有效期可以通过ExpiryPolicy设置。

![image-20200822100227070](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpringBoot%E9%AB%98%E7%BA%A7%E7%AF%87.assets%5Cimage-20200822100227070.png)

### 二、 Spring缓存抽象

Spring从3.1开始定义了org.springframework.cache.Cache 和org.springframework.cache.CacheManager接口来**统一**不同的缓存技术； \*\*并支持使用JCache（JSR-107）\*\*注解简化我们开发；

Cache接口有以下功能：

* 为缓存的组件规范定义，包含缓存的各种操作集合；
* Spring提供了各种xxxCache的实现；如RedisCache，EhCacheCache , ConcurrentMapCache等；

![image-20200822104409425](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpringBoot%E9%AB%98%E7%BA%A7%E7%AF%87.assets%5Cimage-20200822104409425.png)

### 三、 重要缓存注解及概念

| **Cache**          | **缓存接口，定义缓存操作。实现有：RedisCache、EhCacheCache、ConcurrentMapCache等** |
| ------------------ | --------------------------------------------------------------- |
| **CacheManager**   | **缓存管理器，管理各种缓存（Cache）组件**                                       |
| **@Cacheable**     | \*\*根据方法的请求参数对其进行查询，若无则对结果进行缓存，有则读取 \*\*                        |
| **@CacheEvict**    | **清空缓存**                                                        |
| **@CachePut**      | **更新缓存**                                                        |
| **@EnableCaching** | **开启基于注解的缓存**                                                   |
| **keyGenerator**   | **缓存数据时key生成策略**                                                |
| **serialize**      | **缓存数据时value序列化策略**                                             |

#### @Cacheable

* 先查看cache 中有没有相应的缓存，再执行方法，并把return的结果保存到缓存中
*   **value**

    **缓存名称**，字符串/字符数组形式；

    如@Cacheable(value=”mycache”) 或者@Cacheable(value={”cache1”,”cache2”}
*   **key**

    **缓存中数据的key**,需要按照SpEL表达式编写，如果不指定则按照方法所有参数进行组合；

    如@Cacheable(value=”testcache”,key=”#userName”)
* **keyGenerator**

key的生成器；可以自己指定key的生成器的组件id

注意\*\*：key/keyGenerator：二选一使用;\*\*

* **cacheManager/cacheResolver**：指定使用的cache管理器
*   **condition**

    **缓存条件**，使用SpEL编写，在调用方法**之前之后**都能判断；true为缓存

    如@Cacheable(value=”testcache”,condition=”#userName.length()>2”)
*   **unless**

    **缓存条件**，只在方法执行**之后**判断，true为不缓存；

    如@Cacheable(value=”testcache”,unless=”#result ==null”)
* **sync**：异步模式，异步模式下不能用unless

#### @CachePut

* 先执行方法（此时不读取缓存），再直接放入缓存
* 注意传参时，key的默认生成策略仍为形参，因此若想刷新的是缓存的，**注意修改key属性**

#### @CacheEvict

* 清空缓存，默认为先执行方法，再清空，也不读取缓存
*   **beforeInvocation**

    是否在执行前清空缓存，默认为false，false情况下方法执行异常则不会清空；

    如@CachEvict(value=”testcache”，beforeInvocation=true)
*   **allEntries**

    是否清空所有缓存内容，默认为false；

    如@CachEvict(value=”testcache”,allEntries=true)

#### **@CacheConfig**

标注在类上，用于抽取@Cacheable的公共属性

由于一个类中可能会使用多次@Cacheable等注解，所以各项属性可以抽取到@CacheConfig

#### **@Caching**

组合使用@Cacheable、@CachePut、@CacheEvict

#### 2 . 缓存可用的SpEL表达式

**root表示根对象，不可省略**

*   被调用方法名 methodName

    如 **#root.methodName**
*   也是被调用方法 method的名字

    如 **#root.method.name**
*   被调用的目标对象 target

    如 **#root.target**
*   被调用的目标对象类 targetClass

    如 **#root.targetClass**
*   被调用的方法的参数列表 **args**

    如 **#root.args\[0]**
*   方法调用使用的缓存列表 **caches**，用于指定@Cacheable的value

    如 **#root.caches\[0].name**
*   参数名:方法参数的名字. 可以直接 **#参数名 ，也可以使用 #p0或#a0 的形式，0代表参数的索引；**

    如 #iban 、 #a0 、 #p0
*   返回值:方法执行后的返回值\*\*（仅当方法执行之后的判断有效，如‘unless’ ， @CachePut、@CacheEvict’的表达式beforeInvocation=false ）\*\*

    如 **#result**

### 四、 缓存使用

#### 1. 基本使用步骤

1. 引入spring-boot-starter-cache模块

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-cache</artifactId>
</dependency>
```

1.  @EnableCaching开启缓存

    在主配置类上标注
2.  使用缓存注解

    如@Cacheable、@CachePut
3. 切换为其他缓存

#### 2. 搭建实验环境

1.  导入数据库文件 创建出department和employee表

    ```sql
    -- ----------------------------
    -- Table structure for department
    -- ----------------------------
    DROP TABLE IF EXISTS `department`;
    CREATE TABLE `department` (
      `id` int(11) NOT NULL AUTO_INCREMENT,
      `departmentName` varchar(255) DEFAULT NULL,
      PRIMARY KEY (`id`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8;

    -- ----------------------------
    -- Table structure for employee
    -- ----------------------------
    DROP TABLE IF EXISTS `employee`;
    CREATE TABLE `employee` (
      `id` int(11) NOT NULL AUTO_INCREMENT,
      `lastName` varchar(255) DEFAULT NULL,
      `email` varchar(255) DEFAULT NULL,
      `gender` int(2) DEFAULT NULL,
      `d_id` int(11) DEFAULT NULL,
      PRIMARY KEY (`id`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8;
    ```
2. 创建javaBean封装数据
3.  整合MyBatis操作数据库

    配置数据源信息

    ```java
    spring.datasource.username=root
    spring.datasource.password=123
    spring.datasource.url=jdbc:mysql://localhost:3306/springboot?serverTimezone=GMT
    spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver

    # 开启驼峰命名法(否则部分字段封装不了)
    mybatis.configuration.map-underscore-to-camel-case=true
    #打印sql
    logging.level.cn.edu.ustc.springboot.mapper=debug

    debug=true
    ```

    使用注解版的MyBatis；

    @MapperScan指定需要扫描的mapper接口所在的包
4. 主配置类开启@EnableCaching

#### 3. 快速体验缓存

**@Cacheable、@CachePut、@CacheEvict的使用**

```java
@Service
public class EmployeeService {
    @Autowired
    private EmployeeMapper employeeMapper;

    @Cacheable(value={"emp"},
            key = "#id+#root.methodName+#root.caches[0].name",
            condition = "#a0>1",
            unless = "#p0==2"
    )
    public Employee getEmpById(Integer id) {
        System.out.println("查询员工："+id);
        return employeeMapper.getEmpById(id);
    }

    @CachePut(value = {"emp"},key = "#employee.id" )
    public Employee updateEmp(Employee employee) {
        System.out.println("更新员工"+employee);
        employeeMapper.updateEmp(employee);
        return employee;
    }

    @CacheEvict(value = {"emp"},allEntries = true,beforeInvocation = true)
    public Integer delEmp(Integer id){
        int i=1/0;
        System.out.println("删除员工："+id);
        employeeMapper.delEmp(id);
        return id;
    }
}
```

**自定义KeyGenerator**

使用时在注解属性内指定KeyGenerator=“myKeyGenerator”

```java
@Configuration
public class MyCacheConfig {
    @Bean("myKeyGenerator")
    public KeyGenerator myKeyGenerator() {
        return new KeyGenerator(){
            @Override
            public Object generate(Object target, Method method, Object... params) {
                return method.getName()+"["+ Arrays.asList(params).toString()+target+"]";
            }
        };
    }
}
```

**@CacheConfig**

标注在类上，用于抽取@Cacheable的公共属性

由于一个类中可能会使用多次@Cacheable等注解，所以各项属性可以抽取到@CacheConfig

**@Caching**

组合使用@Cacheable、@CachePut、@CacheEvict

```java
  @Caching(
         cacheable = {
             @Cacheable(/*value="emp",*/key = "#lastName")
         },
         put = {
             @CachePut(/*value="emp",*/key = "#result.id"),
             @CachePut(/*value="emp",*/key = "#result.email")
         }
    )
    public Employee getEmpByLastName(String lastName){
        return employeeMapper.getEmpByLastName(lastName);
    }
```

#### 4. 工作原理

* 缓存的自动配置类`CacheAutoConfiguration`向容器中导入了`CacheConfigurationImportSelector`，此类的`selectImports`()方法通过调用**CacheConfigurations**中的**staic块和static方法初始化**了许多的`cacheConfiguration`的Class信息在集合中
* key值为`CacheType`枚举类中的值，因此**逐个调用CacheType**中的值就能读取到有哪些可以读取的cacheConfiguration，其中`SimpleCacheConfiguration`默认生效。
*   **==@Cacheable的运行流程==**（会将有@Cacheable的类进行动态代理，对@Cacheable的方法在Invoker中进行判断增强）

    1. 方法运行之前，先去查询Cache（缓存组件），**按照cacheNames指定的名字获取**； （CacheManager先获取相应的缓存），第一次获取缓存如果没有Cache组件会自动创建,并以cacheNames-cache对放入ConcurrentMap。
    2.  去Cache中查找缓存的内容，使用一个key，默认就是方法的参数； **key是按照某种策略生成的**；默认是使用keyGenerator生成的，默认使用SimpleKeyGenerator生成key；

        SimpleKeyGenerator生成key的默认策略；

        如果没有参数；key=new SimpleKey()； 如果有一个参数：key=参数的值 如果有多个参数：key=new SimpleKey(params)；
    3. 没有查到缓存就调用目标方法；
    4. 将目标方法返回的结果，放进缓存中

    @Cacheable标注的方法执行之前先来检查缓存中有没有这个数据，默认按照参数的值作为key去查询缓存， 如果没有就运行方法并将结果放入缓存；以后再来调用就可以直接使用缓存中的数据；

```java
@Import({ CacheConfigurationImportSelector.class, CacheManagerEntityManagerFactoryDependsOnPostProcessor.class })
public class CacheAutoConfiguration {
    static class CacheConfigurationImportSelector implements ImportSelector {

		@Override
		public String[] selectImports(AnnotationMetadata importingClassMetadata) {
            //CacheType为CacheConfigurations中保存的key，借这个可以查询到所有的可以配置的CacheConfigurations
			CacheType[] types = CacheType.values();
			String[] imports = new String[types.length];
			for (int i = 0; i < types.length; i++) {
                //将即将导入的各配置类存入字符数组内
				imports[i] = CacheConfigurations.getConfigurationClass(types[i]);
			}
			return imports;
		}

	}   
}
```

`SimpleCacheConfiguration`向容器中导入了`ConcurrentMapCacheManager`

```java
@Configuration(proxyBeanMethods = false)
//判断是否有别的CacheManager，有则不配置
@ConditionalOnMissingBean(CacheManager.class)
@Conditional(CacheCondition.class)
class SimpleCacheConfiguration {
    //向容器中导入ConcurrentMapCacheManager
	@Bean
	ConcurrentMapCacheManager cacheManager(CacheProperties cacheProperties,
			CacheManagerCustomizers cacheManagerCustomizers) {
		ConcurrentMapCacheManager cacheManager = new ConcurrentMapCacheManager();
		List<String> cacheNames = cacheProperties.getCacheNames();
		if (!cacheNames.isEmpty()) {
			cacheManager.setCacheNames(cacheNames);
		}
		return cacheManagerCustomizers.customize(cacheManager);
	}
}
```

`ConcurrentMapCacheManager`以HashMap形式存储ConcurrentMapCache

```java
public void setCacheNames(@Nullable Collection<String> cacheNames) {
		if (cacheNames != null) {
			for (String name : cacheNames) {
                //依据@标签的value传入的cacheNames创建ConcurrentMapCache并加入cacheMap中
				this.cacheMap.put(name, createConcurrentMapCache(name));
			}
			this.dynamic = false;
		}
		else {
			this.dynamic = true;
		}
	}
@Override
	@Nullable
	public Cache getCache(String name) {
		Cache cache = this.cacheMap.get(name);
		if (cache == null && this.dynamic) {
			synchronized (this.cacheMap) {
				cache = this.cacheMap.get(name);
				if (cache == null) {
                     //依据@标签的value传入的cacheNames创建ConcurrentMapCache并加入cacheMap中
					cache = createConcurrentMapCache(name);
					this.cacheMap.put(name, cache);
				}
			}
		}
		return cache;
	}
```

* 对需要的进行缓存操作的方法所增强的切面类`CacheAspectSupport`

```java
public abstract class CacheAspectSupport extends AbstractCacheInvoker
		implements BeanFactoryAware, InitializingBean, SmartInitializingSingleton {
    //在执行@Cacheable/@CachePut等标注的方法前执行此方法
    @Nullable
	private Object execute(final CacheOperationInvoker invoker, Method method, CacheOperationContexts contexts) {
        //CacheOperationContexts contexts，在动态代理中生成的cache上下文容器
        //若是缓存容器加锁则顺序执行
		if (contexts.isSynchronized()) {
            // 获取当前缓存容器中存有的cache和保存目标对象的信息
			CacheOperationContext context = contexts.get(CacheableOperation.class).iterator().next();
			if (isConditionPassing(context, CacheOperationExpressionEvaluator.NO_RESULT)) {
                //生成查询的对象的key值，依据一定策略生成
				Object key = generateKey(context, CacheOperationExpressionEvaluator.NO_RESULT);
                //获取容器中的cache
				Cache cache = context.getCaches().iterator().next();
				try {
					return wrapCacheValue(method, cache.get(key, () -> unwrapReturnValue(invokeOperation(invoker))));
				}
				catch (Cache.ValueRetrievalException ex) {
					throw (CacheOperationInvoker.ThrowableWrapper) ex.getCause();
				}
			}
			else {
				return invokeOperation(invoker);
			}
		}
        
        //若是有@CacheEvicts，执行该语句，判断清除参数，依据参数清除缓存；若@CacheEvicts方法全部删除，则后面的cacheHit仍为空；若只删除部分，且只有@CacheEvicts则findCachedItem中没有key值，也不会查询缓存。只有@Cacheable的key才会被用以查询缓存
		processCacheEvicts(contexts.get(CacheEvictOperation.class), true,
				CacheOperationExpressionEvaluator.NO_RESULT);

        // 见findCachedItem方法
        //获取context中的CacheableOperation.class（即@Cacheable注解的方法），再findCachedItem通过一定规则生成的key找cache，若没找到则返回null
		Cache.ValueWrapper cacheHit = findCachedItem(contexts.get(CacheableOperation.class));

		List<CachePutRequest> cachePutRequests = new LinkedList<>();
		if (cacheHit == null) {
			collectPutRequests(contexts.get(CacheableOperation.class),
					CacheOperationExpressionEvaluator.NO_RESULT, cachePutRequests);
		}

		Object cacheValue;
		Object returnValue;

		if (cacheHit != null && !hasCachePut(contexts)) {
            // 若是contexts中有CachePut，如用@Caching或单纯@CachePut，都将强制先执行方法
            // 若cacheEvict为提前执行，则此时cacheHit为null，执行方法；若为延后执行，则执行方法前不会用到Cache
			// 若为Cacheable且无CachePut，如果通过该key找到缓存,则直接返回cacheValue，跳过执行方法（对数据库的查询）返回key对应的value到ruturnvalue中。因此要注意key的设置
			cacheValue = cacheHit.get();
			returnValue = wrapCacheValue(method, cacheValue);
		}
		else {
			// 若通过该key未找到缓存，则执行@cacheable标注方法
			returnValue = invokeOperation(invoker);
			cacheValue = unwrapReturnValue(returnValue);
		}

		// Collect any explicit @CachePuts
		collectPutRequests(contexts.get(CachePutOperation.class), cacheValue, cachePutRequests);

		// Process any collected put requests, either from @CachePut or a @Cacheable miss
		for (CachePutRequest cachePutRequest : cachePutRequests) {
			cachePutRequest.apply(cacheValue);
		}

		// 若是默认的延后执行，则此时清空缓存
		processCacheEvicts(contexts.get(CacheEvictOperation.class), false, cacheValue);

		return returnValue;
	}
    
    @Nullable
	private Cache.ValueWrapper findCachedItem(Collection<CacheOperationContext> contexts) {
		Object result = CacheOperationExpressionEvaluator.NO_RESULT;
		for (CacheOperationContext context : contexts) {
			if (isConditionPassing(context, result)) {
                //通过一定规则生成key值(生成规则见generateKey方法)
				Object key = generateKey(context, result);
                //通过生成的key寻找缓存
				Cache.ValueWrapper cached = findInCaches(context, key);
				if (cached != null) {
					return cached;
				}
				else {
					if (logger.isTraceEnabled()) {
						logger.trace("No cache entry for key '" + key + "' in cache(s) " + context.getCacheNames());
					}
				}
			}
		}
		return null;
	}
    
    //key的生成策略
    @Nullable
    protected Object generateKey(@Nullable Object result) {
        //如果@Cacheable设置了属性key，则根据设置值生成key
        if (StringUtils.hasText(this.metadata.operation.getKey())) {
            EvaluationContext evaluationContext = createEvaluationContext(result);
            return evaluator.key(this.metadata.operation.getKey(), this.metadata.methodKey, evaluationContext);
        }
        //否则使用keyGenerator生成key，默认keyGenerator为SimpleKeyGenerator
        return this.metadata.keyGenerator.generate(this.target, this.metadata.method, this.args);
    }
```

默认情况下使用SimpleKeyGenerator生成key

```java
public class SimpleKeyGenerator implements KeyGenerator {
    //SimpleKeyGenerator的生成规则
    public static Object generateKey(Object... params) {
        //若无参，则返回空key
		if (params.length == 0) {
			return SimpleKey.EMPTY;
		}
		if (params.length == 1) {
			Object param = params[0];
			if (param != null && !param.getClass().isArray()) {
                //1个参数，则直接返回该参数
				return param;
			}
		}
          //多个参数返回数组
		return new SimpleKey(params);
	}
}
```

默认的缓存类ConcurrentMapCache，使用ConcurrentMap存储k-v

```java
public class ConcurrentMapCache extends AbstractValueAdaptingCache {

	private final String name;

    //存储key-cacheValue
	private final ConcurrentMap<Object, Object> store;
    
    //通过key查找cacheValue
	protected Object lookup(Object key) {
		return this.store.get(key);
	}
    
    //方法调用完后将结果存入缓存中
    public void put(Object key, @Nullable Object value) {
		this.store.put(key, toStoreValue(value));
	}
}
```

核心： 1）、使用CacheManager【ConcurrentMapCacheManager】按照名字得到Cache【ConcurrentMapCache】组件

​ 2）、key使用keyGenerator生成的，默认是SimpleKeyGenerator

### 五、Redis与缓存

#### 1. 环境搭建

导入依赖

```xml
       <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>
```

在spring.properties指定Redis服务器地址

```properties
#redis服务器主机地址
spring.redis.host=192.168.31.162
```

**Redis常见的五大数据类型**

String（字符串）、List（列表）、Set（集合）、Hash（散列）、ZSet（有序集合）

stringRedisTemplate.opsForValue()\[String（字符串）]

stringRedisTemplate.opsForList()\[List（列表）]

stringRedisTemplate.opsForSet()\[Set（集合）]

stringRedisTemplate.opsForHash()\[Hash（散列）]

stringRedisTemplate.opsForZSet()\[ZSet（有序集合）]

#### 2. RedisTemplate使用

```java
@Autowired
	StringRedisTemplate stringRedisTemplate;  //操作k-v都是字符串的

	@Autowired
	RedisTemplate redisTemplate;  //k-v都是对象的

	@Autowired
	RedisTemplate<Object, Employee> empRedisTemplate;

@Test
	public void test01(){
		//给redis中保存数据
	    //stringRedisTemplate.opsForValue().append("msg","hello");
//		String msg = stringRedisTemplate.opsForValue().get("msg");
//		System.out.println(msg);

//		stringRedisTemplate.opsForList().leftPush("mylist","1");
//		stringRedisTemplate.opsForList().leftPush("mylist","2");
	}

	//测试保存对象
	@Test
	public void test02(){
		Employee empById = employeeMapper.getEmpById(1);
		//默认如果保存对象，使用jdk序列化机制，序列化后的数据保存到redis中
		//redisTemplate.opsForValue().set("emp-01",empById);
		//1、将数据以json的方式保存
		 //(1)自己将对象转为json
		 //(2)redisTemplate默认的序列化规则；改变默认的序列化规则；
		empRedisTemplate.opsForValue().set("emp-01",empById);
	}
```

#### 3. 自定义RedisTemplate

```java
@Bean
    public RedisTemplate<Object, Employee> empRedisTemplate(
            RedisConnectionFactory redisConnectionFactory)
            throws UnknownHostException {
        RedisTemplate<Object, Employee> template = new RedisTemplate<Object, Employee>();
        template.setConnectionFactory(redisConnectionFactory);
        Jackson2JsonRedisSerializer<Employee> ser = new Jackson2JsonRedisSerializer<Employee>(Employee.class);
        template.setDefaultSerializer(ser);
        return template;
    }
    @Bean
    public RedisTemplate<Object, Department> deptRedisTemplate(
            RedisConnectionFactory redisConnectionFactory)
            throws UnknownHostException {
        RedisTemplate<Object, Department> template = new RedisTemplate<Object, Department>();
        template.setConnectionFactory(redisConnectionFactory);
        Jackson2JsonRedisSerializer<Department> ser = new Jackson2JsonRedisSerializer<Department>(Department.class);
        template.setDefaultSerializer(ser);
        return template;
    }
```

#### 4. RedisTemplate原理

**4.1 CacheAutoConfiguration**

首先，在application的refresh生成组件的阶段，会对在Application类上的如@Srpingboot和@MapperScan@EnableCaching依据顺序执行，而@EnableCaching的官方注解为

```java
/* In both of the scenarios above, {@code @EnableCaching} and {@code
* <cache:annotation-driven/>} are responsible for registering the necessary Spring
* components that power annotation-driven cache management, such as the
* {@link org.springframework.cache.interceptor.CacheInterceptor CacheInterceptor} and the
* proxy- or AspectJ-based advice that weaves the interceptor into the call stack when
* {@link org.springframework.cache.annotation.Cacheable @Cacheable} methods are invoked.
*/
//即会注册相应的CacheInterceptor组件
```

该组件使得`CacheAutoConfiguration`生效并加入到ioc容器中，该类若是使用@EnableCache则默认情况下会实现

```java
//有了 proxyBeanMethods 属性后，配置类不会被代理了。主要是为了提高性能，如果你的 @Bean 方法之间没有调用关系的话可以把 proxyBeanMethods 设置为 false。否则，方法内部引用的类生产的类和 Spring 容器中类是两个类。
@Configuration(proxyBeanMethods = false)
// 查看是否有CacheManager的Class信息使得下文可以初始化
@ConditionalOnClass(CacheManager.class)
//查看是否有对@Cacheabe等注解的方法的类进行AOP增强的工具类
@ConditionalOnBean(CacheAspectSupport.class)
//查看是否注册了CacheManager在容器中，若是则该自动配置类失效
@ConditionalOnMissingBean(value = CacheManager.class, name = "cacheResolver")

@EnableConfigurationProperties(CacheProperties.class)
@AutoConfigureAfter({ CouchbaseDataAutoConfiguration.class, HazelcastAutoConfiguration.class,
		HibernateJpaAutoConfiguration.class, RedisAutoConfiguration.class })
@Import({ CacheConfigurationImportSelector.class, CacheManagerEntityManagerFactoryDependsOnPostProcessor.class })
public class CacheAutoConfiguration {

	@Bean
	@ConditionalOnMissingBean
    // CacheManagerCustomizers，可用来加入容器以修改cacheManager初始化配置
	public CacheManagerCustomizers cacheManagerCustomizers(ObjectProvider<CacheManagerCustomizer<?>> customizers) {
		return new CacheManagerCustomizers(customizers.orderedStream().collect(Collectors.toList()));
	}

	@Bean
    // CacheManagerValidator，可用来加入容器以修改cacheManager校验配置，在内部类实现
	public CacheManagerValidator cacheAutoConfigurationValidator(CacheProperties cacheProperties,
			ObjectProvider<CacheManager> cacheManager) {
		return new CacheManagerValidator(cacheProperties, cacheManager);
	}

	@ConditionalOnClass(LocalContainerEntityManagerFactoryBean.class)
	@ConditionalOnBean(AbstractEntityManagerFactoryBean.class)
    //后置处理器
	static class CacheManagerEntityManagerFactoryDependsOnPostProcessor
			extends EntityManagerFactoryDependsOnPostProcessor {

		CacheManagerEntityManagerFactoryDependsOnPostProcessor() {
			super("cacheManager");
		}

	}

	/**
	 * Bean used to validate that a CacheManager exists and provide a more meaningful
	 * exception.
	 */
	static class CacheManagerValidator implements InitializingBean {

		private final CacheProperties cacheProperties;

		private final ObjectProvider<CacheManager> cacheManager;

		CacheManagerValidator(CacheProperties cacheProperties, ObjectProvider<CacheManager> cacheManager) {
			this.cacheProperties = cacheProperties;
			this.cacheManager = cacheManager;
		}

		@Override
		public void afterPropertiesSet() {
			Assert.notNull(this.cacheManager.getIfAvailable(),
					() -> "No cache manager could be auto-configured, check your configuration (caching type is '"
							+ this.cacheProperties.getType() + "')");
		}

	}

	/**
	 * {@link ImportSelector} to add {@link CacheType} configuration classes.
	 */
    //CacheConfigurations中有CacheType该枚举类中的值为key的spring包中存在的XXXCacheConfiguration.class。
    // 将其取出，applicationContext实例化组件时按一定顺序实现（条件成立）
	static class CacheConfigurationImportSelector implements ImportSelector {

		@Override
		public String[] selectImports(AnnotationMetadata importingClassMetadata) {
			CacheType[] types = CacheType.values();
			String[] imports = new String[types.length];
			for (int i = 0; i < types.length; i++) {
                //getConfigurationClass为static方法，可直接调用
				imports[i] = CacheConfigurations.getConfigurationClass(types[i]);
			}
			return imports;
		}

	}
	// 加载RedisAutoConfiguration，见4.2
}
```

**4.2 RedisAutoConfiguration**

若是我们使用的是redisNoSql,则我们从spring-boot-starter-data-redis中导入了spring-data-redis包，使得`RedisAutoConfiguration`生效(redis有在springboot的AutoConfiguration JAr包中有自动配置类，Hazelcast等CacheConfigurations中的需要连接外部服务端的也有，但都需要导入相关依赖jar包中的资源才可以实例化）

Cache类型的AutoConfiguration依据设计有不同的功能，有的会实例化环境类进入ioc容器，而RedisAutoConfiguration其作用是配置Redis的客户端操作类`RedisTemplate`。

```java
@Configuration(proxyBeanMethods = false)
//只有实现了RedisOperations才能使得RedisAutoConfiguration实例化，而在spring-data-redis中才有，需要用starter导入，所以没加入默认不开启。
@ConditionalOnClass(RedisOperations.class)
//实例化Redis配置类的引用
@EnableConfigurationProperties(RedisProperties.class)
@Import({ LettuceConnectionConfiguration.class, JedisConnectionConfiguration.class })
public class RedisAutoConfiguration {
    ···
}
```

> Lettuce 和 Jedis 都是Redis的client，所以他们都可以连接 Redis Server。 Jedis在实现上是直接连接的Redis Server，如果在多线程环境下是非线程安全的。 每个线程都去拿自己的 Jedis 实例，当连接数量增多时，资源消耗阶梯式增大，连接成本就较高了。
>
> Lettuce的连接是基于Netty的，Netty 是一个多线程、事件驱动的 I/O 框架。连接实例可以在多个线程间共享，当多线程使用同一连接实例时，是线程安全的。 所以，一个多线程的应用可以使用同一个连接实例，而不用担心并发线程的数量。 当然这个也是可伸缩的设计，一个连接实例不够的情况也可以按需增加连接实例。
>
> 通过异步的方式可以让我们更好的利用系统资源，而不用浪费线程等待网络或磁盘I/O。 所以 Lettuce 可以帮助我们充分利用异步的优势。
>
> 使用连接池，为每个Jedis实例增加物理连接Lettuce的连接是基于Netty的，连接实例（StatefulRedisConnection）可以在多个线程间并发访问，应为StatefulRedisConnection是线程安全的，所以一个连接实例（StatefulRedisConnection）就可以满足多线程环境下的并发访问，当然这个也是可伸缩的设计，一个连接实例不够的情况也可以按需增加连接实例。

`RedisAutoConfiguration`向容器中导入了两个类 `RedisTemplate`和`StringRedisTemplate`，作为Redis客户端分别操作k-v都为对象和k-v都为字符串的值。

factory中会创建且保存LettuceConnection

Conenction的创建会绑定一个Provider

Provider会绑定一个Client，Client才是对RedisServer的连接者

`RedisTemplate`：主要是直接面对Redis数据库Server的CRUD操作，可以看成一个JAVA版本的客户端

* 采用Lettuce或者Jedis提供的对Redis数据库的connection，使得在java层面对connection进行增删查改即可对Redis数据库生效，只需面向connection进行编程配置实现想要的功能，并将需要得参数传入connection即可。

```java
@Configuration(proxyBeanMethods = false)
//判断是否有引入org.springframework.data.redis.core
@ConditionalOnClass(RedisOperations.class)
@EnableConfigurationProperties(RedisProperties.class)
// 创建Lettuce（默认）类型的redisConnectionFactory（其中有对于Redis的pool以及pool管理，以及对RedisClient的创建，起作用如同JDbcConnectionFactory中线程池的思想，不过这变得 ），见4.3
@Import({ LettuceConnectionConfiguration.class, JedisConnectionConfiguration.class })
public class RedisAutoConfiguration {

   @Bean
   @ConditionalOnMissingBean(name = "redisTemplate")
   public RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory)
         throws UnknownHostException {
       //只实现对默认RedisTemplate的实例化，见4.4
      RedisTemplate<Object, Object> template = new RedisTemplate<>();
       //传入由LettuceConnectionConfiguration生成的redisConnectionFactory
       //调用其相应得方法会自动从redisConnectionFactory中获取连接，只需往其中传入参数即可
      template.setConnectionFactory(redisConnectionFactory);
      return template;
   }

   @Bean
   @ConditionalOnMissingBean
    
   // 这种xxxRedisTemplate其本质上仍是一个redisTemplate，只是配置得不同，最常见得便是序列化与反序列化的采用的json转换器的不同配置。
   public StringRedisTemplate stringRedisTemplate(RedisConnectionFactory redisConnectionFactory)
         throws UnknownHostException {
      StringRedisTemplate template = new StringRedisTemplate();
      template.setConnectionFactory(redisConnectionFactory);
      return template;
   }

}
```

**4.3 LettuceConnectionFactory**

* Conenctionfactory作为template的参数保存在Template中，但不能直接使用，需要借助RedisConnectionUtils在有action到达时进行afterPropertiesSet的配置生成Client和connection、ConnectionProvider，
* 将Client绑定到ConnectionProvider上，在将ConnectionProvider绑定到connction上，使得Template及工具类可以面向Connection进行操作
* Client通过DefaultConnectionFuture进行Redis连接

```java
public class LettuceConnectionFactory
      implements InitializingBean, DisposableBean, RedisConnectionFactory, ReactiveRedisConnectionFactory {

   private static final ExceptionTranslationStrategy EXCEPTION_TRANSLATION = new PassThroughExceptionTranslationStrategy(
         LettuceConverters.exceptionConverter());

   private final Log log = LogFactory.getLog(getClass());
   private final LettuceClientConfiguration clientConfiguration;
	
    // 如同一个客户端对Redis的操作
   private @Nullable AbstractRedisClient client;
    // 对于客户端以何种方式执行client的工具类
   private @Nullable LettuceConnectionProvider connectionProvider;
   private @Nullable LettuceConnectionProvider reactiveConnectionProvider;
   private boolean validateConnection = false;
   private boolean shareNativeConnection = true;
   private boolean eagerInitialization = false;
   private @Nullable SharedConnection<byte[]> connection;
   private @Nullable SharedConnection<ByteBuffer> reactiveConnection;
   private @Nullable LettucePool pool;
   /** Synchronization monitor for the shared Connection */
   private final Object connectionMonitor = new Object();
   private boolean convertPipelineAndTxResults = true;

   private RedisStandaloneConfiguration standaloneConfig = new RedisStandaloneConfiguration("localhost", 6379);
   private PipeliningFlushPolicy pipeliningFlushPolicy = PipeliningFlushPolicy.flushEachCommand();

   private @Nullable RedisConfiguration configuration;

   private @Nullable ClusterCommandExecutor clusterCommandExecutor;
    
    // 默认用MutableLettuceClientConfiguration进行工厂客户端配置
    public LettuceConnectionFactory() {
		this(new MutableLettuceClientConfiguration());
	}
    
    //standaloneConfig 为Redis的默认端口的配置类
    //clientConfig为Client配置类
    private LettuceConnectionFactory(LettuceClientConfiguration clientConfig) {

		Assert.notNull(clientConfig, "LettuceClientConfiguration must not be null!");
		// 获取配置
		this.clientConfiguration = clientConfig;
		this.configuration = this.standaloneConfig;
	}
    public void afterPropertiesSet() {
		
        // 新建客户端，为RedisClient的扩展类或RedisClusterClient，Client通过DefaultConnectionFuture进行Redis连接
		this.client = createClient();
		
        // 新建connectionProvider，默认为StandaloneConnectionProvider，主要功能为设置传入的Client以哪种形式对Redis进行连接
		this.connectionProvider，默认为StandaloneConnectionProvider = new ExceptionTranslatingConnectionProvider(createConnectionProvider(client, CODEC));
		this.reactiveConnectionProvider = new ExceptionTranslatingConnectionProvider(
				createConnectionProvider(client, LettuceReactiveRedisConnection.CODEC));

		if (isClusterAware()) {

			this.clusterCommandExecutor = new ClusterCommandExecutor(
					new LettuceClusterTopologyProvider((RedisClusterClient) client),
					new LettuceClusterConnection.LettuceClusterNodeResourceProvider(this.connectionProvider),
					EXCEPTION_TRANSLATION);
		}
		
        // 若是获取本地连接或者需要此时便初始化连接则初始化
		if (getEagerInitialization() && getShareNativeConnection()) {
			initConnection();
		}
	}
```

**4.4 RedisTemplate**

* RedisTemplate由两个较为重要的execute方法，其他的方法也是将参数处理后用这两个方法运行

```java
@Nullable
public <T> T execute(RedisCallback<T> action, boolean exposeConnection, boolean pipeline) {

    //查看template是否初始化了，一般RedisTemplate不会直接使用，其会在StringRedisTemplate等的构造函数中调用afterPropertiesSet()方法初始化后initialized=true后才可以调用其中的execute方法
   Assert.isTrue(initialized, "template not initialized; call afterPropertiesSet() before using it");
   // 是否有需要执行的动作
   Assert.notNull(action, "Callback object must not be null");
	
   //获取连接工厂
   RedisConnectionFactory factory = getRequiredConnectionFactory();
   RedisConnection conn = null;
   try {

      if (enableTransactionSupport) {
          // 是否开启了事务支持，若是则获取事务管理器中持有的连接
         // only bind resources in case of potential transaction synchronization
         conn = RedisConnectionUtils.bindConnection(factory, enableTransactionSupport);
      } else {
         // 从工厂中获取一个redis连接
         conn = RedisConnectionUtils.getConnection(factory);
      }
		
       // 查看事务管理器是否持有该工厂的连接
      boolean existingConnection = TransactionSynchronizationManager.hasResource(factory);
		
       // 若是有则用事务连接中的
      RedisConnection connToUse = preProcessConnection(conn, existingConnection);
		
       // 是否支持管道操作
      boolean pipelineStatus = connToUse.isPipelined();
      if (pipeline && !pipelineStatus) {
          // 若是则开启
         connToUse.openPipeline();
      }
		
       // 若工厂的连接池中有连接并获取到或者事务管理器中有保存连接，则获取，否则动态代理创建一个代理类
      RedisConnection connToExpose = (exposeConnection ? connToUse : createRedisConnectionProxy(connToUse));
       // 通过获取的连接执行action到Redis中
       //其后会用connection中的provider，再调用Client执行action
      T result = action.doInRedis(connToExpose);

      // close pipeline
      if (pipeline && !pipelineStatus) {
         connToUse.closePipeline();
      }

      // TODO: any other connection processing?
      return postProcessResult(result, connToUse, existingConnection);
   } finally {
       // 断开连接返回连接池
      RedisConnectionUtils.releaseConnection(conn, factory, enableTransactionSupport);
   }
}
public void afterPropertiesSet() {

		super.afterPropertiesSet();

		boolean defaultUsed = false;
		//默认使用JDK的序列化器
		if (defaultSerializer == null) {

			defaultSerializer = new JdkSerializationRedisSerializer(
					classLoader != null ? classLoader : this.getClass().getClassLoader());
		}

    	// 当key，value的序列化器为空时，给他们设置默认的序列化器
		if (enableDefaultSerializer) {
			
			if (keySerializer == null) {
				keySerializer = defaultSerializer;
				defaultUsed = true;
			}
			if (valueSerializer == null) {
				valueSerializer = defaultSerializer;
				defaultUsed = true;
			}
			if (hashKeySerializer == null) {
				hashKeySerializer = defaultSerializer;
				defaultUsed = true;
			}
			if (hashValueSerializer == null) {
				hashValueSerializer = defaultSerializer;
				defaultUsed = true;
			}
		}

		if (enableDefaultSerializer && defaultUsed) {
			Assert.notNull(defaultSerializer, "default serializer null and not all serializers initialized");
		}

		if (scriptExecutor == null) {
			this.scriptExecutor = new DefaultScriptExecutor<>(this);
		}

		initialized = true;
	}
```

#### 4. 自定义RedisCacheManager

RedisCacheManager在@Cacheable等注解时生效，也可独立使用，我们直接对Redis操作一般用template，但缓存的获取等我们一般让其自动化完成，所以RedisCacheManager的重要性才会上升，和Template分离

在导入redis依赖后RedisCacheConfiguration类就会自动生效，创建RedisCacheManager，并使用RedisCache进行缓存数据，要缓存的对象的类要实现Serializable接口，默认情况下是以**jdk序列化数据**存在redis中，如下：

```json
k:"emp::1"
v:
\xAC\xED\x00\x05sr\x00$cn.edu.ustc.springboot.bean.Employeeuqf\x03p\x9A\xCF\xE0\x02\x00\x05L\x00\x03dIdt\x00\x13Ljava/lang/Integer;L\x00\x05emailt\x00\x12Ljava/lang/String;L\x00\x06genderq\x00~\x00\x01L\x00\x02idq\x00~\x00\x01L\x00\x08lastNameq\x00~\x00\x02xpsr\x00\x11java.lang.Integer\x12\xE2\xA0\xA4\xF7\x81\x878\x02\x00\x01I\x00\x05valuexr\x00\x10java.lang.Number\x86\xAC\x95\x1D\x0B\x94\xE0\x8B\x02\x00\x00xp\x00\x00\x00\x03t\x00\x07cch@aaasq\x00~\x00\x04\x00\x00\x00\x01q\x00~\x00\x08t\x00\x03cch
```

要想让对象以**json形式**存储在redis中，需要自定义RedisCacheManager，使用GenericJackson2JsonRedisSerializer类对value进行序列化。2.0版本后默认创建

```java
@Configuration
public class MyRedisConfig {
    @Bean
    RedisCacheManager cacheManager(RedisConnectionFactory factory){
        //创建默认RedisCacheWriter
        RedisCacheWriter cacheWriter = RedisCacheWriter.nonLockingRedisCacheWriter(factory);
        
        //创建默认RedisCacheConfiguration并使用GenericJackson2JsonRedisSerializer构造的		SerializationPair对value进行转换
        //创建GenericJackson2JsonRedisSerializer的json序列化器
        GenericJackson2JsonRedisSerializer jsonRedisSerializer = new GenericJackson2JsonRedisSerializer();
        //使用json序列化器构造出对转换Object类型的SerializationPair序列化对
        RedisSerializationContext.SerializationPair<Object> serializationPair = RedisSerializationContext.SerializationPair.fromSerializer(jsonRedisSerializer);
        //将可以把Object转换为json的SerializationPair传入RedisCacheConfiguration
        //使得RedisCacheConfiguration在转换value时使用定制序列化器
        RedisCacheConfiguration cacheConfiguration=RedisCacheConfiguration.defaultCacheConfig().serializeValuesWith(serializationPair);
        
        RedisCacheManager cacheManager = new RedisCacheManager(cacheWriter,cacheConfiguration);
        return cacheManager;
    }
}
```

序列化数据如下：

```json
k:"emp::3"

v:
{
  "@class": "cn.edu.ustc.springboot.bean.Employee",
  "id": 3,
  "lastName": "aaa",
  "email": "aaaa",
  "gender": 1,
  "dId": 5
}
```

**注意**，这里必须用**GenericJackson2JsonRedisSerializer**进行value的序列化解析，如果使用Jackson2JsonRedisSerializer，序列化的json没有 `"@class": "cn.edu.ustc.springboot.bean.Employee"`,在读取缓存时会报类型转换异常。

#### 5. RedisCacheManager原理

我们用AOP动态增强我们的service类，使得对`@Cacheable`等的方法进行判断存储，调用时`RedisCacheManager`会绑定到`CacheAspectSupport`，==CacheAspectSupport==中的方法会到对应`RedisCacheManager`的对应的cache中去查找

2.0版本以前，RedisCacheManager通过RedisTemplate前往redis进行CRUD操作，但在2.0版本后面，则出于解耦的考虑，将他们解耦开来。不然所有的配置都需要单独配置相应的Template来实现，使得每个template的复用情况下降

\==注意RedisCacheManager是Spring层面的管理类和RidisServer本身实现无关==

**5.1 RedisCacheConfiguration**

```java
@Configuration(proxyBeanMethods = false)
//仍需要RedisConnectionFactory，而他也在starter导入的jar包中，因此没添加依赖也默认不会加载
@ConditionalOnClass(RedisConnectionFactory.class)
// 为了防止别的同级别的RedisCacheConfiguration加载他们的RedisCacheManager，因此在RedisAutoConfiguration便加载，2.0后不需要RedisTemplate也可以实现注解的缓存，但要自己将工厂添加到容器中
@AutoConfigureAfter(RedisAutoConfiguration.class)
@ConditionalOnBean(RedisConnectionFactory.class)
@ConditionalOnMissingBean(CacheManager.class)
@Conditional(CacheCondition.class)
class RedisCacheConfiguration {
    
    //向容器中导入RedisCacheManager
	@Bean
    //cacheManager不同于以前，自己导入RedisConnectionFactory，能够自己获取connection进行操作
	RedisCacheManager cacheManager(CacheProperties cacheProperties, CacheManagerCustomizers cacheManagerCustomizers,
			ObjectProvider<org.springframework.data.redis.cache.RedisCacheConfiguration> redisCacheConfiguration,
			ObjectProvider<RedisCacheManagerBuilderCustomizer> redisCacheManagerBuilderCustomizers,
			RedisConnectionFactory redisConnectionFactory, ResourceLoader resourceLoader) {
		//使用determineConfiguration()的返回值生成RedisCacheManagerBuilder，用DefaultRedisCacheWriter完成I/O操作，见4.2
        //RedisCacheManager.builder将redisConnectionFactory放入DefaultRedisCacheWriter中，见4.3
        //调用了RedisCacheManagerBuilder的cacheDefaults()方法返回以determineConfiguration生成的redisCacheConfiguration
        //determineConfiguration为本类的方法，见下面
        RedisCacheManagerBuilder builder = RedisCacheManager.builder(redisConnectionFactory).cacheDefaults(
				determineConfiguration(cacheProperties, redisCacheConfiguration, resourceLoader.getClassLoader()));
		List<String> cacheNames = cacheProperties.getCacheNames();
		if (!cacheNames.isEmpty()) {
			builder.initialCacheNames(new LinkedHashSet<>(cacheNames));
		}
		redisCacheManagerBuilderCustomizers.orderedStream().forEach((customizer) -> customizer.customize(builder));
        //使用RedisCacheManagerBuilder的build()方法创建RedisCacheManager并进行定制操作
		return cacheManagerCustomizers.customize(builder.build());
	}

    
    //determineConfiguration，生成RedisCacheManagerBuilder用到的参数/
    //注意此时的类是org.springframework.data.redis.cache.RedisCacheConfiguration，为导入的Redis的相关Jar包中的RedisCache配置类，用以配置cache初始化信息，因为与本类的方法同名，所以用全类名。
    //之所以该类要用这个名字，是因为其他的生成RedisCacheManager的命名规范如此，诈胡
	private org.springframework.data.redis.cache.RedisCacheConfiguration determineConfiguration(
			CacheProperties cacheProperties,
			ObjectProvider<org.springframework.data.redis.cache.RedisCacheConfiguration> redisCacheConfiguration,
			ClassLoader classLoader) {
        //determineConfiguration()调用了createConfiguration()，也在该类中
		return redisCacheConfiguration.getIfAvailable(() -> createConfiguration(cacheProperties, classLoader));
	}

    
    //createConfiguration()定义了其序列化value的规则，这个方法的作用与RedisTemplate中的afterPropertiesSet方法一样
    //RedisCacheConfiguration见4.4
	private org.springframework.data.redis.cache.RedisCacheConfiguration createConfiguration(
			CacheProperties cacheProperties, ClassLoader classLoader) {
		Redis redisProperties = cacheProperties.getRedis();
		org.springframework.data.redis.cache.RedisCacheConfiguration config = org.springframework.data.redis.cache.RedisCacheConfiguration
				.defaultCacheConfig();
        //使用jdk序列化器对value进行序列化
		config = config.serializeValuesWith(
				SerializationPair.fromSerializer(new JdkSerializationRedisSerializer(classLoader)));
        //设置properties文件中设置的各项属性
		if (redisProperties.getTimeToLive() != null) {
			config = config.entryTtl(redisProperties.getTimeToLive());
		}
        //获取key时，是否加上前缀，一般前缀为Redis的一个命名空间
		if (redisProperties.getKeyPrefix() != null) {
			config = config.prefixKeysWith(redisProperties.getKeyPrefix());
		}      
        //是否运行cache中的value为空
		if (!redisProperties.isCacheNullValues()) {
			config = config.disableCachingNullValues();
		}
        //使用key时，是否加上前缀，一般前缀为Redis的一个命名空间
		if (!redisProperties.isUseKeyPrefix()) {
			config = config.disableKeyPrefix();
		}
		return config;
	}

}
```

**5.2 RedisCacheManager**

RedisCacheManager的直接构造类，该类保存了配置类RedisCacheConfiguration，该配置在会传递给RedisCacheManager

```java
public static class RedisCacheManagerBuilder {

		private final RedisCacheWriter cacheWriter;
    	//默认缓存配置使用RedisCacheConfiguration的默认配置
    	//该默认配置缓存时默认将k按字符串存储，v按jdk序列化数据存储(见下一代码块)
		private RedisCacheConfiguration defaultCacheConfiguration = RedisCacheConfiguration.defaultCacheConfig();
		private final Map<String, RedisCacheConfiguration> initialCaches = new LinkedHashMap<>();
		private boolean enableTransactions;
		boolean allowInFlightCacheCreation = true;

		private RedisCacheManagerBuilder(RedisCacheWriter cacheWriter) {
			this.cacheWriter = cacheWriter;
		}
    
    //将connectionFactory放入DefaultRedisCacheWriter中，对redis的操作尤其接手，见4.3
    public static RedisCacheManagerBuilder builder(RedisConnectionFactory connectionFactory) {

		Assert.notNull(connectionFactory, "ConnectionFactory must not be null!");
	
		return RedisCacheManagerBuilder.fromConnectionFactory(connectionFactory);
	}


		
    	//传入RedisCacheManagerBuilder使用的缓存配置规则RedisCacheConfiguration类
		public RedisCacheManagerBuilder cacheDefaults(RedisCacheConfiguration defaultCacheConfiguration) {

			Assert.notNull(defaultCacheConfiguration, "DefaultCacheConfiguration must not be null!");

			this.defaultCacheConfiguration = defaultCacheConfiguration;

			return this;
		}
    
    
    //使用默认defaultCacheConfiguration创建RedisCacheManager
    public RedisCacheManager build() {

		RedisCacheManager cm = new RedisCacheManager(cacheWriter, defaultCacheConfiguration, initialCaches,allowInFlightCacheCreation);
        cm.setTransactionAware(enableTransactions);

			return cm;
		}
```

**5.3 DefaultRedisCacheWriter**

* 其功能上有些像弱化版本的RedisTemplate

```java
/*{@link RedisCacheWriter} implementation capable of reading/writing binary data from/to Redis in {@literal standalone}
 * and {@literal cluster} environments. Works upon a given {@link RedisConnectionFactory} to obtain the actual*/
class DefaultRedisCacheWriter implements RedisCacheWriter {

@Override
public void put(String name, byte[] key, byte[] value, @Nullable Duration ttl) {

   Assert.notNull(name, "Name must not be null!");
   Assert.notNull(key, "Key must not be null!");
   Assert.notNull(value, "Value must not be null!");

   execute(name, connection -> {

      if (shouldExpireWithin(ttl)) {
         connection.set(key, value, Expiration.from(ttl.toMillis(), TimeUnit.MILLISECONDS), SetOption.upsert());
      } else {
         connection.set(key, value);
      }

      return "OK";
   });
}
    //获取连接并执行操作
    private <T> T execute(String name, Function<RedisConnection, T> callback) {

		RedisConnection connection = connectionFactory.getConnection();
		try {

			checkAndPotentiallyWaitUntilUnlocked(name, connection);
			return callback.apply(connection);
		} finally {
			connection.close();
		}
	}
}
```

**5.4 RedisCacheConfiguration**

RedisCacheConfiguration（导入的jar包中）保存了许多缓存规则，这些规则都保存在RedisCacheManagerBuilder的RedisCacheConfiguration defaultCacheConfiguration属性中，并且当RedisCacheManagerBuilder创建RedisCacheManager传递过去

```java
public class RedisCacheConfiguration {

	private final Duration ttl;
	private final boolean cacheNullValues;
	private final CacheKeyPrefix keyPrefix;
	private final boolean usePrefix;

	private final SerializationPair<String> keySerializationPair;
	private final SerializationPair<Object> valueSerializationPair;

	private final ConversionService conversionService;
    
    //默认缓存配置
    public static RedisCacheConfiguration defaultCacheConfig(@Nullable ClassLoader classLoader) {

            DefaultFormattingConversionService conversionService = new DefaultFormattingConversionService();

            registerDefaultConverters(conversionService);
				
            return new RedisCacheConfiguration(Duration.ZERO, true, true, CacheKeyPrefix.simple(),
                                     SerializationPair.fromSerializer(RedisSerializer.string()),
                                               //key使用字符串
                                               SerializationPair.fromSerializer(RedisSerializer.java(classLoader)), conversionService);
        //value按jdk序列化存储
    }
```

**5.5 RedisCacheManager创建cache**

RedisCacheManager在创建RedisCache时将RedisCacheConfiguration传递过去，并在创建RedisCache时通过createRedisCache()起作用

```java
public class RedisCacheManager extends AbstractTransactionSupportingCacheManager {

	private final RedisCacheWriter cacheWriter;
	private final RedisCacheConfiguration defaultCacheConfig;
	private final Map<String, RedisCacheConfiguration> initialCacheConfiguration;
	private final boolean allowInFlightCacheCreation;
    
    	protected RedisCache createRedisCache(String name, @Nullable RedisCacheConfiguration cacheConfig) {
        // 每一个cache的创建都加入了RedisCacheManager创建时放入了RedisFactory的cacheWriter
        //如果调用该方法时RedisCacheConfiguration有值则使用定制的，否则则使用默认的RedisCacheConfiguration defaultCacheConfig，即RedisCacheManagerBuilder传递过来的配置，见4.6
		return new RedisCache(name, cacheWriter, cacheConfig != null ? cacheConfig : defaultCacheConfig);
	}
```

**5.6 RedisCache**

每一个RedisCache的操作

RedisCache，Redis缓存，具体负责将缓存数据序列化的地方，将RedisCacheConfiguration的序列化对SerializationPair提取出来并使用其定义的序列化方式分别对k和v进行序列化操作

```java
public class RedisCache extends AbstractValueAdaptingCache {
    
    private static final byte[] BINARY_NULL_VALUE = RedisSerializer.java().serialize(NullValue.INSTANCE);

	private final String name;
	private final RedisCacheWriter cacheWriter;
	private final RedisCacheConfiguration cacheConfig;
	private final ConversionService conversionService;
    
    public void put(Object key, @Nullable Object value) {

		Object cacheValue = preProcessCacheValue(value);

		if (!isAllowNullValues() && cacheValue == null) {

			throw new IllegalArgumentException(String.format(
					"Cache '%s' does not allow 'null' values. Avoid storing null via '@Cacheable(unless=\"#result == null\")' or configure RedisCache to allow 'null' via RedisCacheConfiguration.",
					name));
		}

        //在put k-v时使用cacheConfig中的k-v序列化器分别对k-v进行序列化
        //均是用cacheWriter获取连接再用RedisClient和RedisProvide等进行操作，与RedisTemplate相似
		cacheWriter.put(name, createAndConvertCacheKey(key), serializeCacheValue(cacheValue), cacheConfig.getTtl());
	}
    
    //从cacheConfig(即RedisCacheConfiguration)中获取KeySerializationPair并写入key值
    protected byte[] serializeCacheKey(String cacheKey) {
		return ByteUtils.getBytes(cacheConfig.getKeySerializationPair().write(cacheKey));
	}
    
    
    //从cacheConfig(即RedisCacheConfiguration)中获取ValueSerializationPair并写入key值
    protected byte[] serializeCacheValue(Object value) {

        if (isAllowNullValues() && value instanceof NullValue) {
            return BINARY_NULL_VALUE;
        }

        return ByteUtils.getBytes(cacheConfig.getValueSerializationPair().write(value));
    }
```

分析到这也就不难理解，要使用json保存序列化数据时，需要自定义RedisCacheManager，在RedisCacheConfiguration中定义序列化转化规则，并向RedisCacheManager传入我们自己定制的RedisCacheConfiguration了，我定制的序列化规则会跟随RedisCacheConfiguration一直传递到RedisCache，并在序列化时发挥作用。

## (二) Spring Boot与消息

### 一、消息简介

当过多的用户并发访问时，出于服务器的负载上限考虑，我们需要使得发送的和接收异步处理，才能降低服务器的压力。

因此我们可通过消息服务中间件来提升系统异步通信、扩展解耦能力，当消息发送者发送消息以后，将由消息代理接管，消息代理保证消息传递到指定目的地。

消息服务中两个重要概念：

**消息代理（message broker）和目的地（destination）**

消息队列主要有两种形式的目的地

1. **队列（queue）：点对点消息通信（point-to-point）**
2. **主题（topic）：发布（publish）/订阅（subscribe）消息通信**

**应用场景**

1. 异步处理

用户注册操作和消息处理并行，提高响应速度

![image-20200824100440722](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpringBoot%E9%AB%98%E7%BA%A7%E7%AF%87.assets%5Cimage-20200824100440722.png)

1. 应用解耦

在下单时库存系统不能正常使用。也不影响正常下单，因为下单后，订单系统写入消息队列就不再关心其他的后续操作了。实现订单系统与库存系统的应用解耦

![image-20200824100712271](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpringBoot%E9%AB%98%E7%BA%A7%E7%AF%87.assets%5Cimage-20200824100712271.png)

1. 流量削峰

用户的请求，服务器接收后，首先写入消息队列。假如消息队列长度超过最大数量，则直接抛弃用户请求或跳转到错误页面

秒杀业务根据消息队列中的请求信息，再做后续处理

![image-20200824100728024](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpringBoot%E9%AB%98%E7%BA%A7%E7%AF%87.assets%5Cimage-20200824100728024.png)

**点对点式：**

* 消息发送者发送消息，消息代理将其放入一个队列中，消息接收者从队列中获取消息内容，消息读取后被移出队列
* 消息**只有唯一的发送者和接受者**，但**并不是说只能有一个接收者**

**发布订阅式：**（观察者模式）

* 发送者（发布者）发送消息到主题，多个接收者（订阅者）监听（订阅）这个主题，那么就会在消息到达时同时收到消息

**消息代理规范**

* JMS（Java Message Service）JAVA消息服务
  * 基于JVM消息代理的规范。ActiveMQ、HornetMQ是JMS实现
* AMQP（Advanced Message Queuing Protocol）
  * 高级消息队列协议，也是一个消息代理的规范，兼容JMS
  * RabbitMQ是AMQP的实现

![image-20200824101834448](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpringBoot%E9%AB%98%E7%BA%A7%E7%AF%87.assets%5Cimage-20200824101834448.png)

\==Spring支持==

**spring-jms提供了对JMS的支持**

**spring-rabbit提供了对AMQP的支持**

**需要ConnectionFactory的实现来连接消息代理**

**提供JmsTemplate、RabbitTemplate来发送消息**

**@JmsListener（JMS）、@RabbitListener（AMQP）注解在方法上监听消息代理发布的消息**

**@EnableJms、@EnableRabbit开启支持**

\==Spring Boot自动配置==

**JmsAutoConfiguration**

**RabbitAutoConfiguration**

### 二、RabbitMQ

RabbitMQ是一个由erlang开发的AMQP(Advanved Message Queue Protocol)的开源实现。

#### 1. 核心概念

* **Message**
  * 消息，消息是不具名的，它由消息头和消息体组成
  * 消息头，包括**routing-key（路由键）**、priority（相对于其他消息的优先权）、delivery-mode（指出该消息可能需要持久性存储）等
* Publisher
  * 消息的生产者，也是一个向交换器发布消息的客户端应用程序
* **Exchange**
  * 交换器，将生产者消息路由给服务器中的队列
  * 类型有direct(默认)，fanout, topic, 和headers，具有不同转发策略
  * Exchange**也有routing-key（可以说是Bindingkey）**，**每个binding的queue都有自己的routing-key（可以说是Bindingkey）**
  * **Message会先指定前往哪个Exchange再将自己的routing-key依据不同类型的Exchange的不同匹配策略，与Exchange的routing-key匹配，exchange再将Message转发到key匹配的queeu中**
* **Queue**（Destination）
  * 消息队列，保存消息直到发送给消费者
  * queue只有name，绑定在Exchange上，并无routing-key
* **Binding**
  * 绑定，用于消息队列和交换器之间的关联
* Connection
  * 网络连接，比如一个TCP连接
* Consumer
  * 消息的消费者，表示一个从消息队列中取得消息的客户端应用程序
* Virtual Host
  * 虚拟主机，表示一批交换器、消息队列和相关对象。
  * vhost 是 AMQP 概念的基础，必须在连接时指定
  * RabbitMQ 默认的 vhost 是 /
* Broker
  * 消息队列服务器实体

![image-20200824104053579](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpringBoot%E9%AB%98%E7%BA%A7%E7%AF%87.assets%5Cimage-20200824104053579.png)

#### 2. 运行机制

**消息路由**

AMQP 中的消息路由

AMQP 中消息的路由过程和 Java 开发者熟悉的 JMS 存在一些差别，AMQP 中增加了**Exchange** 和 **Binding** 的角色。生产者把消息发布到 Exchange 上，消息最终到达队列并被消费者接收，而 Binding 决定交换器的消息应该发送到那个队列。

![image-20200824104351148](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpringBoot%E9%AB%98%E7%BA%A7%E7%AF%87.assets%5Cimage-20200824104351148.png)

**Exchange 类型**

1.  direct

    点对点模式，消息中的路由键（routing key）如果和 Binding 中的 binding key 一致， 交换器就将消息发到对应的队列中。。路由键与队列名**完全匹配**，如果一个队列绑定到交换机要求路由键为“dog”，则只转发 routing key 标记为“dog”的消息，不会转发“dog.puppy”，也不会转发“dog.guard”等等。它是完全匹配、单播的模式
2.  fanout

    广播模式，每个发到 fanout 类型交换器的消息都会**分到所有绑定的队列上去，fanout 类型转发消息是最快的**
3.  topic

    将路由键和某个模式进行匹配，此时队列需要绑定到一个模式上。它将路由键和绑定键的字符串切分成单词，这些单词之间用点隔开。 识别通配符： # 匹配 0 个或多个单词， \*匹配一个单词

### 三、 Springboot中的RabbitMQ

#### 1. 环境准备

在docker中安装rabbitmq并运行

```shell
 docker pull rabbitmq:3.8-management-alpine #apline 为一种Linux的轻量型发行版本，只拿了有关功能的核心部分，提升效率和压缩体积

# 5672为服务端口，15672为web控制台端口
docker run -d -p 5672:5672 -p 15672:15672 38e57f281891
```

如果要修改账号密码

```shell
1、进入docker 的 RabbitMQ 容器中
docker exec -it rabbitmq01 bash

2、查看用户
rabbitmqctl list_users

3、修改密码
rabbitmqctl change_password userName newPassword

4、如果不想要guest的账号也可以新增账号
 rabbitmqctl add_user userName newPassword
 
5、看guest不爽，你还可以delete它
rabbitmqctl delete_user guest

6、最后别忘了给自己添加的账号增加超级管理员权限
rabbitmqctl set_user_tags userName administrator

7、也可以在运行容器时设定
$ docker run -d --hostname my-rabbit --name some-rabbit -e RABBITMQ_DEFAULT_USER=user -e RABBITMQ_DEFAULT_PASS=password rabbitmq:3-management
```

导入依赖

```
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-amqp</artifactId>
    </dependency>
	<!--自定义消息转化器Jackson2JsonMessageConverter所需依赖-->
    <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-databind</artifactId>
    </dependency>
```

配置文件

```
# 指定rebbitmq服务器主机
spring.rabbitmq.host=192.168.31.162
#spring.rabbitmq.username=guest  默认值为guest
#spring.rabbitmq.password=guest	 默认值为guest
```

#### 2. RabbitMQ的使用

RabbitAutoConfiguration中有内部类RabbitTemplateConfiguration,在该类中向容器中分别导入了**RabbitTemplate**和**AmqpAdmin**

在测试类中分别注入

```java
	@Autowired
    private RabbitTemplate rabbitTemplate;

    @Autowired
    private AmqpAdmin amqpAdmin;
```

*   **RabbitTemplate消息发送处理组件**

    可用来发送和接收消息

```java
		//发送消息
		rabbitTemplate.convertAndSend("amq.direct","ustc","aaaa");
        Book book = new Book();
        book.setName("西游记");
        book.setPrice(23.2f);
		//Book要实现Serializable接口
        rabbitTemplate.convertAndSend("amq.direct","ustc",book);

		//接收消息
		Object o = rabbitTemplate.receiveAndConvert("ustc");
        System.out.println(o.getClass());  //class cn.edu.ustc.springboot.bean.Book
        System.out.println(o);			//Book{name='西游记', price=23.2}
```

默认的消息转化器是SimpleMessageConverter，对于对象以jdk序列化方式存储，若要以Json方式存储对象，就要自定义消息转换器

```java
@Configuration
public class AmqpConfig {
    @Bean
    public MessageConverter messageConverter() {
        //在容器中导入Json的消息转换器
        return new Jackson2JsonMessageConverter();
    }
}
```

*   **AmqpAdmin管理组件**

    可用于创建和删除exchange、binding和queue

```java
//创建Direct类型的Exchange
amqpAdmin.declareExchange(new DirectExchange("admin.direct"));
//创建Queue
amqpAdmin.declareQueue(new Queue("admin.test"));
//将创建的队列与Exchange绑定
amqpAdmin.declareBinding(new Binding("admin.test", Binding.DestinationType.QUEUE,"admin.direct","admin.test",null));
//在网页后台管理中，可以将Exchange绑定到Exchange，所以，需要指定DestinationType
```

**消息的监听**

在回调方法上标注@RabbitListener注解，并设置其属性queues，注册监听队列，当该队列收到消息时，标注方法遍会调用

可分别使用Message和保存消息所属对象进行消息接收，若使用Object对象进行消息接收，实际上接收到的也是Message

```java
@Service
public class BookService {
    @RabbitListener(queues = {"admin.test"})
    public void receive1(Book book){
        System.out.println("收到消息："+book);
    }

    @RabbitListener(queues = {"admin.test"})
    public void receive1(Object object){
        System.out.println("收到消息："+object.getClass());
        //收到消息：class org.springframework.amqp.core.Message
    }
    
    @RabbitListener(queues = {"admin.test"})
    public void receive2(Message message){
        System.out.println("收到消息"+message.getHeaders()+"---"+message.getPayload());
    }
}
```

#### 2. RabbitTemplate原理

Publisher借助rabbitTemplate进行消息添加接收，rabbitTemplate使用connection和channel（若是在事务支持的情况下，为了节约TCP资源，会用RabbitResourceHolder保存connection和其上面的channelList的关系在其中，之后相同的），通过各种方法最后通过execute方法执行，该方法中若是存在重试模板（retryTemplate，失败会进行重试）则用retryTemplate的execute方法进行获取连接最后还是会调用rabbitTemplate的doExecute

而rabbitTemplate中的connection的创建由connectionFactory（会经过ConnectionFactoryUtils，其主要作用是会在有事务支持的时候进行资源获取和绑定）进行创建，而connectionFactory创建的connection是实现了com.rabbitmq.client.Connection（即导入jar中的Connection）接口的AMQConnection（其start方法才是真正连接RabbitServer的）

而我们还会从AMQConnection中创建channenl，每个channel都会绑定绑定唯一一个connection，而每个channel创建时都是由AMQConnection中的ChannelManager管理（channel对connection是多对一的关系）

而ChannelManager只有在connectionFactory（由connection包的connectionFactory调用client包的connectionFactory）创建了AMQConnection（创建后会调用Connection的start方法，会创建初始化ChannelManager，才能往其中创建新的或添加channnel）后才能使用.

template调用invokeAction来实现操作，为传入的channel添加监视器，最后由ChannelCallback.doInRabbit（channel）实现，该接口没有实现类，该接口的实现全由lambda表达式实现，使得能先执行execute方法获得connection和channel后能直接回调实现lambda表达式，使得不用在为相同参数的方法起不同的方法名或者复杂的条件判断实现。简化匿名内部类。

只要访问成功没有异常，最后都会执行doInRabbit，返回rabbitTemplate用获取的connection和channel通过Message通信，message会被解读，最后信息放在AMQCommand中，获取channel中的connection将commnd中的信息提取转换为Stream调用flush方法输出到Buffer中（实现flushable接口的将被动态代理由代理类实现），由其他设备读取传送，并由metricsCollector做资源收集，查看发布情况等

虽然不像Cache有专门的Client类，但是原理相似

## (三) Spring boot与检索

### 一、 ElasticSearch入门

#### 1. ES的简介

**简介**

我们的应用经常需要添加检索功能，开源的 ElasticSearch 是目前全文搜索引擎的首选。他可以快速的存储、搜索和分析海量数据。Spring Boot通过整合Spring Data ElasticSearch为我们提供了非常便捷的检索功能支持； Elasticsearch是一个分布式搜索服务，提供Restful API，底层基于Lucene，采用多shard（分片）的方式保证数据安全，并且提供自动resharding的功能，github等大型的站点也是采用了ElasticSearch作为其搜索服务。

**概念**

员工文档 的形式存储为例：一个**文档**代表一个**员工数据**。存储数据到ElasticSearch 的行为叫做 **索引** ，但在索引一个文档之前，需要确定将文档存储在哪里。

一个 ElasticSearch 集群可以包含多个 **索引** ，相应的每个索引可以包含多个 **类型** 。 这些不同的类型存储着多个 文**档** ，每个文档又有 多个 **属性** 。

> 索引（名词）：
>
> 如前所述，一个 _索引_ 类似于传统关系数据库中的一个 _数据库_ ，是一个存储关系型文档的地方。 _索引_ (_index_) 的复数词为 _indices_ 或 _indexes_ 。
>
> 索引（动词）：
>
> _索引一个文档_ 就是存储一个文档到一个 _索引_ （名词）中以便被检索和查询。这非常类似于 SQL 语句中的 `INSERT` 关键词，除了文档已存在时，新文档会替换旧文档情况之外。

类似关系：

![image-20200825160927339](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpringBoot%E9%AB%98%E7%BA%A7%E7%AF%87.assets%5Cimage-20200825160927339.png)

#### 2. ES的安装与运行

与ES交互

*   9200端口

    RESTful API通过HTTP通信
*   9300端口

    Java客户端与ES的原生传输协议和集群交互

```
# 拉取ES镜像
docker pull elasticsearch:7.6.1
#运行ES
docker run -e "discovery.type=single-node" -e ES_JAVA_OPTS="-Xms256m -Xmx256m" -d -p 9200:9200 -p 9300:9300 --name ES03 41072cdeebc5
```

`ES_JAVA_OPTS`指定java虚拟机相关参数

`-Xms256m` 初始堆内存大小为256m

`-Xmx256m` 最大堆内存大小为256m

`discovery.type=single-node` 设置为单点启动

#### 3. ES的基础入门

https://www.elastic.co/guide/cn/elasticsearch/guide/current/\_indexing\_employee\_documents.html

**案例：创建一个员工目录，并支持各类型检索**

**索引员工文档**

对于员工目录，我们将做如下操作：

* 每个员工索引一个文档，文档包含该员工的所有信息。
* 每个文档都将是 `employee` _类型_ 。
* 该类型位于 _索引_ `megacorp` 内。

```
PUT /megacorp/employee/1
{
    "first_name" : "John",
    "last_name" :  "Smith",
    "age" :        25,
    "about" :      "I love to go rock climbing",
    "interests": [ "sports", "music" ]
}
```

注意，路径 `/megacorp/employee/1` 包含了三部分的信息：

*   **`megacorp`**

    索引名称
*   **`employee`**

    类型名称
*   **`1`**

    特定雇员的ID

请求体 —— JSON 文档 —— 包含了这位员工的所有详细信息

```
{
    "_index": "megacorp",
    "_type": "employee",
    "_id": "1",
    "_version": 1,
    "result": "created",
    "_shards": {
        "total": 2,
        "successful": 1,
        "failed": 0
    },
    "_seq_no": 0,
    "_primary_term": 1
}
```

同理，添加更多员工

```
PUT /megacorp/employee/2
{
    "first_name" :  "Jane",
    "last_name" :   "Smith",
    "age" :         32,
    "about" :       "I like to collect rock albums",
    "interests":  [ "music" ]
}

PUT /megacorp/employee/3
{
    "first_name" :  "Douglas",
    "last_name" :   "Fir",
    "age" :         35,
    "about":        "I like to build cabinets",
    "interests":  [ "forestry" ]
}
```

**检索文档**

HTTP `GET` 请求并指定文档的地址——索引库、类型和ID。

```
GET /megacorp/employee/1
```

返回数据

```
{
    "_index": "megacorp",
    "_type": "employee",
    "_id": "1",
    "_version": 1,
    "_seq_no": 0,
    "_primary_term": 1,
    "found": true,
    "_source": {
        "first_name": "John",
        "last_name": "Smith",
        "age": 25,
        "about": "I love to go rock climbing",
        "interests": [
            "sports",
            "music"
        ]
    }
}
```

将 HTTP 命令由 `PUT` 改为 `GET` 可以用来检索文档，同样的，可以使用 `DELETE` 命令来删除文档，以及使用 `HEAD` 指令来检查文档是否存在。如果想更新已存在的文档，只需再次 `PUT` 。

**轻量搜索**

搜索所有雇员：

```
GET /megacorp/employee/_search
```

返回数据

```
{
    "took": 46,
    "timed_out": false,
    "_shards": {
        "total": 1,
        "successful": 1,
        "skipped": 0,
        "failed": 0
    },
    "hits": {
        "total": {
            "value": 3,
            "relation": "eq"
        },
        "max_score": 1,
        "hits": [
            {
                "_index": "megacorp",
                "_type": "employee",
                "_id": "1",
                "_score": 1,
                "_source": {
                    "first_name": "John",
                    "last_name": "Smith",
                    "age": 25,
                    "about": "I love to go rock climbing",
                    "interests": [
                        "sports",
                        "music"
                    ]
                }
            },
            {
                "_index": "megacorp",
                "_type": "employee",
                "_id": "2",
                "_score": 1,
                "_source": {
                    "first_name": "Jane",
                    "last_name": "Smith",
                    "age": 32,
                    "about": "I like to collect rock albums",
                    "interests": [
                        "music"
                    ]
                }
            },
            {
                "_index": "megacorp",
                "_type": "employee",
                "_id": "3",
                "_score": 1,
                "_source": {
                    "first_name": "Douglas",
                    "last_name": "Fir",
                    "age": 35,
                    "about": "I like to build cabinets",
                    "interests": [
                        "forestry"
                    ]
                }
            }
        ]
    }
}
```

返回结果包括三个文档，放在数据`hits`中。

搜索姓氏为 `Smith` 的雇员

```
GET /megacorp/employee/_search?q=last_name:Smith
```

在请求路径中使用 `_search` 端点，并将查询本身赋值给参数 `q=` 。返回结果给出了所有的 Smith：

```
{
    "took": 23,
    "timed_out": false,
    "_shards": {
        "total": 1,
        "successful": 1,
        "skipped": 0,
        "failed": 0
    },
    "hits": {
        "total": {
            "value": 2,
            "relation": "eq"
        },
        "max_score": 0.4700036,
        "hits": [
            {
                "_index": "megacorp",
                "_type": "employee",
                "_id": "1",
                "_score": 0.4700036,
                "_source": {
                    "first_name": "John",
                    "last_name": "Smith",
                    "age": 25,
                    "about": "I love to go rock climbing",
                    "interests": [
                        "sports",
                        "music"
                    ]
                }
            },
            {
                "_index": "megacorp",
                "_type": "employee",
                "_id": "2",
                "_score": 0.4700036,
                "_source": {
                    "first_name": "Jane",
                    "last_name": "Smith",
                    "age": 32,
                    "about": "I like to collect rock albums",
                    "interests": [
                        "music"
                    ]
                }
            }
        ]
    }
}
```

**使用查询表达式搜索**

Query-string 搜索通过命令非常方便地进行临时性的即席搜索 ，但它有自身的局限性。Elasticsearch 提供一个丰富灵活的查询语言叫做 _查询表达式_ ， 它支持构建更加复杂和健壮的查询。

```
GET /megacorp/employee/_search
{
    "query" : {
        "match" : {
            "last_name" : "Smith"
        }
    }
}
```

返回效果与之前一样

**更复杂的搜索**

同样搜索姓氏为 Smith 的员工，但这次我们只需要年龄大于 30 的

```
GET /megacorp/employee/_search
{
    "query" : {
        "bool": {
            "must": {
                "match" : {
                    "last_name" : "smith" 
                }
            },
            "filter": {
                "range" : {
                    "age" : { "gt" : 30 } 
                }
            }
        }
    }
}
```

**全文搜索**

搜索下所有喜欢攀岩（rock climbing）的员工：

```
GET /megacorp/employee/_search
{
    "query" : {
        "match" : {
            "about" : "rock climbing"
        }
    }
}
```

返回结果

```
{
    "took": 13,
    "timed_out": false,
    "_shards": {
        "total": 1,
        "successful": 1,
        "skipped": 0,
        "failed": 0
    },
    "hits": {
        "total": {
            "value": 2,
            "relation": "eq"
        },
        "max_score": 1.4167401,
        "hits": [
            {
                "_index": "megacorp",
                "_type": "employee",
                "_id": "1",
                "_score": 1.4167401,
                "_source": {
                    "first_name": "John",
                    "last_name": "Smith",
                    "age": 25,
                    "about": "I love to go rock climbing",
                    "interests": [
                        "sports",
                        "music"
                    ]
                }
            },
            {
                "_index": "megacorp",
                "_type": "employee",
                "_id": "2",
                "_score": 0.4589591,
                "_source": {
                    "first_name": "Jane",
                    "last_name": "Smith",
                    "age": 32,
                    "about": "I like to collect rock albums",
                    "interests": [
                        "music"
                    ]
                }
            }
        ]
    }
}
```

可以看到返回结果还带有相关性得分`_score`

**短语搜索**

**精确匹配**一系列单词或者\_短语\_ 。 比如， 执行这样一个查询，短语 “rock climbing” 的形式紧挨着的雇员记录。

为此对 `match` 查询稍作调整，使用一个叫做 `match_phrase` 的查询

```
GET /megacorp/employee/_search
{
    "query" : {
        "match_phrase" : {
            "about" : "rock climbing"
        }
    }
}
```

**高亮搜索**

每个搜索结果中 _高亮_ 部分文本片段

再次执行前面的查询，并增加一个新的 `highlight` 参数：

```
GET /megacorp/employee/_search
{
    "query" : {
        "match_phrase" : {
            "about" : "rock climbing"
        }
    },
    "highlight": {
        "fields" : {
            "about" : {}
        }
    }
}
```

返回结果

```
{
		...
    
        "hits": [
            {
                "_index": "megacorp",
                "_type": "employee",
                "_id": "1",
                "_score": 1.4167401,
                "_source": {
                    "first_name": "John",
                    "last_name": "Smith",
                    "age": 25,
                    "about": "I love to go rock climbing",
                    "interests": [
                        "sports",
                        "music"
                    ]
                },
                "highlight": {
                    "about": [
                        "I love to go <em>rock</em> <em>climbing</em>"
                    ]
                }
            }
        ]
    }
}
```

结果中还多了一个叫做 `highlight` 的部分。这个部分包含了 `about` 属性匹配的文本片段，并以 HTML 标签 `<em>` 封装

### 二、 Springboot整合ElasticSearch

#### 1. 概述

SpringBoot默认支持两种技术来和ES交互；

* Jest（默认不生效）
  * 需要导入jest的工具包（io.searchbox.client.JestClient）
  * 从springboot 2.2.0以后被弃用
*   SpringData ElasticSearch

    版本适配说明

| Spring Data Elasticsearch | Elasticsearch |
| ------------------------- | ------------- |
| 3.2.x                     | 6.8.1         |
| 3.1.x                     | 6.2.2         |
| 3.0.x                     | 5.5.0         |
| 2.1.x                     | 2.4.0         |
| 2.0.x                     | 2.2.0         |
| 1.3.x                     | 1.5.2         |

Springboot 2.2.6对应于 Spring Data Elasticsearch 3.2.6，即适配Elasticsearch 6.8.1

#### 2. 环境搭建

编写文件对应Java bean，指定索引名和类型

```java
@Document(indexName = "ustc",type = "book")
public class Book {
    private Integer id;
    private String bookName;
    private String author;

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getBookName() {
        return bookName;
    }

    public void setBookName(String bookName) {
        this.bookName = bookName;
    }

    public String getAuthor() {
        return author;
    }

    public void setAuthor(String author) {
        this.author = author;
    }

    @Override
    public String toString() {
        return "Book{" +
                "id=" + id +
                ", bookName='" + bookName + '\'' +
                ", author='" + author + '\'' +
                '}';
    }
}
```

#### 3. ElasticSearch客户端

*   **Transport Client**

    在ES7中已经被弃用，将在ES8被移除
*   **High Level REST Client**

    ES的默认客户端
*   **Reactive Client**

    非官方驱动，基于WebClient

下面以REST客户端为例说明ES的使用

**配置主机地址**

方式一 配置类配置

注意：这种方式底层依赖于Http相关类，因此要导入web相关jar包

```java
@Configuration
static class Config {
  @Bean
  RestHighLevelClient client() {

    ClientConfiguration clientConfiguration = ClientConfiguration.builder() 
      .connectedTo("localhost:9200")
      .build();

    return RestClients.create(clientConfiguration).rest();                  
  }
}
```

方式二 spring配置文件指定

```properties
spring.elasticsearch.rest.uris=http://192.168.31.162:9200
```

**在测试类中注入客户端**

```java
@Autowired
RestHighLevelClient highLevelClient;
```

**创建索引**

```java
//自6版本的elasticsearch后type不再使用，原因如下
IndexRequest request = new IndexRequest("ustc", "book",
        UUID.randomUUID().toString())
        .source(Collections.singletonMap("feature", "high-level-rest-client"))
        .setRefreshPolicy(WriteRequest.RefreshPolicy.IMMEDIATE);
IndexResponse index = highLevelClient.index(request, RequestOptions.DEFAULT);
System.out.println(index.toString());
```

> #### What are mapping types?
>
> Since the first release of Elasticsearch, each document has been stored in a single index and assigned a single mapping type. A mapping type was used to represent the type of document or entity being indexed, for instance a `twitter` index might have a `user` type and a `tweet` type.
>
> Each mapping type could have its own fields, so the `user` type might have a `full_name` field, a `user_name` field, and an `email` field, while the `tweet` type could have a `content` field, a `tweeted_at` field and, like the `user` type, a `user_name` field.
>
> Each document had a `_type` metadata field containing the type name, and searches could be limited to one or more types by specifying the type name(s) in the URL:
>
> ```js
> GET twitter/user,tweet/_search
> {
>   "query": {
>     "match": {
>       "user_name": "kimchy"
>     }
>   }
> }
> ```
>
> The `_type` field was combined with the document’s `_id` to generate a `_uid` field, so documents of different types with the same `_id` could exist in a single index.
>
> Mapping types were also used to establish a [parent-child relationship](https://www.elastic.co/guide/en/elasticsearch/reference/current/parent-join.html) between documents, so documents of type `question` could be parents to documents of type `answer`.
>
> #### Why are mapping types being removed?
>
> Initially, we spoke about an “index” being similar to a “database” in an SQL database, and a “type” being equivalent to a “table”.
>
> This was a bad analogy that led to incorrect assumptions. In an SQL database, tables are independent of each other. The columns in one table have no bearing on columns with the same name in another table. This is not the case for fields in a mapping type.
>
> In an Elasticsearch index, fields that have the same name in different mapping types are backed by the same Lucene field internally. In other words, using the example above, the `user_name` field in the `user` type is stored in exactly the same field as the `user_name` field in the `tweet` type, and both `user_name` fields must have the same mapping (definition) in both types.
>
> This can lead to frustration when, for example, you want `deleted` to be a `date` field in one type and a `boolean` field in another type in the same index.
>
> On top of that, storing different entities that have few or no fields in common in the same index leads to sparse data and interferes with Lucene’s ability to compress documents efficiently.
>
> For these reasons, we have decided to remove the concept of mapping types from Elasticsearch.
>
> 简单来说：
>
> 1、我们经常把二维数据库与ES作类比的方式是不正确的假设。把“index”类比为数据库，“type”类比为表。具体原因是，数据库的表是物理独立的，一个表的列跟另外一张表相同名称的列没有关系，而ES中并非如此，不同type映射类型中具有相同名称的字段在内部由相同的Lucene字段支持。
>
> 2、当您想要索引一个deleted字段在不同的type中数据类型不一样。一个类型中为日期字段，另外一个类型中为布尔字段时，这可能会导致ES的存储失败,因为这影响了ES的初衷设计。
>
> 3、另外，在一个index中建立很多实体，type，没有相同的字段，会导致数据稀疏，最终结果是干扰了Lucene有效压缩文档的能力，说白了就是影响ES的存储、检索效率。这意味着，只有同一个 index 的中的 type 都有类似的映射 (mapping) 时，才应该使用 type。否则，使用多个 type 可能比使用多个 index 消耗的资源更多。
>
> （\_type本身的目的就是为了能在同一个index中能存放多种数据类型,提高index的检索效率，可以类比为两个不同的圆，每个字段为其中划分出来的不同大小小圆（每个小圆代表index中的一部分固定的存储区域，区域被等分成相同大小，因此必须相同类型），相同的字段即是相交的小圆，若是不同type的字段名相同则代表存储的是同一个小圆中的不同等分区域，每次检索先检索index中的大圆（type）匹配，再进入小圆匹配，但是若是字段过多，则不如将Index 拆分成多个 Lucene Index，虽然可能导致碎片化现象加重，但是能提升检索效率，不需要每检查一个index都需要检查每个type）

> **Index 是什么**
>
> Index 存储在多个分片中，其中每一个分片都是一个独立的 Lucene Index。这就应该能提醒你，添加新 index 应该有个限度：每个 Lucene Index 都需要消耗一些磁盘，内存和文件描述符。因此，一个大的 index 比多个小 index 效率更高：Lucene Index 的固定开销被摊分到更多文档上了。
>
> 另一个重要因素是你准备怎么搜索你的数据。在搜索时，每个分片都需要搜索一次， 然后 ES 会合并来自所有分片的结果。例如，你要搜索 10 个 index，每个 index 有 5 个分片，那么协调这次搜索的节点就需要合并 5x10=50 个分片的结果。这也是一个你需要注意的地方：如果有太多分片的结果需要合并，或者你发起了一个结果巨大的搜索请求，合并任务会需要大量 CPU 和内存资源。这是第二个让 index 少一些的理由。

下面为创建索引

```
  {
      "_index": "ustc",
      "_type": "book",
      "_id": "0dc9f47a-7913-481d-a36d-e8f034a6a3ac",
      "_score": 1,
      "_source": {
          "feature": "high-level-rest-client"
      }
  }
```

得到索引

```
//分别指定要获取的索引、类型、id
GetRequest getRequest = new GetRequest("ustc","book","0dc9f47a-7913-481d-a36d-e8f034a6a3ac");
GetResponse documentFields = highLevelClient.get(getRequest, RequestOptions.DEFAULT);
System.out.println(documentFields);
```

#### 4. ElasticsearchRestTemplate

**ElasticSearchTemplate更多是对ESRepository的补充，里面提供了一些更底层的方法。**

ES有两个模板，分别为`ElasticsearchRestTemplate`和`ElasticsearchTemplate`

分别对应于**High Level REST Client**和**Transport Client**(弃用)，两个模板都实现了`ElasticsearchOperations`接口，因此使用时我们一般使用`ElasticsearchOperations`，具体实现方式由底层决定。

由于在`AbstractElasticsearchConfiguration`中已经向容器中导入了`ElasticsearchRestTemplate`，因此我们使用时可以直接注入

**注入模板**

```java
@Autowired
ElasticsearchOperations elasticsearchOperations;
```

**保存索引**

```java
    Book book = new Book();
    book.setAuthor("路遥");
    book.setBookName("平凡的世界");
    book.setId(1);
    IndexQuery indexQuery = new IndexQueryBuilder()
        .withId(book.getId().toString())
        .withObject(book)
        .build();
    String index = elasticsearchOperations.index(indexQuery);
```

**查询索引**

```java
Book book = elasticsearchOperations.queryForObject(GetQuery.getById("1"), Book.class);
```

#### 5. Elasticsearch Repositories

和普通的JPA 框架（JAVA Persistence API java持久层API）的操作类似

编写相关Repository并继承Repository或ElasticsearchRepository，泛型分别为<查询类，主键>

```java
public interface BookRepository extends Repository<Book,Integer> {
    //BookNameAndAuthor会被代理解析成两个我们查询的条件，具体规则guan'wang
    List<Book> findByBookNameAndAuthor(String bookName, String author);
}
```

查询的方法仅需按照**一定规则**命名即可实现功能，**无需编写实现**，如上findByBookNameAndAuthor()方法相当于ES的json查询

```
{
    "query": {
        "bool" : {
            "must" : [
                { "query_string" : { "query" : "?", "fields" : [ "bookName" ] } },
                { "query_string" : { "query" : "?", "fields" : [ "author" ] } }
            ]
        }
    }
}
```

[更多命名规则见本文档](https://docs.spring.io/spring-data/elasticsearch/docs/3.2.6.RELEASE/reference/html/#elasticsearch.repositories)

**@Query**

此外，还可以使用`@Query`自定义请求json

```java
interface BookRepository extends ElasticsearchRepository<Book, String> {
    @Query("{\"match\": {\"name\": {\"query\": \"?0\"}}}")
    Page<Book> findByName(String name,Pageable pageable);
}
```

若参数为John，相当于请求体为

```
{
  "query": {
    "match": {
      "name": {
        "query": "John"
      }
    }
  }
}
```

**更多ES与springboot整合内容见**[**官方文档**](https://docs.spring.io/spring-data/elasticsearch/docs/3.2.6.RELEASE/reference/html/#new-features)

#### 6.ElasticsearchRepository原理

ElasticsearchRepositoriesAutoConfiguration自动化配置类会导入ElasticsearchRepositoriesRegistrar这个ImportBeanDefinitionRegistrar。

ElasticsearchRepositoriesRegistrar继承自AbstractRepositoryConfigurationSourceSupport，是个ImportBeanDefinitionRegistrar接口的实现类（beanFactory会扫描实现该接口的类），被Spring容器调用registerBeanDefinitions进行自定义bean的注册。

ElasticsearchRepositoriesRegistrar委托给RepositoryConfigurationDelegate完成bean的解析。

整个解析过程可以分3个步骤：

1. 找出模块中的org.springframework.data.repository.Repository接口的实现类或者org.springframework.data.repository.RepositoryDefinition注解的修饰类，并会过滤掉org.springframework.data.repository.NoRepositoryBean注解的修饰类。找出后封装到RepositoryConfiguration中
2. 遍历这些RepositoryConfiguration，然后构造成BeanDefinition并注册到Spring容器中。需要注意的是这些BeanDefinition会由Factory生成相应的**ElasticsearchRepositoryFactoryBean**，并把对应的Repository接口当做构造参数传递给ElasticsearchRepositoryFactoryBean，还会设置相应的属性比如elasticsearchOperations、evaluationContextProvider、namedQueries、repositoryBaseClass、lazyInitqueryLookupStrategyKey
3. ElasticsearchRepositoryFactoryBean被实例化的时候设置对应的构造参数和属性。设置完毕以后调用afterPropertiesSet方法(实现了InitializingBean接口)。在afterPropertiesSet方法内部会去创建RepositoryFactorySupport类，并进行一些初始化，比如namedQueries、repositoryBaseClass等。然后通过这个RepositoryFactorySupport的getRepository方法基于Repository接口创建出代理类，并使用AOP添加了几个MethodInterceptor

**AbstractRepositoryConfigurationSourceSupport**

```java
public void registerBeanDefinitions(AnnotationMetadata importingClassMetadata, BeanDefinitionRegistry registry,
			BeanNameGenerator importBeanNameGenerator) {
		RepositoryConfigurationDelegate delegate = new RepositoryConfigurationDelegate(
				getConfigurationSource(registry, importBeanNameGenerator), this.resourceLoader, this.environment);
		delegate.registerRepositoriesIn(registry, getRepositoryConfigurationExtension());
	}
```

**RepositoryConfigurationDelegate**

```java
public List<BeanComponentDefinition> registerRepositoriesIn(BeanDefinitionRegistry registry,
			RepositoryConfigurationExtension extension) {

		if (LOG.isInfoEnabled()) {
			LOG.info("Bootstrapping Spring Data {} repositories in {} mode.", //
					extension.getModuleName(), configurationSource.getBootstrapMode().name());
		}

		extension.registerBeansForRoot(registry, configurationSource);

		RepositoryBeanDefinitionBuilder builder = new RepositoryBeanDefinitionBuilder(registry, extension,
				configurationSource, resourceLoader, environment);
		List<BeanComponentDefinition> definitions = new ArrayList<>();

		StopWatch watch = new StopWatch();

		if (LOG.isDebugEnabled()) {
			LOG.debug("Scanning for {} repositories in packages {}.", //
					extension.getModuleName(), //
					configurationSource.getBasePackages().stream().collect(Collectors.joining(", ")));
		}

		watch.start();

		Collection<RepositoryConfiguration<RepositoryConfigurationSource>> configurations = extension
				.getRepositoryConfigurations(configurationSource, resourceLoader, inMultiStoreMode);

		Map<String, RepositoryConfiguration<?>> configurationsByRepositoryName = new HashMap<>(configurations.size());

		for (RepositoryConfiguration<? extends RepositoryConfigurationSource> configuration : configurations) {

			configurationsByRepositoryName.put(configuration.getRepositoryInterface(), configuration);
             
            // 构造出BeanDefinitionBuilder
			BeanDefinitionBuilder definitionBuilder = builder.build(configuration);

			extension.postProcess(definitionBuilder, configurationSource);

			if (isXml) {
                // 设置elasticsearchOperations属性
				extension.postProcess(definitionBuilder, (XmlRepositoryConfigurationSource) configurationSource);
			} else {
                // 设置elasticsearchOperations属性
				extension.postProcess(definitionBuilder, (AnnotationRepositoryConfigurationSource) configurationSource);
			}

            // 使用命名策略生成bean的名字
			AbstractBeanDefinition beanDefinition = definitionBuilder.getBeanDefinition();
			beanDefinition.setResourceDescription(configuration.getResourceDescription());

			String beanName = configurationSource.generateBeanName(beanDefinition);

			if (LOG.isTraceEnabled()) {
				LOG.trace(REPOSITORY_REGISTRATION, extension.getModuleName(), beanName, configuration.getRepositoryInterface(),
						configuration.getRepositoryFactoryBeanClassName());
			}

			beanDefinition.setAttribute(FACTORY_BEAN_OBJECT_TYPE, configuration.getRepositoryInterface());

            // 注册到Spring容器中
			registry.registerBeanDefinition(beanName, beanDefinition);
			definitions.add(new BeanComponentDefinition(beanDefinition, beanName));
		}

		potentiallyLazifyRepositories(configurationsByRepositoryName, registry, configurationSource.getBootstrapMode());

		watch.stop();

		if (LOG.isInfoEnabled()) {
			LOG.info("Finished Spring Data repository scanning in {}ms. Found {} {} repository interfaces.", //
					watch.getLastTaskTimeMillis(), configurations.size(), extension.getModuleName());
		}

		return definitions;
	}
```

**BeanDefinitionBuilder.build**

```java
public BeanDefinitionBuilder build(RepositoryConfiguration<?> configuration) {

  Assert.notNull(registry, "BeanDefinitionRegistry must not be null!");
  Assert.notNull(resourceLoader, "ResourceLoader must not be null!");
  // 得到factoryBeanName，这里会使用extension.getRepositoryFactoryClassName()去获得
  // extension.getRepositoryFactoryClassName()返回的正是ElasticsearchRepositoryFactoryBean
  String factoryBeanName = configuration.getRepositoryFactoryBeanName();
  factoryBeanName = StringUtils.hasText(factoryBeanName) ? factoryBeanName
      : extension.getRepositoryFactoryClassName();
  // 基于factoryBeanName构造BeanDefinitionBuilder
  BeanDefinitionBuilder builder = BeanDefinitionBuilder.rootBeanDefinition(factoryBeanName);

  builder.getRawBeanDefinition().setSource(configuration.getSource());
  // 设置ElasticsearchRepositoryFactoryBean的构造参数，这里是对应的Repository接口
  // 设置一些的属性值
  builder.addConstructorArgValue(configuration.getRepositoryInterface());
  builder.addPropertyValue("queryLookupStrategyKey", configuration.getQueryLookupStrategyKey());
  builder.addPropertyValue("lazyInit", configuration.isLazyInit());
  builder.addPropertyValue("repositoryBaseClass", configuration.getRepositoryBaseClassName());

  NamedQueriesBeanDefinitionBuilder definitionBuilder = new NamedQueriesBeanDefinitionBuilder(
      extension.getDefaultNamedQueryLocation());

  if (StringUtils.hasText(configuration.getNamedQueriesLocation())) {
    definitionBuilder.setLocations(configuration.getNamedQueriesLocation());
  }

  builder.addPropertyValue("namedQueries", definitionBuilder.build(configuration.getSource()));
  // 查找是否有对应Repository接口的自定义实现类
  String customImplementationBeanName = registerCustomImplementation(configuration);
  // 存在自定义实现类的话，设置到属性中
  if (customImplementationBeanName != null) {
    builder.addPropertyReference("customImplementation", customImplementationBeanName);
    builder.addDependsOn(customImplementationBeanName);
  }

  RootBeanDefinition evaluationContextProviderDefinition = new RootBeanDefinition(
      ExtensionAwareEvaluationContextProvider.class);
  evaluationContextProviderDefinition.setSource(configuration.getSource());
  // 设置一些的属性值
  builder.addPropertyValue("evaluationContextProvider", evaluationContextProviderDefinition);

  return builder;
}
```

**ElasticsearchRepositoryFactoryBean**

ElasticsearchRepositoryFactoryBean是对代理类的一些公共参数与设置的保存以供代理工厂对代理类的生成。

当相应的BeanDefinitionRepository将BeanDefinition注册到相应的数据结构后，BeanFactory进行bean的创建时，会依据BeanDefinition将生成相应的Bean，而ElasticsearchRepository需要生成相应的代理类，而ElasticsearchRepositoryFactoryBean的afterPropertiesSet的作用便是依据接口创建出代理类。

```java
// RepositoryFactorySupport的getRepository方法，获得Repository接口的代理类
public <T> T getRepository(Class<T> repositoryInterface, Object customImplementation) {

  // 获取Repository的元数据
  RepositoryMetadata metadata = getRepositoryMetadata(repositoryInterface);
  // 获取Repository的自定义实现类
  Class<?> customImplementationClass = null == customImplementation ? null : customImplementation.getClass();
  // 根据元数据和自定义实现类得到Repository的RepositoryInformation信息类
  // 获取信息类的时候如果发现repositoryBaseClass是空的话会根据meta中的信息去自动匹配
  // 具体匹配过程在下面的getRepositoryBaseClass方法中说明
  RepositoryInformation information = getRepositoryInformation(metadata, customImplementationClass);
  // 验证
  validate(information, customImplementation);
  // 得到最终的目标类实例，会通过repositoryBaseClass去查找
  Object target = getTargetRepository(information);

  // 创建代理工厂
  ProxyFactory result = new ProxyFactory();
  result.setTarget(target);
  result.setInterfaces(new Class[] { repositoryInterface, Repository.class });
  // 进行aop相关的设置
  result.addAdvice(SurroundingTransactionDetectorMethodInterceptor.INSTANCE);
  result.addAdvisor(ExposeInvocationInterceptor.ADVISOR);

  if (TRANSACTION_PROXY_TYPE != null) {
    result.addInterface(TRANSACTION_PROXY_TYPE);
  }
  // 使用RepositoryProxyPostProcessor处理
  for (RepositoryProxyPostProcessor processor : postProcessors) {
    processor.postProcess(result, information);
  }

  if (IS_JAVA_8) {
    // 如果是JDK8的话，添加DefaultMethodInvokingMethodInterceptor
    result.addAdvice(new DefaultMethodInvokingMethodInterceptor());
  }

  // 添加QueryExecutorMethodInterceptor
  result.addAdvice(new QueryExecutorMethodInterceptor(information, customImplementation, target));
  // 使用代理工厂创建出代理类，这里是使用jdk内置的代理模式
  return (T) result.getProxy(classLoader);
}
```

#### Elasticsearch官方已支持SQL查询

Elasticsearch SQL是一个X-Pack组件，它允许针对Elasticsearch实时执行类似SQL的查询。无论使用REST接口，命令行还是JDBC，任何客户端都可以使用SQL对Elasticsearch中的数据进行原生搜索和聚合数据。可以将Elasticsearch SQL看作是一种翻译器，它可以将SQL翻译成Query DSL。

Elasticsearch SQL具有如下特性：

* 原生支持：Elasticsearch SQL是专门为Elasticsearch打造的。
* 没有额外的零件：无需其他硬件，处理器，运行环境或依赖库即可查询Elasticsearch，Elasticsearch SQL直接在Elasticsearch内部运行。
* 轻巧高效：Elasticsearch SQL并未抽象化其搜索功能，相反的它拥抱并接受了SQL来实现全文搜索，以简洁的方式实时运行全文搜索。

## (四) Spring boot与任务

### 一、异步任务

在Java应用中，绝大多数情况下都是通过同步的方式来实现交互处理的；但是在处理与第三方系统交互的时候，容易造成响应迟缓的情况，之前大部分都是使用多线程来完成此类任务，springboot中可以用异步任务解决。

**两个注解：**

`@Async` 在需要异步执行的方法上标注注解

`@EnableAsync` 在主类上标注开启异步任务支持

开启异步任务后，当controller层调用该方法会直接返回结果，该任务异步执行

```
@Service
public class AsyncService {
    @Async
    public void sayHello() {
        try {
            Thread.sleep(3000);
            System.out.println("hello async task!");
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

### 二、 定时任务

项目开发中经常需要执行一些定时任务，比如需要在每天凌晨时候，分析一次前一天的日志信息。Spring为我们提供了异步执行任务调度的方式，提供TaskExecutor 、TaskScheduler 接口。

**两个注解：**

`@EnableScheduling` 标注在主类，开启对定时任务支持

`@Scheduled` 标注在执行的方法上，并制定cron属性

```
@Service
public class ScheduleService {
    @Scheduled(cron = "0,1,2,3,4,5,30,50 * * * * 0-7")
    public void schedule() {
        System.out.println("I am executing..");
    }
}
```

**cron**表达式：

second(秒), minute（分）, hour（时）, day of month（日）, month（月）, day of week（周几）.

`0 0/5 14,18 * * ?` 每天14点整，和18点整，每隔5分钟执行一次

`0 15 10 ? * 1-6` 每个月的周一至周六10:15分执行一次

`0 0 2 ? * 6L` 每个月的最后一个周六凌晨2点执行一次

`0 0 2 LW * ?` 每个月的最后一个工作日凌晨2点执行一次

`0 0 2-4 ? * 1#1` 每个月的第一个周一凌晨2点到4点期间，每个整点都执行一次；

| **字段** | **允许值**             | **允许的特殊字符**      |
| ------ | ------------------- | ---------------- |
| 秒      | 0-59                | , - \* /         |
| 分      | 0-59                | , - \* /         |
| 小时     | 0-23                | , - \* /         |
| 日期     | 1-31                | , - \* ? / L W C |
| 月份     | 1-12                | , - \* /         |
| 星期     | 0-7或SUN-SAT 0,7是SUN | , - \* ? / L C # |

| **特殊字符** | **代表含义**          |
| -------- | ----------------- |
| ,        | 枚举                |
| -        | 区间                |
| \*       | 任意                |
| /        | 步长                |
| ?        | 日/星期冲突匹配          |
| L        | 最后                |
| W        | 工作日               |
| C        | 和calendar联系后计算过的值 |
| #        | 星期，4#2，第2个星期四     |

### 三、 邮件任务

springboot自动配置包中`MailSenderAutoConfiguration`通过`@Import`注解向容器中导入了`MailSenderJndiConfiguration`,而`MailSenderJndiConfiguration`向容器中导入了`JavaMailSenderImpl`类，我们可以使用该类发送邮件

**配置文件**

```properties
spring.mail.username=邮箱用户名
spring.mail.password=邮箱密码或授权码
spring.mail.host=smtp.example.com 
```

**自动注入**

```java
@Autowired
private JavaMailSenderImpl javaMailSender;
```

**简单邮件发送**

```java
SimpleMailMessage message = new SimpleMailMessage();
//设置主题和内容
message.setSubject("今天开会");
message.setText("物质楼555开会，不要迟到");
//设置发送方和接收方
message.setFrom("xxx@163.com");
message.setTo("xxx@qq.com");

javaMailSender.send(message);
```

**复杂邮件发送**

带有附件或html页面的邮件

**两个设置**

`new MimeMessageHelper(message,true)` 设置multipart=true，开启对内联元素和附件的支持

`helper.setText("xxxx",true)` html=ture，设置content type=text/html，默认为text/plain

```java
MimeMessage message = javaMailSender.createMimeMessage();
//multipart=true
//开启对内联元素和附件的支持
MimeMessageHelper helper = new MimeMessageHelper(message,true);

helper.setSubject("今天开会");
//html=ture
//设置content type=text/html，默认为text/plain
helper.setText("<b style='color:red'>物质楼555开会，不要迟到</b>",true);

helper.setFrom("hongshengmo@163.com");
helper.setTo("1043245239@qq.com");
//设置附件
helper.addAttachment("2.png",new File("D:\\Works\\Note\\images\\图片2.png"));
helper.addAttachment("3.png",new File("D:\\Works\\Note\\images\\图片3.png"));
javaMailSender.send(message);
```

## (五) Spring boot与安全

### 一、安全

应用程序的两个主要区域是“认证”和“授权”（或者访问控制），这两个主要区域是安全的两个目标。 身份验证意味着**确认您自己的身份**，而授权意味着**授予对系统的访问权限**

**认证**

身份验证是关于验证您的凭据，如用户名/用户ID和密码，以验证您的身份。系统确定您是否就是您所说的使用凭据。在公共和专用网络中，系统通过登录密码验证用户身份。身份验证通常通过用户名和密码完成，

**授权**

另一方面，授权发生在系统成功验证您的身份后，最终会授予您访问资源（如信息，文件，数据库，资金，位置，几乎任何内容）的完全权限。简单来说，授权决定了您访问系统的能力以及达到的程度。验证成功后，系统验证您的身份后，即可授权您访问系统资源。

### 二、Spring Security

Spring Security是针对Spring项目的安全框架，也是Spring Boot底层安全模块默认的技术选型。他可以实现强大的web安全控制。对于安全控制，我们仅需引入`spring-boot-starter-security`模块，进行少量的配置，即可实现强大的安全管理。

**WebSecurityConfigurerAdapter：自定义Security策略**

通过在配置类中继承该类重写`configure(HttpSecurity http)`方法来实现自定义策略

**@EnableWebSecurity：开启WebSecurity模式**

在配置类上标注`@EnableWebSecurity`开启WebSecurity模式

### 三、 Springboot整合security

#### 1. 导入依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

导入spring security的包之后，默认情况所有应用访问认证授权，默认用户名user，密码为随机生成的uuid，启动时打印在控制台

#### 2. 登录/注销

```java
@EnableWebSecurity
public class MySecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        //根目录允许所有人访问，其他目录都需要对应角色
        http.authorizeRequests().antMatchers("/").permitAll()
                .antMatchers("/level1/**").hasRole("VIP1")
                .antMatchers("/level2/**").hasRole("VIP2")
                .antMatchers("/level3/**").hasRole("VIP3");
        
        //开启自动配置的登陆功能，效果，如果没有登陆，没有权限就会来到登陆页面
        //	/login来到登陆页
        //	重定向到/login?error表示登陆失败
        http.formLogin();
        
        //开启自动配置的注销功能
        //向/logout发送post请求表示注销
        http.logout();
    }
}
```

此时除了主页，点击其他的页面都会自动跳转到security自动生成的登录页面，`/login`来到登陆页,重定向到`/login?error`表示登陆失败；

`http.logout()`开启自动配置的注销功能,向`/logout`发送post请求表示注销,需要在欢迎页加上注销表单，默认注销后自动跳转到登录页面，若想改变转发路径，可以通过`logoutSuccessUrl(url)`设置路径

```html
<form th:action="@{/logout}" method="post">
   <input type="submit" value="注销">
</form>
```

#### 3. 定义认证规则

为了保证密码能安全存储，springboot内置`PasswordEncoder`对密码进行转码，默认密码编码器为`DelegatingPasswordEncoder`。在定义认证规则时，我们需要使用`PasswordEncoder`将密码转码，由于`withDefaultPasswordEncoder()`并非安全已被弃用，因此仅在测试中使用。

```java
    @Bean
    public UserDetailsService users() {
        //使用默认的PasswordEncoder
        User.UserBuilder builder = User.withDefaultPasswordEncoder();
        //定义账户用户名、密码、权限
        UserDetails user1 = builder.username("zhangsan")
                .password("123456")
                .roles("VIP1", "VIP2")
                .build();
        UserDetails user2 = builder.username("lisi")
                .password("123456")
                .roles("VIP3", "VIP2")
                .build();
        UserDetails user3 = builder.username("wangwu")
                .password("123456")
                .roles("VIP1", "VIP3")
                .build();
        //使用内存保存用户信息
        return new InMemoryUserDetailsManager(user1,user2,user3);
    }
```

#### 4.自定义欢迎页

**导入依赖**

```xml
<dependency>
    <groupId>org.thymeleaf.extras</groupId>
    <artifactId>thymeleaf-extras-springsecurity5</artifactId>
</dependency>
```

**引入命名空间**

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org"
     xmlns:sec="http://www.thymeleaf.org/extras/spring-security">
```

**根据是否登录显示游客或用户信息**

```html
<!-- 未登录显示此div -->
<div sec:authorize="!isAuthenticated()">
   <h2 align="center">游客您好，如果想查看武林秘籍 <a th:href="@{/userlogin}">请登录</a></h2>
</div>
<!-- 登录显示此div -->
<div sec:authorize="isAuthenticated()">
    <!-- 显示用户名 -->
   <h2>尊敬的<span th:text="${#authentication.name}"></span>,您好！您的角色有：
       <!-- 显示用户角色 -->
      <span th:text="${#authentication.authorities}"></span></h2>
   <form th:action="@{/logout}" method="post">
      <input type="submit" value="注销">
   </form>
</div>
```

**根据角色类型显示信息**

```html
<!-- 具有VIP1的角色显示以下div -->
<div sec:authorize="hasRole('VIP1')">
   <h3>普通武功秘籍</h3>
   <ul>
      <li><a th:href="@{/level1/1}">罗汉拳</a></li>
      <li><a th:href="@{/level1/2}">武当长拳</a></li>
      <li><a th:href="@{/level1/3}">全真剑法</a></li>
   </ul>
</div>
```

[更多spring-security与thymeleaf整合教程](https://github.com/thymeleaf/thymeleaf-extras-springsecurity)

#### 5. 自定义登录页/记住我

```java
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        ...
        //定制登录页
        http.formLogin()
                .usernameParameter("user")  //表单用户名name
                .passwordParameter("pwd")   //表单密码name
                .loginPage("/userlogin");   //定制登陆页路径
        ...

        //开启记住我
        http.rememberMe().
            rememberMeParameter("rem");		//设置表单记住我name值

    }
```

通过`loginPage(url)`设置登录页路径后，在定制的登录页发送`post url`即为登录请求，并设置表单的`name`属性都为对应值；

通过勾选`记住我`，session退出后依然能通过`cookie`保存用户信息，下次免登陆

```html
<form th:action="@{/userlogin}" method="post">
   用户名:<input name="user"/><br>
   密码:<input name="pwd"><br/>
   <input type="checkbox" name="rem">记住我<br>
   <input type="submit" value="登陆">
</form>
```

[更多spring-security参阅官方文档](https://docs.spring.io/spring-security/site/docs/5.3.2.BUILD-SNAPSHOT/reference/html5/)

## (六) Spring boot与分布式

### 一、分布式应用

分布式应用（distributed application）指的是应用程序分布在不同计算机上，通过网络来共同完成一项任务的工作方式。

**为什么需要分布式？**

* **单一应用架构** 当网站流量很小时，只需一个应用，将所有功能都部署在一起，以减少部署节点和成本。此时，用于简化增删改查工作量的数据访问框架(ORM)是关键。
* **垂直应用架构** 当访问量逐渐增大，单一应用增加机器带来的加速度越来越小，将应用拆成互不相干的几个应用，以提升效率。此时，用于加速前端页面开发的Web框架(MVC)是关键。
* 分布式服务架构
  * 当垂直应用越来越多，应用之间交互不可避免，将核心业务抽取出来，作为独立的服务，逐渐形成稳定的服务中心，使前端应用能更快速的响应多变的市场需求。此时，用于提高业务复用及整合的分布式服务框架(RPC)是关键。
  * **流动计算架构** 当服务越来越多，容量的评估，小服务资源的浪费等问题逐渐显现，此时需增加一个调度中心基于访问压力实时管理集群容量，提高集群利用率。此时，用于提高机器利用率的资源调度和治理中心(SOA)是关键。

在分布式系统中，国内常用zookeeper+dubbo组合，而Spring Boot推荐使用全栈的Spring，Spring Boot+Spring Cloud。

![image-20200826154603377](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpringBoot%E9%AB%98%E7%BA%A7%E7%AF%87.assets%5Cimage-20200826154603377.png)

### 二、Zookeeper和Dubbo

#### 1. 概述

**ZooKeeper** ZooKeeper 是一个分布式的，开放源码的分布式应用程序协调服务。它是一个为分布式应用提供一致性服务的软件，提供的功能包括：配置维护、域名服务、分布式同步、组服务等。

**Dubbo** Dubbo是Alibaba开源的分布式服务框架，它最大的特点是按照分层的方式来架构，使用这种方式可以使各个层之间解耦合（或者最大限度地松耦合）。从服务模型的角度来看，Dubbo采用的是一种非常简单的模型，要么是提供方提供服务，要么是消费方消费服务，所以基于这一点可以抽象出服务提供方（Provider）和服务消费方（Consumer）两个角色。

![image-20200826154849247](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpringBoot%E9%AB%98%E7%BA%A7%E7%AF%87.assets%5Cimage-20200826154849247.png)

**https://github.com/alibaba/dubbo**

#### 2. 整合springboot

**环境搭建**

```shell
 docker pull zookeeper:3.4.13
 
# zookeeper有client端口，集群端口，分类端口，这里只用到client端口
 docker run  --name zookeeper01 -p 2181:2181  --restart always -d 4ebfb9474e72 
```

分别创建provider和consumer模块并分别导入依赖,dubbo-spring-boot-starter自2.7.7版本开始不支持@Reference和@Service

```xml
 <dependencies>
       <!-- 导入dubbo与springboot整合启动器 -->
		<dependency>
			<groupId>org.apache.dubbo</groupId>
			<artifactId>dubbo-spring-boot-starter</artifactId>
			<version>2.7.6</version>
		</dependency>
 	 
        
       <!-- 导入zookeeper客户端 -->
		<dependency>
			<groupId>com.github.sgroschupf</groupId>
			<artifactId>zkclient</artifactId>
			<version>0.1</version>
		</dependency>
      <!-- zookeeper版本指定，zookeeper类（导入的jar包中的，不是docker中启动的）的使用与JDK版本联系较为紧密，1.8的版本只能用到3.4.12，再高会难以运行 -->
		<dependency>
			<groupId>org.apache.zookeeper</groupId>
			<artifactId>zookeeper</artifactId>
			<version>3.4.12</version>
		</dependency>
     
     <!-- 导入zookeeper客户端所需依赖：curator框架 -->
     <dependency>
			<groupId>org.apache.curator</groupId>
			<artifactId>curator-framework</artifactId>
			<version>4.2.0</version>
		</dependency>
		<dependency>
			<groupId>org.apache.curator</groupId>
			<artifactId>curator-recipes</artifactId>
			<version>4.2.0</version>
		</dependency>
    </dependencies>
```

2.7.6后有了dubbo-dependencies-zookeeper，其会导入zookeeper和curator框架依赖，但是要注意zookeeper版本，2.7.6版本的与我的jdk版本（1.8.241）不对应

```xml
<properties>
    <dubbo.version>2.7.8</dubbo.version>
</properties>
    
<dependencies>
    <dependency>
        <groupId>org.apache.dubbo</groupId>
        <artifactId>dubbo</artifactId>
        <version>${dubbo.version}</version>
    </dependency>
    <dependency>
        <groupId>org.apache.dubbo</groupId>
        <artifactId>dubbo-dependencies-zookeeper</artifactId>
        <version>${dubbo.version}</version>
        <type>pom</type>
    </dependency>
</dependencies>
```

provider配置文件

```properties
dubbo.application.name=provider-ticket

dubbo.registry.address=zookeeper://192.168.98.128:2181

dubbo.scan.base-packages=com.atguigu.ticket.service

dubbo.config-center.timeout=100000
```

consumer配置文件

```properties
 dubbo.application.name=consumer-user

dubbo.registry.address=zookeeper://192.168.98.128:2181
```

**生产者服务**

`@EnableDubboConfig:`启动dubbo配置

`@EnableDubbo` ：

可以在指定的包名下（通过 `scanBasePackages`），或者指定的类中（通过 `scanBasePackageClasses`）扫描 Dubbo 的服务提供者（以 `@Service` 标注）以及 Dubbo 的服务消费者（以 `Reference` 标注）。

`@Service`：dubbo的Service

表示服务的具体实现，被注解的类会被dubbo扫描

```java
import org.apache.dubbo.config.annotation.Service;
import org.apache.dubbo.config.spring.context.annotation.EnableDubbo;
import org.springframework.stereotype.Component;


@EnableDubbo //开启对dubbo支持
@Component
@Service //标记此类，表示服务的具体实现
public class TicketServiceImpl implements TicketService{
    @Override
    public String getTicket() {
        return "Gxx:合肥-北京";
    }
}
```

**消费者服务**

编写与分布式服务类相同的接口(不必实现)，并保证包结构相同

```java
public interface TicketService {
    String getTicket();
}
```

@Reference 可以定义在类中的一个字段、方法上，表示一个服务的引用。通常 @Reference 定义在一个字段上

```java
@Service
public class UserService {

    @Reference
    TicketService ticketService;

    public void hello() {
        String ticket = ticketService.getTicket();
        System.out.println("买到票了:"+ticket);
    }
}
```

此时若调用`hello()`,控制台将打印

```
买到票了:Gxx:合肥-北京
```

**有关dubbo更多**

[dubbo注解详细解释](http://dubbo.apache.org/zh-cn/blog/dubbo-annotation.html)

[dubbo与zookeeper官方整合案例](http://dubbo.apache.org/zh-cn/blog/dubbo-zk.html)

### 三、Spring Cloud

#### 1. 概述

Spring Cloud是一个分布式的整体解决方案。Spring Cloud 为开发者提供了在分布式系统（配置管理，服务发现，熔断，路由，微代理，控制总线，一次性token，全局琐，leader选举，分布式session，集群状态）中快速构建的工具，使用Spring Cloud的开发者可以快速的启动服务或构建应用、同时能够快速和云平台资源进行对接。

SpringCloud分布式开发五大常用组件

* 服务发现——Netflix Eureka
* 客服端负载均衡——Netflix Ribbon
* 断路器——Netflix Hystrix
* 服务网关——Netflix Zuul
* 分布式配置——Spring Cloud Config

#### 2. 入门

**Eureka注册中心**

创建工程导入`eureka-server`模块

```xml
	<dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>
        ...
    </dependencies>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

配置文件

```properties
server:
  port: 8761
eureka:
  instance:
    hostname: eureka-server  # eureka实例的主机名
  client:
    register-with-eureka: false #不把自己注册到eureka上
    fetch-registry: false #不从eureka上来获取服务的注册信息
    service-url:
      defaultZone: http://localhost:8761/eureka/
```

**生产者模块**

创建工程导入`eureka-client`和`web`模块

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    ...
</dependencies>

<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <version>${spring-cloud.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

配置文件

```properties
server:
  port: 8002
spring:
  application:
    name: provider-ticket


eureka:
  instance:
    prefer-ip-address: true # 注册服务的时候使用服务的ip地址
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka/
```

编写controller层和service层demo

```java
@Service
public class TicketService {
    public String getTicket(){
        System.out.println("8002");
        return "《厉害了，我的国》";
    }
}

@RestController
public class TicketController {
    @Autowired
    TicketService ticketService;

    @GetMapping("/ticket")
    public String getTicket(){
        return ticketService.getTicket();
    }
}
```

**消费者模块**

创建工程导入`eureka-client`和`web`模块

配置文件

```properties
spring:
  application:
    name: consumer-user
server:
  port: 8200

eureka:
  instance:
    prefer-ip-address: true # 注册服务的时候使用服务的ip地址
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka/
```

向容器中注入`RestTemplate`, 并使用`@EnableDiscoveryClient`开启发现服务功能

```java
@EnableDiscoveryClient //开启发现服务功能
@SpringBootApplication
public class ConsumerUserApplication {

    public static void main(String[] args) {
        SpringApplication.run(ConsumerUserApplication.class, args);
    }

    @LoadBalanced //使用负载均衡机制
    @Bean
    public RestTemplate restTemplate(){
        return new RestTemplate();
    }
}
```

编写controller并使用`RestTemplate`发现服务

```java
@RestController
public class UserController {

    @Autowired
    RestTemplate restTemplate;

    @GetMapping("/buy")
    public String buyTicket(String name){
        String s = restTemplate.getForObject("http://PROVIDER-TICKET/ticket", String.class);
        return name+"购买了"+s;
    }
}
```

向`http://localhost:8200/buy?username=zhangsan`发请求，则会响应

```
zhangsan购买了《厉害了，我的国》
```

并且在使用了`@LoadBalanced`之后实现了负载均衡，如果创建不同端口的`provider`应用，则访问会被均衡到各个应用

## (七) Spring boot与热部署

在开发中我们修改一个Java文件后想看到效果不得不重启应用，这导致大量时间花费，我们希望不重启应用的情况下，程序可以自动部署（热部署）。有以下四种情况，如何能实现热部署。

### 一、模板引擎

在Spring Boot中开发情况下禁用模板引擎的cache 页面模板改变ctrl+F9可以重新编译当前页面并生效

### 二、Spring Loaded

Spring官方提供的热部署程序，实现修改类文件的热部署

* 下载Spring Loaded（项目地址https://github.com/spring-projects/spring-loaded）
* 添加运行时参数；
* javaagent:C:/springloaded-1.2.5.RELEASE.jar –noverify

### 三、JRebel

收费的一个热部署软件 安装插件使用即可

### 四、 Spring Boot Devtools（推荐）

引入依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
</dependency>
```

IDEA使用ctrl+F9重新编译实现热部署

## (八) Spring Boot与监控管理

通过引入spring-boot-starter-actuator，可以使用Spring Boot为我们提供的准生产环境下的应用监控和管理功能。我们可以通过HTTP，JMX，SSH协议来进行操作，自动得到审计、健康及指标信息等

### 一、 Actuator监控管理

**导入依赖**

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

浏览器打开链接`http://localhost:8080/actuator/`,可以看到所有支持的连接，响应如下,默认只支持这些端点

```json
{
    "_links": {
        "self": {
            "href": "http://localhost:8080/actuator",
            "templated": false
        },
        "health": {
            "href": "http://localhost:8080/actuator/health",
            "templated": false
        },
        "health-path": {
            "href": "http://localhost:8080/actuator/health/{*path}",
            "templated": true
        },
        "info": {
            "href": "http://localhost:8080/actuator/info",
            "templated": false
        }
    }
}
```

如果要看到所有支持的状态查询，需要配置

```
management.endpoints.web.exposure.include=*
```

bean加载情况`http://localhost:8080/actuator/beans`,显示了容器中各类各项属性

```json
{
    "contexts": {
        "application": {
            "beans": {
                "endpointCachingOperationInvokerAdvisor": {
                    "aliases": [],
                    "scope": "singleton",
                    "type": "org.springframework.boot.actuate.endpoint.invoker.cache.CachingOperationInvokerAdvisor",
                    "resource": "class path resource [org/springframework/boot/actuate/autoconfigure/endpoint/EndpointAutoConfiguration.class]",
                    "dependencies": [
                        "environment"
                    ]
                },
                "defaultServletHandlerMapping": {
                    "aliases": [],
                    "scope": "singleton",
                    "type": "org.springframework.web.servlet.HandlerMapping",
                    "resource": "class path resource [org/springframework/boot/autoconfigure/web/servlet/WebMvcAutoConfiguration$EnableWebMvcConfiguration.class]",
                    "dependencies": []
                },
            }
            ...
```

![image-20200827100901645](F:%5CTypora%E6%95%B0%E6%8D%AE%E5%82%A8%E5%AD%98%5CSpring%5CSpringBoot%E9%AB%98%E7%BA%A7%E7%AF%87.assets%5Cimage-20200827100901645.png)

### 二、 端点配置

默认情况下，除shutdown以外的所有端点均已启用。要配置单个端点的启用，请使用`management.endpoint.<id>.enabled`属性。以下示例启用`shutdown`端点：

```properties
# shundown路径，POST请求
management.endpoint.shutdown.enabled=true
```

另外可以通过`management.endpoints.enabled-by-default`来修改全局端口默认配置,以下示例启用info端点并禁用所有其他端点：

```properties
management.endpoints.enabled-by-default=false
management.endpoint.info.enabled=true
```

修改路径

```properties
# 修改根目录路径，即prefix
management.endpoints.web.base-path=/management
# 修改/health路径
management.endpoints.web.path-mapping.health=healthcheck
```

修改端口

```properties
#若是不存在的端口如-1，则服务消失
management.port=8181
```

### 三、 自定义XXXHealthIndicator

* spring-boot-starter-actuator包下的Health包下为/health路径所能监视健康状态的组件，相关依赖导入时会自动装配进容器，若是自己使用的组件没有相应的XXXHealthIndicator，可以自己自定义实现HealthIndicator接口的监视类，加入容器中，/health请求会扫描所有的HealthIndicator接口实现类

```java
/**
 * 自定义健康状态指示器
 * 1、编写一个指示器 实现 HealthIndicator 接口
 * 2、指示器的名字 xxxxHealthIndicator，xxx会作为/health读取的组件名，xxx后的HealthIndicator会被过滤，因此要规范命名
 * 3、加入容器中
 */

@Component
public class MyAppHealthIndicator implements HealthIndicator {

    @Override
    public Health health() {

        //自定义的检查方法
        //Health.up().build()代表健康
        return Health.down().withDetail("msg","服务异常").build();
    }
}
```

[更多参阅spring-actuator官方文档](https://docs.spring.io/spring-boot/docs/2.2.6.RELEASE/reference/html/production-ready-features.html#production-ready-endpoints)
