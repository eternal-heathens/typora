### war exploded 与 war

（1）war模式这种可以称之为是发布模式，看名字也知道，这是先打成war包，再发布；

（2）war exploded模式是直接把文件夹、jsp页面 、classes等等移到Tomcat 部署文件夹里面，进行加载部署。因此这种方式支持热部署，一般在开发的时候也是用这种方式。

（3）在平时开发的时候，使用热部署的话，应该对Tomcat进行相应的设置，这样的话修改的jsp界面什么的东西才可以及时的显示出来。

（4）通过`war模式`是最终打包部署到Tomcat的位置。`war exploded模式`,同样进行设置终，得到的是我这个项目的位置，其实就是这个项目target的位置。

### artifact和classpath

根据artifacts中的配置，项目会被复制到target/中的某个位置，而classpath，也就是target/项目/WEB-INF/classes/。

### profile

　profile可以让我们定义一系列的配置信息，然后指定其激活条件。这样我们就可以定义多个profile，然后每个profile对应不同的激活条件和配置信息，从而达到不同环境使用不同配置信息的效果。

**profile定义的位置**

（1）  针对于特定项目的profile配置我们可以定义在该**项目**的pom.xml中。（下面举例是这种方式）

（2）  针对于特定用户的profile配置，我们可以在用户的settings.xml文件中定义profile。该文件在用户家目录下的“.m2”目录下。

（3）  全局的profile配置。全局的profile是定义在Maven安装目录下的“conf/settings.xml”文件中的。

``` xml
<profiles>
    <profile>
        <!-- 本地开发环境 -->
        <id>dev</id>
        <properties>
            <profiles.active>dev</profiles.active>
        </properties>
        <activation>
            <!-- 设置默认激活这个配置 -->
            <activeByDefault>true</activeByDefault>
        </activation>
    </profile>
    <profile>
        <!-- 发布环境 -->
        <id>release</id>
        <properties>
            <profiles.active>release</profiles.active>
        </properties>
    </profile>
    <profile>
        <!-- 测试环境 -->
        <id>beta</id>
        <properties>
            <profiles.active>beta</profiles.active>
        </properties>
    </profile>
</profiles>
```

**使用-P参数显示激活一个profile**

　　1. 当我们在进行Maven操作时就可以使用-P参数显示的指定当前激活的是哪一个profile了。比如我们需要在对项目进行打包的时候使用id为dev的profile，我们就可以这样做：

```bash
mvn package –Pdev
```

2. 在开发工具中，如idea 的maven插件里勾选