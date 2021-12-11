# 简介

Distributed Configuration Management Platform（分布式配置管理）

>  https://blog.csdn.net/qinxuefly/article/details/53287189

* 模块架构图

![image-20210926221716299](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/image-20210926221717782.png)

# 客户端使用流程

1. 导入jar包

> ``` xml
>  <dependency>
>                 <groupId>com.baidu.disconf</groupId>
>                 <artifactId>disconf-client</artifactId>
>                 <version> 2.6.30</version>
> </dependency>
> ```

2. **在`classpath.xml`添加Disconf启动支持**

> ```xml
> <!-- 使用disconf必须添加以下配置 -->
> <bean id="disconfMgrBean" class="com.baidu.disconf.client.DisconfMgrBean"
>       destroy-method="destroy">
>     <property name="scanPackage" value="com.sf.o2o.dds.dms.admin"/>
> </bean>
> <bean id="disconfMgrBean2" class="com.baidu.disconf.client.DisconfMgrBeanSecond"
>       init-method="init" destroy-method="destroy">
> </bean>
> 
> <!-- 使用托管方式的disconf配置(无代码侵入, 配置更改会自动reload)-->
> <bean id="configproperties_disconf"
>       class="com.baidu.disconf.client.addons.properties.ReloadablePropertiesFactoryBean">
>     <property name="locations">
>         <list>
>             <value>classpath*:dms-admin.properties</value>
>         </list>
>     </property>
> </bean>
> ```

3. **在客户端应用的classpath下新增disconf.properties文件**

> **disconf.properties会在DIsconfMgrBean注册时(实现了BeanDefinitionRegistryPostProcessor接口，成功注册了BeanDefinition，使得之后BeanFactory使用BeanDefinition进行Bean的实例化、注入属性，前置后置处理、实现Aware接口，初始化垫了基础)在FirstScan方法调用时被读取入DisClientConfig对象中**

```	properties
# 是否使用远程配置文件
# true(默认)会从远程获取配置 false则直接获取本地配置
enable.remote.conf=true
#
# 配置服务器的 HOST,用逗号分隔  127.0.0.1:8000,127.0.0.1:8000
#
conf_server_host=127.0.0.1:8080
# 版本, 请采用 X_X_X_X 格式 
version=1_0_0_0
# APP 请采用 产品线_服务名 格式 
app=disconf_demo
# 环境
env=rd
# debug
debug=true
# 忽略哪些分布式配置，用逗号分隔
ignore=
# 获取远程配置 重试次数，默认是3次
conf_server_url_retry_times=1
# 获取远程配置 重试时休眠时间，默认是5秒
conf_server_url_retry_sleep_seconds=1

```

详细设计请参考：

http://disconf.readthedocs.io/zh_CN/latest/design/index.html

4. **配置项注解使用**

![image-20210926222110473](https://raw.githubusercontent.com/eternal-heathens/picgoBeg/master/JavaImages/image-20210926222110473.png)

1. 为这个类定义 @DisconfFile 注解，指定文件名为 code.properties 。
2. 定义域codeError，并使用Eclipse为其自动生成 get&set 方法。
3. 为该域的get方法上添加注解 @DisconfFileItem 。添加标记 name, 表示配置文件中的KEY名，这是必填的
4. 标记associateField是可选的，它表示此get方法相关连的域的名字，如果此标记未填，则系统会自动 分析get方法，猜测其相对应于域名。**强烈建议添加associateField标记，这样就可以避免Eclipse生成的Get/Set方法不符合 Java规范的问题。**
5. 标记它为Spring托管的类 （使用@Service），且 "scope" 都必须是singleton的。
   

**5.配置更新回调**

实现IDisconfUpdate接口，并且该类是由spring管理

注解@DisconfUpdateService， confFileKeys为监控配置文件更新，itemKeys为监控配置项更新

```java
@Slf4j
@Configuration
@Component
@DisconfFile(filename = "producer.kafka.value.json")
@DisconfUpdateService(classes = {KafkaProducerBeanFactory.class})
public class KafkaProducerBeanFactory implements InitializingBean,IDisconfUpdate{
```

若是程序本身：程序不会自动reload配置，需要自己写回调函数(实现IDisconfUpdate接口，并添加DisconfUpdateService注解)。

若仅仅是托管文件本身：

``` xml
<!-- 使用托管方式的disconf配置(无代码侵入, 配置更改会自动reload)-->
    <bean id="configproperties_disconf"
          class="com.baidu.disconf.client.addons.properties.ReloadablePropertiesFactoryBean">
        <property name="locations">
            <list>
                <value>classpath*:dms-admin.properties</value>
            </list>
        </property>
    </bean>
	
    <bean id="propertyConfigurer"
          class="com.baidu.disconf.client.addons.properties.ReloadingPropertyPlaceholderConfigurer">
        <!-- ReloadingPropertyPlaceholderConfigurer 具有 reloadable 的 property bean 扩展了 DefaultPropertyPlaceholderConfigurer 特性如下： 1. 启动时 监控 动态config，并维护它们与相应bean的关系 2. 当动态config变动时，此configurer会进行reload 3. reload 时会 compare config value, and set value for beans-->
        <property name="ignoreResourceNotFound" value="true"/>
        <property name="ignoreUnresolvablePlaceholders" value="true"/>
        <property name="propertiesArray">
            <list>
                <ref bean="configproperties_disconf"/>
            </list>
        </property>
    </bean>
```

# 服务端部署

 Linux：https://blog.csdn.net/qinxuefly/article/details/53287189

windows：https://www.jianshu.com/p/131c56f2a934

# 疑问

**4.1 Disconf怎么做到实时修改？**

Disconf主要是依靠zookeeper的Watch机制来做配置实时修改的,我们都知道ZK是通过目录挂载的方式来做服务的自动注册与发布。客户端启动时注册了一个回调接口,当zk目录发生变化时会回调所有客户端节点,从而做到"实时"更新配置的目的。

**4.2 Disconf怎么做到数据持久化？**

在配置中心添加的配置数据都被持久化到了DB中,每次客户端启动的时候会调用Disconf的Http接口获取最新的配置数据,如果网络不通,默认会重试三次,如果还不通,则抛出异常。如果第一次拉取配置就有问题,作为配置中心来讲是肯定是无解的,需要客户端去解决（一般这种情况是网络问题或者配置中心服务不可用导致）。
 我们这里不需要考虑第一次加载配置就失败的情况.那么问题来了：

- 1.如果第一次加载配置后,配置中心不可用,会影响到客户端吗？答案：不会。因为动态修改的配置已经加载到内存了,是不影响的。
- 2.如果配置中心宕机,加上客户端需要发布新版本面临重启呢？答案：不影响,因为第一次从配置中心拉取配置后会持久化到磁盘固定目录上,如果配置中心不可用,会读取缓存文件的数据(需要做文件防篡改操作)

**4.3 如果ZK宕机会影响配置中心使用吗？**

答案:不会,因为disconf会单独起一个线程做重连操作。

**4.4 Disconf怎么保证同一项目、环境所在节点都修改配置成功。**

答案:没有做这方面的保证。因为客户端连接到配置中心上以后会将机器名挂载到zk目录下,可以通过界面查看配置使用的机器数。

# Disconf自定义功能扩展

- 1 客户端接入功能增强,增加默认配置属性。
- 2 Disconf针对key-value配置属性没有做持久化,当配置中心宕机、客户端重启时无法拉取历史配置信息。
- 3 增强Disconf节点之间配置不同步的问题
- 4 去掉修改配置同步发送功能,改成异步(它采用的是同步发送,非常卡慢)

# 配置时各个bean的作用

**DisconfMgrBean**

此Bean实现了BeanFactoryPostProcessor和PriorityOrdered接口。它的Bean初始化Order是最高优先级的。

因此，当Spring扫描了所有的Bean信息后，在所有Bean初始化（init）之前，DisconfMgrBean的postProcessBeanFactory方法将被调用，在这里，Disconf-Client会进行第一次扫描。

扫描按顺序做了以下几个事情：

1. 初始化Disconf-client自己的配置模块。
2. 初始化Scan模块。
3. 初始化Core模块，并极联初始化Watch，Fetcher，Restful模块。
4. 扫描用户类，整合分布式配置注解相关的静态类信息至配置仓库里。
5. 执行Core模块，从disconf-web平台上下载配置数据：配置文件下载到本地，配置项直接下载。
6. 配置文件和配置项的数据会注入到配置仓库里。
7. 使用watch模块为所有配置关联ZK上的结点。

**DisconfMgrBeanSecond**

DisconfMgrBean的扫描主要是静态数据的初始化，并未涉及到动态数据。DisconfMgrBeanSecond Bean则是将一些动态的数据写到仓库里。

本次扫描按顺序做了以下几个事情：

1. 将配置更新回调实例放到配置仓库里
2. 为配置实例注入值。

* 当DisconfMgrBean第一次扫描时，watcher监控不到相应的类时，但是对应的disconf.user_define_download_dir中再本地存在相应的properties，MgrBeanSecond也能执行注入

**ReloadablePropertiesFactoryBean**

ReloadablePropertiesFactoryBean继承了PropertiesFactoryBean类，它主要做到：

- 托管配置文件至disconf仓库，并下载至本地。
- 解析配置数据传递到 ReloadingPropertyPlaceholderConfigurer

**ReloadingPropertyPlaceholderConfigurer**

ReloadingPropertyPlaceholderConfigurer继承自Spring的配置类PropertyPlaceholderConfigurer，它会在Spring启动时将配置数据与Bean做映射，以便在检查到配置文件更改时，可以实现Bean相关域值的自动注入。

```XML
如果想配置文件，但是不想自动reload，那么该怎么办？
<!-- 使用托管方式的disconf配置(无代码侵入, 配置更改不会自动reload)-->
<bean id="configproperties_no_reloadable_disconf"
      class="com.baidu.disconf.client.addons.properties.ReloadablePropertiesFactoryBean">
    <property name="locations">
        <list>
            <value>myserver.properties</value>
        </list>
    </property>
</bean>

<bean id="propertyConfigurerForProject1"
      class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
    <property name="ignoreResourceNotFound" value="true"/>
    <property name="ignoreUnresolvablePlaceholders" value="true"/>
    <property name="propertiesArray">
        <list>
            <ref bean="configproperties_no_reloadable_disconf"/>
        </list>
    </property>
</bean>
在这里，myserver.properties被disconf托管，当在disconf-web上修改配置文件时，配置文件会被自动下载至本地，但是不会reload到系统里。
```

**ReloadConfigurationMonitor**

它是一个Timer类，定时校验配置是否有更改，进而促发 ReloadingPropertyPlaceholderConfigurer 类来分析要对哪些 Bean实例进行重新注入。

