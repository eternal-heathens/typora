##  简介

* Model    模型   JAVABean

  > - 数据模型，提供要展示的数据，因此包含数据和行为，可以认为是领域模型或 JavaBean 组件（包含数据和行为），不过现在一般都分离开来：Value Object（数据） 和 服务层（行为）。也就是模型提供了模型数据查询和模型数据的状态更新等功能，包括数据和业务。

* View    视图   JSP

  > 负责进行模型的展示，一般就是我们见到的用户界面，客户想看到的东西。

* Controller  控制器 servlet

  > 接收用户请求，委托给模型进行处理（状态改变），处理完毕后把返回的模型数据返回给视图，由视图负责展示。

![image-20200723170654922](F:\Typora数据储存\Spring\SpringMvc.assets\image-20200723170654922.png)

![img](https://upload-images.jianshu.io/upload_images/7896890-a25782fb05f315de.png?imageMogr2/auto-orient/strip|imageView2/2/format/webp)

SpringMVC运行原理

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200418161602129.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwMTgxNDM1,size_16,color_FFFFFF,t_70)

> 1. 前端控制器(核心控制器)DispatcherServlet:用户请求最先达到的控制器,前端控制器调用其他组件处理请求,是MVC架构中的C,是整个流程控制的核心.其存在降低了组件间的耦合性.
> 2. 处理器映射器HandlerMapping:负责根据用户请求找到处理器.
> 3. 处理器Handler:具体的业务方法.
> 4. 处理器适配器HandlAdapter: 对处理器进行执行.这是一种适配器模式的应用.
> 5. 视图解析器ViewResolver: 负责将处理结果生成视图. ViewResolver首先根据逻辑视图名解析成物理视图名
>    即具体的页面地址,再生成View视图对象,最后对View进行渲染将处理结果通过页面展示给用户.
> 6. 视图View: 具体的页面
>    * 其中处理器映射器HandlerMapping,处理器适配器HandlAdapter,视图解析器ViewResolver称为SpringMVC三大组件.在bean.xml中声明<mvc:annotation-driven conversion-service="conversionService"/>标签相当于自动配置了处理器映射器和处理器适配

#### SPringMVC和struts2

![image-20200723185729708](F:\Typora数据储存\Spring\SpringMvc.assets\image-20200723185729708.png)

## 标签、配置

### 请求路径匹配

####  RequestMapping

@RequestMapping注解用于建立请求URL路径和处理器之间的对应关系.

* 出现位置: 可以出现在类上,也可以出现在方法上.
  * 当它既出现在类上也出现在方法上时,类上注解值为请求URL的一级目录,方法上注解值为请求URL的二级目录
  * 当它只出现在方法上时,该注解值为请求URL的一级目录
* 其属性如下:

  * path: value属性的别名,指定请求的URL,支持Ant风格表达式,通配符如下:

| 通配符 | **说明**                                    |
| ------ | ------------------------------------------- |
| ?      | 匹配文件(路径)名中的一个字符                |
| *      | 匹配文件(路径)名中的任意数量(包括0个)的字符 |
| **     | 匹配任意数量(包括0个)的路径                 |

> 路径/project/.a匹配项目根路径下所有在/project路径下的.a文件
> 路径/project/p?ttern匹配项目根路径下的/project/pattern和/project/pXttern,但不能匹配/project/pttern
> 路径//example匹配项目根路径下的/project/example,/project/foo/example,和/example
> 路径/project//dir/file.匹配项目根路径下的/project/dir/file.jsp,/project/foo/dir/file.html,/project/foo/bar/dir/file.pdf
> 路径//.jsp匹配项目根路径下的所有jsp文件
> 另外,遵循最长匹配原则,若URL请求了/project/dir/file.jsp,现在存在两个匹配模式:/*/.jsp和/project/dir/.jsp,那么会根据/project/dir/.jsp来匹配.

* method: 指定HTTP请求方法(可选RequestMethod.GET,RequestMethod.HEAD,RequestMethod.POST,RequestMethod.PUT等),多个值之间是或的关系.

* params: 指定请求参数的限制,支持简单的表达式,如:

> @RequestMapping(params={"param1"}),表示请求参数中param1必须出现
> @RequestMapping(params={"!param1"}),表示请求参数中param1不能出现
> @RequestMapping(params={"param1=value1"}),表示请求参数中param1必须出现且为value1
> @RequestMapping(params={"param1!value1"}),表示请求参数中param1必须出现且不为value1
> 多个值之间是与的关系

* headers: 限定HTTP请求中必须包含的请求头,同样支持简单的表达式

**其中path和method属性较常用**

### 参数绑定

#### 请求参数绑定

ognl约等于el（取值${}：范围有：page、request、session、application	）+jstl（实现类似Java条件表达式：for/if等）

SpringMVC支持三种类型的参数绑定

1. 基本数据类型和String类型
2. JavaBean类型
3. 集合类型

数据绑定要求**请求参数名和方法中的参数名相同**,或使用`@RequestParam`为方法参数起别名.
```
<a href="account/findAccount?accountId=10&accountName=zhangsan">查询账户</a>
```
```
// 控制器类
@Controller
@RequestMapping(path = "/account")
public class HelloController {

    @RequestMapping("/findAccount")
    public String findAccount(Integer accountId, String accountName) {
        // accountId = 10, accountName = "zhangsan"
        // 方法体...
    }
}
```

#### `@RequestParam`注解: 为处理器方法参数起别名

`@RequestParam`注解作用在方法参数上,把请求中指定名称的参数给处理器方法中的形参赋值,相当于给处理器方法起别名.其属性如下:

- `name`: `value`属性的别名,指定请求参数的名称
- `required`: 指定该请求参数是否必须的,默认为`true`

例如jsp中发送请求如下

```
<a href="testRequestParam?param1=value">测试requestParam注解</a>
```

处理器方法中给对应参数加上`@RequestParam(name="param1")`注解来接收参数

```
@RequestMapping("/testRequestParam")
public String handlerMethod(@RequestParam("param1") String username) {
    // 方法体...
}
```

#### `@RequestBody`注解: 为处理器方法参数起别名

* @RequestBody主要用来接收前端传递给后端的json字符串中的数据的(请求体中的数据的)；GET方式无请求体，所以使用@RequestBody接收数据时，前端不能使用GET方式提交数据，而是用POST方式进行提交。在后端的同一个接收方法里，@RequestBody与@RequestParam()可以同时使用，@RequestBody最多只能有一个，而@RequestParam()可以有多个。
* 后端@RequestBody注解对应的类在将HTTP的输入流(含请求体)装配到目标类(即：@RequestBody后面的类)时，会根据json字符串中的key来匹配对应实体类的属性，如果匹配一致且json中的该key对应的值符合则自动解析转换。
* son字符串中，如果value为""的话，后端对应属性如果是String类型的，那么接受到的就是""，如果是后端属性的类型是Integer、Double等类型，那么接收到的就是null。

```
@RequestMapping("/testRequestBody")
public String handlerMethod(@RequestBody("param1") String body) {
    // 方法体...
}
```

```
<form action="anno/testRequestBody" method="post">
    <label>名称</label><input type="text" name="username"><br/>
    <label>年龄</label><input type="text" name="age"><br/>
    <input type="submit" value="保存">
</form>
```

#### @RequestParam、@RequestBody和@ModelAttribute区别

1. application/x-www-form-urlencoded，这种情况的数据@RequestParam、@ModelAttribute可以处理，@RequestBody也可以处理。
2. multipart/form-data，**@RequestBody不能处理这种格式的数据**,可以用@ModelAttribute进行处理。（form表单里面有文件上传时，必须要指定enctype属性值为multipart/form-data，意思是以二进制流的形式传输文件。）
3. application/**json**、application/xml等格式的数据，**必须使用@RequestBody来处理**。

> 一、application/x-www-form-urlencoded
>
> 1、它是post的默认格式，使用js中URLencode转码方法。包括将name、value中的空格替换为加号；将非ascii字符做百分号编码；将input的name、value用‘=’连接，不同的input之间用‘&’连接。
>
> 2、百分号编码什么意思呢。比如汉字‘丁’吧，他的utf8编码在十六进制下是0xE4B881，占3个字节，把它转成字符串‘E4B881’，变成了六个字节，每两个字节前加上百分号前缀，得到字符串“%E4%B8%81”，变成九个ascii字符，占九个字节（十六进制下是0x244534254238253831）。把这九个字节拼接到数据包里，这样就可以传输“非ascii字符的  utf8编码的 十六进制表示的 字符串的 百分号形式”，^_^。
>
> 3、同样使用URLencode转码，这种post格式跟get的区别在于，get把转换、拼接完的字符串用‘?’直接与表单的action连接作为URL使用，所以请求体里没有数据；而post把转换、拼接后的字符串放在了请求体里，不会在浏览器的地址栏显示，因而更安全一些。
>
> 二、multipart/form-data
>
> 1、对于一段utf8编码的字节，用application/x-www-form-urlencoded传输其中的ascii字符没有问题，但对于非ascii字符传输效率就很低了（汉字‘丁’从三字节变成了九字节），因此在传很长的字节（如文件）时应用multipart/form-data格式。smtp等协议也使用或借鉴了此格式。
>
> 2、此格式表面上发送了什么呢。用此格式发送一段一句话和一个文件，请求体如下
>
> ![image-20210419201324986](F:\Typora数据储存\Spring\SpringMvc.assets\image-20210419201324986.png)
>
> 同时请求头里规定了Content-Type: multipart/form-data; boundary=----WebKitFormBoundarymNhhHqUh0p0gfFa8
>
> 可见请求体里不同的input之间用一段叫boundary的字符串分割，每个input都有了自己一个小header，其后空行接着是数据。
>
> 3、此格式实际上发送了什么呢。fiddler抓包如下
>
> ![image-20210419201308313](F:\Typora数据储存\Spring\SpringMvc.assets\image-20210419201308313.png)
>
> 右边明显看到了一段乱码，为什么呢，以汉字‘丁’为例，其utf8编码为0xE4B881，这三个字节会直接拼接到数据包中，即其在实际发送时只占三字节，上图右边是逐字节转为ascii字符显示的，因此会显示为三个乱码字符。
>
> 4、由上可见，multipart/form-data将表单中的每个input转为了一个由boundary分割的小格式，没有转码，直接将utf8字节拼接到请求体中，在本地有多少字节实际就发送多少字节，极大提高了效率，适合传输长字节。
> ------------------------------------------------
> 版权声明：本文为CSDN博主「hula_好天气」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
> 原文链接：https://blog.csdn.net/u013827143/article/details/86222486

#### `@PathVaribale`注解: 绑定URL占位符,支持REST风格URL

在`@RequestMapping`注解的`path`属性中用`{param}`声明占位符,将展位符`@PathVariable`注解的`name`属性来修饰处理器参数来将占位符对应的值赋给方法的参数.例如

对于以下的请求和方法:

```
<a href="account/findAccount/10">查询账户</a>
```

```
// 控制器类
@Controller
@RequestMapping(path = "/account")
public class HelloController {

    @RequestMapping("/findAccount/{id}")
    public void findAccount(@PathVariable(name = "id") Integer accountId) {
        // accountId = 10
        // 方法体...
    }
}
访问URLhttp://localhost:8080/myProject/account/findAccount/10会将10传给findAccount方法的accountId参数.
```



#### JavaBean类型的参数绑定

> JavaBean类型的参数,要想实现绑定,就必须实现其空参构造函数和所有属性的get,set方法

1. 若JavaBean参数的属性中只包含**基本数据类型和String类型**属性,以属性名作为请求参数名,则SpringMVC会自动将其封装成JavaBean对象.示例如下:

   例如JavaBean类的定义如下:

   ```
   // JavaBean类
   public class Account implements Serializable {
   
       private String username;
       private Integer age;
   
       // 所有属性的getset方法...
   }
   
   ```

   则其对应的请求参数名如下:

   ```
   <form action="account/updateAccount" method="post">
       <label>名称</label><input type="text" name="username"><br/>
       <label>年龄</label><input type="text" name="age"><br/>
       <input type="submit" value="保存">
   </form>
   
   ```

2. 若JavaBean参数的属性中包含**其它JavaBean对象**,则以`外层类属性名.内层类属性名`作为请求参数,示例如下:

    例如JavaBean类的定义如下:

   ```
   public class Account implements Serializable {
   
       private String username;
       private Intger age;
       private User user;
   
   	// 所有属性的getset方法...
   }
   
   public class User implements Serializable{
   
       private String uname;
       private Double umoney;
       
       // 所有属性的getset方法...
   }
   
   ```
   
   则其对应的请求参数名如下:
   
   ```
   <form action="account/updateAccount" method="post">
       <label>名称</label><input type="text" name="username"><br/>
       <label>年龄</label><input type="text" name="age"><br/>
       <label>用户名</label><input type="text" name="user.uname"><br/>
       <label>用户余额</label><input type="text" name="user.umoney"><br/>
       <input type="submit" value="保存">
   </form>
   ```

#### 集合类型的参数绑定

对JavaBean类中的集合属性进行参数绑定,可以分为List类型的参数绑定和Set类型的参数绑定

> 对于List类型参数,其请求参数名为集合名[下标],List类型参数可以对List,Set,数组类型成员进行赋值,但是诡异的是对Set和数组成员进行赋值时,要在Bean类的构造函数中new出对应的Set或数组,然而对于List就不用,下边的代码就可以直接跑,不知道为什么,希望知道的朋友不吝赐教
> 对于Set类型参数,其请求参数名为集合名[键],Set类型参数可以对Set,Propertis类型成员进行赋值.

例如JavaBean类的定义如下:

```
public class Account implements Serializable {

    private String username;
    private Intger age;
    
    private List<User> list;		// List集合属性
    private Map<String, User> map;	// Map集合属性


	// 所有属性的getset方法...
}

public class User implements Serializable{

    private String uname;
    private Double umoney;
    
    // 所有属性的getset方法...
}

```

则其对应的请求参数名如下:

```
<form action="account/updateAccount" method="post">
	用户名称：<input type="text" name="username"><br/>
	用户密码：<input type="password" name="password"><br/>
	用户年龄：<input type="text" name="age"><br/>
	账户1名称：<input type="text" name="accounts[0].name"><br/>
	账户1金额：<input type="text" name="accounts[0].money"><br/>
	账户2名称：<input type="text" name="accounts[1].name"><br/>
	账户2金额：<input type="text" name="accounts[1].money"><br/>
	账户3名称：<input type="text" name="accountMap['one'].name"><br/>
	账户3金额：<input type="text" name="accountMap['one'].money"><br/>
	账户4名称：<input type="text" name="accountMap['two'].name"><br/>
	账户4金额：<input type="text" name="accountMap['two'].money"><br/>
	<input type="submit" value="保存">
</form>

```

#### 自定义数据类型参数绑定

表单提交的任何数据类型都是字符串类型,SpringMVC定义了转换器,将字符串转化为我们方法参数的各种类型.我们也可以实现自定义的转换器以实现自定义的参数类型转换
自定义的类型转换器要实现Converter<String, T>接口,并在Spring容器配置bean.xml中配置该实现类. 示例如下:

在工程的java目录下创建控制器类cn.maoritian.util.StringToDateConverter类,实现Converter<String, Date>接口,完成从String类到Date类的转换:

```
// 自定义的类型转换器,完成从String类到Date类的转换
public class StringToDateConverter implements Converter<String, Date> {

    public Date convert(String source) {
        try {
            DateFormat df = new SimpleDateFormat("yyyy-MM-dd");
            Date date = df.parse(source);
            return date;
        } catch (Exception e) {
            throw new RuntimeException("类型转换错误");
        }
    }
}

```

在Spring容器配置`bean.xml`中加入如下配置:

```
<!-- 配置的类型转换器工厂 -->
<bean id="conversionService" class="org.springframework.context.support.ConversionServiceFactoryBean">
    <property name="converters">
        <set>
            <!-- 诸如我们自定义的类型转换器 -->
            <bean class="cn.maoritian.utils.StringToDateConverter"/>
        </set>
    </property>
</bean>

```

### 解决请求参数绑定中文乱码问题

在`web.xml`中配置编码转换过滤器,即可解决请求参数中文乱码的问题.

```
<!-- 配置编码转换过滤器 -->
<filter>
	<filter-name>characterEncodingFilter</filter-name>
	<filter-class>org.springframework.web.filter.CharacterEncodingFilter</filterclass>
	<!-- 指定字符集 -->
	<init-param>
		<param-name>encoding</param-name>
		<param-value>UTF-8</param-value>
	</init-param>
    <init-param>
        <param-name>forceEncoding</param-name>
        <param-value>true</param-value>
    </init-param>
</filter>
<!-- 过滤所有请求 -->
<filter-mapping>
	<filter-name>characterEncodingFilter</filter-name>
	<url-pattern>/*</url-pattern>
</filter-mapping>
```

### 通过原始ServletAPI对象处理请求

SpringMVC支持使用原始ServletAPI作为控制器方法的参数,包括`HttpServletRequest`,`HttpServletResponse`,`HttpSession`对象,他们都可以直接用做控制器方法的参数（原理如同form表单传入的对象自己会匹配方法的参数，form表单的会封装成一个对象，与其他类型的如`HttpServletRequest`一起返回给adapter,**方法的形参并没有显式的对象传入，而是spring自己依据类型反射创建并传参**）.示例如下:

```
@RequestMapping("/path")
public void myHandler(HttpServletRequest request, HttpServletResponse response) throws IOException {
    
    System.out.println(request.getParameter("param1"));
    System.out.println(request.getParameter("param1"));

    response.getWriter().println("<h3>操作成功</h3>");

}
```

### ModelAttribute

@ModelAttribute注解如果用在方法上，则用于设置参数，他会在执行处理前将参数设置到Model中。规则如下：

1. ModelAttribute设置了value属性，则将其value作为参数名，返回值作为参数值设置到Model中

    ```
    @ModelAttribute("model1")//设置了value属性为model1
    public String setModel1(){
        return "v_1";
    }
    ```
    
    
    
2. 如果方法含有Model、Map、ModelMap类型的参数，则可以直接将参数设置上去

    ```
    @ModelAttribute("model3")
    public void setModel3(Model model){
        model.addAttribute("model3","v3");
    }
    ```
    
    
    
3. 如果既没有设置value，也没有设置Model等参数，则根据返回值类型解析出参数名，返回值作为参数值设置到Model中

```
 @ModelAttribute
    public String setModel2(){
        return "v2";
    }
```


获取：

```
@RequestMapping("/testModelAttribute")
    public String testModelAttribute(Model model){
        Map datas = model.asMap();
        datas.get("model1");

​    System.out.println(datas.get("model1"));//v1
​    System.out.println(datas.get("string"));//v2
​    System.out.println(datas.get("model3"));//v3
​    return "testModelAttribute";
}
```

如果要针对某些controller生效，可结合@ModelAttribute与@ControllerAdvice使用


### @SessionAttributes 

@SessionAttributes 只能作用在类上，作用是将指定的Model中的键值对添加至session中，方便在下一次请求中使用。

* 若是model中不存在的值，则会将他添加进model

* 可由@modelAttribute 或者在参数中调用model对象，手动将其加入map中

* 可在jsp中用el表达式获取model中的值

  > 在没用使用SessionStatus清除过之前，通过@ModelAttributes和@SessionAttributes共同添加至session中的数据并不会更新.
  >
  > 原因在于org.springframework.web.bind.annotation.support.HandlerMethodInvoker#invokeHandlerMethod
  >
  > 在执行被@ModelAttributes注解的方法前，会将上一次通过@SessionAttributes添加至session中的数据添加添加至model中；
  >
  > 在执行被@ModelAttributes注解的方法前，springMVC会判断model中是否已经包含了@ModelAttributes给出的attrName，如果包含，则被@ModelAttributes注解的方法则不再执行

* 如果需要清除通过@SessionAttribute添加至 session 中的数据，则需要在controller 的 handler method中添加 SessionStatus参数，在方法体中调用SessionStatus#setComplete。

  需要注意的是，此时清除的只是该Controller通过@SessionAttribute添加至session的数据（当然，如果不同controller的@SessionAttribute拥有相同的值，则也会清除）

### 关于URL路径

确定使用哪个web应用处理请求后，根据具体web应用的配置（主要是Servlet配置的<url-pattern>），确定使用哪一个Servlet来处理请求，在请求URL中去掉context path和请求参数后，按照顺序，根据如下规则进行匹配，使用第一个匹配成功的规则进行处理。

1.完整路径匹配；

2.根据路径前缀，进行最长匹配，使用'/'作为路径分隔符；

3.使用请求路径中的后缀进行匹配，比如'.jsp'；

4.如果以上都没有匹配到合适的Servlet，将使用默认(default)Servlet来处理请求；

注意：路径匹配过程中，区分大小写；

### 如何配置Servlet中的<url-pattern>

使用‘/’开头，使用‘/*’结尾，表示使用路径匹配，比如/foo/bar/*

使用'*.xxx'表示使用后缀匹配；

只使用‘/*’，表示匹配所有的请求；

只使用'/'，表示是一个默认的Servlet；

除此之外，其他的字符都是准确匹配；

## 响应数据和结果视图

### 通过处理器方法返回值指定返回视图

SpringMVC中的处理器方法的返回值用来指定页面跳转到哪个视图,处理器的返回值可以为`String`,`void`,`ModelAndView`对象.

#### @ResponseBody

@ResponseBody的作用其实是将java对象转为json格式的数据。

@responseBody注解的作用是将controller的方法返回的对象通过适当的转换器转换为指定的格式之后，写入到response对象的body区，通常用来返回JSON数据或者是XML数据。
注意：在使用此注解之后不会再走视图处理器，而是直接将数据写入到输入流中，他的效果等同于通过response对象输出指定格式的数据。

@ResponseBody是作用在方法上的，@ResponseBody 表示该方法的返回结果直接写入 HTTP response body 中，一般在异步获取数据时使用【也就是AJAX】。
注意：==在使用 @RequestMapping后，返回值通常解析为跳转路径，但是加上 @ResponseBody 后返回结果不会被解析为跳转路径，而是直接写入 HTTP response body 中。== 比如异步获取 json 数据，加上 @ResponseBody 后，会直接返回 json 数据。@RequestBody 将 HTTP 请求正文插入方法中，使用适合的 HttpMessageConverter 将请求体写入某个对象。

#### 处理器返回`String`对象: 转发到字符串指定的URL

处理器方法返回字符串可以指定逻辑视图名,通过视图解析器解析为物理视图地址.

在本例中,因为我们在Spring容器配置文件bean.xml中配置的视图解析器中注入prefix和suffix属性,所以视图解析器会把处理器返回的"字符串值"解析为"/WEB-INF/pages/字符串值.jsp",再请求对应视图.这是一个请求转发过程,浏览器地址栏不会发生变化.

bean.xml中配置的视图解析器如下:

```xml
<bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <property name="prefix" value="/WEB-INF/pages/"></property>
    <property name="suffix" value=".jsp"></property>
</bean>
```

处理器方法如下:

```java
@Controller
@RequestMapping("/user")
public class UserController {

    @RequestMapping("/testString")
    public String testString(Model model) {
		// 执行方法体...向隐式对象添加属性attribute_user,可以在jsp中通过 ${attribute_user} 获取到
		model.addAttribute("attribute_user", new User("张三", "123"));
        
        // 经过视图解析器的处理,SpringMVC会将请求转发到/WEB-INF/pages/succeess.jsp,但浏览器地址栏显示的一直是 项目域名/user/testString
        return "success";
    }
}
```

#### 处理器返回`void`: 转发到当前URL

若处理器返回`void`,表示执行完处理器方法体内代码后,不进行请求转发,而直接转发到当前URL.若没有在`web.xml`中配置当前对应的`url-pattern`,则会返回`404`错误.

```
@Controller
@RequestMapping("/user")
public class UserController {

    @RequestMapping("/testVoid")
    public void testVoid(Model model) {
		// 执行方法体...向隐式对象添加属性attribute_user,可以在jsp中通过 ${attribute_user} 获取到
		model.addAttribute("attribute_user", new User("张三", "123"));
        
        // 处理器没有返回值,则会将请求转发到当前 项目域名/user/testVoid 路径
        // 若在web.xml中没有配置 项目域名/user/testVoid 对应的url-pattern,则会返回404错误
        return;
    }
}
```

可以在返回语句之前执行请求转发,重定向或`getWriter`方法指定视图,示例如下:

```
@Controller
@RequestMapping("/user")
public class UserController {

	@RequestMapping("/testVoid")
    public void testVoid(HttpServletRequest request, HttpServletResponse response) throws Exception {
        // 执行方法体...向隐式对象添加属性attribute_user,可以在jsp中通过 ${attribute_user} 获取到
		model.addAttribute("attribute_user", new User("张三", "123"));
        
        // 通过下面三个方法之一,可以指定访问的视图
        // 指定视图的方式1: 请求转发
        request.getRequestDispatcher("/WEB-INF/pages/success.jsp").forward(request,response);

        // 指定视图的方式2: 重定向
        response.sendRedirect(request.getContextPath() + "/index.jsp");

        // 指定视图的方式3: 通过Writer对象写入内容
        response.setCharacterEncoding("UTF-8");
        response.setContentType("text/html;charset=UTF-8");
        response.getWriter().print("你好");

        return;
    }
}

```

#### 处理器返回`ModelAndView`对象: 更灵活地添加属性和指定返回视图

ModelAndView为我们提供了一种更灵活地为页面添加属性和指定返回视图的方法,其主要方法如下:

* public ModelMap getModelMap(): 返回当前页面的ModelMap对象.

* public ModelAndView addObject(Object attributeValue): 向当前页面的ModelMap对象中添加属性

* public void setViewName(@Nullable String viewName): 指定返回视图,viewName会先被视图解析器处理解析成对应视图.

  ```java
  @Controller
  @RequestMapping("/user")
  public class UserController {
  
  	@RequestMapping("/testModelAndView")
      public ModelAndView testModelAndView() {
          
          // 创建ModelAndView对象
          ModelAndView mv = new ModelAndView();
          
          // 向model中存入属性attribute_user
  		mv.addObject("attribute_user", new User("张三", "123"));
  
          // 指定返回视图,视图解析器将"success"解析为视图URL /WEB-INF/pages/succeess.jsp
          mv.setViewName("success");
  
          return mv;
      }
  }
  
  ```

### 转发和重定向

要使用SpringMVC框架提供的请求转发,只需要在处理器方法返回的`viewName`字符串首加上`forward:`即可,要注意的是,此时`forward:`后的地址不能直接被视图解析器解析,因此要写完整的相对路径.示例如下:

```java
@Controller
@RequestMapping("/user")
public class UserController {
	@RequestMapping("/testForward")
    public String testForward() {
        // 在forward:要写完整的相对路径
        // return "forward:success"	// 错误,会将请求转发到 /项目名/user/success
        return "forward:/WEB-INF/pages/success.jsp";
    }
}

```

要使用SpringMVC框架提供的请求重定向,只需要在处理器方法返回的`viewName`字符串首加上`redirect:`即可,要注意的是,此时`redirect:`后的地址要写相对于`ContextPath`的地址.示例如下:

```java
@Controller
@RequestMapping("/user")
public class UserController {
	@RequestMapping("/testRedirct")
    public String testRedirct() {
        // 在forward:要写完整的相对路径
        // return "redirect:" + request.getContextPath() + "/index.jsp";	// 错误,会将请求转发到 /项目名/项目名/index.jsp
		return "redirect:/index.jsp";
    }
}

```

### jquery.ajax

* 异步javascript和xml
* 指一种创建交互式、快速动态[网页](https://baike.baidu.com/item/网页/99347)应用的网页开发技术，无需重新加载整个网页的情况下，能够更新部分网页的技术。

#### 前期准备

jsp在页面上引入jQuery以发送json数据,因此需要向服务器发起一个对jQuery的请求.像这种对静态资源的请求,不应当经过具体的某个处理器处理,而应当直接返回对应的静态资源.

因此我们需要在Spring容器配置bean.xml中使用<mvc:resources>标签声明该资源为静态资源,否则请求该资源会报404错误.该标签的属性如下:

location属性: 表示该资源在服务器上所在的位置,必须是一个有效的目录
mapping属性: 指定匹配的URL
我们在bean.xml中配置各静态文件的位置如下:**使得调用到不需要视图解析器的静态资源可以不经过前端控制器，否则将应不符合前端控制器的控制流程而失败**

```
<!-- 配置静态文件的路径于对应的URL -->
<!-- location属性必须是一个有效的目录,因此必须以 / 结尾 -->
<mvc:resources location="/css/" mapping="/css/**"/>
<mvc:resources location="/images/" mapping="/images/**"/>
<mvc:resources location="/js/" mapping="/js/**"/>
```

要将json字符串与JavaBean对象相互转换,我们需要引用`jackson`的jar包,在`pom.xml`中添加依赖坐标如下:

```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.9.0</version>
</dependency>
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-core</artifactId>
    <version>2.9.0</version>
</dependency>
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-annotations</artifactId>
    <version>2.9.0</version>
</dependency>
```

#### 在jsp中编写代码发送json数据

在jsp页面中编写代码发送json请求如下:

```
<script>
// 页面加载，绑定单击事件
$(function () {
    $("#btn").click(function () {
        // 发送ajax请求
        $.ajax({
            // 配置请求参数
            url: "user/testAjax",
            contentType: "application/json;charset=UTF-8",
            dataType: "json",
            type: "post",
            // 请求的json数据
            data: '{"username":"myname","password":"mypassowrd","age":30}',
            // 回调函数,处理服务器返回的数据returnData
            success: function (returnData) {
                // 我们假定服务器返回的是一个user对象,将其输出在控制台上
                console.log(returnData);            }
        });
    });
});
</script>

```

#### 在控制器中编写代码响应json数据

使用`@RequestBody`注解将请求体绑定到控制器方法参数上,使用`@ResponseBody`注解表示将该方法的返回值直接写回到HTTP响应中,而不是存入`Model`或解析为视图名.

我们引入的`jackson`包自动完成从Java实体类到json数据之间的相互转换.

```java
@Controller
@RequestMapping("/user")
public class UserController {
    @RequestMapping("/testAjax")
    @ResponseBody
    public User testAjax(@RequestBody User user) {

        System.out.println(user);
        
        // 将user对象返回给前端页面
        return user;
    }
}
```

## 文件上传

### 前提

1. <form>表单的enctype属性取值必须是multipart/form-data(默认值是application/x-www-form-urlencoded),表示表单内容是分块的.这时request对象的getParameter()方法将失效.
   我们设置了enctype属性取值为multipart/form-data,因此在请求参数头中会有一项Content-Type:multipart/form-data; boundary=----WebKitFormBoundaryOMtUEa1sSZ3ayCfC,表示当前表单内容数据是分块的,每两块之间以----WebKitFormBoundaryOMtUEa1sSZ3ayCfC分界.
   服务器通过遍历每一块数据,找到文件所在的数据块并执行保存.


2. <form>表单的method属性取值必须是post,因为get请求长度有限制.

3. 提供一个<input/>标签,用来选择上传文件.

```
<form action="/fileUpload/uploadHandler" method="post" enctype="multipart/form-data">
    param1<input type="text" name="param1"/><br/>
    param2<input type="text" name="param2"/><br/>
    选择文件<input type="file" name="fileParam"/><br/>
    <input type="submit" value="上传文件"/>
</form>

```

4. 引用文件上传的相关jar包

```
<dependency>
    <groupId>commons-fileupload</groupId>
    <artifactId>commons-fileupload</artifactId>
    <version>1.3.1</version>
</dependency>
<dependency>
    <groupId>commons-io</groupId>
    <artifactId>commons-io</artifactId>
    <version>2.4</version>
</dependency>

```

### 三种实现

#### 使用JavaEE进行文件上传

传统的JavaEE文件上传思路是通过解析`request`对象,获取表单中的上传文件项并执行保存.

```
@Controller
@RequestMapping("/fileUpload")
public class FileUploadController {

	@RequestMapping("/javaEE")
    public String fileupload1(HttpServletRequest request) throws Exception {
  
        // 创建目录保存上传的文件
        String path = request.getSession().getServletContext().getRealPath("/uploads/");
        File file = new File(path);
        if (!file.exists()) {
            file.mkdirs();
        }

        // 创建ServletFileUpload来解析request
        ServletFileUpload upload = new ServletFileUpload(new DiskFileItemFactory());
        List<FileItem> items = upload.parseRequest(request);
        // 遍历解析的结果,寻找上传文件项
        for (FileItem item : items) { 
            if (!item.isFormField()) {
                // 不是普通表单项,说明是文件上传项
                
                // 服务器中保存的文件名
                String filename = UUID.randomUUID().toString().replace("-", "") + "_" + item.getName();
                // 上传文件
                item.write(new File(path, filename));
                // 删除临时文件
                item.delete();
            }
        }

        return "success";
    }
}
```

#### 使用SpringMVC进行单服务器文件上传

配置文件解析器（springbean文件）：

```
 <!-- 配置文件上传解析器 -->
    <bean id="multipartResolver"
        class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
            <!-- 设置上传文件的最大尺寸为 5MB -->
            <property name="maxUploadSize">   
                <value>5242880</value>
            </property>
    </bean>

```

可以使用SpringMVC提供的文件解析器实现文件上传,在Spring容器中注入文件解析器`CommonsMultipartResolver`对象如下:

```
<!-- 配置文件解析器,其id是固定的,必须为multipartResolver -->
<bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
    <!-- 设置文件的最大尺寸 -->
    <property name="maxUploadSize" value="10485760"/>
</bean>
```

只要在处理器方法的参数列表中定义一个与表单文件项同名的`MultipartFile`参数,就可以将上传的文件绑定到该`MultipartFile`对象(该对象已经经过)上,调用其`transferTo(File file)`方法即可保存文件.

```
@Controller
@RequestMapping("/fileUpload")
public class FileUploadController {

	@RequestMapping("/springMVC")
    public String fileupload2(HttpServletRequest request, @RequestParam("fileParam") MultipartFile upload) throws Exception {
        
        // 创建目录保存上传的文件
        String path = request.getSession().getServletContext().getRealPath("/uploads/");
        File file = new File(path);
        if (!file.exists()) {
            file.mkdirs();
        }

        // 服务器中保存的文件名
        String filename = UUID.randomUUID().toString().replace("-", "") + "_" + upload.getOriginalFilename();
        // 上传文件
        upload.transferTo(new File(path,filename));

        return "success";
    }
}
```

#### 使用SpringMVC进行跨服务器文件上传

* 布置两个tomcat需要主要 hTTP PORT和 JMX PORT
* 所谓JMX，是Java Management Extensions(Java管理扩展)的缩写，是一个为应用程序植入管理功能的框架。用户可以在任何Java应用程序中使用这些代理和服务实现管理。

我们可以引入`jersey`库进行服务器间通信,实现将文件上传到一个专用的文件服务器,需要在`pom.xml`中引入`jersey`库的坐标如下:

```
<dependency>
    <groupId>com.sun.jersey</groupId>
    <artifactId>jersey-core</artifactId>
    <version>1.18.1</version>
</dependency>
<dependency>
    <groupId>com.sun.jersey</groupId>
    <artifactId>jersey-client</artifactId>
    <version>1.18.1</version>
</dependency>

```

在处理器方法中创建`Client`对象实现服务器间通信,将文件上传到文件服务器上,代码如下:

```
@Controller
@RequestMapping("/fileUpload")
public class FileUploadController {

	@RequestMapping("/betweenServer")
    public String fileupload3(@RequestParam("fileParam") MultipartFile upload) throws Exception {
        System.out.println("跨服务器文件上传...");

        // 文件服务器URL
        String fileServerPath = "http://localhost:9090/uploads/";	
        
        // 获取服务器中保存的文件名
        String filename = UUID.randomUUID().toString().replace("-", "") + "_" + upload.getOriginalFilename();

        // 创建客户端对象并在文件服务器上创建资源
        Client client = Client.create();
        WebResource webResource = client.resource(fileServerPath + filename);
        webResource.put(upload.getBytes());

        return "success";
    }
}
```

## 异常处理

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020032610065459.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxMTMzOTg2,size_16,color_FFFFFF,t_70)

#### 自定义异常类

```
public class SysException extends Exception {

    //异常提示信息
    private String message;

    @Override
    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }

    public SysException(String message) {
        this.message = message;
    }
}

```

#### 编写异常处理器

```
public class SysExceptionResolver implements HandlerExceptionResolver {
    @Override
    public ModelAndView resolveException(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, Exception ex) {
        SysException e = null;
        //判断异常类型是否是自定义类型
        if(ex instanceof SysException){
            //如果抛出的是系统自定义异常则直接转换
            e = (SysException) ex;
        }else{
            //如果抛出的不是系统自定义异常则重新构造一个系统错误异常。
            e = new SysException("执行失败，请联系管理员");
        }

        ModelAndView mv = new ModelAndView();
        //存入错误提示信息
        mv.addObject("errormsg",e.getMessage());
        //跳转到jsp界面
        mv.setViewName("error");
        return mv;
    }
}
```

#### 模拟异常

```
@RequestMapping("/testException")
    public String testException(){
        int i = 10/0; //模拟异常
        return "success";
    }
```

#### jsp

```
<%@ page contentType="text/html;charset=UTF-8" language="java" isELIgnored="false" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <h3>错误提示界面</h3>
    ${errormsg}
</body>
</html>
```



## 分页

前端分页

一次性请求数据表格中的所有记录(ajax)，然后在前端缓存并且计算count和分页逻辑，一般前端组件(例如dataTable)会提供分页动作。

特点是：简单，很适合小规模的web平台；当数据量大的时候会产生性能问题，在查询和网络传输的时间会很长。

后端分页

在ajax请求中指定页码(pageNum)和每页的大小(pageSize)，后端查询出当页的数据返回，前端只负责渲染。

特点是：复杂一些；性能瓶颈在MySQL的查询性能，这个当然可以调优解决。一般来说，web开发使用的是这种方式。

我们说的也是后端分页。

MySQL对分页的支持：

```
limit关键字的用法是
LIMIT [offset,] rows
offset是相对于首行的偏移量(首行是0)，rows是返回条数。

# 每页10条记录，取第一页，返回的是前10条记录
select * from tableA limit 0,10;
# 每页10条记录，取第二页，返回的是第11条记录，到第20条记录，
select * from tableA limit 10,10;
```

### 1. 引入分页插件

引入分页插件有下面2种方式，推荐使用 Maven 方式。

#### 1). 引入 Jar 包

你可以从下面的地址中下载最新版本的 jar 包

- https://oss.sonatype.org/content/repositories/releases/com/github/pagehelper/pagehelper/
- http://repo1.maven.org/maven2/com/github/pagehelper/pagehelper/

由于使用了sql 解析工具，你还需要下载 jsqlparser.jar(需要和PageHelper 依赖的版本一致) ：

- http://repo1.maven.org/maven2/com/github/jsqlparser/jsqlparser/

#### 2). 使用 Maven

在 pom.xml 中添加如下依赖：

```
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper</artifactId>
    <version>最新版本</version>
</dependency>
```

最新版本号可以从首页查看。

### 2. 配置拦截器插件

特别注意，新版拦截器是 `com.github.pagehelper.PageInterceptor`。 `com.github.pagehelper.PageHelper` 现在是一个特殊的 `dialect` 实现类，是分页插件的默认实现类，提供了和以前相同的用法。

#### 1. 在 MyBatis 配置 xml 中配置拦截器插件

```xml
<!-- 
    plugins在配置文件中的位置必须符合要求，否则会报错，顺序如下:
    properties?, settings?, 
    typeAliases?, typeHandlers?, 
    objectFactory?,objectWrapperFactory?, 
    plugins?, 
    environments?, databaseIdProvider?, mappers?
-->
<plugins>
    <!-- com.github.pagehelper为PageHelper类所在包名 -->
    <plugin interceptor="com.github.pagehelper.PageInterceptor">
        <!-- 使用下面的方式配置参数，后面会有所有的参数介绍 -->
        <property name="param1" value="value1"/>
	</plugin>
</plugins>
```

#### 2. 在 Spring 配置文件中配置拦截器插件

使用 spring 的属性配置方式，可以使用 `plugins` 属性像下面这样配置：

```xml
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
  <!-- 注意其他配置 -->
  <property name="plugins">
    <array>
      <bean class="com.github.pagehelper.PageInterceptor">
        <property name="properties">
          <!--使用下面的方式配置参数，一行配置一个 -->
          <value>
            params=value1
          </value>
        </property>
      </bean>
    </array>
  </property>
</bean>
```

#### 3. 分页插件参数介绍

分页插件提供了多个可选参数，这些参数使用时，按照上面两种配置方式中的示例配置即可。

分页插件可选参数如下：

- `dialect`：默认情况下会使用 PageHelper 方式进行分页，如果想要实现自己的分页逻辑，可以实现 `Dialect`(`com.github.pagehelper.Dialect`) 接口，然后配置该属性为实现类的全限定名称。

**下面几个参数都是针对默认 dialect 情况下的参数。使用自定义 dialect 实现时，下面的参数没有任何作用。**

1. `helperDialect`：分页插件会自动检测当前的数据库链接，自动选择合适的分页方式。 你可以配置`helperDialect`属性来指定分页插件使用哪种方言。配置时，可以使用下面的缩写值：
   `oracle`,`mysql`,`mariadb`,`sqlite`,`hsqldb`,`postgresql`,`db2`,`sqlserver`,`informix`,`h2`,`sqlserver2012`,`derby`
   **特别注意：**使用 SqlServer2012 数据库时，需要手动指定为 `sqlserver2012`，否则会使用 SqlServer2005 的方式进行分页。
   你也可以实现 `AbstractHelperDialect`，然后配置该属性为实现类的全限定名称即可使用自定义的实现方法。
2. `offsetAsPageNum`：默认值为 `false`，该参数对使用 `RowBounds` 作为分页参数时有效。 当该参数设置为 `true` 时，会将 `RowBounds` 中的 `offset` 参数当成 `pageNum` 使用，可以用页码和页面大小两个参数进行分页。
3. `rowBoundsWithCount`：默认值为`false`，该参数对使用 `RowBounds` 作为分页参数时有效。 当该参数设置为`true`时，使用 `RowBounds` 分页会进行 count 查询。
4. `pageSizeZero`：默认值为 `false`，当该参数设置为 `true` 时，如果 `pageSize=0` 或者 `RowBounds.limit = 0` 就会查询出全部的结果（相当于没有执行分页查询，但是返回结果仍然是 `Page` 类型）。
5. `reasonable`：分页合理化参数，默认值为`false`。当该参数设置为 `true` 时，`pageNum<=0` 时会查询第一页， `pageNum>pages`（超过总数时），会查询最后一页。默认`false` 时，直接根据参数进行查询。
6. `params`：为了支持`startPage(Object params)`方法，增加了该参数来配置参数映射，用于从对象中根据属性名取值， 可以配置 `pageNum,pageSize,count,pageSizeZero,reasonable`，不配置映射的用默认值， 默认值为`pageNum=pageNum;pageSize=pageSize;count=countSql;reasonable=reasonable;pageSizeZero=pageSizeZero`。
7. `supportMethodsArguments`：支持通过 Mapper 接口参数来传递分页参数，默认值`false`，分页插件会从查询方法的参数值中，自动根据上面 `params` 配置的字段中取值，查找到合适的值时就会自动分页。 使用方法可以参考测试代码中的 `com.github.pagehelper.test.basic` 包下的 `ArgumentsMapTest` 和 `ArgumentsObjTest`。
8. `autoRuntimeDialect`：默认值为 `false`。设置为 `true` 时，允许在运行时根据多数据源自动识别对应方言的分页 （不支持自动选择`sqlserver2012`，只能使用`sqlserver`），用法和注意事项参考下面的**场景五**。
9. `closeConn`：默认值为 `true`。当使用运行时动态数据源或没有设置 `helperDialect` 属性自动获取数据库类型时，会自动获取一个数据库连接， 通过该属性来设置是否关闭获取的这个连接，默认`true`关闭，设置为 `false` 后，不会关闭获取的连接，这个参数的设置要根据自己选择的数据源来决定。
10. `aggregateFunctions`(5.1.5+)：默认为所有常见数据库的聚合函数，允许手动添加聚合函数（影响行数），所有以聚合函数开头的函数，在进行 count 转换时，会套一层。其他函数和列会被替换为 count(0)，其中count列可以自己配置。

**重要提示：**

当 `offsetAsPageNum=false` 的时候，由于 `PageNum` 问题，`RowBounds`查询的时候 `reasonable` 会强制为 `false`。使用 `PageHelper.startPage` 方法不受影响。

#### 4. 如何选择配置这些参数

单独看每个参数的说明可能是一件让人不爽的事情，这里列举一些可能会用到某些参数的情况。

##### 场景一

如果你仍然在用类似ibatis式的命名空间调用方式，你也许会用到`rowBoundsWithCount`， 分页插件对`RowBounds`支持和 MyBatis 默认的方式是一致，默认情况下不会进行 count 查询，如果你想在分页查询时进行 count 查询， 以及使用更强大的 `PageInfo` 类，你需要设置该参数为 `true`。

**注：** `PageRowBounds` 想要查询总数也需要配置该属性为 `true`。

##### 场景二

如果你仍然在用类似ibatis式的命名空间调用方式，你觉得 `RowBounds` 中的两个参数 `offset,limit` 不如 `pageNum,pageSize` 容易理解， 你可以使用 `offsetAsPageNum` 参数，将该参数设置为 `true` 后，`offset`会当成 `pageNum` 使用，`limit` 和 `pageSize` 含义相同。

##### 场景三

如果觉得某个地方使用分页后，你仍然想通过控制参数查询全部的结果，你可以配置 `pageSizeZero` 为 `true`， 配置后，当 `pageSize=0` 或者 `RowBounds.limit = 0` 就会查询出全部的结果。

##### 场景四

如果你分页插件使用于类似分页查看列表式的数据，如新闻列表，软件列表， 你希望用户输入的页数不在合法范围（第一页到最后一页之外）时能够正确的响应到正确的结果页面， 那么你可以配置 `reasonable` 为 `true`，这时如果 `pageNum<=0` 会查询第一页，如果 `pageNum>总页数` 会查询最后一页。

##### 场景五

如果你在 Spring 中配置了动态数据源，并且连接不同类型的数据库，这时你可以配置 `autoRuntimeDialect` 为 `true`，这样在使用不同数据源时，会使用匹配的分页进行查询。 这种情况下，你还需要特别注意 `closeConn` 参数，由于获取数据源类型会获取一个数据库连接，所以需要通过这个参数来控制获取连接后，是否关闭该连接。 默认为 `true`，有些数据库连接关闭后就没法进行后续的数据库操作。而有些数据库连接不关闭就会很快由于连接数用完而导致数据库无响应。所以在使用该功能时，特别需要注意你使用的数据源是否需要关闭数据库连接。

当不使用动态数据源而只是自动获取 `helperDialect` 时，数据库连接只会获取一次，所以不需要担心占用的这一个连接是否会导致数据库出错，但是最好也根据数据源的特性选择是否关闭连接。

### 3. 如何在代码中使用

阅读前请注意看[重要提示](https://github.com/pagehelper/Mybatis-PageHelper/blob/master/wikis/zh/Important.md)

分页插件支持以下几种调用方式：

```java
//第一种，RowBounds方式的调用
List<User> list = sqlSession.selectList("x.y.selectIf", null, new RowBounds(0, 10));

//第二种，Mapper接口方式的调用，推荐这种使用方式。
PageHelper.startPage(1, 10);
List<User> list = userMapper.selectIf(1);

//第三种，Mapper接口方式的调用，推荐这种使用方式。
PageHelper.offsetPage(1, 10);
List<User> list = userMapper.selectIf(1);

//第四种，参数方法调用
//存在以下 Mapper 接口方法，你不需要在 xml 处理后两个参数
public interface CountryMapper {
    List<User> selectByPageNumSize(
            @Param("user") User user,
            @Param("pageNum") int pageNum, 
            @Param("pageSize") int pageSize);
}
//配置supportMethodsArguments=true
//在代码中直接调用：
List<User> list = userMapper.selectByPageNumSize(user, 1, 10);

//第五种，参数对象
//如果 pageNum 和 pageSize 存在于 User 对象中，只要参数有值，也会被分页
//有如下 User 对象
public class User {
    //其他fields
    //下面两个参数名和 params 配置的名字一致
    private Integer pageNum;
    private Integer pageSize;
}
//存在以下 Mapper 接口方法，你不需要在 xml 处理后两个参数
public interface CountryMapper {
    List<User> selectByPageNumSize(User user);
}
//当 user 中的 pageNum!= null && pageSize!= null 时，会自动分页
List<User> list = userMapper.selectByPageNumSize(user);

//第六种，ISelect 接口方式
//jdk6,7用法，创建接口
Page<User> page = PageHelper.startPage(1, 10).doSelectPage(new ISelect() {
    @Override
    public void doSelect() {
        userMapper.selectGroupBy();
    }
});
//jdk8 lambda用法
Page<User> page = PageHelper.startPage(1, 10).doSelectPage(()-> userMapper.selectGroupBy());

//也可以直接返回PageInfo，注意doSelectPageInfo方法和doSelectPage
pageInfo = PageHelper.startPage(1, 10).doSelectPageInfo(new ISelect() {
    @Override
    public void doSelect() {
        userMapper.selectGroupBy();
    }
});
//对应的lambda用法
pageInfo = PageHelper.startPage(1, 10).doSelectPageInfo(() -> userMapper.selectGroupBy());

//count查询，返回一个查询语句的count数
long total = PageHelper.count(new ISelect() {
    @Override
    public void doSelect() {
        userMapper.selectLike(user);
    }
});
//lambda
total = PageHelper.count(()->userMapper.selectLike(user));
```

下面对最常用的方式进行详细介绍

#### 1). RowBounds方式的调用

```
List<User> list = sqlSession.selectList("x.y.selectIf", null, new RowBounds(1, 10));
```

使用这种调用方式时，你可以使用RowBounds参数进行分页，这种方式侵入性最小，我们可以看到，通过RowBounds方式调用只是使用了这个参数，并没有增加其他任何内容。

分页插件检测到使用了RowBounds参数时，就会对该查询进行**物理分页**。

关于这种方式的调用，有两个特殊的参数是针对 `RowBounds` 的，你可以参看上面的 **场景一** 和 **场景二**

**注：**不只有命名空间方式可以用RowBounds，使用接口的时候也可以增加RowBounds参数，例如：

```
//这种情况下也会进行物理分页查询
List<User> selectAll(RowBounds rowBounds);
```

**注意：** 由于默认情况下的 `RowBounds` 无法获取查询总数，分页插件提供了一个继承自 `RowBounds` 的 `PageRowBounds`，这个对象中增加了 `total` 属性，执行分页查询后，可以从该属性得到查询总数。

#### 2). `PageHelper.startPage` 静态方法调用

除了 `PageHelper.startPage` 方法外，还提供了类似用法的 `PageHelper.offsetPage` 方法。

在你需要进行分页的 MyBatis 查询方法前调用 `PageHelper.startPage` 静态方法即可，紧跟在这个方法后的第一个**MyBatis 查询方法**会被进行分页。

##### 例一：

```
//获取第1页，10条内容，默认查询总数count
PageHelper.startPage(1, 10);
//紧跟着的第一个select方法会被分页
List<User> list = userMapper.selectIf(1);
assertEquals(2, list.get(0).getId());
assertEquals(10, list.size());
//分页时，实际返回的结果list类型是Page<E>，如果想取出分页信息，需要强制转换为Page<E>
assertEquals(182, ((Page) list).getTotal());
```

##### 例二：

```
//request: url?pageNum=1&pageSize=10
//支持 ServletRequest,Map,POJO 对象，需要配合 params 参数
PageHelper.startPage(request);
//紧跟着的第一个select方法会被分页
List<User> list = userMapper.selectIf(1);

//后面的不会被分页，除非再次调用PageHelper.startPage
List<User> list2 = userMapper.selectIf(null);
//list1
assertEquals(2, list.get(0).getId());
assertEquals(10, list.size());
//分页时，实际返回的结果list类型是Page<E>，如果想取出分页信息，需要强制转换为Page<E>，
//或者使用PageInfo类（下面的例子有介绍）
assertEquals(182, ((Page) list).getTotal());
//list2
assertEquals(1, list2.get(0).getId());
assertEquals(182, list2.size());
```

##### 例三，使用`PageInfo`的用法：

```
//获取第1页，10条内容，默认查询总数count
PageHelper.startPage(1, 10);
List<User> list = userMapper.selectAll();
//用PageInfo对结果进行包装
PageInfo page = new PageInfo(list);
//测试PageInfo全部属性
//PageInfo包含了非常全面的分页属性
assertEquals(1, page.getPageNum());
assertEquals(10, page.getPageSize());
assertEquals(1, page.getStartRow());
assertEquals(10, page.getEndRow());
assertEquals(183, page.getTotal());
assertEquals(19, page.getPages());
assertEquals(1, page.getFirstPage());
assertEquals(8, page.getLastPage());
assertEquals(true, page.isFirstPage());
assertEquals(false, page.isLastPage());
assertEquals(false, page.isHasPreviousPage());
assertEquals(true, page.isHasNextPage());
```

#### 3). 使用参数方式

想要使用参数方式，需要配置 `supportMethodsArguments` 参数为 `true`，同时要配置 `params` 参数。 例如下面的配置：

```
<plugins>
    <!-- com.github.pagehelper为PageHelper类所在包名 -->
    <plugin interceptor="com.github.pagehelper.PageInterceptor">
        <!-- 使用下面的方式配置参数，后面会有所有的参数介绍 -->
        <property name="supportMethodsArguments" value="true"/>
        <property name="params" value="pageNum=pageNumKey;pageSize=pageSizeKey;"/>
	</plugin>
</plugins>
```

在 MyBatis 方法中：

```
List<User> selectByPageNumSize(
        @Param("user") User user,
        @Param("pageNumKey") int pageNum, 
        @Param("pageSizeKey") int pageSize);
```

当调用这个方法时，由于同时发现了 `pageNumKey` 和 `pageSizeKey` 参数，这个方法就会被分页。params 提供的几个参数都可以这样使用。

除了上面这种方式外，如果 User 对象中包含这两个参数值，也可以有下面的方法：

```
List<User> selectByPageNumSize(User user);
```

当从 User 中同时发现了 `pageNumKey` 和 `pageSizeKey` 参数，这个方法就会被分页。

注意：`pageNum` 和 `pageSize` 两个属性同时存在才会触发分页操作，在这个前提下，其他的分页参数才会生效。

#### 3). `PageHelper` 安全调用

##### 1. 使用 `RowBounds` 和 `PageRowBounds` 参数方式是极其安全的

##### 2. 使用参数方式是极其安全的

##### 3. 使用 ISelect 接口调用是极其安全的

ISelect 接口方式除了可以保证安全外，还特别实现了将查询转换为单纯的 count 查询方式，这个方法可以将任意的查询方法，变成一个 `select count(*)` 的查询方法。

##### 4. 什么时候会导致不安全的分页？

`PageHelper` 方法使用了静态的 `ThreadLocal` 参数，分页参数和线程是绑定的。

只要你可以保证在 `PageHelper` 方法调用后紧跟 MyBatis 查询方法，这就是安全的。因为 `PageHelper` 在 `finally` 代码段中自动清除了 `ThreadLocal` 存储的对象。

如果代码在进入 `Executor` 前发生异常，就会导致线程不可用，这属于人为的 Bug（例如接口方法和 XML 中的不匹配，导致找不到 `MappedStatement` 时）， 这种情况由于线程不可用，也不会导致 `ThreadLocal` 参数被错误的使用。

但是如果你写出下面这样的代码，就是不安全的用法：

```
PageHelper.startPage(1, 10);
List<User> list;
if(param1 != null){
    list = userMapper.selectIf(param1);
} else {
    list = new ArrayList<User>();
}
```

这种情况下由于 param1 存在 null 的情况，就会导致 PageHelper 生产了一个分页参数，但是没有被消费，这个参数就会一直保留在这个线程上。当这个线程再次被使用时，就可能导致不该分页的方法去消费这个分页参数，这就产生了莫名其妙的分页。

上面这个代码，应该写成下面这个样子：

```
List<User> list;
if(param1 != null){
    PageHelper.startPage(1, 10);
    list = userMapper.selectIf(param1);
} else {
    list = new ArrayList<User>();
}
```

这种写法就能保证安全。

如果你对此不放心，你可以手动清理 `ThreadLocal` 存储的分页参数，可以像下面这样使用：

```
List<User> list;
if(param1 != null){
    PageHelper.startPage(1, 10);
    try{
        list = userMapper.selectAll();
    } finally {
        PageHelper.clearPage();
    }
} else {
    list = new ArrayList<User>();
}
```

这么写很不好看，而且没有必要。



###  5、梦开始的地方

* 探究原理我们就需要从哪开始会与PageHelper开始有关系，首先PageHelper只能用在selsect上，相关性最大的便是getmapper生成的代理类与sqlsession.selectList方法，而getmapper的最终的实现中也有selectList，从复用的角度，两者应该殊途同归。

* 大致的流程如此：

  ![img](https://picb.zhimg.com/80/v2-29eba52a245fca548c26aa853ae5b576_720w.jpg)

* 而defaultSqlsession中有selectCursor和selectList，两者也都用到了RowBounds

```java
public class DefaultSqlSession implements SqlSession {
public <T> Cursor<T> selectCursor(String statement, Object parameter, RowBounds rowBounds) {
    Cursor var6;
    try {
        MappedStatement ms = this.configuration.getMappedStatement(statement);
        Cursor<T> cursor = this.executor.queryCursor(ms, this.wrapCollection(parameter), rowBounds);
        this.registerCursor(cursor);
        var6 = cursor;
    } catch (Exception var10) {
        throw ExceptionFactory.wrapException("Error querying database.  Cause: " + var10, var10);
    } finally {
        ErrorContext.instance().reset();
    }

    return var6;
}
public <E> List<E> selectList(String statement, Object parameter, RowBounds rowBounds) {
        List var5;
        try {
             
          // 获取需要执行的statement语句
            MappedStatement ms = this.configuration.getMappedStatement(statement);
            
         //Page能被引用的原因的开头   
         //Page能被引用的原因的开头
         //梦开始的地方
            var5 = this.executor.query(ms, this.wrapCollection(parameter), rowBounds, Executor.NO_RESULT_HANDLER);
        } catch (Exception var9) {
            throw ExceptionFactory.wrapException("Error querying database.  Cause: " + var9, var9);
        } finally {
            ErrorContext.instance().reset();
        }

        return var5;
    }
```



* 正常流程下，接着便是调用SimpleExecutor（extends BaseExecutor）中的query方法 ，queryFromDatabase方法，doquery方法(这三个都是BaseExecutor的)

```
public class SimpleExecutor extends BaseExecutor {

public <E> List<E> doQuery(MappedStatement ms, Object parameter, RowBounds rowBounds, ResultHandler resultHandler, BoundSql boundSql) throws SQLException {
    Statement stmt = null;

    List var9;
    try {
        Configuration configuration = ms.getConfiguration();
        
        // 实例化一个语句处理类，很关键
        StatementHandler handler = configuration.newStatementHandler(this.wrapper, ms, parameter, rowBounds, resultHandler, boundSql);
        
        //获取connection
        stmt = this.prepareStatement(handler, ms.getStatementLog());
        
        //执行语句
        var9 = handler.query(stmt, resultHandler);
    } finally {
        this.closeStatement(stmt);
    }

    return var9;
}
}
```



* Configuration中的newStatementHandler

```java
public StatementHandler newStatementHandler(Executor executor, MappedStatement mappedStatement, Object parameterObject, RowBounds rowBounds, ResultHandler resultHandler, BoundSql boundSql) {
	//新建一个StatementHandler的实现类
    StatementHandler statementHandler = new RoutingStatementHandler(executor, mappedStatement, parameterObject, rowBounds, resultHandler, boundSql);
    
    
    //最最关键的一步，将StatementHandler进行动态代理，实现责任链中Interceptor对StatementHandler的增强，生成代理类
    StatementHandler statementHandler = (StatementHandler)this.interceptorChain.pluginAll(statementHandler);
    return statementHandler;
}
```



* InterceptorChain便是责任链的实现类了，他存储了我们再.xml文件的plugins中的interceptor，在Configutation起初创建的时候便已经同时创建了，Configuraiton自从sqlsessionFatoryBean容器化调用getObjct后，在buildSqlSessionFactory方法创建后，便一直贯穿了基本所有有sqlsession字样的业务，sqlsession中“最顶”的类了。

```
public class InterceptorChain {

// 自configuration被创建时也随之创建并赋值好了
private final List<Interceptor> interceptors = new ArrayList();

 public Object pluginAll(Object target) {
        Interceptor interceptor;
        
        //将interceptors中的interceptor逐个取出，调用plugin方法，用Plugin类生成代理对象
        for(Iterator var2 = this.interceptors.iterator(); var2.hasNext(); target = interceptor.plugin(target)) {
            interceptor = (Interceptor)var2.next();
        }

        return target;
    }
}
```

* 我们可能对interceptor会有很多疑问

> https://www.jianshu.com/p/9c1c78604e4e
>
> **问题一: 我们的 Interceptor 是何时被注册到 ibatis 的, 注册到哪里去了**
>
> 首先回答注册到哪里去: configuration.InterceptorChain 中 , 结构为 : List interceptors =new ArrayList();
>
> xml 声明 Interceptor 的地方 有两个: 
>
> \1. ibatis 的 config 配置 <plugins> 中配置 , 这个配置文件 我们一般叫做 mybatis-config.xml 
>
> \2. spring 配置数据源的地方配置 sqlSessionFactory(class="org.mybatis.spring.SqlSessionFactoryBean") 时 以property 的方式给sqlSessionFactoryBean 的 plugins 赋值
>
> ① 方式声明的plugin 添加的 configuration的 InterceptorChain 路径为: SqlSessionFactoryBean.afterPropertiesSet().buildSqlSessionFactory() ---> XmlConfigBuilder.parse().parseConfiguration(XNode root).pluginElement(root.evalNode("plugins")).**configuration.addInterceptor(interceptorInstance)** 
>
> 进而调用 InterceptorChain的addInterceptor 方法添加 到 InterceptorChain 的 List interceptors =new ArrayList(); 中
>
> ②方式声明的plugin 添加到 configuration的InterceptorChain 路径为:
>
> SqlSessionFactoryBean.afterPropertiesSet().buildSqlSessionFactory()**.configuration.addInterceptor(interceptorInstance)**
>
> ①和②的实现逻辑都从 sqlSessionFactoryBean.afterPropertiesSet().buildSqlSessionFactory() 开始看就好 
>
> **问题二: 我们的Interceptor 是何时被调用的 , 初次被调用时调用了哪个方法 **
>
> InterceptorChain 除了问题一的 addInterceptor 方法外 还有两个方法:
>
> > public Object pluginAll(Object target)
> >
> > public List getInterceptors()
>
> 下面我们看一下 pluginAll 的实现:
>
> > public Object pluginAll(Object target) {
> >
> > for (Interceptor interceptor :interceptors) {
> >
> > target = interceptor.plugin(target);
> >
> >  }
> >
> > return target;
> >
> > }

* 接下去我们需要了解**mybatis 拦截器主体结构**，通过一个完整的流程来了解什么是责任链，他的作用，他是何时开始便被决定要调用的。

  

### 5、mybatis 拦截器主体结构

https://www.cnblogs.com/sanzao/p/11423849.html

在编写 mybatis 插件的时候，首先要实现 **Interceptor** 接口，然后在 mybatis-conf.xml 中添加插件，

```xml
<configuration>
  <plugins>
    <plugin interceptor="***.interceptor1"/>
    <plugin interceptor="***.interceptor2"/>
  </plugins>
</configuration>

```

这里需要注意的是，添加的插件是有顺序的，因为在解析的时候是依次放入 ArrayList 里面，而调用的时候其顺序为：**2 > 1 > target > 1 > 2**；（插件的顺序可能会影响执行的流程）更加细致的讲解可以参考 [QueryInterceptor 规范](https://pagehelper.github.io/docs/interceptor/) ;

![img](https://picb.zhimg.com/80/v2-d68dbb4e95b07a4adfb91e88be262718_720w.jpg)

然后当插件初始化完成之后，添加插件的流程如下：

![img](https://img2018.cnblogs.com/blog/1119937/201908/1119937-20190828142637395-1990851698.png)

首先要注意的是，mybatis 插件的拦截目标有四个，Executor、StatementHandler、ParameterHandler、ResultSetHandler：

```java
public ParameterHandler newParameterHandler(MappedStatement mappedStatement, Object parameterObject, BoundSql boundSql) {
  ParameterHandler parameterHandler = mappedStatement.getLang().createParameterHandler(mappedStatement, parameterObject, boundSql);
  parameterHandler = (ParameterHandler) interceptorChain.pluginAll(parameterHandler);
  return parameterHandler;
}

public ResultSetHandler newResultSetHandler(Executor executor, MappedStatement mappedStatement, RowBounds rowBounds, ParameterHandler parameterHandler,
    ResultHandler resultHandler, BoundSql boundSql) {
  ResultSetHandler resultSetHandler = new DefaultResultSetHandler(executor, mappedStatement, parameterHandler, resultHandler, boundSql, rowBounds);
  resultSetHandler = (ResultSetHandler) interceptorChain.pluginAll(resultSetHandler);
  return resultSetHandler;
}

public StatementHandler newStatementHandler(Executor executor, MappedStatement mappedStatement, Object parameterObject, RowBounds rowBounds, ResultHandler resultHandler, BoundSql boundSql) {
  StatementHandler statementHandler = new RoutingStatementHandler(executor, mappedStatement, parameterObject, rowBounds, resultHandler, boundSql);
  statementHandler = (StatementHandler) interceptorChain.pluginAll(statementHandler);
  return statementHandler;
}

public Executor newExecutor(Transaction transaction, ExecutorType executorType) {
  executorType = executorType == null ? defaultExecutorType : executorType;
  executorType = executorType == null ? ExecutorType.SIMPLE : executorType;
  Executor executor;
  if (ExecutorType.BATCH == executorType) {
    executor = new BatchExecutor(this, transaction);
  } else if (ExecutorType.REUSE == executorType) {
    executor = new ReuseExecutor(this, transaction);
  } else {
    executor = new SimpleExecutor(this, transaction);
  }
  if (cacheEnabled) {
    executor = new CachingExecutor(executor);
  }
  executor = (Executor) interceptorChain.pluginAll(executor);
  return executor;
}
```

**这里使用的时候都是用动态代理将多个插件用责任链的方式添加的，最后返回的是一个代理对象；** 其责任链的添加过程如下：

```java
public Object pluginAll(Object target) {
  for (Interceptor interceptor : interceptors) {
    target = interceptor.plugin(target);
  }
  return target;
}
```

最终动态代理生成和调用的过程都在 Plugin 类中：

```java
public static Object wrap(Object target, Interceptor interceptor) {
  Map<Class<?>, Set<Method>> signatureMap = getSignatureMap(interceptor); // 获取签名Map
  Class<?> type = target.getClass(); // 拦截目标 (ParameterHandler|ResultSetHandler|StatementHandler|Executor)
  Class<?>[] interfaces = getAllInterfaces(type, signatureMap);  // 获取目标接口
  if (interfaces.length > 0) {
    return Proxy.newProxyInstance(  // 生成代理
        type.getClassLoader(),
        interfaces,
        new Plugin(target, interceptor, signatureMap));
  }
  return target;
}
```

这里所说的签名是指在编写插件的时候，指定的目标接口和方法，例如：

```java
@Intercepts({
  @Signature(type = Executor.class, method = "update", args = {MappedStatement.class, Object.class}),
  @Signature(type = Executor.class, method = "query", args = {MappedStatement.class, Object.class, RowBounds.class, ResultHandler.class})
})
public class ExamplePlugin implements Interceptor {
  public Object intercept(Invocation invocation) throws Throwable {
    ...
  }
}
```

这里就指定了拦截 Executor 的具有相应方法的 update、query 方法；注解的代码很简单，大家可以自行查看；然后通过 getSignatureMap 方法反射取出对应的 Method 对象，在通过 getAllInterfaces 方法判断，目标对象是否有对应的方法，有就生成代理对象，没有就直接反对目标对象；

在调用的时候：

```java
public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
  try {
    Set<Method> methods = signatureMap.get(method.getDeclaringClass());  // 取出拦截的目标方法
    if (methods != null && methods.contains(method)) { // 判断这个调用的方法是否在拦截范围内
      return interceptor.intercept(new Invocation(target, method, args)); // 在目标范围内就拦截
    }
    return method.invoke(target, args); // 不在目标范围内就直接调用方法本身
  } catch (Exception e) {
    throw ExceptionUtil.unwrapThrowable(e);
  }
}
```

### 6、PageHelper 拦截器分析

mybatis 插件我们平时使用最多的就是分页插件了，这里以 PageHelper 为例，其使用方法可以查看相应的文档 [如何使用分页插件](https://pagehelper.github.io/docs/howtouse/)，因为官方文档讲解的很详细了，我这里就简单补充分页插件需要做哪几件事情；

**使用：**

```java
PageHelper.startPage(1, 2);
List<User> list = userMapper1.getAll();
```

PageHelper 还有很多中使用方式，这是最常用的一种，他其实就是在 ThreadLocal 中设置了 Page 对象，能取到就代表需要分页，在分页完成后在移除，这样就不会导致其他方法分页；（PageHelper 使用的其他方法，也是围绕 Page 对象的设置进行的）

```java
protected static final ThreadLocal<Page> LOCAL_PAGE = new ThreadLocal<Page>();
public static <E> Page<E> startPage(int pageNum, int pageSize, boolean count, Boolean reasonable, Boolean pageSizeZero) {
  Page<E> page = new Page<E>(pageNum, pageSize, count);
  page.setReasonable(reasonable);
  page.setPageSizeZero(pageSizeZero);
  //当已经执行过orderBy的时候
  Page<E> oldPage = getLocalPage();
  if (oldPage != null && oldPage.isOrderByOnly()) {
    page.setOrderBy(oldPage.getOrderBy());
  }
  setLocalPage(page);
  return page;
}
```

**主要实现：**

```java
@Intercepts({
  @Signature(type = Executor.class, method = "query", args = {MappedStatement.class, Object.class, RowBounds.class, ResultHandler.class}),
  @Signature(type = Executor.class, method = "query", args = {MappedStatement.class, Object.class, RowBounds.class, ResultHandler.class, CacheKey.class, BoundSql.class}),
})
public class PageInterceptor implements Interceptor {

  @Override
  public Object intercept(Invocation invocation) throws Throwable {
    try {
      Object[] args = invocation.getArgs();
      MappedStatement ms = (MappedStatement) args[0];
      Object parameter = args[1];
      RowBounds rowBounds = (RowBounds) args[2];
      ResultHandler resultHandler = (ResultHandler) args[3];
      Executor executor = (Executor) invocation.getTarget();
      CacheKey cacheKey;
      BoundSql boundSql;
      //由于逻辑关系，只会进入一次
      if (args.length == 4) {
        //4 个参数时
        boundSql = ms.getBoundSql(parameter);
        cacheKey = executor.createCacheKey(ms, parameter, rowBounds, boundSql);
      } else {
        //6 个参数时
        cacheKey = (CacheKey) args[4];
        boundSql = (BoundSql) args[5];
      }
      checkDialectExists();

      List resultList;
      //调用方法判断是否需要进行分页，如果不需要，直接返回结果
      if (!dialect.skip(ms, parameter, rowBounds)) {
        //判断是否需要进行 count 查询
        if (dialect.beforeCount(ms, parameter, rowBounds)) {
          //查询总数
          Long count = count(executor, ms, parameter, rowBounds, resultHandler, boundSql);
          //处理查询总数，返回 true 时继续分页查询，false 时直接返回
          if (!dialect.afterCount(count, parameter, rowBounds)) {
            //当查询总数为 0 时，直接返回空的结果
            return dialect.afterPage(new ArrayList(), parameter, rowBounds);
          }
        }
        resultList = ExecutorUtil.pageQuery(dialect, executor,
            ms, parameter, rowBounds, resultHandler, boundSql, cacheKey);
      } else {
        //rowBounds用参数值，不使用分页插件处理时，仍然支持默认的内存分页
        resultList = executor.query(ms, parameter, rowBounds, resultHandler, cacheKey, boundSql);
      }
      return dialect.afterPage(resultList, parameter, rowBounds);
    } finally {
      if(dialect != null){
        dialect.afterAll();
      }
    }
  }
}
```

- 首先可以看到拦截的是 Executor 的两个 query 方法（这里的两个方法具体拦截到哪一个受插件顺序影响，最终影响到 cacheKey 和 boundSql 的初始化）；
- 然后使用 checkDialectExists 判断是否支持对应的数据库；
- 在分页之前需要查询总数，这里会生成相应的 sql 语句以及对应的 MappedStatement 对象，并缓存；
- 然后拼接分页查询语句，并生成相应的 MappedStatement 对象，同时缓存；
- 最后查询，查询完成后使用 dialect.afterPage 移除 Page对象

### 7. ExecutorUtil和MySqlDialect

* 当代理对象被调用时，便会调用Plugin的wrap方法生成的代理对象的invoke方法，其对调用interceptor.intercept，即上面的主要实现，**若是用mysql，则其中的dialect便是MySqlDialect**，ExecutorUtil.pageQuery中也会调用的方法。
* 我们先讲下ExecutorUtil.pageQuery,因为是静态方法，存于JVM的方法区中，可直接调用。

> ```java
> public abstract class ExecutorUtil {
> 
> public static <E> List<E> pageQuery(Dialect dialect, Executor executor, MappedStatement ms, Object parameter, RowBounds rowBounds, ResultHandler resultHandler, BoundSql boundSql, CacheKey cacheKey) throws SQLException {
> 	//判断是否有Page
>  if (!dialect.beforePage(ms, parameter, rowBounds)) 
>  {
>  	//没有则用RowBounds.DEFAULT执行query
>      return executor.query(ms, parameter, RowBounds.DEFAULT, resultHandler, cacheKey, boundSql);
>  } else {
>  
>  	//执行dialect.processParameterObject和getPageSql
>      parameter = dialect.processParameterObject(ms, parameter, boundSql, cacheKey);
>      String pageSql = dialect.getPageSql(ms, boundSql, parameter, rowBounds, cacheKey);
>      
>      //保存page的sql相关信息
>      BoundSql pageBoundSql = new BoundSql(ms.getConfiguration(), pageSql, boundSql.getParameterMappings(), parameter);
>      Map<String, Object> additionalParameters = getAdditionalParameter(boundSql);
>      Iterator var12 = additionalParameters.keySet().iterator();
> 
>      while(var12.hasNext()) {
>          String key = (String)var12.next();
>          pageBoundSql.setAdditionalParameter(key, additionalParameters.get(key));
>      }
> 		
> 		//用pageBoundSql执行代替旧的sql语句执行
>      return executor.query(ms, parameter, RowBounds.DEFAULT, resultHandler, cacheKey, pageBoundSql);
>  }
> }
> ```
>
> 

* 接着便是MySqlDialect的时间了，可以看出是继承于AbstractHelperDialect（提供了beforePage、beforeCount、afterPage、afterCount等常用的判断，结束处理方法，也实现了processParameterObject（对各个数据库的Dialect的processPageParameter方法调用前的预处理，生成parameter参数放到paramMap，用来生成应用了Page后的BoundSql））

```
public class MySqlDialect extends AbstractHelperDialect {
    public MySqlDialect() {
    }

    public Object processPageParameter(MappedStatement ms, Map<String, Object> paramMap, Page page, BoundSql boundSql, CacheKey pageKey) {
        paramMap.put("First_PageHelper", page.getStartRow());
        paramMap.put("Second_PageHelper", page.getPageSize());
        pageKey.update(page.getStartRow());
        pageKey.update(page.getPageSize());
        if (boundSql.getParameterMappings() != null) {
            List<ParameterMapping> newParameterMappings = new ArrayList(boundSql.getParameterMappings());
            if (page.getStartRow() == 0) {
                newParameterMappings.add((new Builder(ms.getConfiguration(), "Second_PageHelper", Integer.class)).build());
            } else {
                newParameterMappings.add((new Builder(ms.getConfiguration(), "First_PageHelper", Integer.class)).build());
                newParameterMappings.add((new Builder(ms.getConfiguration(), "Second_PageHelper", Integer.class)).build());
            }

            MetaObject metaObject = MetaObjectUtil.forObject(boundSql);
            metaObject.setValue("parameterMappings", newParameterMappings);
        }

        return paramMap;
    }

    public String getPageSql(String sql, Page page, CacheKey pageKey) {
        StringBuilder sqlBuilder = new StringBuilder(sql.length() + 14);
        sqlBuilder.append(sql);
        if (page.getStartRow() == 0) {
            sqlBuilder.append(" LIMIT ? ");
        } else {
            sqlBuilder.append(" LIMIT ?, ? ");
        }

        return sqlBuilder.toString();
    }
}
```

```java
public Object processParameterObject(MappedStatement ms, Object parameterObject, BoundSql boundSql, CacheKey pageKey) {
    Page page = this.getLocalPage();
    if (page.isOrderByOnly()) {
        return parameterObject;
    } else {
        Map<String, Object> paramMap = null;
        if (parameterObject == null) {
            paramMap = new HashMap();
        } else if (parameterObject instanceof Map) {
            paramMap = new HashMap();
            paramMap.putAll((Map)parameterObject);
        } else {
            paramMap = new HashMap();
            boolean hasTypeHandler = ms.getConfiguration().getTypeHandlerRegistry().hasTypeHandler(parameterObject.getClass());
            MetaObject metaObject = MetaObjectUtil.forObject(parameterObject);
            if (!hasTypeHandler) {
                String[] var9 = metaObject.getGetterNames();
                int var10 = var9.length;

                for(int var11 = 0; var11 < var10; ++var11) {
                    String name = var9[var11];
                    paramMap.put(name, metaObject.getValue(name));
                }
            }

            if (boundSql.getParameterMappings() != null && boundSql.getParameterMappings().size() > 0) {
                Iterator var13 = boundSql.getParameterMappings().iterator();

                ParameterMapping parameterMapping;
                String name;
                do {
                    do {
                        do {
                            do {
                                if (!var13.hasNext()) {
                                    return this.processPageParameter(ms, paramMap, page, boundSql, pageKey);
                                }

                                parameterMapping = (ParameterMapping)var13.next();
                                name = parameterMapping.getProperty();
                            } while(name.equals("First_PageHelper"));
                        } while(name.equals("Second_PageHelper"));
                    } while(paramMap.get(name) != null);
                } while(!hasTypeHandler && !parameterMapping.getJavaType().equals(parameterObject.getClass()));

                paramMap.put(name, parameterObject);
            }
        }

        return this.processPageParameter(ms, paramMap, page, boundSql, pageKey);
    }
}
```

* 依照**Interceptor** 的拦截顺序依次实现了相应的Intercepte方法后，相关参数改变，条件判断时便会直接跳过就如jdbc的execute。
* 最后便是执行转换后的sql语句的事情了

### 8、Filter和MVC Interceptor

* **该PageInterceptor是在dao层下面的sqlsession.selectlist下面对statemnehandler进行拦截生成代理对象的，可以说是对dao层（sql语句execute前）的拦截增强，配置进sqlsessionfactory的configuration**
* **Spirngmvc的是对servlet容器的增强后的代理对象实行拦截，是在进入DispactherServlet经过handlermappering之后而在Controller之前，在springmvc文件配置，依据他生成servlet的代理对象，所以需要配置在springmvc文件中**，我们service层（若事务管理则是AOP生成service代理对象）的对象一般也只是封装到了servlet层中的service中的doget/dopost等7个方法中，而引用service的controller层便是由dispatcherservler经过Handlermapping返回的HandlerExecutionChain（包含handler和handlerInterceptor）进行控制的，因此在配置dispatcherSevlet时用的init-param中对应的SpringMvcConfig.xml文件配置Interceptor就可以对所有controller（最后也是servlet）生成代理类，在请求来时对设定的path进行拦截增强。
* **filter它能够对Servlet容器的请求和响应对象进行检查和修改，它在Servlet被调用之前检查Request对象， 修改Request Header和Request内容，注意，是对前往servlet容器的请求和响应对象进行检查修改，并不是对servlet进行动态代理的代理对象，是独立的一个对象，在web.xml进行配置**



#### 1、过滤器（Filter）

https://www.cnblogs.com/hellojava/archive/2012/12/19/2824444.html

##### **简介**

　　Filter也称之为过滤器，它是Servlet技术中最实用的技术，WEB开发人员通过Filter技术，对web服务器管理的所有web资源：例如Jsp, Servlet, 静态图片文件或静态 html 文件等进行拦截，从而实现一些特殊的功能。例如实现URL级别的权限访问控制、过滤敏感词汇、压缩响应信息等一些高级功能。

　　它主要用于对用户请求进行预处理，也可以对HttpServletResponse 进行后处理。使用Filter 的完整流程：Filter 对用户请求进行预处理，接着将请求交给Servlet 进行处理并生成响应，最后Filter 再对服务器响应进行后处理。

　　Filter功能：

- 在HttpServletRequest 到达 Servlet 之前，拦截客户的 HttpServletRequest 。 根据需要检查 HttpServletRequest ，也可以修改HttpServletRequest 头和数据。
- 在HttpServletResponse 到达客户端之前，拦截HttpServletResponse 。 根据需要检查 HttpServletResponse ，也可以修改HttpServletResponse头和数据。

##### **如何实现拦截**

　　Filter接口中有一个doFilter方法，当开发人员编写好Filter，并配置对哪个web资源进行拦截后，WEB服务器每次在调用web资源的service方法之前，都会先调用一下filter的doFilter方法，因此，在该方法内编写代码可达到如下目的：

1. 调用目标资源之前，让一段代码执行。
2. 是否调用目标资源（即是否让用户访问web资源）。

　　web服务器在调用doFilter方法时，会传递一个filterChain对象进来，**filterChain对象是filter接口中最重要的一个对象**，它也提供了一个doFilter方法，开发人员可以根据需求决定是否调用此方法，调用该方法，则web服务器就会调用web资源的service方法，即web资源就会被访问，否则web资源不会被访问。 

##### **Filter开发两步走**

1. 编写java类实现Filter接口，并实现其doFilter方法。 
2. 在 web.xml 文件中使用<filter>和<filter-mapping>元素对编写的filter类进行注册，并设置它所能拦截的资源。

　　web.xml配置各节点介绍：

```
<filter-name>用于为过滤器指定一个名字，该元素的内容不能为空。 
<filter-class>元素用于指定过滤器的完整的限定类名。 
<init-param>元素用于为过滤器指定初始化参数，它的子元素<param-name>指定参数的名字，<param-value>指定参数的值。
在过滤器中，可以使用FilterConfig接口对象来访问初始化参数。

<filter-mapping>元素用于设置一个 Filter 所负责拦截的资源。一个Filter拦截的资源可通过两种方式来指定：Servlet 名称和资源访问的请求路径 
<filter-name>子元素用于设置filter的注册名称。该值必须是在<filter>元素中声明过的过滤器的名字 
<url-pattern>设置 filter 所拦截的请求路径(过滤器关联的URL样式) 
<servlet-name>指定过滤器所拦截的Servlet名称。 
<dispatcher>指定过滤器所拦截的资源被 Servlet 容器调用的方式，可以是REQUEST,INCLUDE,FORWARD和ERROR之一，默认REQUEST。用户可以设置多个<dispatcher> 子元素用来指定 Filter 对资源的多种调用方式进行拦截。 

<dispatcher> 子元素可以设置的值及其意义： 
REQUEST：当用户直接访问页面时，Web容器将会调用过滤器。如果目标资源是通过RequestDispatcher的include()或forward()方法访问时，那么该过滤器就不会被调用。 
INCLUDE：如果目标资源是通过RequestDispatcher的include()方法访问时，那么该过滤器将被调用。除此之外，该过滤器不会被调用。 
FORWARD：如果目标资源是通过RequestDispatcher的forward()方法访问时，那么该过滤器将被调用，除此之外，该过滤器不会被调用。 
ERROR：如果目标资源是通过声明式异常处理机制调用时，那么该过滤器将被调用。除此之外，过滤器不会被调用。
```

##### **Filter链**

　　在一个web应用中，可以开发编写多个Filter，这些Filter组合起来称之为一个Filter链。

　　**web服务器根据Filter在web.xml文件中的注册顺序，决定先调用哪个Filter**，当第一个Filter的doFilter方法被调用时，web服务器会创建一个代表Filter链的FilterChain对象传递给该方法。在doFilter方法中，开发人员如果调用了FilterChain对象的doFilter方法，则web服务器会检查FilterChain对象中是否还有filter，如果有，则调用第2个filter，如果没有，则调用目标资源。

**Filter的生命周期**

```
public void init(FilterConfig filterConfig) throws ServletException;//初始化
```

　　和我们编写的Servlet程序一样，Filter的创建和销毁由WEB服务器负责。 web 应用程序启动时，web 服务器将创建Filter 的实例对象，并调用其init方法，读取web.xml配置，完成对象的初始化功能，从而为后续的用户请求作好拦截的准备工作**（filter对象只会创建一次，init方法也只会执行一次）**。开发人员通过init方法的参数，可获得代表当前filter配置信息的FilterConfig对象。

```
public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException;//拦截请求
```

　　这个方法完成实际的过滤操作。当客户请求访问与过滤器关联的URL的时候，Servlet过滤器将先执行doFilter方法。FilterChain参数用于访问后续过滤器。

```
public void destroy();//销毁
```

　　Filter对象创建后会驻留在内存，当web应用移除或服务器停止时才销毁。在Web容器卸载 Filter 对象之前被调用。该方法在Filter的生命周期中仅执行一次。在这个方法中，可以释放过滤器使用的资源。

##### **FilterConfig接口**

　　用户在配置filter时，可以使用<init-param>为filter配置一些初始化参数，当web容器实例化Filter对象，调用其init方法时，会把封装了filter初始化参数的filterConfig对象传递进来。因此开发人员在编写filter时，通过filterConfig对象的方法，就可获得以下内容：

```
String getFilterName();//得到filter的名称。 
String getInitParameter(String name);//返回在部署描述中指定名称的初始化参数的值。如果不存在返回null. 
Enumeration getInitParameterNames();//返回过滤器的所有初始化参数的名字的枚举集合。 
public ServletContext getServletContext();//返回Servlet上下文对象的引用。
```

##### **Filter使用案例**

　　**1、使用Filter验证用户登录安全控制**

　　前段时间参与维护一个项目，用户退出系统后，再去地址栏访问历史，根据url，仍然能够进入系统响应页面。我去检查一下发现对请求未进行过滤验证用户登录。添加一个filter搞定问题！

　　先在web.xml配置

```
<filter>
    <filter-name>SessionFilter</filter-name>
    <filter-class>com.action.login.SessionFilter</filter-class>
    <init-param>
        <param-name>logonStrings</param-name><!-- 对登录页面不进行过滤 -->
        <param-value>/project/index.jsp;login.do</param-value>
    </init-param>
    <init-param>
        <param-name>includeStrings</param-name><!-- 只对指定过滤参数后缀进行过滤 -->
        <param-value>.do;.jsp</param-value>
    </init-param>
    <init-param>
        <param-name>redirectPath</param-name><!-- 未通过跳转到登录界面 -->
        <param-value>/index.jsp</param-value>
    </init-param>
    <init-param>
        <param-name>disabletestfilter</param-name><!-- Y:过滤无效 -->
        <param-value>N</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>SessionFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

　　接着编写FilterServlet：

```java
package com.action.login;

import java.io.IOException;

import javax.servlet.Filter;
import javax.servlet.FilterChain;
import javax.servlet.FilterConfig;
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpServletResponseWrapper;

/**
 *    判断用户是否登录,未登录则退出系统
 */
public class SessionFilter implements Filter {
    
    public FilterConfig config;
    
    public void destroy() {
        this.config = null;
    }
    
    public static boolean isContains(String container, String[] regx) {
        boolean result = false;

        for (int i = 0; i < regx.length; i++) {
            if (container.indexOf(regx[i]) != -1) {
                return true;
            }
        }
        return result;
    }

    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
        HttpServletRequest hrequest = (HttpServletRequest)request;
        HttpServletResponseWrapper wrapper = new HttpServletResponseWrapper((HttpServletResponse) response);
        
        String logonStrings = config.getInitParameter("logonStrings");        // 登录登陆页面
        String includeStrings = config.getInitParameter("includeStrings");    // 过滤资源后缀参数
        String redirectPath = hrequest.getContextPath() + config.getInitParameter("redirectPath");// 没有登陆转向页面
        String disabletestfilter = config.getInitParameter("disabletestfilter");// 过滤器是否有效
        
        if (disabletestfilter.toUpperCase().equals("Y")) {    // 过滤无效
            chain.doFilter(request, response);
            return;
        }
        String[] logonList = logonStrings.split(";");
        String[] includeList = includeStrings.split(";");
        
        if (!this.isContains(hrequest.getRequestURI(), includeList)) {// 只对指定过滤参数后缀进行过滤
            chain.doFilter(request, response);
            return;
        }
        
        if (this.isContains(hrequest.getRequestURI(), logonList)) {// 对登录页面不进行过滤
            chain.doFilter(request, response);
            return;
        }
        
        String user = ( String ) hrequest.getSession().getAttribute("useronly");//判断用户是否登录
        if (user == null) {
            wrapper.sendRedirect(redirectPath);
            return;
        }else {
            chain.doFilter(request, response);
            return;
        }
    }

    public void init(FilterConfig filterConfig) throws ServletException {
        config = filterConfig;
    }
}
```

　　这样既可完成对用户所有请求，均要经过这个Filter进行验证用户登录。

　　**2、防止中文乱码过滤器**

　　项目使用spring框架时。当前台JSP页面和JAVA代码中使用了不同的字符集进行编码的时候就会出现表单提交的数据或者上传/下载中文名称文件出现乱码的问题，那就可以使用这个过滤器。

```xml
<filter>
    <filter-name>encoding</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name><!--用来指定一个具体的字符集-->
        <param-value>UTF-8</param-value>
    </init-param>
    <init-param>
        <param-name>forceEncoding</param-name><!--true：无论request是否指定了字符集，都是用encoding；false：如果request已指定一个字符集，则不使用encoding-->
        <param-value>false</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>encoding</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

　　**3、Spring+Hibernate的OpenSessionInViewFilter控制session的开关**

　　当hibernate+spring配合使用的时候，如果设置了lazy=true（延迟加载）,那么在读取数据的时候，当读取了父数据后，hibernate 会自动关闭session，这样，当要使用与之关联数据、子数据的时候，系统会抛出lazyinit的错误，这时就需要使用spring提供的OpenSessionInViewFilter过滤器。

　　OpenSessionInViewFilter主要是保持Session状态直到request将全部页面发送到客户端，直到请求结束后才关闭session，这样就可以解决延迟加载带来的问题。

　　注意：OpenSessionInViewFilter配置要写在struts2的配置前面。因为tomcat容器在加载过滤器的时候是按照顺序加载的，如果配置文件先写的是struts2的过滤器配置，然后才是OpenSessionInViewFilter过滤器配置，所以加载的顺序导致，action在获得数据的时候session并没有被spring管理。

```xml
<!-- lazy loading enabled in spring -->
<filter>
    <filter-name>OpenSessionInViewFilter</filter-name>
    <filter-class>org.springframework.orm.hibernate3.support.OpenSessionInViewFilter</filter-class>
    <init-param>
        <param-name>sessionFactoryBeanName</param-name><!-- 可缺省。默认是从spring容器中找id为sessionFactory的bean，如果id不为sessionFactory，则需要配置如下，此处SessionFactory为spring容器中的bean。 -->
        <param-value>sessionFactory</param-value>
    </init-param>
    <init-param>
        <param-name>singleSession</param-name><!-- singleSession默认为true,若设为false则等于没用OpenSessionInView -->
        <param-value>true</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>OpenSessionInViewFilter</filter-name>
    <url-pattern>*.do</url-pattern>
</filter-mapping>
```

　　**4、Struts2的web.xml配置**

　　项目中使用Struts2同样需要在web.xml配置过滤器，用来截取请求，转到Struts2的Action进行处理。

　　注意：如果在2.1.3以前的Struts2版本，过滤器使用org.apache.struts2.dispatcher.FilterDispatcher。否则使用org.apache.struts2.dispatcher.ng.filter.StrutsPrepareAndExecuteFilter。从Struts2.1.3开始，将废弃ActionContextCleanUp过滤器，而在StrutsPrepareAndExecuteFilter过滤器中包含相应的功能。

　　三个初始化参数配置：

1. config参数：指定要加载的配置文件。逗号分割。
2. actionPackages参数：指定Action类所在的包空间。逗号分割。
3. configProviders参数：自定义配置文件提供者，需要实现ConfigurationProvider接口类。逗号分割。

```
<!-- struts 2.x filter -->
<filter>
    <filter-name>struts2</filter-name>
    <filter-class>org.apache.struts2.dispatcher.ng.filter.StrutsPrepareAndExecuteFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>struts2</filter-name>
    <url-pattern>*.do</url-pattern>
</filter-mapping>
```

#### 2、拦截器（Interceptor）

https://www.cnblogs.com/banning/p/6195072.html

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200326110602183.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxMTMzOTg2,size_16,color_FFFFFF,t_70)

* 基于AOP，应该是上层实现了所传入对象作为封装类，在封装类中调用实现 HandlerInterceptor 接口并按顺序在spring容器配置文件中配置的实现类中过的函数，将前置、后置通知等都封装好了，只需要直接重写他便可。

##### 拦截器的作用

Spring MVC 的处理器拦截器类似于 Servlet 开发中的过滤器 Filter，用于对处理器进行预处理和后处理。 用户可以自己定义一些拦截器来实现特定的功能。

谈到拦截器，还要向大家提一个词——拦截器链（Interceptor Chain）。拦截器链就是将拦截器按一定的顺序联结成一条链。在访问被拦截的方法或字段时，拦截器链中的拦截器就会按其之前定义的顺序被调用。

说到这里，可能大家脑海中有了一个疑问，这不是我们之前学的过滤器吗？是的它和过滤器是有几分相似，但是也有区别，接下来我们就来说说他们的区别：

过滤器是 servlet 规范中的一部分，任何 java web 工程都可以使用。 拦截器是 SpringMVC 框架自己的，只有使用了 SpringMVC 框架的工程才能用。
过滤器在 url-pattern 中配置了 /* 之后，可以对所有要访问的资源拦截。 拦截器它是只会拦截访问的控制器方法，如果访问的是 jsp，html, css, image 或者 js 是不会进行拦 截的。
拦截器也是 AOP 思想的具体应用。 我们要想自定义拦截器， 要求必须实现：HandlerInterceptor 接口。

##### 自定义拦截器的步骤

##### 编写一个普通类实现 HandlerInterceptor 接口

```java
public class HandlerInterceptorDemo1 implements HandlerInterceptor { 
 	
 	/**
 	* 控制器之前执行
 	*/
 	@Override  
 	public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)    throws Exception {   
 		System.out.println("preHandle 拦截器拦截了");   
 		return true;  //如果程序员决定不需要再调用其他的组件去处理请求，则返回 false。 
 	} 
 	
 	/*
 	* 控制器之后执行
 	* 拦截器链内所有 preHandle 返回 true 才会调用 
 	* 若在该方法中跳转掉了其他页面，则浏览器显示的将是中途跳转的页面，但controller 返回的页面仍将执行（不显示而已）
 	*/
 	@Override  
 	public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, 
 ModelAndView modelAndView) throws Exception {   
 		System.out.println("postHandle 方法执行了");  
 	} 
 	
 	/**
 	* 最后执行,只有 preHandle 返回 true 才调用 
 	* 可以在该方法中进行一些资源清理的操作。 
 	*/
 	@Override  
 	public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex)    throws Exception {   
 		System.out.println("afterCompletion 方法执行了");  
 	} 
} 

```

##### 配置拦截器

```xml
<!-- 配置拦截器 --> 
<mvc:interceptors>  
	<mvc:interceptor>   
		<mvc:mapping path="/user/*"/> <!--指定需要进行拦截的url /**/表示拦截所有-->
		<mvc:exclude-mapping path=""/> <!--指定不进行拦截的url-->
  		<bean id="handlerInterceptorDemo1"     class="com.itheima.web.interceptor.HandlerInterceptorDemo1"></bean>  		
  	</mvc:interceptor> 
</mvc:interceptors> 

```

#####  多个拦截器的执行顺序

```xml
<!-- 配置拦截器 --> 
<mvc:interceptors>  
	<mvc:interceptor>   
		<mvc:mapping path="/user/*"/> <!--指定需要进行拦截的url /**/表示拦截所有-->
		<mvc:exclude-mapping path=""/> <!--指定不进行拦截的url-->
  		<bean id="handlerInterceptorDemo1"     class="com.itheima.web.interceptor.HandlerInterceptorDemo1"></bean>  		
  	</mvc:interceptor> 

	<mvc:interceptor>   
		<mvc:mapping path="/**"/> 
  		<bean id="handlerInterceptorDemo2"     class="com.itheima.web.interceptor.HandlerInterceptorDemo2"></bean>  		
  	</mvc:interceptor> 
</mvc:interceptors> 

```

![img](https://img-blog.csdnimg.cn/20200326112322973.png)

#####  拦截器方法细节

preHandler ：只要配置了都会调用 。如果程序员决定该拦截器对请求进行拦截处理后还要调用其他的拦截器，或者是业务处理器去进行处理，则返回 true。 如果程序员决定不需要再调用其他的组件去处理请求，则返回 false。

postHandle ：在拦截器链内所有拦截器的 preHandle 返回 true 才调用

afterCompletion：自己的拦截器 preHandle 返回 true 调用（2）拦截器（Interceptor）：它依赖于web框架，在SpringMVC中就是依赖于SpringMVC框架。在实现上,基于Java的反射机制，属于面向切面编程（AOP）的一种运用，就是在service或者一个方法前，调用一个方法，或者在方法后，调用一个方法，比如动态代理就是拦截器的简单实现，在调用方法前打印出字符串（或者做其它业务逻辑的操作），也可以在调用方法后打印出字符串，甚至在抛出异常的时候做业务逻辑的操作。由于拦截器是基于web框架的调用，因此可以使用Spring的依赖注入（DI）进行一些业务操作，同时一个拦截器实例在一个controller生命周期之内可以多次调用。但是缺点是只能对controller请求进行拦截，对其他的一些比如直接访问静态资源的请求则没办法进行拦截处理。

**最后图片中的并不能说明拦截器中的prehandle方法是在DispactherServlet之前执行的(执行的是生成的代理对象)，只能说是在执行Controller中的具体方法之前执行。看了其他的博客prehandle方法应该是在进入DispactherServlet经过handlermappering之后而在Controller之前**

![这里写图片描述](https://img-blog.csdn.net/20180603133007923?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p4ZDE0MzU1MTM3NzU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**SPringMVC对静态资源的拦截****

SpringMVC提供<mvc:resources>来设置静态资源，**但是增加该设置如果采用通配符的方式增加拦截器的话仍然会被拦截器拦截**，可采用如下方案进行解决：

**方案一、拦截器中增加针对静态资源不进行过滤(涉及spring-mvc.xml)**

```
<mvc:resources location="/" mapping="/**/*.js"/>
<mvc:resources location="/" mapping="/**/*.css"/>
<mvc:resources location="/assets/" mapping="/assets/**/*"/>
<mvc:resources location="/images/" mapping="/images/*" cache-period="360000"/>

<mvc:interceptors>
    <mvc:interceptor>
        <mvc:mapping path="/**/*"/>
        <mvc:exclude-mapping path="/**/fonts/*"/>
        <mvc:exclude-mapping path="/**/*.css"/>
        <mvc:exclude-mapping path="/**/*.js"/>
        <mvc:exclude-mapping path="/**/*.png"/>
        <mvc:exclude-mapping path="/**/*.gif"/>
        <mvc:exclude-mapping path="/**/*.jpg"/>
        <mvc:exclude-mapping path="/**/*.jpeg"/>
        <mvc:exclude-mapping path="/**/*login*"/>
        <mvc:exclude-mapping path="/**/*Login*"/>
        <bean class="com.luwei.console.mg.interceptor.VisitInterceptor"></bean>
    </mvc:interceptor>
</mvc:interceptors>
```

 

**方案二、使用默认的静态资源处理Servlet处理静态资源(涉及spring-mvc.xml, web.xml)**

在spring-mvc.xml中启用默认Servlet

```
 <mvc:default-servlet-handler/>
```

在web.xml中增加对静态资源的处理

```
<servlet-mapping>
    <servlet-name>default</servlet-name>
    <url-pattern>*.js</url-pattern>
    <url-pattern>*.css</url-pattern>
    <url-pattern>/assets/*"</url-pattern>
    <url-pattern>/images/*</url-pattern>
</servlet-mapping>
```

但是当前的设置必须在Spring的Dispatcher的前面

 

**方案三、修改Spring的全局拦截设置为\*.do的拦截（涉及web.xml）**

```
<servlet>
    <servlet-name>SpringMVC</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:spring-mvc.xml</param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
    <async-supported>true</async-supported>
</servlet>
<servlet-mapping>
    <servlet-name>SpringMVC</servlet-name>
    <url-pattern>*.do</url-pattern>
</servlet-mapping>
```

这样设置，Spring就会只针对以'.do'结尾的请求进行处理，不再维护静态资源

**web.xml 中配置了url-pattern后，会起到两个作用：
（1）是限制 url 的后缀名，只能为".do"。
（2）就是在没有填写后缀时，默认在你配置的 Controller 的 RequestMapping 中添加".do"的后缀。**

针对这三种方案的优劣分析：

第一种方案配置比较臃肿，多个拦截器时增加文件行数，不推荐使用；第二种方案使用默认的Servlet进行资源文件的访问，Spring拦截所有请求，然后再将资源文件交由默认的Sevlet进行处理，性能上少有损耗；第三种方案Spring只是处理以'.do'结尾的访问，性能上更加高效，但是再访问路径上必须都以'.do'结尾，URL不太文雅；

综上所述，推荐使用第二和第三中方案

