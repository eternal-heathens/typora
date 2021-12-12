### JSP介绍
* 主要目的就是希望在最终输出的html的代码中嵌入后台数据罢了

* JSP = HTML + Java片段，为了改变需要将前端代码装化成java代码后手动写入servlet 的情况，将HTML转化一个转换成servlet中的java语句中去专门设置了一个组件-->jsp.

* JSP在服务器端运行

  * Tomcat中有一个叫JspServlet的东西，它负责给JSP卸妆，让它变成恢复本来面目：Servlet

    > index.jsp->index_jsp.java(servlet)

    

    - 要么被当成字符串输出（HTML片段）
    - 要么本身就是Java片段
    - 要么会**转成**Java片段（EL表达式）

![preview](https://pic3.zhimg.com/v2-98ec73e31685456b9a6ca1558e8f47a6_r.jpg)

- 要么被当成字符串输出（HTML片段）
- 要么本身就是Java片段
- 要么会**转成**Java片段（EL表达式:在JSP文件编译成Servlet时，自动转化为Java代码，然后对数据做处理)

* 是动态资源到静态资源的转换（省略JSP转为Servlet输出的过程）

![preview](https://pic2.zhimg.com/v2-d61a05e5c8823bc6ddc42ccbb19d38e8_r.jpg)


### JSP与AJAX+HTML

1.卖家组装好商品后再发货（JSP）

2.卖家把部件拆开，运到之后买家自己组装（AJAX+HTML）

![preview](https://pic2.zhimg.com/v2-44c9bdf357743abb38cd984a3b6bf901_r.jpg)

* 客户端之所以能显示页面，是因为JSP已经把数据和HTML片段拼凑成完整的静态页面返回给客户端

![preview](https://pic2.zhimg.com/v2-f936b7b3ddef8248879c7f414475d459_r.jpg)

### MVC模式与JAVAEE三层架构

![preview](https://pic2.zhimg.com/v2-955ff75e5217fe00274a13ca880cb80c_r.jpg)

* MVC是web开发都有的一种模式

* JAVAEE 三层框架:Controller/Service/Dao
* MVC只是在web层。当然，如果站在更高的角度，可以看成这样：


 ![preview](https://pic3.zhimg.com/v2-b28da8a6d686a7232c22c3c9480c95ec_r.jpg)