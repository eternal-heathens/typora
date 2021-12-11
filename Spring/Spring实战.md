Spring 起步

### 什么是Spring

* Spring 核心： 提供了一个**容器(container)**，通常称为**Spring应用上下文**(Spring application context),他们会创建和管理应用组件（也可以称为bean），将bean装配在一起的行为是通过一种基于**依赖注入**

> 那么通过控制反转（IOC）是怎么实现减少耦合的呢？总结网上的说法，从两个角度出发
>
> 1、控制反转
> ① 软件系统在没有引入IoC容器之前，对象A依赖对象B，那么A对象在实例化或者运行到某一点的时候，自己必须主动创建对象B或者使用已经创建好的对象B，其中不管是创建还是使用已创建的对象B，控制权都在我们自己手上。 
> ② 如果软件系统引入了Ioc容器之后，对象A和对象B之间失去了直接联系，所以，当对象A实例化和运行时，如果需要对象B的话，IoC容器会主动创建一个对象B注入到对象A所需要的地方。 
> ③ 通过前面①②的对比，可以看到对象A获得依赖对象B的过程，由主动行为变成了被动行为，即把创建对象交给了IoC容器处理，控制权颠倒过来了，这就是控制反转的由来！
>
> 注：在控制反转与解耦过程中使用了设计模式中的工厂模式。
>
> * 解耦的作用：该换包时，仅需要改变配置文件，而不用改变源代码
>
> 2、工厂模式
>
> 工厂模式是指当应用程序中甲组件需要乙组件协助时，并不是在甲组件中直接实例化乙组件对象，而是通过乙组件的工厂获取，即该工厂可以生成某一类型组件的实例对象。在这种模式下，甲组件无需与乙组件以硬编码的方式耦合在一起，而只需与乙组件的工厂耦合
>
> 那么这样的话，通过依赖注入就可以完全不用关心对象的生命周期，什么时候被创建，什么时候销毁，只需直接使用即可，对象的生命周期由提供依赖注入的框架来管理，从而，让使用框架者，可以将重心完全放到业务逻辑处理的开发上。

* 将Spring应用上下文将bean装配在一起的方式有：

> **显式** ：XML文件，Java配置（更加）
> **隐式**：自动配置:自动装配（autowiring）和组件扫描（component scanning）
>
> >借助组件扫描技术：Spring能够自动发现应用类路径下的组件，并将它们创建成Spring应用上下文的Bean。借助自动装配技术，Spring能够自动为组件注入他们所依赖的其他bean

* Spring Initializr：初始化一个Spring boot 项目，同时也是一个REST API

> REST(Representational State Transfer):表现层状态转化
>
> * 资源（Resourses）
>
> REST的名称"表现层状态转化"中，省略了主语。"表现层"其实指的是"资源"（Resources）的"表现层"。
>
> 所谓"资源"，就是网络上的一个实体，或者说是网络上的一个具体信息。它可以是一段文本、一张图片、一首歌曲、一种服务，总之就是一个具体的实在。你可以用一个URI（统一资源定位符）指向它，每种资源对应一个特定的URI。要获取这个资源，访问它的URI就可以，因此URI就成了每一个资源的地址或独一无二的识别符。
>
> 所谓"上网"，就是与互联网上一系列的"资源"互动，调用它的URI。
>
> * 表现层（Representation）
>
> "资源"是一种信息实体，它可以有多种外在表现形式。我们把"资源"具体呈现出来的形式，叫做它的"表现层"（Representation）。
>
>   比如，文本可以用txt格式表现，也可以用HTML格式、XML格式、JSON格式表现，甚至可以采用二进制格式；图片可以用JPG格式表现，也可以用PNG格式表现。
>
>   URI只代表资源的实体，不代表它的形式。严格地说，有些网址最后的".html"后缀名是不必要的，因为这个后缀名表示格式，属于"表现层"范畴，而URI应该只代表"资源"的位置。它的具体表现形式，应该在HTTP请求的头信息中用Accept和Content-Type字段指定，这两个字段才是对"表现层"的描述。
>
> * 状态转化（State Transfer）
>
> 访问一个网站，就代表了客户端和服务器的一个互动过程。在这个过程中，势必涉及到数据和状态的变化。
>
> 互联网通信协议HTTP协议，是一个无状态协议。这意味着，所有的状态都保存在服务器端。因此，如果客户端想要操作服务器，必须通过某种手段，让服务器端发生"状态转化"（State Transfer）。而这种转化是建立在表现层之上的，所以就是"表现层状态转化"。
>
> 客户端用到的手段，只能是HTTP协议。具体来说，就是HTTP协议里面，四个表示操作方式的动词：GET、POST、PUT、DELETE。它们分别对应四种基本操作：GET用来获取资源，POST用来新建资源（也可以用于更新资源），PUT用来更新资源，DELETE用来删除资源。
>
> * RESTful架构：
>
> * （1）每一个URI代表一种资源；
>
>   （2）客户端和服务器之间，传递这种资源的某种表现层；
>
>   （3）客户端通过四个HTTP动词，对服务器端资源进行操作，实现"表现层状态转化"。



---

> 在 pom.xml 文件中，声明了对 Web 和 Thymeleaf 启动器的依赖。这两个依赖关系带来了一些其他的依赖关系，包括：

- Spring MVC 框架
- 嵌入式 Tomcat
- Thymeleaf 和 Thymeleaf 布局方言

它还带来了 Spring Boot 的自动配置库。当应用程序启动时，Spring Boot 自动配置自动检测这些库并自动执行：

- 在 Spring 应用程序上下文中配置 bean 以启用 Spring MVC
- 将嵌入式 Tomcat 服务器配置在 Spring 应用程序上下文中
- 为使用 Thymeleaf 模板呈现 Spring MV C视图，配置了一个 Thymeleaf 视图解析器

---

* Spring 项目的结构：

```
 `mvnw` 和 `mvnw.cmd` —— 这些是 Maven 包装器脚本。即使你的计算机上没有安装 Maven，也可以使用这些脚本构建项目。

`pom.xml` —— 这是 Maven 构建规范，一会儿我们将对此进行更深入的研究。

`TacoCloudApplication.java` —— 这是引导项目的 Spring Boot 主类。稍后，我们将在这节详细介绍。

`application.properties` —— 该文件最初为空，但提供了一个可以指定配置属性的地方。我们将在本章中对此文件进行一些修改，但在第 5 章中将详细介绍配置属性。

`static` —— 在此文件夹中，可以放置要提供给浏览器的任何静态内容（图像、样式表、JavaScript 等），最初为空。

`templates` —— 在此文件夹中，放置用于向浏览器呈现内容的模板文件。最初为空，但很快会添加 Thymeleaf 模板。

`TacoCloudApplicationTests.java` —— 这是一个简单的测试类，可确保成功加载 Spring 应用程序上下文。开发应用程序时，将添加更多的测试。

```

- 

最后，构建规范以 Spring Boot 插件结束。这个插件执行一些重要的功能：

- 提供了一个 Maven 编译目标，让你能够使用 Maven 运行应用程序。这将在第 1.3.4 节中尝试实现这个目标。
- 确保所有的依赖库都包含在可执行的 JAR 文件中，并且在运行时类路径中可用。
- 在 JAR 文件中生成一个 manifest 文件，表示引导类（在本书例子中是 `TacoCloudApplication`）是可执行 JAR 的主类。

```
       <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        
        <dependency>
            <groupId>org.seleniumhq.selenium</groupId>
            <artifactId>selenium-java</artifactId>
            <scope>test</scope>
        </dependency>
        
        <dependency>
            <groupId>org.seleniumhq.selenium</groupId>
            <artifactId>htmlunit-driver</artifactId>
            <scope>test</scope>
        </dependency>
```

* 引导类（1.执行可执行的JAR运行应用程序时作为主类执行 2.包含一个最小的 Spring 配置文件来引导应用程序）

```
程序清单 1.2 Taco Cloud 引导类
package tacos;
​
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
​
@SpringBootApplication
public class TacoCloudApplication {
    public static void main(String[] args) {
        SpringApplication.run(TacoCloudApplication.class, args);
    }
}
```

> `@SpringBootApplication` 是一个组合了其他三个注释的复合应用程序：
>
> - `@SpringBootConfiguration` —— 指定这个类为配置类。尽管这个类中还没有太多配置，但是如果需要，可以将 Javabased Spring Framework 配置添加到这个类中。实际上，这个注释是`@Configuration` 注释的一种特殊形式。
> - `@EnableAutoConfiguration` —— 启用 Spring 自动配置。稍后我们将详细讨论自动配置。现在，要知道这个注释告诉 Spring Boot 自动配置它认为需要的任何组件。
> - `@ComponentScan` —— 启用组件扫描。这允许你声明其他带有 `@Component`、`@Controller`、`@Service` 等注释的类，以便让 Spring 自动发现它们并将它们注册为 Spring 应用程序上下文中的组件。



> `TacoCloudApplication` 的另一个重要部分是 `main()` 方法。
>
> `main()` 方法调用 SpringApplication 类上的静态 `run()` 方法，该方法执行应用程序的实际引导，创建`Spring` 应用程序上下文。传递给 `run()` 方法的两个参数是一个**配置类**和**命令行参数**。
>
> 你可能不需要更改引导类中的任何内容。对于简单的应用程序，你可能会发现在引导类中配置一两个其他组件很方便，但是对于大多数应用程序，最好为任何没有自动配置的东西创建一个单独的配置类。



* 测试应用：

```
程序清单 1.3 基准应用测试
package tacos;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

@RunWith(SpringRunner.class)
@SpringBootTest
public class TacoCloudApplicationTests {
    @Test
    public void contextLoads() {
    }
}
```

> `SpringRunner` 是 `SpringJUnit4ClassRunner` 的别名，它是在 Spring 4.3 中引入的，用于删除与特定版本的 JUnit （例如，JUnit4）的关联。毫无疑问，别名更易于阅读和输入。
>
> `@RunWith(SpringRunner.class)` 和 `@SpringBootTest` 的任务是加载用于测试的 Spring 应用程序上下文



### 编写Spring应用

* **Spring MVC**（核心是控制器：controller）

> 控制器是处理请求 并以某种方式进行信息响应的类

#### 处理Web请求

```
程序清单 1.4 主页控制器
package tacos;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class HomeController {
    @GetMapping("/")
    public String home() {
        return "home";
    }
}
/*
	它使用 @GetMapping 进行注释，以指示如果接收到根路径 / 的 HTTP GET 请求，则此方法应该处理该请求。除了返回 home 的 String 值外，它什么也不做。
	** 这个值将会被解析为试图的逻辑名
*/
```

* Thymeleaf作为视图模板引擎 （替代JSP，JSP是垃圾） 

  > 大部分是前端知识，但还是前后端结合，不用深学

  

  > 模板名称是由逻辑视图名派生而来的，再加上"/templates/"前缀和“.html”后缀。
  >
  > eg:模板的结果路径是 /templates/home.html。因此，需要将模板放在项目的 /src/main/resources/templates/home.html 中。

#### 定义视图

```
程序清单 1.5 Taco Cloud 主页模板
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml"
      xmlns:th="http://www.thymeleaf.org">
    <head>
        <title>Taco Cloud</title>
    </head>
    
    <body>
        <h1>Welcome to...</h1>
        <img th:src="@{/images/TacoCloud.png}"/>
    </body>
</html>
/*
该图片是通过上下文相对路径 /images/TacoCloud.png 进行引用的。
从我们对项目结构的回顾中可以想起，像图片这样的静态内容保存在 /src/main/resources/static 文件夹中。
这意味着 Taco Cloud 标志图片也必须驻留在项目的 /src/main/resources/static/images/TacoCloud.png 中。
*/
```

#### 测试运行器

```
程序清单 1.6 主页控制器测试
package tacos;

import static org.hamcrest.Matchers.containsString;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.content;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.view;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.test.context.junit4.SpringRunner;
import org.springframework.test.web.servlet.MockMvc;

@RunWith(SpringRunner.class) /*提供一个测试运行器(runner)来指导Junit如何运行测试*/
@WebMvcTest(HomeController.class)
public class HomeControllerTest {
    @Autowired
    private MockMvc mockMvc;
    
    @Test
    public void testHomePage() throws Exception {
        mockMvc.perform(get("/"))
            .andExpect(status().isOk())
            .andExpect(view().name("home"))
            .andExpect(content().string(containsString("Welcome to...")));
    }
}
```

> `@WebMvcTest` 还为测试 Spring MVC 提供了 Spring 支持。虽然可以让它启动服务器，但模拟 Spring MVC 的机制就足以满足你的目的了。测试类被注入了一个 `MockMvc` 对象中，以此用来测试来驱动模型。
>
> `testHomePage()` 方法定义了要对主页执行的测试。



#### Spring Boot DevTools

* 开发期工具，其中包括：

1. **代码变更后应用自启动**

> DevTools运行的时候，应用程序会被加载到Java虚拟机**两个对立的类加载器**中。
>
> 其中同一个类加载器会加载你的Java代码、属性文件以及项目中“src/main/”路径下几乎所有的内容。
>
> 另外一个类加载器会加载依赖的库
>
> 当检测到变更时，Devtool只会重新加载包含项目代码的类加载器，并重启Spring的应用上下文，另一个和JVM原封不动，能减少应用的启动的时间，但自动重启无法反映依赖项的变化，若此种更改，我们需要重新启动应用。

2. **自动刷新浏览器和禁用模板缓存**

> 默认情况下，模板选项（如 Thymeleaf 和 FreeMarker）被配置为缓存模板解析的结果，这样模板就不需要对它们所服务的每个请求进行修复。这在生产中非常有用，因为它可以带来一些性能上的好处。
>
> 但是，缓存的模板在开发时不是很好。缓存的模板使它不可能在应用程序运行时更改模板，并在刷新浏览器后查看结果。
>
> DevTools 通过自动禁用所有模板缓存来解决这个问题。
>
> 但如果像我一样，甚至不想被点击浏览器的刷新按钮所累，如果能够立即在浏览器中进行更改并查看结果，那就更好了。幸运的是，DevTools 为我们这些懒得点击刷新按钮的人提供了一些特别的功能。
>
> 当 DevTools 起作用时，它会自动启用 LiveReload （http://livereload.com/）服务器和应用程序。就其本身而言，LiveReload 服务器并不是很有用。但是，当与相应的 LiveReload 浏览器插件相结合时，它会使得浏览器在对模板、图像、样式表、JavaScript 等进行更改时自动刷新 —— 实际上，几乎所有最终提供给浏览器的更改都会自动刷新。
>
> LiveReload 有针对 Google Chrome、Safari 和 Firefox 浏览器的插件。（对不起，ie 和 Edge 的粉丝们。）请访问 http://livereload.com/extensions/，了解如何为浏览器安装 LiveReload。

3.**在 H2 控制台中构建**

> 如果选择使用 H2 数据库进行开发，DevTools 还将自动启用一个 H2 控制台，你可以从 web 浏览器访问该控制台。只需将 web 浏览器指向 http://localhost:8080/h2-console，就可以深入了解应用程序正在处理的数据。



表 2.1 Spring MVC 请求映射注释**

| 注释            | 描述                  |
| --------------- | --------------------- |
| @RequestMapping | 通用请求处理          |
| @GetMapping     | 处理 HTTP GET 请求    |
| @PostMapping    | 处理 HTTP POST 请求   |
| @PutMapping     | 处理 HTTP PUT 请求    |
| @DeleteMapping  | 处理 HTTP DELETE 请求 |
| @PatchMapping   | 处理 HTTP PATC        |

## 开发WEB应用程序

### 展示信息

#### 建立域

```
package tacos;
​
import lombok.Data;
import lombok.RequiredArgsConstructor;
​
@Data
@RequiredArgsConstructor
public class Ingredient {
    private final String id;
    private final String name;
    private final Type type;
    
    public static enum Type {
        WRAP, PROTEIN, VEGGIES, CHEESE, SAUCE
    }
}
```

> 这是一个普通的 Java 域类，定义了描述一个成分所需的三个属性。
>
> 对于程序清单 2.1 中定义的 `Ingredient` 类，最不寻常的事情可能是它似乎缺少一组常用的 getter 和 setter 方法，更不用说像 `equals()`、`hashCode()`、`toString()` 等有用的方法。

> 在清单中看不到它们，部分原因是为了节省空间，但也因为使用了一个名为 Lombok 的出色库，它会在运行时自动生成这些方法
>
> 。实际上，类级别的 `@Data` 注释是由 Lombok 提供的，它告诉 Lombok 生成所有缺少的方法，以及接受所有最终属性作为参数的构造函数。通过使用 Lombok，可以让 `Ingredient` 的代码保持整洁。

#### 创建控制类

```
程序清单 2.2 Spring 控制器类的开始
package tacos.web;
​
import java.util.Arrays;
import java.util.List;
import java.util.stream.Collectors;
import javax.validation.Valid;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.validation.Errors;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import lombok.extern.slf4j.Slf4j;
​
import tacos.Taco;
import tacos.Ingredient;
import tacos.Ingredient.Type;
​
@Slf4j
@Controller
@RequestMapping("/design")
public class DesignTacoController {
    @GetMapping
    public String showDesignForm(Model model) {
        List<Ingredient> ingredients = Arrays.asList(
            new Ingredient("FLTO", "Flour Tortilla", Type.WRAP),
            new Ingredient("COTO", "Corn Tortilla", Type.WRAP),
            new Ingredient("GRBF", "Ground Beef", Type.PROTEIN),
            new Ingredient("CARN", "Carnitas", Type.PROTEIN),
            new Ingredient("TMTO", "Diced Tomatoes", Type.VEGGIES),
            new Ingredient("LETC", "Lettuce", Type.VEGGIES),
            new Ingredient("CHED", "Cheddar", Type.CHEESE),
            new Ingredient("JACK", "Monterrey Jack", Type.CHEESE),
            new Ingredient("SLSA", "Salsa", Type.SAUCE),
            new Ingredient("SRCR", "Sour Cream", Type.SAUCE)
        );
        
        Type[] types = Ingredient.Type.values();
        for (Type type : types) {
            model.addAttribute(type.toString().toLowerCase(),
                filterByType(ingredients, type));
        }
        
        model.addAttribute("design", new Taco());
        return "design";
    }
}
```

* 第一个是 @Slf4j，它是 Lombok 提供的注释，在运行时将自动生成类中的 SLF4J（Java 的简单日志门面，https://www.slf4j.org/）记录器。这个适当的注释具有与显式地在类中添加以下行相同的效果：

```
private static final org.slf4j.Logger log = 
       org.slf4j.LoggerFactory.getLogger(DesignTacoController.class);
```

* 类级别的 @RequestMapping 注释用于 showDesignForm() 方法时，可以用 @GetMapping 注释进行改进。@GetMapping 与类级别的 @RequestMapping 配对使用，指定何时接收 `/design` 的 HTTP GET 请求，showDesignForm() 将用来处理请求。(@GetMapping 是一个相对较新的注释，是在 Spring 4.3 中引入的。在 Spring 4.3 之前，可能使用了一个方法级别的 @RequestMapping 注释)

**Spring MVC 请求映射注释**

| 注释            | 描述                  |
| --------------- | --------------------- |
| @RequestMapping | 通用请求处理          |
| @GetMapping     | 处理 HTTP GET 请求    |
| @PostMapping    | 处理 HTTP POST 请求   |
| @PutMapping     | 处理 HTTP PUT 请求    |
| @DeleteMapping  | 处理 HTTP DELETE 请求 |
| @PatchMapping   | 处理 HTTP PATCH 请求  |

* model.addattribute()的作用

>  往前台传数据，可以传对象，可以传List，通过el表达式 ${}可以获取到，

#### 设计视图

**Thymeleaf 基本语法**

1. 像 Thymeleaf 这样的视图库被设计成与任何特定的 web 框架解耦。因此，他们不知道 Spring 的模型抽象，并且无法处理控制器放置在模型中的数据。但是它们可以处理 servlet 请求属性。因此，在 Spring 将请求提交给视图之前，它将模型数据复制到请求属性中，而 Thymeleaf 和其他视图模板选项可以随时访问这些属性。

2. Thymeleaf 模板只是 HTML 与一些额外的元素属性，指导模板在渲染请求数据。

```
例如，如果有一个请求属性，它的键是 “message”，你希望它被 Thymeleaf 渲染成一个 HTML <p> 标签，你可以在你的 Thymeleaf 模板中写以下内容：

<p th:text="${message}">placeholder message</p>


当模板被呈现为 HTML 时，<p> 元素的主体将被 servlet 请求属性的值替换，其键值为 “message”。th:text 是一个 Thymeleaf 的命名空间属性，用于需要执行替换的地方。${} 操作符告诉它使用请求属性的值（在本例中为 “message”）。
Thymeleaf 还提供了另一个属性 th:each，它遍历元素集合，为集合中的每个项目呈现一次 HTML。当设计视图列出模型中的玉米饼配料时，这将非常方便。例如，要呈现 “wrap” 配料列表，可以使用以下 HTML 片段：

<h3>Designate your wrap:</h3>
<div th:each="ingredient : ${wrap}">
    <input name="ingredients" type="checkbox"                  		 th:value="${ingredient.id}" />
    <span th:text="${ingredient.name}">INGREDIENT</span><br/>
</div>
<span> 元素使用 th:text 属性把 "INGREDIENT" 占位符替换为成分 name 属性的值。
当使用实际的模型数据呈现时，这个 <div> 循环迭代一次可能是这样的：
<div>
    <input name="ingredients" type="checkbox" value="FLTO" />
    <span>Flour Tortilla</span><br/>
</div>
```

#### 处理表单提交

* 由于<form>标签的method属性被设置微POST。而且<form>没有声明action属性

> 这意味着在提交表单时，浏览器将收集表单中的所有数据，并通过 HTTP POST 请求将其发送到服务器，发送到显示表单的 GET 请求的同一路径 —— `/design` 路径。

```
程序清单 2.4 使用 @PostMapping 处理 POST 请求
@PostMapping
public String processDesign(Design design) {
    // Save the taco design...
    // We'll do this in chapter 3
    log.info("Processing design: " + design);
    return "redirect:/orders/current";
}
```

> 从 processDesign() 返回的值的前缀是 “redirect:”，表示这是一个重定向视图。更具体地说，它表明在 processDesign() 完成之后，用户的浏览器应该被重定向到相对路径 /order/current。

#### 验证表单输入

##### 声明校验规则

> 执行表单验证的一种方法是在 processDesign() 和 processOrder() 方法中加入一堆 if/then 块，检查每个字段以确保它满足适当的验证规则。但是这样做会很麻烦，并且难于阅读和调试。
> 幸运的是，Spring 支持 Java's Bean Validation API。这使得声明验证规则比在应用程序代码中显式地编写声明逻辑更容易。使用 Spring Boot，不需要做任何特殊的事情来将验证库添加到项目中，因为 Validation API 和 Validation API 的 Hibernate 实现作为Spring Boot web 启动程序的临时依赖项自动添加到了项目中。

* 要在 Spring MVC 中应用验证，需要这样做：

1. 对要验证的类声明验证规则：特别是 Taco 类。

2. 指定验证应该在需要验证的控制器方法中执行，具体来说就是：DesignTacoController 的 processDesign() 方法和 OrderController 的 processOrder() 方法。

3. 修改表单视图以显示验证错误。

##### 在表单绑定的时候执行校验

```
程序清单 2.12 验证 POST 来的 Taco
@PostMapping
public String processDesign(@Valid Taco design, Errors errors) {
    if (errors.hasErrors()) {
        return "design";
    }
    
    // Save the taco design...
    // We'll do this in chapter 3
    log.info("Processing design: " + design);
    return "redirect:/orders/current";
}
@Valid 注释告诉 Spring MVC 在提交的 Taco 对象绑定到提交的表单数据之后，以及调用 processDesign() 方法之前，对提交的 Taco 对象执行验证。如果存在任何验证错误，这些错误的详细信息将在传递到 processDesign() 的错误对象中捕获。processDesign() 的前几行查询 Errors 对象，询问它的 hasErrors() 方法是否存在任何验证错误。如果有，该方法结束时不处理 Taco，并返回 “design” 视图名，以便重新显示表单。
```

#####  缓存模板

| Freemarker       | spring.freemarker.cache      |
| ---------------- | ---------------------------- |
| Groovy Templates | spring.groovy.template.cache |
| Mustache         | spring.mustache.cache        |
| Thymeleaf        | spring.thymeleaf.cache       |

默认情况下，所有这些属性都设置为 true 以启用缓存。可以通过将其缓存属性设置为 false 来禁用所选模板引擎的缓存。例如，要禁用 Thymeleaf 缓存，请在 application.properties 中添加以下行：

```
spring.thymeleaf.cache = false
```

而在 DevTools 提供的许多有用的开发时帮助中，它将禁用所有模板库的缓存，但在部署应用程序时将禁用自身（从而重新启用模板缓存）。

#### 小结

* Spring 提供了一个强大的 web 框架，称为 Spring MVC，可以用于开发 Spring 应用程序的 web 前端。

* Spring MVC 是基于注解的，可以使用 @RequestMapping、@GetMapping 和 @PostMapping 等注解来声明请求处理方法。

* 大多数请求处理方法通过返回视图的逻辑名称来结束，例如一个 Thymeleaf 模板，请求（以及任何模型数据）被转发到该模板。

* Spring MVC 通过 Java Bean Validation API 和 Hibernate Validator 等验证 API 的实现来支持验证。

* 视图控制器可以用来处理不需要模型数据或处理的 HTTP GET 请求。

* 除了 Thymeleaf，Spring 还支持多种视图选项，包括 FreeMarker、Groovy Templates 和 Mustache。

### 处理数据

#### 使用JDBC读写数据

在开始使用 JdbcTemplate 之前，需要将它添加到项目类路径中。这很容易通过添加 Spring Boot 的 JDBC starter 依赖来实现：

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>
```

还需要一个存储数据的数据库。出于开发目的，嵌入式数据库也可以。我喜欢 H2 嵌入式数据库，所以我添加了以下依赖进行构建：

```
<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <scope>runtime</scope>
</dependency>
```

##### 定义 JDBC 存储库

ngredient repository 需要执行以下操作：

- 查询所有的 Ingredient 使之变成一个 Ingredient 的集合对象
- 通过它的 id 查询单个 Ingredient
- 保存一个 Ingredient 对象

以下 IngredientRepository 接口将这三种操作定义为方法声明：

```
package tacos.data;

import tacos.Ingredient;

public interface IngredientRepository {
    
    Iterable<Ingredient> findAll();
    
    Ingredient findOne(String id);
    
    Ingredient save(Ingredient ingredient);
}
```



```

public Ingredient findOne(String id) {
    return jdbc.queryForObject(
        "select id, name, type from Ingredient where id=?",
        this::mapRowToIngredient, id);
}
this::mapRowToIngredient: RowMapper 参数作为 mapRowToIngredient() 方法的方法引用
Spring RowMapper（用来将结果集中的每行数据映射为一个对象）
```

##### 定义模式并预加载数据

* 新建H2数据库，需将PATH设置成相应的sql文件的位置

> Ｈ2有三种运行模式。
>
> １、内嵌模式（Embedded Mode）
> 　内嵌模式下，应用和数据库同在一个JVM中，通过JDBC进行连接。 可持久化，但**同时只能一个客户端连接**。内嵌模式性能会比较好。
>
> ２、服务器模式（Server Mode）
> 　使用服务器模式和内嵌模式一样，只不过它可以跑在**另一个进程里**。
>
> ３、 混合模式
> 　第一个应用以内嵌模式启动它，对于后面的应用来说它是服务器模式跑着的。混合模式是内嵌模式和服务器模式的组合。第一个应用通过内嵌模式与数据库建立连接，同时也作为一个服务器启动，于是另外的应用（运行在不同的进程或是虚拟机上）可以同时访问同样的数据。第一个应用的本地连接与嵌入式模式的连接性能一样的快，而其它连接理论上会略慢。
>
> 优点：
>
> - 纯Java编写，不受平台的限制；
> - 只有一个jar文件，适合作为嵌入式数据库使用；
> - h2提供了一个十分方便的web控制台用于操作和管理数据库内容；
> - 功能完整，支持标准SQL和JDBC。麻雀虽小五脏俱全；
> - 支持内嵌模式、服务器模式和集群。
> - 占用空间小：大约1.5 MB JAR文件大小
> - 速度快
>
> 缺点：
>
> - 由于它是内存型的，所以并不会持久化数据
>
>  **H2 目录结构**
>
> h2
>
> 　|---bin
>
> 　|  |---h2-1.1.116.jar　　//H2数据库的jar包（驱动也在里面）
>
> 　|  |---h2.bat　　//Windows控制台启动脚本
>
> 　|  |---h2.sh　　//Linux控制台启动脚本
>
> 　|  |---h2w.bat　　//Windows控制台启动脚本（不带黑屏窗口）
>
> 　|---docs　　//H2数据库的帮助文档（内有H2数据库的使用手册）
>
> 　|---service　　//通过wrapper包装成服务。
>
> 　|---src　　//H2数据库的源代码
>
> 　|---build.bat　　//windows构建脚本
>
> 　|---build.sh　　//linux构建脚本
>
> #### 数据库配置
>
> #### 地址：http://www.h2database.com/html/features.html#connection_modes
>
> 当然你可以可以通过修改 application.properties  文件中配置文件来为你的 H2 数据库指定登录的用户名和密码。
>
> #配置数据库的路径：
>
> spring.datasource.url=jdbc:h2:mem:testdb
>
> spring.datasource.url=jdbc:h2:file:路径
>
> spring.datasource.url=jdbc:h2:tcp://localhost:9092/default
>
> #驱动器
>
> spring.datasource.driverClassName=org.h2.Driver
>
> #用户账号密码
>
> spring.datasource.username=sa
>
> spring.datasource.password=password
>
> #运用平台
>
> spring.jpa.database-platform=org.hibernate.dialect.H2Dialect
>
> #控制台工具进行启用
>
> ```
> spring.h2.console.enabled=``true
> ```
> spring.h2.console.path=/h2-console
>
> 设置了 H2 的控制台访问控制台的 URL 为： */h2-console，*这个链接是针对你当前项目运行的服务器地址和端口的相对地址。
>
> spring.h2.console.settings.trace=false
>
> 设置了 spring.h2.console.settings.trace 参数为 false，这样我们能够避免在系统控制台中输出 trace 级别的日志信息。
>
> spring.h2.console.settings.web-allow-others=false
>
> 通过设置 spring.h2.console.settings.web-allow-others=false 参数，我们能够禁止远程 Web 访问 H2 数据库的信息。

* 如果有一个名为 schema.sql 的文件。在应用程序的类路径根目录下执行 sql，然后在应用程序启动时对数据库执行该文件中的 SQL。因此，应该将程序清单 3.8 的内容写入一个名为 schema.sql 的文件中，然后放在项目的 src/main/resources 文件夹下。

> 在springboot项目启动时，会自动执行项目中的sql文件
>
> ```
> #spring某个版本之后需要加上这句，否则不会自动执行sql文件
> spring.datasource.initialization-mode=always
> ```

##### 插入数据

使用 JdbcTemplate 保存数据的两种方法包括：

- 直接使用 update() 方法

> 通过构造函数注入了 JdbcTemplate,构造函数将 JdbcTemplate 直接分配给一个实例变量使用

*  SimpleJdbcInsert 包装类

> 通过其构造函数注入了 JdbcTemplate,构造函数并没有将 JdbcTemplate 直接分配给一个实例变量，而是使用它来构造两个 SimpleJdbcInsert 实例。

@repository

> @repository跟@Service,@Compent,@Controller这4种注解是没什么本质区别,都是声明作用,取不同的名字只是为了更好区分各自的功能.下图更多的作用是mapper注册到类似于以前mybatis.xml中的mappers里.
>
> ![img](https://img-blog.csdn.net/20180814225153697?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Y0NTA1NjIzMXA=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
>
> //也是因为接口没办法在spring.xml中用bean的方式来配置实现类吧(接口配不了),所以只能用注解或者mybatis.xml中扫描bean的方式来生成实现类吧
>
> 一,首先:@repository是用来注解接口,如下图:这个注解是将接口BookMapper的一个实现类(具体这个实现类的name叫什么,还需要再分析源码找找看)交给spring管理(在spring中有开启对@repository注解的扫描),当哪些地方需要用到这个实现类作为依赖时,就可以注入了.当然我们也可以主动给这个实现类命名,如下图
>
> ![img](https://img-blog.csdn.net/20180814223747529?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Y0NTA1NjIzMXA=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)e
>
> ![img](https://img-blog.csdn.net/20180814224330277?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Y0NTA1NjIzMXA=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
>
> 二,为什么有时候我们不用@repository来注解接口,我们照样可以注入到这个接口的实现类呢?如下图,下图是在接口没有用
>
> @repository注解的情况下,依然可以实现注入它的实现类.
>
> ![img](https://img-blog.csdn.net/20180814224611874?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Y0NTA1NjIzMXA=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
>
> 上面是在idea中报了红线警告,说找不到这个实现类,但依然是可以运行,没有问题(只是单纯的警告),而在myeclipse中,是连警告都没有的,运行完全没问题.这是因为如下图:
>
> ![img](https://img-blog.csdn.net/20180814225153697?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Y0NTA1NjIzMXA=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
>
> 是因为我们在mybatis的xml文件配置了上图这个bean,它会去将dao这个层中的mapper(也就是我们的接口)都生成实现类,然后交给spring管理(因为**mybatis.xml**文件我们最终还是导入了spring容器中),所以我们这里不对这些接口用@repository注解,也是一样可以用它的实现类,(这也是我们写项目时,有时感觉完全是没用到@repository注解的原因,因为没有什么必要)而idea报红线警告,可能是idea自己的原因,这个在我们对它对应的接口用@repository注解后,红线警告会消失,运行也完全没问题

#### 使用Spring Data JPA持久化数据

一些最流行的 Spring 数据项目包括：

- *Spring Data JPA* - 针对关系数据库的持久化
- *Spring Data Mongo* - 针对 Mongo 文档数据库的持久化
- *Spring Data Neo4j* - 针对 Neo4j 图形数据库的持久化
- *Spring Data Redis* - 针对 Redis 键值存储的持久化
- *Spring Data Cassandra* - 针对 Cassandra 数据库的持久化

Spring Data 为所有这些项目提供的最有意思和最有用的特性之一是能够基于存储库规范接口自动创建存储库。

> 我知道Jpa是一种规范，而Hibernate是它的一种实现。除了Hibernate，还有EclipseLink(曾经的toplink)，OpenJPA等可供选择，所以使用Jpa的一个好处是，可以更换实现而不必改动太多代码。
>
> 我原以为hibernate对jpa的支持，是另提供了一套专用于jpa的注解，但现在看起来似乎不是。一些重要的注解如Column, OneToMany等，hibernate没有提供，这说明jpa的注解已经是hibernate的核心，hibernate只提供了一些补充，而不是两 套注解。要是这样，hibernate对jpa的支持还真够足量，我们要使用hibernate注解就必定要使用jpa。

> 1.JDBC提供一种接口，它是由各种数据库厂商提供类和接口组成的数据库驱动，为多种数据库提供统一访问。我们使用数据库时只需要调用JDBC接口就行了。
>
> JDBC的用途：与数据库建立连接、发送 操作数据库的语句并处理结果。
>
> 常见数据库：Oracle、MySQL等。
>
> 2.JPA是Java持久层API。它是对java应用程序访问ORM（对象关系映射）框架的规范。为了我们能用相同的方法使用各种ORM框架。
>
> JPA用途：简化现有Java EE和Java SE应用开发工作；整合ORM技术。
>
> 使用JPA只需要创建实体（这和创建一个POJO（Plain Ordinary Java Object）简单的Java对象一样简单），用@entity进行注解。在Spring Data JPA中，定义一个简单的接口，用于把对象持久化到数据库的仓库。
>
> 常见ORM框架：Hibernate、MyBatis等。
>
> 3.不同点：
>
> 1.使用的sql语言不同：
>
> JDBC使用的是基于关系型数据库的标准SQL语言；
>
> JPA通过面向对象而非面向数据库的查询语言查询数据，避免程序的SQL语句紧密耦合。
>
> 2.操作的对象不同：
>
> JDBC操作的是数据，将数据通过SQL语句直接传送到数据库中执行：
>
> JPA操作的是持久化对象，由底层持久化对象的数据更新到数据库中。
>
> 3.数据状态不同：
>
> JDBC操作的数据是“瞬时”的，变量的值无法与数据库中的值保持一致；
>
> JPA操作的数据时可持久的，即持久化对象的数据属性的值是可以跟数据库中的值保持一致的。
> 