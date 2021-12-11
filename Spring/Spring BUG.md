* 测试使用和提示不同版本的Junit 

> 注意Junit运行测试中Junit 路径配置的Junit 版本

* 解决java.lang.IllegalStateException: Unable to find a @SpringBootConfiguration, you need to use

> 1. 需要一个启动类
> 2. 虽然你写了启动类但是你的启动类所在的包和单元测试的包不在同一级根目录下。

*  respond context 为空

> dependency引入的东西
>
> 作用：代码编译/运行时所需要的东西
>
> 打包：项目打包后这些东西基本都在（一般都在）。
>
> 例如：JSON工具包GSON（com.google.code.gson），不仅开发时要用，项目运行时也要用，就需要打包进项目中；
>
>  
>
> plugin引入的东西
>
> 作用：插件，作为开发/编译/打包时的一种**辅助工具**
>
> 打包：一般不会打包进项目中。
>
> 例如：使用 maven-source-plugin 插件将API包的源码一起打包，方便发布至Maven仓库，而这个插件不会在打包后的项目中出现。
>
> * 需要添加依赖才能在运行时渲染相应的资源，plugin可以在打码时提供语法提示和打包用

* ModelAttribute()将对象加入到容器中，才能thymeleaf中的th:object需要有关联才能用th:field关联

* getHolder.getKey()返回null

> ```java
> PreparedStatementCreatorFactory preparedStatementCreatorFactory = new PreparedStatementCreatorFactory(
>          "insert into PIZZA (name, createdAt) values (?, ?)",
>          Types.VARCHAR, Types.TIMESTAMP
>  );
> 
> // By default, returnGeneratedKeys = false so change it to true
> preparedStatementCreatorFactory.setReturnGeneratedKeys(true);
> 
>  PreparedStatementCreator psc =
>          preparedStatementCreatorFactory.newPreparedStatementCreator(
>                     Arrays.asList(
>                             pizza.getName(),
>                             new Timestamp(pizza.getCreatedAt().getTime())));
> ```

* thymeleaf中checkbox不能互传具体的对象

* JUnit没加@TEST的测试类显示还是为普通类

  