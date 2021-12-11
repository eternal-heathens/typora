### Thymeleaf 概述

#### 特点

**Thymeleaf最大的特点是能够直接在浏览器中打开并正确显示模板页面，而不需要启动整个Web应用，但是总是看到说其效率有点低**

```html
  Thymeleaf 在有网络和无网络的环境下皆可运行，即它可以让美工在浏览器查看页面的静态效果，也可以让程序员在服务器查看带数据的动态页面效果。这是由于它支持 html 原型，然后在 html 标签里增加额外的属性来达到模板+数据的展示方式。浏览器解释 html 时会忽略未定义的标签属性，所以 thymeleaf 的模板可以静态地运行；当有数据返回到页面时，Thymeleaf 标签会动态地替换掉静态内容，使页面动态显示。
```

```html
Thymeleaf 开箱即用的特性。它提供标准和spring标准两种方言，可以直接套用模板实现JSTL、 OGNL表达式效果，避免每天套模板、改jstl、改标签的困扰。同时开发人员也可以扩展和创建自定义的方言。
```

#### 引入

>  在html页面中引入thymeleaf命名空间，即，此时在html模板文件中动态的属性使用th:命名空间修饰 。

```html
<html lang="en" xmlns:th="http://www.thymeleaf.org">
```

EL存取变量数据的方法很简单，例如：${username}。它的意思是取出某一范围中名称为username的变量。

因为我们并没有指定哪一个范围的username，所以它会依序从Page、Request、Session、Application范围查找。

假如途中找到username，就直接回传，不再继续找下去，但是假如全部的范围都没有找到时，就回传""。

#### 标准表达式语法

`${...}` 变量表达式，Variable Expressions

> #### 变量表达式功能
>
> 一、可以获取对象的属性和方法
>
> 二、可以使用ctx，vars，locale，request，response，session，servletContext内置对象
>
> 三、可以使用dates，numbers，strings，objects，arrays，lists，sets，maps等内置方法（重点介绍）
>
> #### 常用的内置对象
>
> 一、**ctx** ：上下文对象。
>
> 二、**vars** ：上下文变量。
>
> 三、**locale**：上下文的语言环境。
>
> 四、**request**：（仅在web上下文）的 HttpServletRequest 对象。
>
> 五、**response**：（仅在web上下文）的 HttpServletResponse 对象。
>
> 六、**session**：（仅在web上下文）的 HttpSession 对象。
>
> 七、**servletContext**：（仅在web上下文）的 ServletContext 对象
>
> 这里以常用的Session举例，用户刊登成功后，会把用户信息放在Session中，Thymeleaf通过内置对象将值从session中获取。
>
> ```java
> // java 代码将用户名放在session中
> session.setAttribute("userinfo",username);
> // Thymeleaf通过内置对象直接获取
> th:text="${session.userinfo}"
> ```
>
> #### 常用的内置方法
>
> 一、**strings**：字符串格式化方法，常用的Java方法它都有。比如：equals，equalsIgnoreCase，length，trim，toUpperCase，toLowerCase，indexOf，substring，replace，startsWith，endsWith，contains，containsIgnoreCase等
>
> 二、**numbers**：数值格式化方法，常用的方法有：formatDecimal等
>
> 三、**bools**：布尔方法，常用的方法有：isTrue，isFalse等
>
> 四、**arrays**：数组方法，常用的方法有：toArray，length，isEmpty，contains，containsAll等
>
> 五、**lists**，**sets**：集合方法，常用的方法有：toList，size，isEmpty，contains，containsAll，sort等
>
> 六、**maps**：对象方法，常用的方法有：size，isEmpty，containsKey，containsValue等
>
> 七、**dates**：日期方法，常用的方法有：format，year，month，hour，createNow等
>
> 文章底部提供了对应的官网链接
>
> ```html
> <!DOCTYPE html>
> <html lang="en" xmlns:th="http://www.thymeleaf.org">
> <head>
>     <meta charset="UTF-8">
>     <title>ITDragon Thymeleaf 内置方法</title>
> </head>
> <body>
>     <h2>ITDragon Thymeleaf 内置方法</h2>
>     <h3>#strings </h3>
>     <div th:if="${not #strings.isEmpty(itdragonStr)}" >
>         <p>Old Str : <span th:text="${itdragonStr}"/></p>
>         <p>toUpperCase : <span th:text="${#strings.toUpperCase(itdragonStr)}"/></p>
>         <p>toLowerCase : <span th:text="${#strings.toLowerCase(itdragonStr)}"/></p>
>         <p>equals : <span th:text="${#strings.equals(itdragonStr, 'itdragonblog')}"/></p>
>         <p>equalsIgnoreCase : <span th:text="${#strings.equalsIgnoreCase(itdragonStr, 'itdragonblog')}"/></p>
>         <p>indexOf : <span th:text="${#strings.indexOf(itdragonStr, 'r')}"/></p>
>         <p>substring : <span th:text="${#strings.substring(itdragonStr, 2, 8)}"/></p>
>         <p>replace : <span th:text="${#strings.replace(itdragonStr, 'it', 'IT')}"/></p>
>         <p>startsWith : <span th:text="${#strings.startsWith(itdragonStr, 'it')}"/></p>
>         <p>contains : <span th:text="${#strings.contains(itdragonStr, 'IT')}"/></p>
>     </div>
> 
>     <h3>#numbers </h3>
>     <div>
>         <p>formatDecimal 整数部分随意，小数点后保留两位，四舍五入: <span th:text="${#numbers.formatDecimal(itdragonNum, 0, 2)}"/></p>
>         <p>formatDecimal 整数部分保留五位数，小数点后保留两位，四舍五入: <span th:text="${#numbers.formatDecimal(itdragonNum, 5, 2)}"/></p>
>     </div>
> 
>     <h3>#bools </h3>
>     <div th:if="${#bools.isTrue(itdragonBool)}">
>         <p th:text="${itdragonBool}"></p>
>     </div>
> 
>     <h3>#arrays </h3>
>     <div th:if="${not #arrays.isEmpty(itdragonArray)}">
>         <p>length : <span th:text="${#arrays.length(itdragonArray)}"/></p>
>         <p>contains : <span th:text="${#arrays.contains(itdragonArray, 5)}"/></p>
>         <p>containsAll : <span th:text="${#arrays.containsAll(itdragonArray, itdragonArray)}"/></p>
>     </div>
>     <h3>#lists </h3>
>     <div th:if="${not #lists.isEmpty(itdragonList)}">
>         <p>size : <span th:text="${#lists.size(itdragonList)}"/></p>
>         <p>contains : <span th:text="${#lists.contains(itdragonList, 0)}"/></p>
>         <p>sort : <span th:text="${#lists.sort(itdragonList)}"/></p>
>     </div>
>     <h3>#maps </h3>
>     <div th:if="${not #maps.isEmpty(itdragonMap)}">
>         <p>size : <span th:text="${#maps.size(itdragonMap)}"/></p>
>         <p>containsKey : <span th:text="${#maps.containsKey(itdragonMap, 'thName')}"/></p>
>         <p>containsValue : <span th:text="${#maps.containsValue(itdragonMap, '#maps')}"/></p>
>     </div>
>     <h3>#dates </h3>
>     <div>
>         <p>format : <span th:text="${#dates.format(itdragonDate)}"/></p>
>         <p>custom format : <span th:text="${#dates.format(itdragonDate, 'yyyy-MM-dd HH:mm:ss')}"/></p>
>         <p>day : <span th:text="${#dates.day(itdragonDate)}"/></p>
>         <p>month : <span th:text="${#dates.month(itdragonDate)}"/></p>
>         <p>monthName : <span th:text="${#dates.monthName(itdragonDate)}"/></p>
>         <p>year : <span th:text="${#dates.year(itdragonDate)}"/></p>
>         <p>dayOfWeekName : <span th:text="${#dates.dayOfWeekName(itdragonDate)}"/></p>
>         <p>hour : <span th:text="${#dates.hour(itdragonDate)}"/></p>
>         <p>minute : <span th:text="${#dates.minute(itdragonDate)}"/></p>
>         <p>second : <span th:text="${#dates.second(itdragonDate)}"/></p>
>         <p>createNow : <span th:text="${#dates.createNow()}"/></p>
>     </div>
> </body>
> </html>
> ```
>
> 后台给负责给变量赋值，和跳转页面。
>
> ```java
> @RequestMapping("varexpressions")
> public String varexpressions(ModelMap map) {
>   map.put("itdragonStr", "itdragonBlog");
>   map.put("itdragonBool", true);
>   map.put("itdragonArray", new Integer[]{1,2,3,4});
>   map.put("itdragonList", Arrays.asList(1,3,2,4,0));
>   Map itdragonMap = new HashMap();
>   itdragonMap.put("thName", "${#...}");
>   itdragonMap.put("desc", "变量表达式内置方法");
>   map.put("itdragonMap", itdragonMap);
>   map.put("itdragonDate", new Date());
>   map.put("itdragonNum", 888.888D);
>   return "grammar/varexpressions";
> ```

`@{...}` 链接表达式，Link URL Expressions

`#{...}` 消息表达式，Message Expressions

> 消息表达式（井号表达式，资源表达式）。通常与th:text属性一起使用，指明声明了th:text的标签的文本是#{}中的key所对应的value，而标签内的文本将不会显示。
>
> 例如：
>
> 新建/WEB-INF/templates/home.html，段落
>
> ```
> <p th: text=" #{home. welcome}" >This text will not be show! </p>
> ```
>
> 新建/WEB-INF/templates/home.properties，home.welcome：
>
> ```
> home.welcome=this messages is from home.properties!
> ```
>
> 测试结果：
>
> ![img](https://images2015.cnblogs.com/blog/783994/201512/783994-20151216165208146-1857573892.png)
>
> 从测试结果可以看出，消息表达式通常用于显示页面静态文本，将静态文本维护在properties文件中也方面维护，做国际化等。

`~{...}` 代码块表达式，Fragment Expressions

`*{...}` 选择变量表达式，Selection Variable Expressions

> 选择表达式（星号表达式）。选择表达式与变量表达式有一个重要的区别：选择表达式计算的是选定的对象，而不是整个环境变量映射。也就是：只要是没有选择的对象，选择表达式与变量表达式的语法是完全一样的。那什么是选择的对象呢？是一个：th:object对象属性绑定的对象。
>
> 例如：
>
> [![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)
>
> ```
> <div th: obj ect=" ${session. user}" >
> 
> <p>Name: <span th: text=" *{firstName}" >Sebastian</span>. </p>
> 
> <p>Surname: <span th: text=" *{lastName}" >Pepper</span>. </p>
> 
> <p>Nationality: <span th: text=" *{nationality}" >Saturn</span>. </p>
> 
> </div>
> ```
>
> [![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)
>
> 上例中，选择表达式选择的是th:object对象属性绑定的session. user对象中的属性。

![img](https://upload-images.jianshu.io/upload_images/3957912-9885c279bc00746e.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/622/format/webp)

![img](https://upload-images.jianshu.io/upload_images/3957912-532c8174208ff5d8.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/623/format/webp)

![img](https://upload-images.jianshu.io/upload_images/3957912-852ab1a5baced776.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/623/format/webp)

* th:field属性常用于表单字段绑定，除了自动生成id和name属性，对不同的节点类型还会有不同的生成逻辑。

* 生成后若是这样：

  ```
  <label for='sex'>性别：</label>
  <input type='text' id='sex' name='gender'>
  表单提交用户输入的数据，是根据表单元素的name值来控制表单控件的。那么表单控件里面的id是干嘛的？就是为了区别不同的表单控件，还有一个作用，就是点击表单控件提示信息时也能让表单控件获得焦点的效果，用label提高用户体验，这时的label属性中的for是跟表单控件的id一致的：
  label标签不会向用户呈现任何特殊效果，它的作用是为鼠标用户改进了可用性。
  如果你在 label标签内点击文本，就会触发此控件。
  就是说，当用户单击选中该label标签时，浏览器就会自动将焦点转到和标签相关的表单控件上
  （就自动选中和该label标签相关连的表单控件上）
  ```

>(1)使用th:field属性时，如果html节点中已经存在相应属性，则不会再另外生成。
>(2)th:field属性需要使用星号表达式*{...}，即先使用th:object声明表单对象，再使用th:field=*{...}对表单域进行处理。
>
>name方便后台方便接收数据的，可以用来区分不同的input变量的，而id是前台元素的标识符，css会用到它。
>
>name是一个控件的名字，知可以很多控件有同一个名字，那样在提交时会自动变成数组。不如你写了3个叫“text_t”的文本框，那么在道用[js脚本](https://www.baidu.com/s?wd=js脚本&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao)取值时就会收到一个叫text_t的字符串数组，里面有3个值。
>id是唯一的，也就是说一个jsp页面不能重复专，如果你写重复了，系统会取第一个控件，其他叫这个id的控件就无法被找到。