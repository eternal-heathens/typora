###  	Maven简介

Maven 翻译为"专家"、"内行"，是 Apache 下的一个纯 Java 开发的开源项目。基于项目对象模型（缩写：POM）概念，Maven利用一个中央信息片断能管理一个项目的构建、报告和文档等步骤。

Maven 是一个项目管理工具，可以对 Java 项目进行构建、依赖管理。

Maven 也可被用于构建和管理各种项目，例如 C#，Ruby，Scala 和其他语言编写的项目。Maven 曾是 Jakarta 项目的子项目，现为由 Apache 软件基金会主持的独立 Apache 项目。

####  能够帮助开发者完成以下工作：

- 构建
- 文档生成
- 报告
- 依赖
- SCMs
- 发布
- 分发
- 邮件列表

#### 约定配置
* Maven 提倡使用一个共同的标准目录结构，Maven 使用约定优于配置的原则，大家尽可能的遵守这样的目录结构。如下所示：

| 目录                               | 目的                                                         |
| :--------------------------------- | :----------------------------------------------------------- |
| ${basedir}                         | 存放pom.xml和所有的子目录                                    |
| ${basedir}/src/main/java           | 项目的java源代码                                             |
| ${basedir}/src/main/resources      | 项目的资源，比如说property文件，springmvc.xml                |
| ${basedir}/src/test/java           | 项目的测试类，比如说Junit代码                                |
| ${basedir}/src/test/resources      | 测试用的资源                                                 |
| ${basedir}/src/main/webapp/WEB-INF | web应用文件目录，web项目的信息，比如存放web.xml、本地图片、jsp视图页面 |
| ${basedir}/target                  | 打包输出目录                                                 |
| ${basedir}/target/classes          | 编译输出目录                                                 |
| ${basedir}/target/test-classes     | 测试编译输出目录                                             |
| Test.java                          | Maven只会自动运行符合该命名规则的测试类                      |
| ~/.m2/repository                   | Maven默认的本地仓库目录位置                                  |

####  Maven 特点

- 项目设置遵循统一的规则。
- 任意工程中共享。
- 依赖管理包括自动更新。
- 一个庞大且不断增长的库。
- 可扩展，能够轻松编写 Java 或脚本语言的插件。
- 只需很少或不需要额外配置即可即时访问新功能。
- **基于模型的构建** − Maven能够将任意数量的项目构建到预定义的输出类型中，如 JAR，WAR 或基于项目元数据的分发，而不需要在大多数情况下执行任何脚本。
- **项目信息的一致性站点** − 使用与构建过程相同的元数据，Maven 能够生成一个网站或PDF，包括您要添加的任何文档，并添加到关于项目开发状态的标准报告中。
- **发布管理和发布单独的输出** − Maven 将不需要额外的配置，就可以与源代码管理系统（如 Subversion 或 Git）集成，并可以基于某个标签管理项目的发布。它也可以将其发布到分发位置供其他项目使用。Maven 能够发布单独的输出，如 JAR，包含其他依赖和文档的归档，或者作为源代码发布。
- **向后兼容性** − 您可以很轻松的从旧版本 Maven 的多个模块移植到 Maven 3 中。
- 子项目使用父项目依赖时，正常情况子项目应该继承父项目依赖，无需使用版本号。
- **并行构建** − 编译的速度能普遍提高20 - 50 %。
- **更好的错误报告** − Maven 改进了错误报告，它为您提供了 Maven wiki 页面的链接，您可以点击链接查看错误的完整描述。


### Maven POM
POM( Project Object Model，项目对象模型 ) 是 Maven 工程的基本工作单元，是一个XML文件，包含了项目的基本信息，用于描述项目如何构建，声明项目依赖，等等。

执行任务或目标时，Maven 会在当前目录中查找 POM。它读取 POM，获取所需的配置信息，然后执行目标。

POM 中可以指定以下配置：

* 项目依赖
* 插件
* 执行目标
* 项目构建 profile
* 项目版本
* 项目开发者列表
* 相关邮件列表信息

在创建 POM 之前，我们首先需要描述项目组 (groupId), 项目的唯一ID。

```
<project xmlns = "http://maven.apache.org/POM/4.0.0"    
xmlns:xsi = "http://www.w3.org/2001/XMLSchema-instance"    
xsi:schemaLocation = "http://maven.apache.org/POM/4.0.0    
http://maven.apache.org/xsd/maven-4.0.0.xsd">     
<!-- 模型版本 -->    
<modelVersion>4.0.0</modelVersion>    
<!-- 公司或者组织的唯一标志，并且配置时生成的路径也是由此生成， 如com.companyname.project-group，maven会将该项目打成的jar包放本地路径：/com/companyname/project-group -->    
<groupId>com.companyname.project-group</groupId>     
<!-- 项目的唯一ID，一个groupId下面可能多个项目，就是靠artifactId来区分的 -->    <artifactId>project</artifactId>     
<!-- 版本号 -->   
<version>1.0</version> 
</project>
```
maven采用坐标来唯一定位一个组件，maven的坐标由以下几个部分组成。

| 节点         | 描述                                                         |
| :----------- | :----------------------------------------------------------- |
| project      | 工程的根标签。                                               |
| modelVersion | 模型版本需要设置为 4.0。                                     |
| groupId      | 这是工程组的标识。它在一个组织或者项目中通常是唯一的。例如，一个银行组织 com.companyname.project-group 拥有所有的和银行相关的项目。 |
| artifactId   | 这是工程的标识。它通常是工程的名称。例如，消费者银行。groupId 和 artifactId 一起定义了 artifact 在仓库中的位置。 |
| version      | 这是工程的版本号。在 artifact 的仓库中，它用来区分不同的版本。例如：`com.company.bank:consumer-banking:1.0 com.company.bank:consumer-banking:1.1` |

### 父（Super）POM

父（Super）POM是 Maven 默认的 POM。所有的 POM 都继承自一个父 POM（无论是否显式定义了这个父 POM）。父 POM 包含了一些可以被继承的默认设置。因此，当 Maven 发现需要下载 POM 中的 依赖时，它会到 Super POM 中配置的默认仓库 http://repo1.maven.org/maven2 去下载。



接下来我们创建目录 MVN/project，在该目录下创建 pom.xml，内容如下：
```
<project xmlns = "http://maven.apache.org/POM/4.0.0"   
xmlns:xsi = "http://www.w3.org/2001/XMLSchema-instance"   
xsi:schemaLocation = "http://maven.apache.org/POM/4.0.0    
http://maven.apache.org/xsd/maven-4.0.0.xsd">     
<!-- 模型版本 -->   
<modelVersion>4.0.0</modelVersion>  
<!-- 公司或者组织的唯一标志，并且配置时生成的路径也是由此生成， 如com.companyname.project-group，maven会将该项目打成的jar包放本地路径：/com/companyname/project-group -->   
<groupId>com.companyname.project-group</groupId>   
<!-- 项目的唯一ID，一个groupId下面可能多个项目，就是靠artifactId来区分的 -->  <artifactId>project</artifactId>     
<!-- 版本号 -->  
<version>1.0</version> 
</project>

若出现确实jar,则输入对应版本的dependencies
<dependencies>
    <dependency>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.2.0</version>
    </dependency>
</dependencies>
```

在命令控制台，进入 MVN/project 目录，执行以下命令：

```
C:\MVN\project>mvn help:effective-pom
```

Maven 将会开始处理并显示 effective-pom。



### 构建生命周期

Maven 有以下三个标准的生命周期：

- **clean**：项目清理的处理
- **default(或 build)**：项目部署的处理
- **site**：项目站点文档创建的处理

#### bulid 生命周期

一个典型的 Maven 构建（build）生命周期是由以下几个阶段的序列组成的：

![image-20200317165436293](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200317165436293.png)

| 阶段          | 处理     | 描述                                                     |
| :------------ | :------- | :------------------------------------------------------- |
| 验证 validate | 验证项目 | 验证项目是否正确且所有必须信息是可用的                   |
| 编译 compile  | 执行编译 | 源代码编译在此阶段完成                                   |
| 测试 Test     | 测试     | 使用适当的单元测试框架（例如JUnit）运行测试。            |
| 包装 package  | 打包     | 创建JAR/WAR包如在 pom.xml 中定义提及的包                 |
| 检查 verify   | 检查     | 对集成测试的结果进行检查，以保证质量达标                 |
| 安装 install  | 安装     | 安装打包的项目到本地仓库，以供其他项目使用               |
| 部署 deploy   | 部署     | 拷贝最终的工程包到远程仓库中，以共享给其他开发人员和工程 |



#### Clean 生命周期

当我们执行 mvn post-clean 命令时，Maven 调用 clean 生命周期，它包含以下阶段：

- pre-clean：执行一些需要在clean之前完成的工作
- clean：移除所有上一次构建生成的文件
- post-clean：执行一些需要在clean之后立刻完成的工作

#### Default (Build) 生命周期

这是 Maven 的主要生命周期，被用于构建应用，包括下面的 23 个阶段：

| 生命周期阶段                                | 描述                                                         |
| :------------------------------------------ | :----------------------------------------------------------- |
| validate（校验）                            | 校验项目是否正确并且所有必要的信息可以完成项目的构建过程。   |
| initialize（初始化）                        | 初始化构建状态，比如设置属性值。                             |
| generate-sources（生成源代码）              | 生成包含在编译阶段中的任何源代码。                           |
| process-sources（处理源代码）               | 处理源代码，比如说，过滤任意值。                             |
| generate-resources（生成资源文件）          | 生成将会包含在项目包中的资源文件。                           |
| process-resources （处理资源文件）          | 复制和处理资源到目标目录，为打包阶段最好准备。               |
| compile（编译）                             | 编译项目的源代码。                                           |
| process-classes（处理类文件）               | 处理编译生成的文件，比如说对Java class文件做字节码改善优化。 |
| generate-test-sources（生成测试源代码）     | 生成包含在编译阶段中的任何测试源代码。                       |
| process-test-sources（处理测试源代码）      | 处理测试源代码，比如说，过滤任意值。                         |
| generate-test-resources（生成测试资源文件） | 为测试创建资源文件。                                         |
| process-test-resources（处理测试资源文件）  | 复制和处理测试资源到目标目录。                               |
| test-compile（编译测试源码）                | 编译测试源代码到测试目标目录.                                |
| process-test-classes（处理测试类文件）      | 处理测试源码编译生成的文件。                                 |
| test（测试）                                | 使用合适的单元测试框架运行测试（Juint是其中之一）。          |
| prepare-package（准备打包）                 | 在实际打包之前，执行任何的必要的操作为打包做准备。           |
| package（打包）                             | 将编译后的代码打包成可分发格式的文件，比如JAR、WAR或者EAR文件。 |
| pre-integration-test（集成测试前）          | 在执行集成测试前进行必要的动作。比如说，搭建需要的环境。     |
| integration-test（集成测试）                | 处理和部署项目到可以运行集成测试环境中。                     |
| post-integration-test（集成测试后）         | 在执行集成测试完成后进行必要的动作。比如说，清理集成测试环境。 |
| verify （验证）                             | 运行任意的检查来验证项目包有效且达到质量标准。               |
| install（安装）                             | 安装项目包到本地仓库，这样项目包可以用作其他本地项目的依赖。 |
| deploy（部署）                              | 将最终的项目包复制到远程仓库中与其他开发者和项目共享。       |

* 在构建环境中，使用下面的调用来纯净地构建和部署项目到共享仓库中

```
mvn clean deploy
```

#### site生命周期

Maven Site 插件一般用来创建新的报告文档、部署站点等。

- pre-site：执行一些需要在生成站点文档之前完成的工作
- site：生成项目的站点文档
- post-site： 执行一些需要在生成站点文档之后完成的工作，并且为部署做准备
- site-deploy：将生成的站点文档部署到特定的服务器上

#### Plugin 和Goal

plugin是一组相关的Goal的集合:

![img](https://img-blog.csdn.net/20140809130246095?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3BhY2V3YWxrbWFu/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

Maven的LifeCycle与Goal之间的关系：(phase :阶段)

![img](https://img-blog.csdn.net/20140809130117312?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3BhY2V3YWxrbWFu/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

当packaging不同时，maven default生命周期绑定到不同的plugins的不同的goal上面。当packaging为jar时，maven default lifeCycle与各个plugins默认的绑定关系：

![img](https://img-blog.csdn.net/20140809130345936?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3BhY2V3YWxrbWFu/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

###  构建配置文件

#### 构建配置文件的类型

（USER_HOME ->F:\apache-maven-3.6.3）

| 类型                  | 在哪定义                                                     |
| :-------------------- | :----------------------------------------------------------- |
| 项目级（Per Project） | 定义在项目的POM文件pom.xml中                                 |
| 用户级 （Per User）   | 定义在Maven的设置xml文件中 (%USER_HOME%/.m2/settings.xml)    |
| 全局（Global）        | 定义在 Maven 全局的设置 xml 文件中 (%M2_HOME%/conf/settings.xml) |

#### 配置文件激活

Maven的构建配置文件可以通过多种方式激活。

- 使用命令控制台输入显式激活。

  >  profile 可以让我们定义一系列的配置信息，然后指定其激活条件。这样我们就可以定义多个 profile，然后每个 profile 对应不同的激活条件和配置信息，从而达到不同环境使用不同配置信息的效果。

- 通过 maven 设置。

  >打开 **%USER_HOME%/.m2** 目录下的 **settings.xml** 文件，配置 setting.xml 文件，增加 <activeProfiles>属性：

- 基于环境变量（用户或者系统变量）。

- 操作系统设置（比如说，Windows系列）。

- 文件的存在或者缺失。


### 仓库

#### 简介

* Maven 仓库是项目中依赖的第三方库，这个库所在的位置叫做仓库。
* 在 Maven 中，任何一个依赖、插件或者项目构建的输出，都可以称之为构件。
> Maven 仓库能帮助我们管理构件（主要是JAR），它就是放置所有JAR文件（WAR，ZIP，POM等等）的地方。
Maven 仓库有三种类型：

本地（local）
中央（central）
远程（remote）



#### 本地仓库	

每个用户在自己的用户目录下都有一个路径名为 .m2/respository/ 的仓库目录。

Maven 本地仓库默认被创建在 %USER_HOME% 目录下。要修改默认位置，在 %M2_HOME%\conf 目录中的 Maven 的 settings.xml 文件中定义另一个路径。

#### 中央仓库

中央仓库包含了绝大多数流行的开源Java构件，以及源码、作者信息、SCM、信息、许可证信息等。一般来说，简单的Java项目依赖的构件都可以在这里下载到。

中央仓库的关键概念：

- 这个仓库由 Maven 社区管理。
- 不需要配置。
- 需要通过网络才能访问。

#### 远程仓库
“远程仓库”是相对“本地仓库”来说的，不是“本地”的，就都是远程，不管是局域网还是互联网上的，远程仓库主要有如下几种类型：

1)        maven的central仓库，在Super POM（$M2_HOME\lib\maven-model-builder-x.x.jar\org\apache\maven\model\pom-4.0.0.xml）中定义；

2)        其他公共仓库：

a)        Java.net Maven的库；

b)        JBoss Maven库。

3)        私服：远程仓库的一种，采用maven仓库管理软件（例如nexus）在本地局域网搭建的maven仓库。

如果 Maven 在中央仓库中也找不到依赖的文件，它会停止构建过程并输出错误信息到控制台。为避免这种情况，Maven 提供了远程仓库的概念，它是开发人员自己定制仓库，包含了所需要的代码库或者其他工程中用到的 jar 文件。

举例说明，使用下面的 pom.xml，Maven 将从远程仓库中下载该 pom.xml 中声明的所依赖的（在中央仓库中获取不到的）文件。

```
<project xmlns="http://maven.apache.org/POM/4.0.0"    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0    http://maven.apache.org/xsd/maven-4.0.0.xsd">   
<modelVersion>4.0.0</modelVersion>  
<groupId>com.companyname.projectgroup</groupId>  
<artifactId>project</artifactId>    
<version>1.0</version>   
<dependencies>       
	<dependency>          
		<groupId>com.companyname.common-lib</groupId>  
		<artifactId>common-lib</artifactId>         
		<version>1.0.0</version>       
	</dependency>   
<dependencies>    
<repositories>  
	<repository>       
		<id>companyname.lib1</id>  
		<url>http://download.companyname.org/maven2/lib1</url>     
	</repository>    
	<repository>   
		<id>companyname.lib2</id>         
		<url>http://download.companyname.org/maven2/lib2</url>  
	</repository>  
</repositories>
</project>
```

##### 私服

![img](https://img-blog.csdn.net/20140809130657957?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3BhY2V3YWxrbWFu/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

私服的作用：

1)        节省网络带宽：每个组件只会从Internet上下载一次（首次请求时下载），然后，就保存在私服的缓存中了，团队其他成员后续的请求，直接从私服下载；没有互联网连接的时候，只要组件已经被私服缓存，就能够不访问Internet从私服下载组件；

2)        加速maven的构建：对SNAPSHOT版本不停的检查更新，会耗费较多的Internet网络流量；

3)        可部署第三方组件至私服，避免重复下载，例如oracle的JDBC驱动，公司内部的组件，如oscar的JDBC驱动和集群的驱动；

4)        可部署团队开发的产品组件至私服，避免组件在团队成员之间不规范的传递，特别是SNAPSHOT版本的传递。

```
 如何配置项目使用其他仓库（非central仓库）？

1)        在pom.xml使用

<respositories>

         <respository>

                   <id></id>

<name></name>

<url></url>

<layout></layout>

         </respository>

</ respositories >

声明；

2)        需要验证的远程仓库，要在settings.xml文件中使用

<servers>

         <server>

                   <id></id>

                   <user></user>

                   <password></password>

</server>

</servers>

声明，并且保持id与pom.xml中声明的respository的id一致。

 

使用nexus私服来mirror central仓库，这样所有本应该从central仓库下载的组件，都直接从该mirror下载：

<mirrors>

         <mirror>

                   <!--Thissends everything else to /public -->

                   <id>nexus</id>

                   <mirrorOf>*</mirrorOf>

                   <url>http://192.168.1.231:8080/nexus/content/groups/public</url>

         </mirror>

</mirrors>

 

部署组件到仓库：在pom.xml文件中采用< distributionManagement >要部署的release和snapshot版本的组件分别到哪个仓库地址：

<distributionManagement>

         <snapshotRepository>

                   <id>nexus-snapshots</id>

                   <name>Server231snapshots</name>

                   <url>http://192.168.1.231:8080/nexus/content/repositories/snapshots</url>

         </snapshotRepository>

         <repository>

                   <id>nexus-release</id>

                   <name>Server231releases</name>

                  <url>http://192.168.1.231:8080/nexus/content/repositories/releases</url>

         </repository>

</distributionManagement>
```

#### 依赖搜索顺序

当我们执行 Maven 构建命令时，Maven 开始按照以下顺序查找依赖的库：

- **步骤 1** － 在本地仓库中搜索，如果找不到，执行步骤 2，如果找到了则执行其他操作。
- **步骤 2** － 在中央仓库中搜索，如果找不到，并且有一个或多个远程仓库已经设置，则执行步骤 4，如果找到了则下载到本地仓库中以备将来引用。
- **步骤 3** － 如果远程仓库没有被设置，Maven 将简单的停滞处理并抛出错误（无法找到依赖的文件）。
- **步骤 4** － 在一个或多个远程仓库中搜索依赖的文件，如果找到则下载到本地仓库以备将来引用，否则 Maven 将停止处理并抛出错误（无法找到依赖的文件）。



### 插件

Maven 有以下三个标准的生命周期：

- **clean**：项目清理的处理
- **default(或 build)**：项目部署的处理
- **site**：项目站点文档创建的处理

每个生命周期中都包含着一系列的阶段(phase)。这些 phase 就相当于 Maven 提供的统一的接口，然后这些 phase 的实现由 Maven 的插件来完成。

Maven 生命周期的每一个阶段的具体实现都是由 Maven 插件实现的。
>  我们在输入 mvn 命令的时候 比如 mvn clean，clean 对应的就是 Clean 生命周期中的 clean 阶段。但是 clean 的具体操作是由 maven-clean-plugin 来实现的。

Maven 实际上是一个依赖插件执行的框架，每个任务实际上是由插件完成。Maven 插件通常被用来：

- 创建 jar 文件
- 创建 war 文件
- 编译代码文件
- 代码单元测试
- 创建工程文档
- 创建工程报告

插件通常提供了一个目标的集合，并且可以使用下面的语法执行：

```
<code>mvn [plugin-name]:[goal-name]</code>
```

例如，一个 Java 工程可以使用 maven-compiler-plugin 的 compile-goal 编译，使用以下命令：

```
<code>mvn compiler:compile</code>
```

#### 插件类型

Maven 提供了下面两种类型的插件：

| 类型              | 描述                                               |
| :---------------- | :------------------------------------------------- |
| Build plugins     | 在构建时执行，并在 pom.xml 的 元素中配置。         |
| Reporting plugins | 在网站生成过程中执行，并在 pom.xml 的 元素中配置。 |

下面是一些常用插件的列表：

| 插件     | 描述                                                |
| :------- | :-------------------------------------------------- |
| clean    | 构建之后清理目标文件。删除目标目录。                |
| compiler | 编译 Java 源文件。                                  |
| surefile | 运行 JUnit 单元测试。创建测试报告。                 |
| jar      | 从当前工程中构建 JAR 文件。                         |
| war      | 从当前工程中构建 WAR 文件。                         |
| javadoc  | 为工程生成 Javadoc。                                |
| antrun   | 从构建过程的任意一个阶段中运行一个 ant 任务的集合。 |

### 构建JAVA项目

* Maven使用原型 **archetype** 插件创建项目。要创建一个简单的 Java 应用，我们将使用 **maven-archetype-quickstart** 插件。

```
mvn archetype:generate -DgroupId=com.companyname.bank -DartifactId=consumerBanking -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

参数说明：
* DgourpId: 组织名，公司网址的反写 + 项目名称
* DartifactId: 项目名-模块名
* DarchetypeArtifactId: 指定 ArchetypeId，maven-archetype-quickstart，创建一个简单的 Java 应用
* DinteractiveMode: 是否使用交互模式
```

```
Unable to add module to the current project as it is not of packaging type 'pom'
问题原因：project里有自己设置的pom.xml和创建项目的结构不一致，导致理解不了插件
```

###  引入外部依赖

* pom.xml 的 dependencies 列表列出了我们的项目需要构建的所有外部依赖项。

* 要添加依赖项，我们一般是先在 src 文件夹下添加 lib 文件夹，然后将你工程需要的 jar 文件复制到 lib 文件夹下，然后添加以下依赖到 pom.xml 文件中：(eg:添加Idapjdk.jar)


```
<dependencies>
 <!-- 在这里添加你的依赖 -->
    <dependency>
        <groupId>ldapjdk</groupId>  <!-- 库名称，也可以自定义 -->
        <artifactId>ldapjdk</artifactId><!--库名称，也可以自定义-->
        <version>1.0</version> <!--版本号-->
        <scope>system</scope> <!--作用域-->
        <systemPath>${basedir}\src\lib\ldapjdk.jar</systemPath> <!--项目根目录下的lib文件夹下-->
    </dependency> 
</dependencies>
```



### 项目模板

* archetype 也就是原型，是一个 Maven 插件，准确说是一个项目模板，它的任务是根据模板创建一个项目结构

#### 使用项目模板

让我们打开命令控制台，跳转到 C:\> MVN 目录并执行以下 mvn 命令:

```
C:\MVN> mvn archetype:generate 
```

1. Maven 将开始处理，并要求选择所需的原型:

按下 **Enter** 选择默认选项 (203:maven-archetype-quickstart)。

2. 询问原型的版本

按下 **Enter** 选择默认选项 (6:maven-archetype-quickstart:1.1)

3. 询问项目细节
4. 要求确认项目细节，按 **Enter** 或按 Y
5. 创建

### 项目文档

修改 pom.xml，添加以下配置（如果没有的话）：

```
<project>  
... 
<build> 
<pluginManagement>     
	<plugins>    
		<plugin>         
			<groupId>org.apache.maven.plugins</groupId>    
			<artifactId>maven-site-plugin</artifactId>        
			<version>3.3</version>     
		</plugin>       
		<plugin>           
			<groupId>org.apache.maven.plugins</groupId>     
			<artifactId>maven-project-info-reports-plugin</artifactId>       
			<version>2.7</version>       
		</plugin>  
	</plugins>   
</pluginManagement>
</build>  
...
</project>

> 不然运行 **mvn site** 命令时出现 **java.lang.NoClassDefFoundError: org/apache/maven/doxia/siterenderer/DocumentContent** 的问题， 这是由于 maven-site-plugin 版本过低，升级到 3.3+ 即可。
```

Maven 使用一个名为 [Doxia](http://maven.apache.org/doxia/index.html)的文档处理引擎来创建文档，它能将多种格式的源码读取成一种通用的文档模型。要为你的项目撰写文档，你可以将内容写成下面几种常用的，可被 Doxia 转化的格式。

| 格式名 | 描述                     | 参考                                                     |
| :----- | :----------------------- | :------------------------------------------------------- |
| Apt    | 纯文本文档格式           | http://maven.apache.org/doxia/references/apt-format.html |
| Xdoc   | Maven 1.x 的一种文档格式 | http://jakarta.apache.org/site/jakarta-site2.html        |
| FML    | FAQ 文档适用             | http://maven.apache.org/doxia/references/fml-format.html |
| XHTML  | 可扩展的 HTML 文档       | http://en.wikipedia.org/wiki/XHTML                       |

### 快照（SNAPSHOT）

在Maven依赖管理中，唯一标识一个依赖项是由该依赖项的三个属性构成的，分别是groupId、artifactId以及version。

* 这三个属性可以唯一确定一个组件（Jar包或者War包）。

其实在Nexus仓库中，一个仓库一般分为public(Release)仓和SNAPSHOT仓，前者存放正式版本，后者存放快照版本。

>nexus是一个强大的maven仓库管理器,它极大的简化了本地内部仓库的维护和外部仓库的访问. 
>nexus是一套开箱即用的系统不需要数据库,它使用文件系统加Lucene来组织数据 
>nexus使用ExtJS来开发界面,利用Restlet来提供完整的REST APIs,通过IDEA和Eclipse集成使用 
>nexus支持webDAV与LDAP安全身份认证. 
>nexus提供了强大的仓库管理功能,构件搜索功能,它基于REST,友好的UI是一个extjs的REST客户端,占用较少的内存,基于简单文件系统而非数据库. 

快照版本和正式版本的主要区别在于，本地获取这些依赖的机制有所不同。

#### 正式版本

假设你依赖一个库的正式版本，构建的时候构建工具会先在本次仓库中查找是否已经有了这个依赖库，如果没有的话才会去远程仓库中去拉取,以后再次构建都不会去访问远程仓库了。

**所以如果你修改了代码，向远程仓库中发布了新的软件包，那么依赖这个库的项目就无法得到最新更新。**

你只有在重新发布的时候升级版本，然后通知依赖该库的项目组也修改依赖版本为Junit-4.11,这样才能使用到你最新添加的功能。

#### SNAPSHOT版本

将需要依赖的项目的snapshot版本写入pom.xml中，每次本项目构建时，会优先去远程仓库中查看是否有最新的SNAPSHOT.jar，如果有则下载下来使用。

即使本地仓库中已经有了SNAPSHOT.jar，它也会尝试去远程仓库中查看同名的jar是否是最新的。

> 在配置Maven的Repository的时候中有个配置项，可以配置对于SNAPSHOT版本向远程仓库中查找的频率。频率共有四种，分别是always、daily、interval、never。
>
> 当本地仓库中存在需要的依赖项目时，always是每次都去远程仓库查看是否有更新，daily是只在第一次的时候查看是否有更新，当天的其它时候则不会查看；
>
> interval允许设置一个分钟为单位的间隔时间，在这个间隔时间内只会去远程仓库中查找一次.
>
> never是不会去远程仓库中查找（这种就和正式版本的行为一样了）。

### 自动化构建

自动化**构建**定义了这样一种场景: 在一个项目成功构建完成后，其相关的依赖工程即开始构建，这样可以保证其依赖项目的稳定。（**测试项目稳定性**）

可以使用两种方式：

* 添加在项目的pom.xml 中

>  在 bus-core-api 项目的 pom 文件中添加一个 post-build 目标操作来启动 app-web-ui 和 app-desktop-ui 项目的构建。

- 使用持续集成（CI） 服务器，比如 Hudson，来自行管理构建自动化。

> 如果使用 CI 服务器更，我们每次的一个新项目,Hudson 将会借助 Maven 的依赖管理功能实现工程的自动化创建。

Hudson 把每个项目构建当成一次任务。

在一个项目的代码提交到 SVN （或者任何映射到 Hudson 的代码管理工具）后，Hudson 将开始项目的构建任务，并且一旦此构建任务完成，Hudson 将自动启动其他依赖的构建任务（其他依赖项目的构建）。

### 依赖管理

#### 可传递性依赖发现

Maven 可以避免去搜索所有所需库的需求。Maven 通过读取项目文件（pom.xml），找出它们项目之间的依赖关系。

| 依赖调节 | 决定当多个手动创建的版本同时出现时，哪个依赖版本将会被使用。 如果两个依赖版本在依赖树里的深度是一样的时候，第一个被声明的依赖将会被使用。 |
| -------- | ------------------------------------------------------------ |
| 依赖管理 | 直接的指定手动创建的某个版本被使用。例如当一个工程 C 在自己的依赖管理模块包含工程 B，即 B 依赖于 A， 那么 A 即可指定在 B 被引用时所使用的版本。 |
| 依赖范围 | 包含在构建过程每个阶段的依赖。                               |
| 依赖排除 | 任何可传递的依赖都可以通过 "exclusion" 元素被排除在外。举例说明，A 依赖 B， B 依赖 C，因此 A 可以标记 C 为 "被排除的"。 |
| 依赖可选 | 任何可传递的依赖可以被标记为可选的，通过使用 "optional" 元素。例如：A 依赖 B， B 依赖 C。因此，B 可以标记 C 为可选的， 这样 A 就可以不再使用 C。 |

传递依赖发现可以通过使用如下的依赖范围来得到限制：

| 范围     | 描述                                                         |
| :------- | :----------------------------------------------------------- |
| 编译阶段 | 该范围表明相关依赖是只在项目的类路径下有效。默认取值。       |
| 供应阶段 | 该范围表明相关依赖是由运行时的 JDK 或者 网络服务器提供的。   |
| 运行阶段 | 该范围表明相关依赖在编译阶段不是必须的，但是在执行阶段是必须的。 |
| 测试阶段 | 该范围表明相关依赖只在测试编译阶段和执行阶段。               |
| 系统阶段 | 该范围表明你需要提供一个系统路径。                           |
| 导入阶段 | 该范围只在依赖是一个 pom 里定义的依赖时使用。同时，当前项目的POM 文件的 部分定义的依赖关系可以取代某特定的 PO |

- ![img](https://www.runoob.com/wp-content/uploads/2018/09/dependency_graph.jpg)
- 公共的依赖可以使用 pom 父的概念被统一放在一起。App-Data-lib 和 App-Core-lib 项目的依赖在 Root 项目里列举了出来（参考 Root 的包类型，它是一个 POM）.
- 没有必要在 App-UI-W 里声明 Lib1, lib2, Lib3 是它的依赖。 Maven 通过使用可传递的依赖机制来实现该细节。

### 自动化部署

#### 部署过程

- 将所的项目代码提交到 SVN 或者代码库中并打上标签。

  > **svn import -m 'project initialization' https://192.168.1.100:8443/svn/myapp/trunk** 
  >
  > 将代码提交到SVN仓库中

- 从 SVN 上下载完整的源代码。

- 构建应用。

- 存储构建输出的 WAR 或者 EAR 文件到一个常用的网络位置下。

- 从网络上获取文件并且部署文件到生产站点上。

- 更新文档并且更新应用的版本号。

其中的步骤都有人为操作的话，可能会导致混乱和错误，所以采用以下方案实现自动化部署：

- 使用 Maven 构建和发布项目
- 使用 SubVersion， 源码仓库来管理源代码
- 使用远程仓库管理软件（Jfrog或者Nexus） 来管理项目二进制文件。
#### 配置连接SVN的pom文件

```
<!--git 远程仓库配置-->
<scm>
        <connection>scm:git:http://项目git地址</connection>
        <url>项目git地址（不加'.git后缀'）</url>
        <developerConnection>scm:项目git地址</developerConnection>
 </scm>

 <!--构建配置-->
 <build>
        <plugins>
            <plugin> <!--release:perform将fork一个新的Maven实例来构建检出项目。这个新的Maven实例将使用与运行release:perform目标相同的系统配置和Maven配置文件。由于没有pom.xml，您应该使用目标的完全限定名称来确保使用正确版本的maven-release-plugin。通过在releaseProfiles参数中设置逗号分隔的配置文件名称列表-->
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-release-plugin</artifactId>
                <version>2.5.3</version>
                <configuration>
                    <tagNameFormat>v@{project.version}</tagNameFormat>
                    <autoVersionSubmodules>true</autoVersionSubmodules>
                </configuration>
            </plugin>
        </plugins>
    </build>
   
<!--分发配置-->
    <distributionManagement>
        <repository>
            <id>deploymentRepo</id>
            <name>releases</name>
            <url>http://somehost/repository/maven-releases/</url>
            <uniqueVersion>true</uniqueVersion>
        </repository>
        <snapshotRepository>
            <id>deploymentRepo</id>
            <name>snapshots</name>
            <url>http://somehost/repository/maven-snapshots/</url>
            <uniqueVersion>true</uniqueVersion>
        </snapshotRepository>
    </distributionManagement>
```
由于会将构建的包分发到仓库，需要在maven的配置文件setting.xml下加入权限配置：
```
<server>
      <id>your id</id>
      <username>your username</username>
      <password>your pass</password>
</server>
```
| 元素节点   | 描述                                                         |
| :--------- | :----------------------------------------------------------- |
| SCM        | 配置 SVN 的路径，Maven 将从该路径下将代码取下来。            |
| repository | w构建的 WAR 或 EAR 或JAR 文件的位置，或者其他源码构建成功后生成的构件的存储位置。 |
| Plugin     | 配置 maven-release-plugin 插件来实现自动部署过程。           |

####  发布到SVN并部署到仓库

**deploy 可以发布snapshot 和realease  ，但realease一般由 maven-realease-plugin交由SVN版本控制再deploy**

Maven 使用 maven-release-plugin 插件来完成以下任务。

```
mvn release:clean
```

清理工作空间，保证最新的发布进程成功进行。

```
mvn release:rollback
```

在上次发布过程不成功的情况下，回滚修改的工作空间代码和配置保证发布过程成功进行。

```
mvn release:prepare
```

执行多种操作：

- 检查本地是否存在还未提交的修改
- 确保没有快照的依赖
- 改变应用程序的版本信息用以发布
- 更新 POM 文件到 SVN
- 运行测试用例
- 提交修改后的 POM 文件
- 为代码在 SVN 上做标记
- 增加版本号和附加快照以备将来发布
- 提交修改后的 POM 文件到 SVN

```
mvn release:perform
```

- 这一步，会去一个临时目录中，把 commit 的代码抓出来 build 之后 deploy。

将代码切换到之前做标记的地方，运行 Maven 部署目标来部署 WAR 文件或者构建相应的结构到仓库里。

打开命令终端，进入到 C:\ > MVN >bus-core-api 目录下，然后执行如下的 mvn 命令。

![image-20200323121049527](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200323121049527.png)

### Web  应用

我们可以使用 maven-archetype-webapp 插件来创建一个简单的 Java web 应用。

Maven 目录结构是标准的，各个目录作用如下表所示:

| 文件夹结构              | 描述                                  |
| :---------------------- | :------------------------------------ |
| trucks                  | 包含 src 文件夹和 pom.xml 文件。      |
| src/main/webapp         | 包含 index.jsp 文件和 WEB-INF 文件夹. |
| src/main/webapp/WEB-INF | 包含 web.xml 文件                     |
| src/main/resources      | 包含图片、properties资源文件。        |

### Eclipse

Eclipse 提供了一个很好的插件 [**m2eclipse** ](http://www.eclipse.org/m2e/)，该插件能将 Maven 和 Eclipse 集成在一起。

下面列出 m2eclipse 的一些特点：

- 可以在 Eclipse 环境上运行 Maven 的目标文件。
- 可以使用其自带的控制台在 Eclipse 中直接查看 Maven 命令的输出。
- 可以在 IDE 下更新 Maven 的依赖关系。
- 可以使用 Eclipse 开展 Maven 项目的构建。
- Eclipse 基于 Maven 的 pom.xml 来实现自动化管理依赖关系。
- 它解决了 Maven 与 Eclipse 的工作空间之间的依赖，而不需要安装到本地 Maven 的存储库（需要依赖项目在同一个工作区）。
- 它可以自动地从远端的 Maven 库中下载所需要的依赖以及源码。
- 它提供了向导，为建立新 Maven 项目，pom.xml 以及在已有的项目上开启 Maven 支持。
- 它提供了远端的 Maven 存储库的依赖的快速搜索。

### IntelliJ

### Multi-Module Project和Maven的继承机制

项目复杂时，需要将项目拆分成多个子模块，能降低耦合，进行并行开发。maven通过<parent>和<module>来定义父子关系，parent的packaging定义成pom：
```
<project>

         <modelVersion>4.0.0</modelVersion>
    
         <groupId>com.geni_sage</groupId>
    
         <artifactId>gdme</artifactId>
    
         <packaging>pom</packaging>

 


         <modules>
    
                   <module>xxx</module>
    
                   <module>yyy</module>

		</modules>

</project>
```
子类通过<parent>声明是谁的module：
```
<parent>

         <groupId>com.geni_sage</groupId>
    
         <artifactId>gdme</artifactId>
    
         <version>5.0-SNAPSHOT</version>

</parent>
```
