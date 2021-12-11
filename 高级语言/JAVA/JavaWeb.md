# Servlet（小服务程序）实例 

* https://how2j.cn/k/servlet/servlet-paramter/547.html

## 开发servlet
* web.xml提供路径与servlet的映射关系
  把/hello这个路径，映射到 HelloServlet这个类上

* 在ecilpse中默认输出的class是在bin目录下，但是tomcat启动之后，在默认情况下，不会去bin目录找这些class文件，而是到WEB-INF/classes这个目录下去寻找。 所以通过这一步的配置，使得eclipse的class文件输出到WEB-INF/classes目录下，那么这样就和tomcat兼容了。

## 调用流程
* ![image-20200226234820158](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200226234820158.png)

## 中文问题

* 获取中文参数

这样需要对每一个提交的数据都进行编码和解码处理，如果觉得麻烦，也可以使用一句话代替：
```
request.setCharacterEncoding("UTF-8"); 
```
并且把这句话放在request.getParameter()之前。

* 获取中文的响应

在Servlet中，加上
```
response.setContentType("text/html; charset=UTF-8");
```

##  servelt  service()
1. doget()
> 当浏览器使用get方式提交数据的时候，servlet需要提供doGet()方法.
form默认的提交方式
如果通过一个超链访问某个地址
如果在地址栏直接输入某个地址
ajax指定使用get方式的时候

```
import java.io.IOException;
 
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
 
public class LoginServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response)throws ServletException, IOException {
    }
 }
```

2. dopost()
>当浏览器使用post方式提交数据的时候，servlet需要提供doPost()方法
哪些是post方式呢？
在form上显示设置 method="post"的时候
ajax指定post方式的时候
```

import java.io.IOException;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public class LoginServlet extends HttpServlet {
	protected void doPost(HttpServletRequest request, HttpServletResponse response)
			throws ServletException, IOException {
	}
}
```

3.  setvice()
>LoginServlet继承了HttpServlet,同时也继承了一个方法  
service(HttpServletRequest , HttpServletResponse )
实际上，在执行doGet()或者doPost()之前，都会先执行service()
由service()方法进行判断，到底该调用doGet()还是doPost()
有时候也会直接重写service()方法，在其中提供相应的服务，就不用区分到底是get还是post了。

## servlet 生命周期

![image-20200225120452856](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200225120452856.png)

1. 实例化

当用户通过浏览器输入一个路径，这个路径对应的servlet被调用的时候，该Servlet就会被实例化

为LoginServlet显式提供一个构造方法 LoginServlet()

然后通过浏览器访问，就可以观察到"LoginServlet 构造方法 被调用"

无论访问了多少次LoginServlet

* LoginServlet构造方法 只会执行一次，所以Servlet是单实例的

```
import java.io.IOException;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public class LoginServlet extends HttpServlet {	
	public LoginServlet(){
		System.out.println("LoginServlet 构造方法 被调用");
	}
	protected void service(HttpServletRequest request, HttpServletResponse response)
			throws ServletException, IOException {	
		//略	
	}
}
```

2.  初始化

LoginServlet 继承了HttpServlet，同时也继承了init(ServletConfig) 方法

init 方法是一个实例方法，所以会在构造方法执行后执行。

无论访问了多少次LoginSerlvet

* init初始化 **只会执行一次**

```
import java.io.IOException;
 
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
 
public class LoginServlet extends HttpServlet {   
    public LoginServlet(){
        System.out.println("LoginServlet 构造方法 被调用");
    }
    protected void service(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        //略
    }
}
```

3. 提供服务
接下来就是执行service()方法，然后通过浏览器传递过来的信息进行判断，是调用doGet()还是doPost()方法

4. 销毁

  接着是销毁destroy()
  在如下几种情况下，会调用destroy()
*  该Servlet所在的web应用重新启动
在server.xml中配置该web应用的时候用到了
```
<Context path="/" docBase="e:\\project\\j2ee\\web" debug="0" reloadable="false" />
```
如果把 reloadable="false" 改为reloadable="true" 就表示有任何类发生的更新，web应用会自动重启
当web应用自动重启的时候，destroy()方法就会被调用

* 关闭tomcat的时候 destroy()方法会被调用，但是这个一般都发生的很快，不易被发现。
```
import java.io.IOException;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
 
public class LoginServlet extends HttpServlet {
 
    public void destroy() {
        System.out.println("destroy()");
    }
    protected void service(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        // 略
    }
}
```
5. 被回收

  当该Servlet被销毁后，就满足垃圾回收的条件了。 当下一次垃圾回收GC来临的时候，就有可能被回收。

## servlet跳转

1. 请求转发：

　　　　(1) 使用requestDispatcher对象：

　　　　　　转发格式：request.getRequestDispatcher("path").forward(response,request)

　　　　(2) 使用jsp动作元素：

　　　　　　<jsp:forward page=""/>

>1，转发是服务器行为（这是不经过浏览器的） 
>2，转发是浏览器只做了一次访问请求 
>3，转发浏览器地址不变 
>4，转发两次跳转之间传输的信息不会丢失，所以可以通过request进行数据的传递 
>5，转发只能将请求转发给同一个WEB应用中的组件 
>注意：如果创建RequestDispatcher 对象时指定的相对URL以“/”开头，它是相对于当前WEB应用程序的根目录。（只能是本工程下的，因为是服务器行为）

2. 请求重定向：

　　　　使用response的rsendRedirect方法：

　　　　　　重定向格式：response.sendRedirect("path");

> (1) 重定向是客户端行为。（浏览器重新请求） 
> (2)重定向是浏览器做了至少两次的访问请求的(当然也可以重定向多次)。 
> (3)定向浏览器地址改变。 
> (4)重定向两次跳转之间传输的信息会丢失（request范围）。 
> (5)重定向可以指向任何的资源，包括当前应用程序中的其他资源，同一个站点上的其他应用程序中的资源，其他站点的资源（如自己工程重定向到百度）。注意：传递给HttpServletResponse.sendRedirect 方法的相对URL以“/”开头，它是相对于整个WEB站点的根目录。

![image-20200225210902175](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200225210902175.png)

![image-20200225211151308](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200225211151308.png)

## servlet 自启动

* <load-on-startup>10</load-on-startup>

  > 即表明该Servlet会随着Tomcat的启动而初始化。
  >
  > 同时，为HelloServlet提供一个init(ServletConfig) 方法，验证自启动如图所示，在tomcat完全启动之前，就打印了**init of HelloServlet**
  > <load-on-startup>**10**</load-on-startup> 中的**10**表示启动顺序
  > 如果有多个Servlet都配置了自动启动，数字越小，启动的优先级越高

servlet 上传文件

1. 首先准备上传页面 upload.html

* form 的**method必须是post**的，get不能上传文件。 还需要加上**enctype="multipart/form-data"** 表示提交的数据是二进制文件
```
<form action="uploadPhoto" method="post" enctype="multipart/form-data">
```
 * 需要提供**type="file"** 的字段进行上传

2. 接着准备 UploadPhotoServlet
```
import java.io.File;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.InputStream;
import java.io.PrintWriter;
import java.util.Iterator;
import java.util.List;
  
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
  
import org.apache.commons.fileupload.FileItem;
import org.apache.commons.fileupload.FileUploadException;
import org.apache.commons.fileupload.disk.DiskFileItemFactory;
import org.apache.commons.fileupload.servlet.ServletFileUpload;
  
public class UploadPhotoServlet extends HttpServlet {
  
    public void doPost(HttpServletRequest request, HttpServletResponse response) {
  
        String filename = null;
        try {
            DiskFileItemFactory factory = new DiskFileItemFactory();
            ServletFileUpload upload = new ServletFileUpload(factory);
            // 设置上传文件的大小限制为1M
            factory.setSizeThreshold(1024 * 1024);
             
            List items = null;
            try {
                items = upload.parseRequest(request);
            } catch (FileUploadException e) {
                e.printStackTrace();
            }
  
            Iterator iter = items.iterator();
            while (iter.hasNext()) {
                FileItem item = (FileItem) iter.next();
                if (!item.isFormField()) {
  
                    // 根据时间戳创建头像文件
                    filename = System.currentTimeMillis() + ".jpg";
                     
                    //通过getRealPath获取上传文件夹，如果项目在e:/project/j2ee/web,那么就会自动获取到 e:/project/j2ee/web/uploaded
                    String photoFolder =request.getServletContext().getRealPath("uploaded");
                     
                    File f = new File(photoFolder, filename);
                    f.getParentFile().mkdirs();
  
                    // 通过item.getInputStream()获取浏览器上传的文件的输入流
                    InputStream is = item.getInputStream();
  
                    // 复制文件
                    FileOutputStream fos = new FileOutputStream(f);
                    byte b[] = new byte[1024 * 1024];
                    int length = 0;
                    while (-1 != (length = is.read(b))) {
                        fos.write(b, 0, length);
                    }
                    fos.close();
  
                } else {
                    System.out.println(item.getFieldName());
                    String value = item.getString();
                    value = new String(value.getBytes("ISO-8859-1"), "UTF-8");
                    System.out.println(value);
                }
            }
             
            String html = "<img width='200' height='150' src='uploaded/%s' />";
            response.setContentType("text/html");
            PrintWriter pw= response.getWriter();
             
            pw.format(html, filename);
             
        } catch (FileNotFoundException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        } catch (Exception e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }
}
```

3. 配置web.xml
4. 导入相关环境

# 动态Web项目

##  动态Servlet 创建

## Servlet 切换（手动切成）

# CRUD

* 与JDBC的增删查改
* https://how2j.cn/k/servlet/servlet-query/563.html

# Tomcat服务器 
* Tomcat服务器 = Web服务器（包括server，service，connector，Engine，host，context） + Servlet/JSP容器（一个Wrapper对应一个servlet，若是jsp，则会由jsp引擎进行处理）

> jsp - java（Servlet） -class
>
> servlet太麻烦了才有了Jsp, 

* Web服务器的作用是接收客户端的请求，给客户端作出响应。
* 所谓动态资源，其实最显著的特征就是它能动态地生成HTML！

# Tomcat架构

https://www.bilibili.com/video/BV1VT4y1g7Kc?from=search&seid=139186387859618333

用到了许多设计模式：facade，adapter等等

## Tomcat 文件含义

![img](https://pic2.zhimg.com/80/v2-d8b75829a65958c65d50781155ae80a1_720w.jpg)

> 其中work为一个Service划分一个JSP文件夹，jsp转换的java文件与编译后的class文件都在里面

## Tomcat 服务器分层

我们需要人工启动startup.bat或者自己设置一个批处理脚本来进行基本的系统环境判断和参数设置，然后启用catalina.bat，其会进行tomcat启动参数的配置和虚拟机环境的判断引用，最后调用Bootstrap类，其为Tomcat的主启动类，其会load和start Tomcat 的 Server和Service等其他组件，如同其他JAVA程序一样进行工作。

其中每一个组件都是一个相应的实现类配合工具类来实现的。

## ![img](https://img-blog.csdnimg.cn/20181206104130815.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI2MzIzMzIz,size_16,color_FFFFFF,t_70)

#### Server

* Server 表示整个servlet容器，因此Tomcat容器中只有一个Server实例(相关配置在server.xml中
* 主要负责对Service组件的生命周期管理，组装启动相应的Servlet引擎

### Service

* Service 表示一个或多个Connector的集合。这些Connector共享同一个Container来处理其他请求。在一个Server中可以包含多个Service，这些Service相互独立

### Connector

* 支持不同的协议及不同IO方式

* Connector的作用说穿了就是监听端口,Connector的作用说穿了就是监听端口,会负责把请求带到Engine那，然后Engine会处理。 
* 接收socket转换成http协议流，再选取需要的部分转换为request对象
* 创建reponse对象交由container处理后返回封装成socket，发送回客户端/浏览器

### Container

* Container 表示能够接收请求并返回响应的一类对象。在Tomcat中存在不同级别的容器：Engine、Host、Context、Wrapper

### Engine 

* Engine 表示整个Servlet引擎，Engine为最高级别的容器。尽量Engine不是直接处理请求的容器却是获得目标容器的入口

### Host 

* Host 表示Engine中的虚拟机，与一个服务器的网络名有关，如域名等。客户端可以使用这个网络名连接服务器，这个名称必须要在DNS服务器上注册

* 虚拟主机号，若是我们需要在同一个ip地址上需要有多个host（多个域名），我们可以在Engine中设置多个Host，用name属性标注多个域名，其中，发布的域名需要发布到DNS服务器（若只是本地则只需加入到），以便其能解析成相应的ip地址，进行tcp封装转发

### Context 

* Context 用于表示ServletContext，在Servlet规范中，一个ServletContext表示一个Web应用，

* 一个context为一个WebApp中的项目，若是不在webapp中，可在相应的host中配置Context，配置相应的docBase和path 属性即可

### Wrapper 

* Wrapper 表示Web应用中定义的Servlet

### Executor 

* 表示Tomcat组件间可以共享的线程池

## 最重要的两个配置文件

**采用配置文件的方式启动程序，使得我们修改一些常用的参数时，可以轻松修改并且启动tomcat时其自动读取配置，不用修改源代码，还需进行编译等操作**

![preview](https://pic2.zhimg.com/v2-142c11f3c514e299bf8ce7364aa55171_r.jpg)

* server.xml与Tomcat架构（在catalina类中被xml解析器与Digester配合使用）

![preview](https://pic2.zhimg.com/v2-fb9aa5d024fa131bbec7fb9fe9d99739_r.jpg)

* 我们每个动态web工程都有个web.xml，而conf里的这个，是它们的“老爹”。它里面的配置，如果动态web工程没有覆盖，就会被“继承”下来。我们会发现，conf/web.xml里配置了一个DefaultServlet：（在Wrapper中将Request转换成ServletRequest时使用）

![preview](https://pic1.zhimg.com/v2-70c094a65cffcfcdb0c74f199c01dbc8_r.jpg)

## 访问tomcat中文件的顺序

![preview](https://pic3.zhimg.com/v2-d0059da485a381d8d3a854f3e2789b2e_r.jpg)


* JspServlet的配置
![preview](https://pic4.zhimg.com/v2-acfd2c1561c561ca26ebbb7de579654f_r.jpg)

Tomcat处理请求的几种方式：
![preview](https://pic2.zhimg.com/v2-498b7e72c2ecbb62d55e3cddaea4ea75_r.jpg)

假设Tomcat里有个main方法：

![preview](https://pic3.zhimg.com/v2-ce6e39bb02e3c6a2f4eb1e5afaa6e4e6_r.jpg)

Tomcat注入：(Filter:过滤器）

![preview](https://pic3.zhimg.com/v2-14c18b69b5fb642f8d56698d2df20171_r.jpg)



![preview](https://pic4.zhimg.com/v2-d473a8662d758859e75c3f9afce9e982_r.jpg)/


![image-20200305173450430](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200305173450430.png)

# Servlet 参数接口

## Servlet 接口方法：

![preview](https://pic1.zhimg.com/v2-1a911529c489ebdcb2a17a8e19d87290_r.jpg)

***

## ServletConfig 

![image-20200305200009337](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200305200009337.png)

需要用dom4j解析xml得到对象（tomcat已经帮我们实现了）：

![preview](https://pic3.zhimg.com/v2-3dd656100783b3e9e62621ad8e2e9b04_r.jpg)

* Request对象：HTTP请求到了Tomcat后，Tomcat通过字符串解析，把各个请求头（Header），请求地址（URL），请求参数（QueryString）都封装进了Request对象中

* Response对象：Tomcat传给Servlet时,它还是空的对象，Servlet逻辑处理后得到结果,最终通过response.write()方法,将结果写入response内部的缓冲区 Tomcat会在servlet处理结束后,拿到response,遍历里面的信息,组装成HTTP响应发给客户端。

![preview](https://pic3.zhimg.com/v2-7405fb1912570c73de8dd76da725b17c_r.jpg)



## service

为了探索业务逻辑代码的继承关系和实现7中方法的模式：

GenericServlet，是个javax抽象类：

![img](https://pic1.zhimg.com/80/v2-b9f65e77009de2832d721cb28d5ae6f1_720w.jpg)	

没有实现Service,寻找继承了GenericServlet,找到了HttpServlet:

![preview](https://pic3.zhimg.com/v2-869e7ef9c2c7aaf4b90572ca140bd1b4_r.jpg)

![preview](https://pic2.zhimg.com/v2-73b703e690ce018ffe88280376a67dc0_r.jpg)

HttpServlet声明成抽象类的原因：

一个类声明成抽象方法，一般有两个原因：

- 有抽象方法
- 没有抽象方法，但是不希望被实例化

HttpServlet做成抽象类，仅仅是为了不让new：

![preview](https://pic4.zhimg.com/v2-b6602e7e7c24862f54041e3998bd7ee0_r.jpg)

它为什么不希望被实例化，且要求子类重写doGet、doPost等方法呢？

源码：

![preview](https://pic2.zhimg.com/v2-ab9504d35eab343a522926bf18c3167b_r.jpg)

若没重写：浏览器页面会显示：405（http.method_get_not_supported）

HttpServlet虽然在service中帮我们写了请求方式的判断。但是针对每一种请求，业务逻辑代码是不同的，HttpServlet无法知晓子类想干嘛，所以就抽出七个方法，并且提供了默认实现：报405、400错误，提示请求不支持。

![preview](https://pic1.zhimg.com/v2-fb86edf9aaac3049028e25fa022a9811_r.jpg)

父类把能写的逻辑都写完，把不确定的业务代码抽成一个方法，调用它。当子类重写该方法，整个业务代码就活了。这就是模板方法模式。

![preview](https://pic4.zhimg.com/v2-b33c2c238803958be0bfa70d0f40c211_r.jpg)
***
## ServletContext

- ServletContext对象的创建是在服务器启动时完成的
- ServletContext对象的销毁是在服务器关闭时完成的

![preview](https://pic2.zhimg.com/v2-40ed984999cab23bc4e9e17a39e84839_r.jpg)

* ServletContext对象的作用是在整个Web应用的动态资源（Servlet/JSP）之间共享数据。例如在AServlet中向ServletContext对象保存一个值，然后在BServlet中就可以获取这个值。

这种用来装载共享数据的对象，在JavaWeb中共有4个，而且更习惯被成为“域对象”：

- ServletContext域（Servlet间共享数据）
- Session域（一次会话间共享数据，也可以理解为多次请求间共享数据）
- Request域（同一次请求共享数据）
- Page域（JSP页面内共享数据）

> 它们都可以看做是map，都有getAttribute()/setAttribute()方法。

* 物理磁盘中的配置文件与内存对象之间的映射关系

  ![preview](https://pic4.zhimg.com/v2-2530b17c1ee7e94bbcce7ca472d6a667_r.jpg)

* 每一个动态web工程，都应该在WEB-INF下创建一个web.xml，它代表当前整个应用。Tomcat会根据这个配置文件创建ServletContext对象

* 如何获取ServletContext

  在GenericServlet的init方法中,将Tomcat传入的ServletConfig对象的作用域变量（方法内使用）提升到成员变量。并且新建了一个getServletContext()：servletConfig是servletContext的一部分，ServletConfig维系着ServletContext的引用。

  ![img](https://pic4.zhimg.com/80/v2-5b2c02d7dac0cd170c0194679f4d9483_720w.jpg)

最后，还有个冷门的

![preview](https://pic2.zhimg.com/v2-ec7e00121cdd1df01898f9b91d27c60d_r.jpg)

所以总共有5种方法：

* ServletConfig#getServletContext();

* GenericServlet#getServletContext();

* HttpSession#getServletContext();

* HttpServletRequest#getServletContext();

* ServletContextEvent#getServletContext();

## Filter拦截

它能够对Servlet容器的请求和响应对象进行检查和修改，它在Servlet被调用之前检查Request对象， 修改Request Header和Request内容；在Servlet被调用之后检查Response对象，修改Response Header和Response内容。Servlet过滤器负责过滤的Web组件可以是Servlet、JSP或HTML文件，具有以下特点：

l         Servlet过滤器可能检查和修改ServletRequest和ServletResponse对象

l         可以指定Servlet过滤器和特定的URL关联，只有当客户请求访问此URL时，才会触发该过滤器工作

l         多个Servlet过滤器可以被串联起来，形成管道效应，协同修改请求和响应对象

l         所有支持Java Servlet规范2.3的Servlet容器，都支持Servlet过滤器

 

所有的Servlet过滤器类都必须实现javax.servlet.Filter接口。该接口定义了以下3个方法：

l         init(FilterConfig)     这是Servlet过滤器的初始化方法，Servlet容器创建Servlet过滤器实例后就会调用这个方法。在这个方法中可以通过 FilterConfig来读取web.xml文件中Servlet过滤器的初始化参数。

l         doFilter(ServletRequest, ServletResponse, FilterChain)  这是完成实际的过滤操作的方法，当客户请求访问与过滤器关联的URL时，Servlet容器先调用该方法。FilterChain参数用来访问后续的过滤 器的doFilter()方法。

Filter  分发器可以设置4种拦截模式：

![preview](https://pic2.zhimg.com/v2-bbd582e840cac5e82eda33bd91cf60bd_r.jpg)

> FORWARD/INCLUDE等请求的分发是服务器内部的流程，不涉及浏览器.
>
> REQUEST是浏览器发起的，而ERROR是发生页面错误时发生的.

过滤器修改后，经过dispatcher分发器分发请求：REQUEST/FORWARD/INCLUDE/ERROR

![preview](https://pic2.zhimg.com/v2-425f3e0e15abf4743546fff21c5d9999_r.jpg)

![image-20200306215735735](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200306215735735.png)


![img](https://pic3.zhimg.com/80/v2-1d6b0e77752d60f39d829ad39a4a630a_720w.jpg)

然后通过Tomcat中一个叫Mapper的类中的internalMapWrapper方法

![preview](https://pic2.zhimg.com/v2-6de02a1543ac243e79adc217012418ad_r.jpg)

定义了7种映射规则：
![preview](https://pic3.zhimg.com/v2-0b486de6085fa9eae71b9203c5560236_r.jpg)

![preview](https://pic2.zhimg.com/v2-27bf362804b224137c0e51d5d17c8639_r.jpg)

![img](https://pic3.zhimg.com/80/v2-e46693fe4049808ba53c4577db61fa12_720w.jpg)

![img](https://pic1.zhimg.com/80/v2-c7ff7422c3a6ac49559e6494b7351590_720w.png)

> 对于静态资源，Tomcat最后会交由一个叫做DefaultServlet的类来处理
> 对于Servlet ，Tomcat最后会交由一个叫做 InvokerServlet的类来处理
  对于JSP，Tomcat最后会交由一个叫做JspServlet的类来处理

## 自定义DispatcherServlet

web.xml

![img](https://pic3.zhimg.com/80/v2-98fb7efadcc537b91fb57b00a100285a_720w.jpg)

DispatcherServlet

![img](https://pic2.zhimg.com/80/v2-4102042f7987731d511b905079908581_720w.jpg)

知道了映射器的映射规则后，我们来分析下上图中三种拦截方式会发生什么。

但在此之前，我必须再次强调，我从没说我现在写的是SpringMVC的DispatcherServlet，**这是我自己自定义的一个普通Servlet，**恰好名字叫DispatcherServlet而已。所以，下面的内容，请当做一个普通Servlet的映射分析。

- ***.do：拦截.do结尾**

![img](https://pic4.zhimg.com/80/v2-042f720df36b6102a380fde37c5b79c3_720w.jpg)

各个Servlet和谐相处，没问题。



- **/\*：拦截所有**

![img](https://pic4.zhimg.com/80/v2-b09b1c9c18463d995ef3fd8172725047_720w.jpg)

这会导致**两个问题**：

- JSP无法被编译成Servlet输出HTML片段（JspServlet短路）
- HTML/CSS/JS/PNG等资源无法获取（DefaultServlet短路）

- **/：拦截所有，但不包括JSP**

![img](https://pic1.zhimg.com/80/v2-e132fe10fc71bd79ce2d1f79964860d4_720w.jpg)

 servlet与servlet MVC

但SpringMVC提供了一个标签，解决上面/无法读取静态资源的问题：

```text
    <!-- 静态资源处理  css js imgs -->
    <mvc:resources location="/resources/**" mapping="/resources"/>
```

其他的我也不说了，一张图，大家体会一下DispatcherServlet与SpringMVC到底是什么关系：

![img](https://pic4.zhimg.com/80/v2-ff3412893ecd4b0737ecd2a40447d48f_720w.jpg)

## conf/web.xml与应用的web.xml

* 如果我们在应用的web.xml中为DispatcherServlet配置/，会和DefaultServlet产生路径冲突，从而覆盖DefaultServlet。此时，所有对静态资源的请求，映射器都会分发给我们自己写的DispatcherServlet处理。遗憾的是，它只写了业务代码，并不能IO读取并返回静态资源。JspServlet的映射路径没有被覆盖，所以动态资源照常响应。

## Listener

```text
6个常规监听器
    |---ServletContext
            |---ServletContextListener（生命周期监听）
            |---ServletContextAttributeListener（属性监听）
    |---HttpSession
            |---HttpSessionListener（生命周期监听）
            |---HttpSessionAttributeListener（属性监听）
    |---ServletRequest
            |---ServletRequestListener（生命周期监听）
            |---ServletRequestAttributeListener（属性监听）

2个感知监听
    |---HttpSessionBindingListener
    |---HttpSessionActivationListener
     
```


![preview](https://pic2.zhimg.com/v2-a11f7cf333741de3b44886db6b0d6e8d_r.jpg)

# tomcat 多个启动

**javax.management.InstanceNotFoundException: Catalina:type=Server**
**修改tomcat端口时却仍是8080**
**没有使用在idea tomcat中配置的端口的情况**

## 查看堆栈错误原因

**mvc跨服务器上传时，需要配置多个服务器，因为对idea启动服务器的流程不够清晰，所以两个tomcat server的configure中设置了同个tomcat 地址，引发了上面的三个同个根源的问题，之后是解决的思路与总结**

1. 直接复制粘贴相应的错误报告（javax.management.InstanceNotFoundException: Catalina:type=Server）到google和百度上查询

* 出现的多为修改host和Connector port 后出现的无法匹配问题，为单个服务器的问题，而我单个服务器的配置时并无问题，也有查过两个服务器与报错两者相关联的报错信息，但是可能因为问题的描述不对，导致无论stackflow还是其他论坛中相同的问题，答案却并不符合，快速解决的路子不通了，只能自己翻看错误报告了

* 首先，错误报告中最吸引人的字样是protocol中的 8080端口，因为idea的中无论 http port无论如何修改，到这里总是显示有关8080 端口的信息。

![image-20200725215847269](F:\Typora数据储存\高级语言\JAVA\servlet.assets\image-20200725215847269.png)

> 这里尝试过先启动idea中设置的8080 端口的服务器，再启动idea中设置为8089的同一个文件夹下的tomcat，服务器会开始部署，但到最后会出现**javax.management.InstanceNotFoundException: Catalina:type=Server**
>
> 而先开启8089的tomcat，再开启8080的，则会直接显示8080端口被占用。
>
> idea 的tomcat组件的作用：如同start.up一样帮我们预处理，其中的http port 的设置作用是idea会先帮我检查这个端口有没有被使用，若有，则直接报错端口占用（若不一致此时他并不会检查你的tomcat的server.xml中有没有启动这个端口）；若没有则，设定部署的一些参数，然后寻找tomcat服务器文件的位置以启动tomcat，启动application server中设置的tomcat服务器地址中的catalina.bat，这也是出现**javax.management.InstanceNotFoundException: Catalina:type=Server**该错误的地方，最后打开默认地址。
>
> ![image-20200726093732736](F:\Typora数据储存\高级语言\JAVA\servlet.assets\image-20200726093732736.png)

* 很明显，多次更改了idea中的配置，仍然出现的是8080端口，证明应该是启动过程中引用的其它文件的问题（因为引用的是tomcat服务，所以自然而然地想到tomcat服务器的配置问题），向上翻找启动顺序。

![image-20200726101953767](F:\Typora数据储存\高级语言\JAVA\servlet.assets\image-20200726101953767.png)



##  tomcat startup.bat

* .bat 批处理脚本，与Linux中的shell脚本原理一致，均是用系统中存在的命令，基于想要实现的功能所需要的逻辑组合使用，完成功能

> Windows 批处理(bat)语法大全https://blog.csdn.net/qq_36838191/article/details/83046599

* 看到了\bin\catalina.bat，可以让我们很容易联想到刚学tomcat时，最原始的启动方式：startup.bat,他也是通过启动catalina.bat来启动tomcat服务器的配置的，从这我们可以看出idea的tomcat server配置相当于自己完成了startup.bat功能。

> **startup.bat详解**
>
> 
>
> if "%OS%" == "Windows_NT" setlocal //判断当前系统是否是window系统
>
> rem --------------------------------------------------------------------------- //rem 是注释（下同）
>
> rem Start script for the CATALINA Server
>
> rem
>
> rem $Id: startup.bat 302918 2004-05-27 18:25:11Z yoavs $
>
> rem ---------------------------------------------------------------------------
>
> rem Guess CATALINA_HOME if not defined
>
> set CURRENT_DIR=%cd% //设置当前目录
>
> if not "%CATALINA_HOME%" == "" goto gotHome //如果设置了CATALINA_HOME环境变量 ，就直接到下面的gotHome处
>
> set CATALINA_HOME=%CURRENT_DIR% //如果没有设置CATALINA_HOME，就设置CATALINA_HOME为当前目录（其实这里她假设你进入tomcat的安装目录）
>
> if exist "%CATALINA_HOME%\bin\catalina.bat" gotookHome//判断一下catalina.bat是否找到了，找到了就直接到下面的gotHome处
>
> cd .. //这里他是假设你开始已经进入到了tomcat的bin目录，所以就退到上一级目录
>
> set CATALINA_HOME=%cd%//现在再设置CATALINA_HOME为tomcat的安装目录
>
> cd %CURRENT_DIR% //这里是进入dos的当前目录
>
> :gotHome
>
> if exist "%CATALINA_HOME%\bin\catalina.bat" goto	okHome //再次判断catalina.bat是否找到了，找到了就直接到下面的okHome处，没有的话，就只能提示你啦！
>
> echo The CATALINA_HOME environment variable is not defined correctly
>
> echo This environment variable is needed to run this program
>
> goto end
>
> :okHome
>
> set EXECUTABLE=%CATALINA_HOME%\bin\catalina.bat //设置要执行的文件
>
> rem Check that target executable exists
>
> if exist "%EXECUTABLE%" gotookExec //再次判断catalina.bat是否找到了，找到了就直接到下面的okExec处，没有的话，就提示。
>
> echo Cannot find %EXECUTABLE%
>
> echo This file is needed to run this program
>
> goto end
>
> :okExec
>
> rem Get remaining unshifted command line arguments and save them in the
>
> set CMD_LINE_ARGS= //这里是设置参数
>
> :setArgs
>
> ==rem %1为我们启动startup.bat的第一个启动参数== **eg：cmd中输入：test.bat 1 2，％0就表示test.bat,％1表示test.bat的第一个参数“1”，%2表示"2"。**

> if ""%1""=="""" gotodoneSetArgs //判断参数是否加入完成
>
> set CMD_LINE_ARGS=%CMD_LINE_ARGS% %1 //将参数组成一行，接在后面
>
> shift
>
> gotosetArgs
>
> :doneSetArgs
>
> call "%EXECUTABLE%" start %CMD_LINE_ARGS% //执行catalina.bat，最好将这行改为:echo "%EXECUTABLE%" start %CMD_LINE_ARGS% 以便阅读、理解本文件的作用
>
> :end



## Catalina.bat

* 可以看出startup.bat 主要是进行一些环境的判断，然后启动catalina.bat,Catalina.bat是tomcat所有脚本中最重要的脚本，完成几乎所有的tomcat操作。如启动，关闭等等,都是由catalina.bat脚本来完成的。

> Catalina.bat脚本解析
>
> 
>
> Catalina.bat是tomcat所有脚本中最重要的脚本，完成几乎所有的tomcat操作。如启动，关闭等等,都是由catalina.bat脚本来完成的。接下来，我将对Tomcat catalina.bat脚本进行分析。 
>
> 首先省去catalina.bat开头诸多注解，这些注解主要是讲解各个变量是干什么的。
>
> rem Guess CATALINA_HOME if not defined 查看是否在tomcat目录下，与startup.bat里相同，不解释了。
> set CURRENT_DIR=%cd% 
> if not "%CATALINA_HOME%" == "" goto gotHome 
> set CATALINA_HOME=%CURRENT_DIR% 
> if exist "%CATALINA_HOME%\bin\catalina.bat" goto okHome 
> cd .. 
> set CATALINA_HOME=%cd% 
> cd %CURRENT_DIR% 
> :gotHome 
> if exist "%CATALINA_HOME%\bin\catalina.bat" goto okHome 
> echo The CATALINA_HOME environment variable is not defined correctly 
> echo This environment variable is needed to run this program 
> goto end 
> :okHome 
>
> rem Get standard environment variables 
> if exist "%CATALINA_HOME%\bin\setenv.bat" call "%CATALINA_HOME%\bin\setenv.bat" 如果存在setenv.bat脚本，调用它，我的tomcat 没有这个脚本 
>
> rem Get standard Java environment variables 
>
> ==**rem  查看是否存在setclasspath.bat脚本，如果存在，转到okSetclasspath位置**== 
>
> if exist "%CATALINA_HOME%\bin\setclasspath.bat" goto okSetclasspath
> echo Cannot find %CATALINA_HOME%\bin\setclasspath.bat 否则输出下面两行，并退出  
> echo This file is needed to run this program 
> goto end 
> :okSetclasspath  okSetclasspath位置 
>
> set BASEDIR=%CATALINA_HOME%  设定BASEDIR变量与CATALINA_HOME变量值相同 
>
> rem ==调用setclasspath.bat脚本并加上参数== (查询我们的系统环境变量中是否有**%JAVA_HOME%**且相应的启动文件齐全，是则将相应的exe启动程序路径保存到**_RUNJAVA、__RUNJDB**等临时环境变量中，以便后续调用)
>
> call "%CATALINA_HOME%\bin\setclasspath.bat" %1  
> if errorlevel 1 goto end   如果存在错误 退出 
>
> rem Add on extra jar files to CLASSPATH  
>
> ==rem 设定JSSE_HOME变量，如果存在加入CLASSPATH，不存在跳过，在最后的命令中用-classpath参数调用== 
>
> if "%JSSE_HOME%" == "" goto noJsse  检查是否存在JSSE_HOME变量 
> set CLASSPATH=%CLASSPATH%;%JSSE_HOME%\lib\jcert.jar;%JSSE_HOME%\lib\jnet.jar;%JSSE_HOME%\lib\jsse.jar 如果有加入到CLASSPATH变量后面 
> :noJsse                  
> set CLASSPATH=%CLASSPATH%;%CATALINA_HOME%\bin\bootstrap.jar 将bootstrap.jar加入到CLASSPATH里 
>
> if not "%CATALINA_BASE%" == "" goto gotBase 如果CATALINA_BASE变量不为空，跳过，转到gotBase位置 
> set CATALINA_BASE=%CATALINA_HOME% 如果为空，将CATALINA_BASE设为CATALINA_HOME变量的值 
> :gotBase 
>
> if not "%CATALINA_TMPDIR%" == "" goto gotTmpdir  CATALINA_TMPDIR不为空，跳过，转到gotTmpdir位置 
> set CATALINA_TMPDIR=%CATALINA_BASE%\temp  如果为空，将 CATALINA_TMPDIR设为%CATALINA_BASE%\temp变量的值（即tomcat\temp） 
> :gotTmpdir 
>
> ==rem 设定java启动时的启动参数 JAVA_OPTS，不存在跳过，在最后的命令中用%JAVA_OPTS% 调用== 
>
> if not exist "%CATALINA_HOME%\bin\tomcat-juli.jar" goto noJuli 如果不存在tomcat-juli.jar这个类，转到noJuli位置 
> set JAVA_OPTS=%JAVA_OPTS% -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager - Djava.util.logging.config.file="%CATALINA_BASE%\conf \logging.properties" 如果存在，将变量加入到JAVA_OPTS里 
> :noJuli 
>
> set JAVA_OPTS=%JAVA_OPTS% -Xms128m -Xmx512m -Dfile .encoding=UTF8 -Duser.timezone=GMT -Djava.security.auth.login.config=%CATALINA_HOME%/conf/jaas.config 设定JAVA_OPTS变量 
>
> ==rem ----- Execute The Requested Command ---------------------------------------==
>
> echo Using CATALINA_BASE:  %CATALINA_BASE%   输出CATALINA_BASE变量值 
> echo Using CATALINA_HOME:  %CATALINA_HOME%   输出CATALINA_HOME变量值 
> echo Using CATALINA_TMPDIR: %CATALINA_TMPDIR% 输出CATALINA_TMPDIR变量值 
> if ""%1"" == ""debug"" goto use_jdk    如果变量%1里存在debug ，转到use_jdk位置 
> echo Using JRE_HOME:    %JRE_HOME%   输出JRE_HOME变量值 
> goto java_dir_displayed   转到java_dir_displayed 
> :use_jdk 
> echo Using JAVA_HOME:    %JAVA_HOME%  输出JAVA_HOME变量值 
> :java_dir_displayed 
>          下面几行设定相应变量 
>
> ==rem 默认的启动参数==
>
> **rem 启动jave的jre/jdk存放的java.exe文件的位置**
>
> set __EXECJAVA=%_RUNJAVA%    
>
> ==rem 启动的主类Bootstrap，从-classpath导入的包中该类的main方法开始执行==
>
> set MAINCLASS=org.apache.catalina.startup.Bootstrap 
>
> **rem 默认启动等级**
>
> set ACTION=start 
> set SECURITY_POLICY_FILE= 
> set DEBUG_OPTS= 
> set JPDA= 
>
> ==rem 若用jdpa ，则jdpa 参数设置（虚拟机为调试和监控虚拟机专门提供的一套接口）==

> if not ""%1"" == ""jpda"" goto noJpda 
> set JPDA=jpda 
> if not "%JPDA_TRANSPORT%" == "" goto gotJpdaTransport 
> set JPDA_TRANSPORT=dt_shmem 
> :gotJpdaTransport 
> if not "%JPDA_ADDRESS%" == "" goto gotJpdaAddress 
> set JPDA_ADDRESS=jdbconn 
> :gotJpdaAddress 
> if not "%JPDA_SUSPEND%" == "" goto gotJpdaSuspend 
> set JPDA_SUSPEND=n 
> :gotJpdaSuspend 
> if not "%JPDA_OPTS%" == "" goto gotJpdaOpts 
> set JPDA_OPTS=-Xdebug -Xrunjdwp:transport=%JPDA_TRANSPORT%,address=%JPDA_ADDRESS%,server=y,suspend=%JPDA_SUSPEND%
> :gotJpdaOpts 
> shift 
> :noJpda 
>
> ==rem 依据启动级别启动，默认为start==
>
> if ""%1"" == ""debug"" goto doDebug  如果%1为debug，转到doDebug，运行debug模式 
> if ""%1"" == ""run"" goto doRun    如果%1为run，转到doRun，运行正常模式 
> if ""%1"" == ""start"" goto doStart  如果%1为start，转到doStart，启动tomcat 
> if ""%1"" == ""stop"" goto doStop   如果%1为stop，转到doStop，关闭tocmat 
> if ""%1"" == ""version"" goto doVersion 如果%1为version，转到doVersion，显示tomcat的版本号 
>
> echo Usage: catalina ( commands ... ) 如果%1没有上述内容，输出下面几行，并结束 
> echo commands: 
> echo  debug       Start Catalina in a debugger 
> echo  debug -security  Debug Catalina with a security manager 
> echo  jpda start    Start Catalina under JPDA debugger 
> echo  run        Start Catalina in the current window 
>
> echo  run -security   Start in the current window with security manager 
> echo  start       Start Catalina in a separate window 
> echo  start -security  Start in a separate window with security manager 
> echo  stop       Stop Catalina 
> echo  version      What version of tomcat are you running? 
> goto end 
>
> :doDebug 
> shift          将%2里的值转到%1 
> set _EXECJAVA=%_RUNJDB% 将变量 _EXECJAVA设为_RUNJDB变量的值 
> set DEBUG_OPTS=-sourcepath "%CATALINA_HOME%\..\..\jakarta-tomcat-catalina\catalina\src\share" 
> 设定DEBUG_OPTS变量 
>
> if not ""%1"" == ""-security"" goto execCmd  
> 如果%1不为-security，转到execCmd位置 
>
> shift    将%2里的值转到%1 
> echo Using Security Manager    输出该行 
> set SECURITY_POLICY_FILE=%CATALINA_BASE%\conf\catalina.policy 
> 设定SECURITY_POLICY_FILE变量的值 
>
> goto execCmd   转到execCmd位置 
>
> :doRun 
> shift    将%2里的值转到%1 
> if not ""%1"" == ""-security"" goto execCmd  如果%1不为-security，转到execCmd位置 
> shift    将%2里的值转到%1 
> echo Using Security Manager  输出该行 
> set SECURITY_POLICY_FILE=%CATALINA_BASE%\conf\catalina.policy 
> 设定SECURITY_POLICY_FILE变量的值 
>
> goto execCmd 转到execCmd位置 
>
> ==rem 设置启动的程序的标题为Tomcat，跳转到命令参数execCmd设置语句==
>
> :doStart 
> shift    将%2里的值转到%1 
> if "%TITLE%" == "" set TITLE=Tomcat
> set _EXECJAVA=start "%TITLE%" %_RUNJAVA%
> if not ""%1"" == ""-security"" goto execCmd
>
> ==rem 若有-security参数，则启动时需要配置安全参数的catalina.policy 文件地址==
>
> shift          将%2里的值转到%1 
> echo Using Security Manager    输出该行 
> set SECURITY_POLICY_FILE=%CATALINA_BASE%\conf\catalina.policy  
> 设定SECURITY_POLICY_FILE变量的值 
>
> goto execCmd    转到execCmd位置 
>
> 
>
> :doStop 
> shift         将%2里的值转到%1 
> set ACTION=stop    将ACTION的变量设为stop 
> set CATALINA_OPTS=  设CATALINA_OPTS为空 
>
> goto execCmd     转到execCmd位置 
>
> :doVersion      显示tomcat版本号 
> %_EXECJAVA% -classpath "%CATALINA_HOME%\server\lib\catalina.jar" org.apache.catalina.util.ServerInfo  执行该命令 
> goto end       结束该程序 
>
> :execCmd      
> rem Get remaining unshifted command line arguments and save them in the 
> 以下几行将命令参数存入CMD_LINE_ARGS变量中 
>
> set CMD_LINE_ARGS= 
> :setArgs 
> if ""%1""=="""" goto doneSetArgs 
> set CMD_LINE_ARGS=%CMD_LINE_ARGS% %1 
> shift 
> goto setArgs 
> :doneSetArgs 
>
> rem Execute Java with the applicable properties 
> if not "%JPDA%" == "" goto doJpda    如果JPDA变量不为空，转到doJpda位置 
> if not "%SECURITY_POLICY_FILE%" == "" goto doSecurity 
> 如果SECURITY_POLICY_FILE变量不为空，转到doSecurity位置 
>
> rem 默认执行语句 大概为：(也是用字节码的方式启动)
>
> ==start “Tomcat” ···/java.exe - JAVA_OPTS -CATALINA_OPTS  -DEBUG_OPTS  -Djava.endorsed.dirs="%JAVA_ENDORSED_DIRS%" -classpath==
>
> =="%CLASSPATH%" -Dcatalina.base="%CATALINA_BASE%" -Dcatalina.home="%CATALINA_HOME%" -Djava.io.tmpdir="%CATALINA_TMPDIR%"==
>
> ==org.apache.catalina.startup.Bootstrap   -CMD_LINE_ARGS   start==
>
> %_EXECJAVA% %JAVA_OPTS% %CATALINA_OPTS% %DEBUG_OPTS% -Djava.endorsed.dirs="%JAVA_ENDORSED_DIRS%" -classpath "%CLASSPATH%" -Dcatalina.base="%CATALINA_BASE%" -Dcatalina.home="%CATALINA_HOME%" -Djava.io.tmpdir="%CATALINA_TMPDIR%" %MAINCLASS% %CMD_LINE_ARGS% %ACTION%
> goto end_
>
> 
>
> :doSecurity
> %_EXECJAVA% %JAVA_OPTS% %CATALINA_OPTS% %DEBUG_OPTS% -Djava.endorsed.dirs="%JAVA_ENDORSED_DIRS%" -classpath "%CLASSPATH%" -Djava.security.manager -Djava.security.policy == "%SECURITY_POLICY_FILE%" -Dcatalina.base="%CATALINA_BASE%" -Dcatalina.home="%CATALINA_HOME%" -Djava.io.tmpdir="%CATALINA_TMPDIR%" %MAINCLASS% %CMD_LINE_ARGS% %ACTION%
> goto end
>
> 
>
> :doJpda
> if not "%SECURITY_POLICY_FILE%" == "" goto doSecurityJpda
> %_EXECJAVA% %JAVA_OPTS% %CATALINA_OPTS% %JPDA_OPTS% %DEBUG_OPTS% -Djava.endorsed.dirs="%JAVA_ENDORSED_DIRS%" -classpath "%CLASSPATH%" -Dcatalina.base="%CATALINA_BASE%" -Dcatalina.home="%CATALINA_HOME%" -Djava.io.tmpdir="%CATALINA_TMPDIR%" %MAINCLASS% %CMD_LINE_ARGS% %ACTION%
> goto end_
>
> 
>
> :doSecurityJpda
> %_EXECJAVA% %JAVA_OPTS% %CATALINA_OPTS% %JPDA_OPTS% %DEBUG_OPTS% -Djava.endorsed.dirs="%JAVA_ENDORSED_DIRS%" -classpath "%CLASSPATH%" -Djava.security.manager -Djava.security.policy == "%SECURITY_POLICY_FILE%" -Dcatalina.base="%CATALINA_BASE%" -Dcatalina.home="%CATALINA_HOME%" -Djava.io.tmpdir="%CATALINA_TMPDIR%" %MAINCLASS% %CMD_LINE_ARGS% %ACTION%
> goto end

## Bootstrap

* bootStrap类主要负责对于command的参数的相应和对于jar包和配置文件路径的解读记录，方便catalina类创建组件时使用

#### Bootstrap属性

```java
public final class Bootstrap {
    private static final Object daemonLock = new Object(); //初始化并发锁
    private static volatile Bootstrap daemon = null; //单例
    private static final File catalinaBaseFile;   //jvm启动参数catalina.base
    private static final File catalinaHomeFile;//jvm启动参数catalina.home
    private static final Pattern PATH_PATTERN = Pattern.compile("(\".*?\")|(([^,])*)");
    private Object catalinaDaemon = null;
    ClassLoader commonLoader = null;//公共类加载器，catalinaLoader，sharedLoader两者共同引用的
    ClassLoader catalinaLoader = null;//tomcat的jvm类加载器
    ClassLoader sharedLoader = null;//共享类加载器
}
```

#### 1. 1 main

 在启动过程中，会首先调用静态方法，将参数`catalina.base`和`catalina.home`存放在System的Properties中。
 在bootstrap的main方法中，首先会将Bootstrap初始化(init)，然后加载(load)，最后启动(start)。

```java
public static void main(String args[]) {
    synchronized (daemonLock) {//加锁，初始化一次daemon
        if (daemon == null) { 
            Bootstrap bootstrap = new Bootstrap();
            try {
                bootstrap.init();//初始化见1.2
            } catch (Throwable t) {
                handleThrowable(t);
                t.printStackTrace();
                return;
            }
            // 将创建后的bootstrap
            daemon = bootstrap;
        } else {
            // 当前线程默认的容器的类加载器，将容器类加载到方法区时可以直接从线程中获取catalinaLoader
            Thread.currentThread().setContextClassLoader(daemon.catalinaLoader);
        }
    }
    
    // 若是没有参数，则默认为start
    String command = "start"; 
    if (args.length > 0) {
        //取第一个启动jvm参数为指令，java org.apache.catalina.startup.Bootstrap start 时，取得为start
        command = args[args.length - 1];
    }

    //如果为startd，当做start处理
    if (command.equals("startd")) { 
        args[args.length - 1] = "start";
        daemon.load(args); //加载 见1.3
        daemon.start(); //启动 见1.4
    }
    //stopd当做stop处理
    else if (command.equals("stopd")) {
        args[args.length - 1] = "stop";
        daemon.stop();
    } else if (command.equals("start")) {
        daemon.setAwait(true); 
        daemon.load(args); //加载
        daemon.start();//启动
        if (null == daemon.getServer()) {
            System.exit(1);
        }
    } else if (command.equals("stop")) {
        daemon.stopServer(args);
    }
}
```

#### 1.2 init

 在启动过程中，会首先调用静态方法，将参数`catalina.base`和`catalina.home`存放在System的Properties中。

然后初始化三个类加载器common、shared、catalina

最后将Catalina对象关联在catalinaDaemon上，之后daemon的load和start也是反射用catalinaDaemon的load和start方法。

```java
 public void init() throws Exception {
        // 为什么要设置自己的类加载器和为什么要依靠反射创建类加载器可以参考下文
     	//将参数catalina.base和catalina.home存放在System的Properties中。
        this.setCatalinaHome();
        this.setCatalinaBase();
     
     	//初始化三个类加载器
        initClassLoaders();
       //将当前线程类加载器设置为catalina类加载器
        Thread.currentThread().setContextClassLoader(catalinaLoader);
        SecurityClassLoad.securityClassLoad(catalinaLoader);
        //初始化Catalina Clss到方法区	
        Class<?> startupClass = catalinaLoader.loadClass("org.apache.catalina.startup.Catalina");、
        // 实例化到堆中
        Object startupInstance = startupClass.getConstructor().newInstance();
        // Set the shared extensions class loader
        if (log.isDebugEnabled())
            log.debug("Setting startup class properties");
     	// 反射调用的方法，设置某个类的类加载器
        String methodName = "setParentClassLoader";
        Class<?> paramTypes[] = new Class[1];
        paramTypes[0] = Class.forName("java.lang.ClassLoader");
        Object paramValues[] = new Object[1];
        paramValues[0] = sharedLoader;
     
     	// 将Catalina的ClassLoader更改为sharedLoader
        Method method =
            startupInstance.getClass().getMethod(methodName, paramTypes);
     
     	
        method.invoke(startupInstance, paramValues); 
     	//将Catalina对象关联在catalinaDaemon上
        catalinaDaemon = startupInstance;
    }

    private void initClassLoaders() {
       commonLoader = createClassLoader("common", null); 见1.2.1
       catalinaLoader = createClassLoader("server", commonLoader);
       sharedLoader = createClassLoader("shared", commonLoader);
    }
```

> https://blog.csdn.net/liweisnake/article/details/8470285
>
> 为什么tomcat需要实现自己的classloader，jvm提供的classloader有什么不符合需要？
>
>   事实上，tomcat之所以造了一堆自己的classloader，大致是出于下面三类目的：
>
> 1. 对于各个webapp中的class和**lib，需要相互隔离**，不能出现一个应用中加载的类库会影响另一个应用的情况；而对于许多应用，需要有**共享的lib**以便不浪费资源，举个例子，如果webapp1和webapp2都用到了log4j，可以将log4j提到tomcat/lib中，表示所有应用共享此类库，试想如果log4j很大，并且20个应用都分别加载，那实在是没有必要的。
>
> 2. 第二个原因则是与jvm一样的**安全性问题**。使用单独的classloader去装载tomcat自身的类库，以免其他恶意或无意的破坏；
>
> 3. 第三个原因是**热部署**的问题。相信大家一定为tomcat修改文件不用重启就自动重新装载类库而惊叹吧。
>
>   由于篇幅所限，本文集中探讨第一个和第三个原因，即tomcat中如何利用classloader做到部分隔离，部分共享的，以及tomcat如何做到热部署的。
>
>   首先，我们讨论tomcat中如何做到lib的部分隔离，部分共享的。在Bootstrap中，可以找到如下代码：
>
> ```java
> private void initClassLoaders() {
>       try {
>           commonLoader = createClassLoader("common", null);
>             if( commonLoader == null ) {
>            // no config file, default to this loader - we might be in a 'single' env.
>            commonLoader=this.getClass().getClassLoader();
> 	        }
>            catalinaLoader = createClassLoader("server", commonLoader);
>             sharedLoader = createClassLoader("shared", commonLoader);
>         } catch (Throwable t) {
>             log.error("Class loader creation threw exception", t);
>             System.exit(1);
>         }
>     }
> ```
>
>   应该可以看出来，这里创建了3个classloader，分别是common，server和shared，并且common是server和shared之父。如果感兴趣，可以看下createClassLoader，它会调用进ClassLoaderFactory.createClassLoader，这个工厂方法最后会创建一个StandardClassLoader，StandardClassLoader仅仅继承了URLClassLoader而没有其他更多改动，也就是说上面3个classloader都是StandardClassLoader，除了层次关系之外，他们与jvm定义的classloader并没有区别，这就意味着他们同样遵循双亲委派模型，只要我们能够用它装载指定的类，则它就自然的嵌入到了jvm的classloader体系中去了。Tomcat的classloader体系如图：
>
> ![image-20200901163143904](F:\Typora数据储存\高级语言\JAVA\JavaWeb.assets\image-20200901163143904.png)
>
>   问题来了，tomcat是如何将自己和webapp的所有类用自己的classloader加载的呢？是否需要有个专门的地方遍历所有的类并将其加载，可是代码里并不能找到这样的地方。而且相对来说，将不用的类显式的加载进来也是一种浪费，那么，tomcat（或者说jvm）是如何做到这点呢？
>
>   这里有个隐式加载的问题，所谓的隐式加载，就是指在当前类中所有new的对象，如果没有被加载，则使用当前类的类加载器加载，即this.getClass(),getClassLoader()会默认加载该类中所有被new出来的对象的类（前提是他们没在别处先被加载过）。从这里思考，我们一个一个的应用，本质上是什么样子，事实上，正如所有程序都有一个main函数一样，所有的应用都有一个或多个startup的类（即入口），这个类是被最先加载的，并且随后的所有类都像树枝一样以此类为根被加载，只要控制了加载该入口的classloader，等于就控制了所有其他相关类的classloader。
>
>   以此为线索来看tomcat的Bootstrap中的init代码
>
> ```java
> public void init() throws Exception {
>         this.setCatalinaHome();
>         this.setCatalinaBase();
>         this.initClassLoaders();
>         Thread.currentThread().setContextClassLoader(this.catalinaLoader);
>         SecurityClassLoad.securityClassLoad(this.catalinaLoader);
>         if (log.isDebugEnabled()) {
>             log.debug("Loading startup class");
>         }
> 
>         Class<?> startupClass = this.catalinaLoader.loadClass("org.apache.catalina.startup.Catalina");
>         Object startupInstance = startupClass.newInstance();
>         if (log.isDebugEnabled()) {
>             log.debug("Setting startup class properties");
>         }
> 		
>     	// 将Catalina的类信息放置到sharedLoader上，跟其他
>         String methodName = "setParentClassLoader";
>         Class<?>[] paramTypes = new Class[]{Class.forName("java.lang.ClassLoader")};
>         Object[] paramValues = new Object[]{this.sharedLoader};
>         Method method = startupInstance.getClass().getMethod(methodName, paramTypes);
>         method.invoke(startupInstance, paramValues);
>         this.catalinaDaemon = startupInstance;
>     }
> ```
>
>  在catalinaLoader.loadClass之后，Catalina事实上就由server这个classloader加载进来了，而下一句newInstance时，所有以Catalina为根的对象的类也会全部被隐式加载进来。
>
> ==但是为什么这里需要在其后费尽笔墨反射去setParentClassLoader呢==，直接用((Catalina)startupInstance).setParentClassLoader岂不是更加方便？要注意，如果这样写，这个强制转换的Catalina便会由加载BootStrap的classloader(URLClassLoader)加载进来（而他并没有），而startupInstance是由StandardClassLoader加载进来的，而ClassLoader只能从下至上，不能自上而下，由此会抛一个ClassCastException。这也是类库可能发生冲突的一个原因。
>
> 那为什么在eclipse中**调试tomcat源码时**把反射换成((Catalina)startupInstance).setParentClassLoader是完全合法的，没有报任何异常。
>
> 这里需要注意tomcat的启动默认会把bin下的bootstrap.jar加入classpath：set "CLASSPATH=%CLASSPATH%%CATALINA_BASE%\bin\tomcat-juli.jar;%CATALINA_HOME%\bin\bootstrap.jar"，而eclipse中调试tomcat是所有相关类都在classpath的。
>
> **区别在于：**
>
> 第一种情况，双亲委派模型在上层找不到Catalina.class，则StandardClassLoader去lib下加载catalina.jar；
>
> 而第二种情况，AppClassLoader直接能够找到Catalina.class
>
> 所以就由他加载了，StandardClassLoader就形同虚设了。所以我们不能单从现象去判断原因，这也是我们为什么要学习classloader加载原理的原因。
>
> 
>
>   搞明白这点，其实就可以理解tomcat是如何使用自己的classloader加载类进来并且如何隔离server和shared类的加载了。
>
>   但是另一个问题，**tomcat又是如何隔离不同的webapp的加载呢？**
>
>   对于每个webapp应用，都会对应唯一的StandContext，在StandContext中会引用WebappLoader，该类又会引用WebappClassLoader，WebappClassLoader就是真正加载webapp的classloader。
>
>   StandContext隶属于Lifecycle管理，在start方法中会做一系列准备工作（有兴趣可以参考，实际上该方法比较重要，但是篇幅太长），比如新建WebappClassLoader，另外loadOnStartup便会加载所有配置好的servlet（每个StandardWrapper负责管理一个servlet），这里同样的一个问题是，在我们自己写的web应用程序中，入口是什么？答案就是Servlet, Listener, Filter这些组件，如果我们控制好入口的classloader，便等于控制了其后所加载的全部类，那么，tomcat是如何控制的呢？且看StandardWrapper中一个重要的方法loadServlet（篇幅所限，隐去了大部分不想关内容），getLoader()事实上调用到了StandContext中保存的WebappLoader，于是，用该loader加载Servlet，从而控制住了Servlet中所有待加载的类。
>
> ```java
> public synchronized Servlet loadServlet() throws ServletException {
> 		...
>         Servlet servlet;
>         try {
>         		 ...
> 
>             // Acquire an instance of the class loader to be used
>             Loader loader = getLoader();
>             if (loader == null) {
>                 unavailable(null);
>              throw new ServletException
> 
>                     (sm.getString("standardWrapper.missingLoader", getName()));
>       		  }
>             ClassLoader classLoader = loader.getClassLoader();
> 
>             // Special case class loader for a container provided servlet           
>             if (isContainerProvidedServlet(actualClass) && 
>                 ! ((Context)getParent()).getPrivileged() ) {
> 
>                 // If it is a priviledged context - using its own
>                 // class loader will work, since it's a child of the container
>                 // loader
>                 classLoader = this.getClass().getClassLoader();
> 
>             }
>             // Load the specified servlet class from the appropriate class loader
> 	          Class classClass = null;
>             try {
>                 if (SecurityUtil.isPackageProtectionEnabled()){
>                     ...
>                 } else {
>                    if (classLoader != null) {
>                       classClass = classLoader.loadClass(actualClass);
>                     } else {
>                         classClass = Class.forName(actualClass);
>                     }
>                 }
>             } catch (ClassNotFoundException e) {
>                 unavailable(null);
> 	            getServletContext().log( "Error loading " + classLoader + " " + actualClass, e );
>                 throw new ServletException
>                     (sm.getString("standardWrapper.missingClass", actualClass), e);
> 
>             }
>             if (classClass == null) {
>                 unavailable(null);
>                 throw new ServletException
>                   (sm.getString("standardWrapper.missingClass", actualClass));
>             }
> 
>             // Instantiate and initialize an instance of the servlet class itself
>             try {
>                 servlet = (Servlet) classClass.newInstance();
>                // Annotation processing
>                 if (!((Context) getParent()).getIgnoreAnnotations()) {
>                     if (getParent() instanceof StandardContext) {
>                        ((StandardContext)getParent()).getAnnotationProcessor().processAnnotations(servlet);
>                        ((StandardContext)getParent()).getAnnotationProcessor().postConstruct(servlet);
>                     }
>                 }
>             } catch (ClassCastException e) {
>                 ...
>             } catch (Throwable e) {
>               ...
> 
>             }
>            ...
>         return servlet;
>     }
> ```
>
>    这里的加载过程与之前的一致，至于如何做到不同webapp之间的隔离，我想大家已经明白，不同的StandardContext有不同的WebappClassLoader，那么不同的webapp的类装载器就是不一致的。装载器的不一致带来了名称空间不一致，所以webapp之间是相互隔离的。
>
> 
>
>   关于tomcat是如何做到**热部署**的，相信不用说也能猜到个十之八九。简单讲就是定期检查是否需要热部署，如果需要，则将类装载器也重新装载，并且去重新装载其他相关类。关于tomcat是如何做的，可以具体看以下分析。
>
>   首先来看一个后台的定期检查，该定期检查是StandardContext的一个后台线程，会做reload的check，过期session清理等等，这里的modified实际上调用了WebappClassLoader中的方法以判断这个class是不是已经修改。注意到他调用了StandardContext的reload方法。
>
> 
>
> ```java
> public void backgroundProcess() {
>         if (reloadable && modified()) {
>           try {
>             Thread.currentThread().setContextClassLoader
>                   (WebappLoader.class.getClassLoader());
>               if (container instanceof StandardContext) {
>                    ((StandardContext) container).reload();
> 
>                 }
>             } finally {
> 
>                 if (container.getLoader() != null) {
>                   Thread.currentThread().setContextClassLoader
>                       (container.getLoader().getClassLoader());
> 
>                 }
> 
>             }
> 
>         } else {
>             closeJARs(false);
>         }
>     }
> ```
>
>   那么reload方法具体做了什么？非常简单，就是tomcat lifecycle中标准的启停方法stop和start，别忘了，start方法会重新造一个WebappClassLoader并且重复loadOnStartup的过程，从而重新加载了webapp中的类，注意到一般应用很大时，热部署通常会报outofmemory: permgen space not enough之类的，这是由于之前加载进来的class还没有清除而方法区内存又不够的原因
>
> ```java
> public synchronized void reload() {
>      // Validate our current component state
>       if (!started)
>          throw new IllegalStateException
>                 (sm.getString("containerBase.notStarted", logName()));
> 
>         // Make sure reloading is enabled
> 
> 
> 
>         //      if (!reloadable)
> 
> 
> 
>         //          throw new IllegalStateException
> 
> 
> 
>         //              (sm.getString("standardContext.notReloadable"));
> 
> 
> 
>         if(log.isInfoEnabled())
> 
> 
> 
>             log.info(sm.getString("standardContext.reloadingStarted",
> 
> 
> 
>                     getName()));
> 
>         // Stop accepting requests temporarily
> 
> 
> 
>         setPaused(true);
>         try {
>             stop();
>         } catch (LifecycleException e) {
>             log.error(sm.getString("standardContext.stoppingContext",
>                     getName()), e);
>         }
> 
>         try {
>            start();
>         } catch (LifecycleException e) {
>             log.error(sm.getString("standardContext.startingContext",
>                     getName()), e);
>         }
>         setPaused(false);
> 
>     }
> ```
>

#### 1.2.1 createClassLoader

依据传入的name和父类加载器分别创建common、catalina、shared三个classloader并加入双亲委派机制中

```csharp
private ClassLoader createClassLoader(String name, ClassLoader parent) throws Exception {
        String value = CatalinaProperties.getProperty(name + ".loader");见1.2.1.1
        if (value != null && !value.equals("")) {
                //获取指定类加载器的加载jar包路径  common.loader路径为${catalina.base}/lib,${catalina.base}/lib/*.jar,${catalina.home}/lib,${catalina.home}/lib/*.jar
            value = this.replace(value);
            List<Repository> repositories = new ArrayList();
            StringTokenizer tokenizer = new StringTokenizer(value, ",");

            while(true) {
                String repository;
                do {
                    if (!tokenizer.hasMoreElements()) {
                        // 读取路径下的JAR包，返回一个注册了类信息的StandardClassLoader（extends URLClassLoader），需要用时便会查询相应的URL读取类信息到方法区中
                        ClassLoader classLoader = ClassLoaderFactory.createClassLoader(repositories, parent);
                        // Managed Bean，JMX(Java Management Extensions)的一个管理构件,这里管理的是classLoader
                        MBeanServer mBeanServer = null;
                        if (MBeanServerFactory.findMBeanServer((String)null).size() > 0) {
                            mBeanServer = (MBeanServer)MBeanServerFactory.findMBeanServer((String)null).get(0);
                        } else {
                            mBeanServer = ManagementFactory.getPlatformMBeanServer();
                        }

                        ObjectName objectName = new ObjectName("Catalina:type=ServerClassLoader,name=" + name);
                        mBeanServer.registerMBean(classLoader, objectName);
                        return classLoader;
                    }

                    repository = tokenizer.nextToken().trim();
                } while(repository.length() == 0);

                try {
                    new URL(repository);
                    repositories.add(new Repository(repository, RepositoryType.URL));
                } catch (MalformedURLException var9) {
                    if (repository.endsWith("*.jar")) {
                        repository = repository.substring(0, repository.length() - "*.jar".length());
                        repositories.add(new Repository(repository, RepositoryType.GLOB));
                    } else if (repository.endsWith(".jar")) {
                        repositories.add(new Repository(repository, RepositoryType.JAR));
                    } else {
                        repositories.add(new Repository(repository, RepositoryType.DIR));
                    }
                }
            }
        } else {
            return parent;
        }
    }
```

#### 1.2.1.1 CatalinaProperties

会从`CatalinaProperties`中获取`common.loader`的配置路径，而初始化`CatalinaProperties`程中会去**加载tomcat路径/conf/catalina.properties**配置,**也就是说CatalinaProperties读取的路径先读`catalina.properties`服务器配置文件，如果没有，读`bootstrap.jar`包中的`catalina.properties`文件**，最终存放在System.Properties中



**catalina.properties文件中存储了jar包等和相关类的URL，初始化classloader等使用**

```dart
public class CatalinaProperties {
	// 日志设置
    private static final Log log = LogFactory.getLog(CatalinaProperties.class);

    private static Properties properties = null;
    // 加载类时初始化的方块，加载Catalina.Properties，其中存放着classloader需要读取的jar包（即需要加载的类信息）
    static {
        loadProperties();
    }
    public static String getProperty(String name) {
        return properties.getProperty(name);
    }
    private static void loadProperties() {
        InputStream is = null;
        String fileName = "catalina.properties";
        //默认这里返回为null
        String configUrl = System.getProperty("catalina.config");
        if (configUrl != null) {
            if (configUrl.indexOf('/') == -1) {
                fileName = configUrl;
            } else {
                is = (new URL(configUrl)).openStream();
            }
        }
        if (is == null) { 
            File home = new File(Bootstrap.getCatalinaBase());
            File conf = new File(home, "conf");
            // /conf/catalina.properties文件读取
            File propsFile = new File(conf, fileName); 
            is = new FileInputStream(propsFile);
        }
        if (is == null) {
            // 还没有就从bootstrap.jar包中读取
            is = CatalinaProperties.class.getResourceAsStream
                ("/org/apache/catalina/startup/catalina.properties");/
  
        if (is != null) {
            properties = new Properties();
            //加载properties
            properties.load(is);
          
        }
        if ((is == null)) {
            properties = new Properties();
        }
        Enumeration<?> enumeration = properties.propertyNames();
        while (enumeration.hasMoreElements()) {
            String name = (String) enumeration.nextElement();
            String value = properties.getProperty(name);
            if (value != null) {
                //存放在System.Properties中
                System.setProperty(name, value);
            }
        }
    }
}
```

#### 1.3 load

反射调用catalina类的load方法

```java
private void load(String[] arguments) throws Exception {
    // 反射调用的方法
    String methodName = "load";
    Object[] param;
    Class[] paramTypes;
    if (arguments != null && arguments.length != 0) {
        paramTypes = new Class[]{arguments.getClass()};
        param = new Object[]{arguments};
    } else {
        paramTypes = null;
        param = null;
    }
	//利用反射调用catalina的load方法 此处的catalinaDaemon为catalina类的实例
    //为什么不强制类型转换直接调用呢，因为我们之前创建该类的时候用的ClassLoader为catalinaLoader而若是强制类型转换后获取classloader会转换为bootstrap的classLoader，而其没有catalina类的jar包，会出现异常
    Method method = this.catalinaDaemon.getClass().getMethod(methodName, paramTypes);
    if (log.isDebugEnabled()) {
        log.debug("Calling startup class " + method);
    }

    method.invoke(this.catalinaDaemon, param);
}
```

#### 1.4 start

```java
public void start() throws Exception {
    if (this.catalinaDaemon == null) {
        this.init();
    }

    // 调用Catalina的start方法
    Method method = this.catalinaDaemon.getClass().getMethod("start", (Class[])null);
    method.invoke(this.catalinaDaemon, (Object[])null);
}
```

## Catalina

源码解析：

https://blog.csdn.net/jingle1882010/article/details/80557808

![image-20200902112955281](F:\Typora数据储存\高级语言\JAVA\JavaWeb.assets\image-20200902112955281.png)

#### 2.1 Catalina.load

Catalina类的主要方法：负责解析Tomcat的配置文件，以此来创建服务器Server组件，并根据bootstrap解读的命令控制管理其他组件和流程

在catalina加载过程中，会解析加载`conf/server.xml`文件,创建`server`、`service`等一系列组件，再依据。

```java
public void load() {
    long t1 = System.nanoTime();
    this.initDirs();
    this.initNaming();
    // 创建Digester对象，设置Rule规则（必须根据自己的XML格式来添加所有的Rule），调用Digester的parse操作来解析XML文件。
    Digester digester = this.createStartDigester();
    InputSource inputSource = null;
    InputStream inputStream = null;
    File file = null;

    try {
        // 会调用configure属性->"conf/server.xml"，
        file = this.configFile();
        // 将文件转化为输入流
        inputStream = new FileInputStream(file);
        // 将server.xml的路径转换成字符串形式
        inputSource = new InputSource(file.toURI().toURL().toString());
    } catch (Exception var29) {
        if (log.isDebugEnabled()) {
            log.debug(sm.getString("catalina.configFail", new Object[]{file}), var29);
        }
    }
    
	// 若是没有conf/server.xml时读取的路径
    if (inputStream == null) {
       
    }

    if (inputStream == null) {
       
    }
    if (inputStream != null && inputSource != null) {
                label217: {
                    try {
                        // 字符流转换为字节流
                        inputSource.setByteStream((InputStream)inputStream);
                        // 将该类放入栈顶
                        digester.push(this);
                        // 解析XML激活Rule规则，创建相应的Server/Service等组件
                        digester.parse(inputSource);
                        break label217;
                    } catch (SAXParseException var24) {
                        log.warn("Catalina.start using " + this.getConfigFile() + ": " + var24.getMessage());
                    } catch (Exception var25) {
                        log.warn("Catalina.start using " + this.getConfigFile() + ": ", var25);
                        return;
                    } finally {
                        try {
                            ((InputStream)inputStream).close();
                        } catch (IOException var22) {
                        }

                    }

                    return;
                }
    	// 将Catalina绑定到StandardSever上
        this.getServer().setCatalina(this);
        // 初始化话输出报告和错误报告
        this.initStreams();

        try {
            //初始化StandardSever，并以链的形式初始化后续组件 见3.1
            this.getServer().init();
        } catch (LifecycleException var23) {
            if (Boolean.getBoolean("org.apache.catalina.startup.EXIT_ON_INIT_FAILURE")) {
                throw new Error(var23);
            }

            log.error("Catalina.start", var23);
        }

        long t2 = System.nanoTime();
        if (log.isInfoEnabled()) {
            log.info("Initialization processed in " + (t2 - t1) / 1000000L + " ms");
        }

    } else {
        if (file == null) {
            log.warn(sm.getString("catalina.configFail", new Object[]{this.getConfigFile() + "] or [server-embed.xml]"}));
        } else {
            log.warn(sm.getString("catalina.configFail", new Object[]{file.getAbsolutePath()}));
            if (file.exists() && !file.canRead()) {
                log.warn("Permissions incorrect, read permission is not allowed on the file.");
            }
        }

    }
}
```

> https://www.cnblogs.com/cenyu/p/11079144.html
>
> # Tomcat组件梳理—Digester的使用
>
> > 再吐槽一下，本来以为可以不用再开一个篇章来梳理Digester了，但是发现在研究Service的创建时，还是对Digester的很多接口或者机制不熟悉，简直搞不懂。想想还是算了，再回头一下，把这个也给梳理了。所以此文主要做两件事情，
> >
> > 1.梳理Digester的设计思想和常用接口。
> >
> > 2.梳理Digester对server.xml文件的解析。这么看也算是理论和实际相结合吧。
>
> ## 1.XML文件解析的两种方案。
>
> Java解析XML文件有两个主要的思想，分别是：
>
> ### 1.1.预加载DOM树
>
> 该方法的思路是：将整个XML文件读取到内存中，在内容中构造一个DOM树，也叫对象模型集合，然后java代码只需要操作这个树就可以。
>
> 该方法的主要实现为DOM解析，在此基础上有两个扩展：JDOM解析，DOM4J解析。这两种方法的思路都是一样的，只不过一个是官方出的，一个是社区出的，好不好用的问题。Java很奇怪，都是社区出的更好用，即DOM4J。
>
> 该思想有优点和缺点：
>
> - 优点：使用DOM时，XML文档的结构在内存中很清晰，元素与元素之间的关系保留了下来。即能记录文档的所有信息。
> - 缺点：如果XML文档非常大，把整个XML文档装在进内存容易造成内容溢出，无法加载了。
>
> ### 1.2.事件机制的SAX
>
> 该方法的思路是：一行一行的读取XML文件，没遇到一个节点，就看看有没有监听该事件的监听器，如果有就触发。当XML文件读取结束后，内存里不会保存任何数据，整个XML的解析就结束了。所以，这里面最重要的是对感兴趣的节点进行监听和处理。
>
> 该思想仍然有优点和缺点：
>
> - 优点：使用SAX不会占用大量内存来保存XML文档数据，效率高。
> - 缺点：当解析一个元素时，上一个元素的信息已经丢弃，也就是说没有保存元素与元素之间的结构关系。
>
> ## 2.Digester的设计思想
>
> ### 2.1.主要解决的问题
>
> Digester是对SAX的封装和抽象，解决在SAX中特别不好用的地方，那么Digester主要解决了哪些问题呢？
>
> - 1.XML的内容是变化的，那么不同的内容如何采用不同的方法去处理呢？用多个if进行判断？显然不是。
>
>   > Digester解决该问题的方法是，抽象出两个东西，一个是XML里面的内容，即XML中的节点，该内容可以使用正则进行书写。另一个是方法，找到该节点时需要调用的处理方法。处理方法统一继承Rule接口。
>   >
>   > 这样当在读取XML文件时，发现新的节点，就可以去找对应的正则，如果匹配，就可以调用对应的处理方法了。
>
> - 2.解析一个XML上的节点时，该如何解析？
>
>   > Digester解决该方法时，是将XML节点解析的生命周期进行抽象，不同生成周期做不同的事情，不同的处理方法只需要重写生命周期对应的方法就行。
>
> - 3.如何确保XML上不同的节点有依赖关系呢？
>
>   > Digester在内部自己维护了一个栈的结构，每遇到一个新的节点时，把这个对象把它压入栈(push)，节点解析结束时，可以再从栈中弹出(pop)，栈里面最顶部的对象永远都是现在正在解析的对象，第二个是它的父节点，提供API调用父节点的方法把引用当前对象，这样就解决了依赖的问题。
>
> ### 2.2.源码主要结构
>
> ![img](https://img2018.cnblogs.com/blog/790334/201906/790334-20190624200033305-1153729928.png)
>
> 先对架构进行解释一下，
>
> - 最上面的`DefaultHandler`来自SAX，`Digester`集成该类，足以说明`Digester`底层用的是SAX了。
> - `Client`表示调用Digester的Java代码。
> - `Digester`是真个Digester组件的核心类和入口类，Client需要实例化该类，设置改类，并调用里面的参数。
> - `Rules`是一个保存XML的节点和规则的映射关系的接口，默认实现类是`RulesBase`
> - `RuesBase`是`Rules`的默认实现类。当有一个XML节点开始解析时，会在这里面找是否有对应的节点，并根据节点查找对应的处理规则。
> - `Rule`对节点的处理方法的接口，内置的或者自定义的规则都是集成该接口。
> - `**Rule`是接口`Rule`的很多实现，根据具体的实现不同而不同。
>
> **使用Digester解析XML文档的流程：**
>
> - 1.首先，Client需要创建一个Digester对象。
> - 2.然后，Client必须根据自己的XML格式来添加所有的Rule。
> - 3.添加完Rule后，Client调用Digester的parse操作来解析XML文件。
> - 4.Digester实现了SAX的接口，解析时遇到具体的XML对象时会调用startElement等接口函数。
> - 5.在这些SAX接口函数中，会扫描规则链(RulesBase)，找到匹配规则，规则匹配一般都是根据具体的元素名称来进行匹配。
> - 6.找到对应的rule后，依次执行rule，这里的startElement对应的是begin操作，endElement对应的是end操作，具体要做的事情都在每个rule的begin，body，end函数中。
> - 7.文档结束后，会执行所有rule的finish函数。
>
> **Digester的巧妙设计：**
>
> 1.XML格式变化：Digester把这个变化抛给具体用户去解决，用户在使用Digester之前必须自己根据自己的XML文件格式来构造规则链，Digester只提供构造规则链的手段，体现了有所为有所不为的设计思想。
>
> 2.处理方式变化的问题，Digester将处理方式抽象为规则Rule，一个规则对应一个处理方式，Digester提供了通用的缺省的Rule，如果觉得提供的规则不满足自己的要求，可以自己另外定制。
>
> **Digester在代码中的使用流程**
>
> ```java
>     //3.用digester解析server.xml文件，把配置文件中的配置解析成java对象
>     //3.1.准备好用来解析server.xml文件需要用的digester。
>     Digester digester = createStartDigester();
>     //3.2.server.xml文件作为一个输入流传入
>     File file = configFile();
>     InputStream inputStream = new FileInputStream(file);
>     //3.3.使用inputStream构造一个sax的inputSource
>     InputSource inputSource = new InputSource(file.toURI().toURL().toString());
>     inputSource.setByteStream(inputStream);
>     //3.4.把当前类压入到digester的栈顶，用来作为digester解析出来的对象的一种引用
>     digester.push(this);
>     //3.5.调用digester的parse()方法进行解析。
>     digester.parse(inputSource);
> ```
>
> - 3.1.准备好用来解析server.xml文件需要用的digester。
> - 3.2.读取server.xml文件作为一个输入流。
> - 3.3.使用inputStream构造一个sax的inputSource，因为digester底层用的是sax去解析的。
> - 3.4.把当前类压入到digester的栈顶，用来作为digester解析出来的对象的一种引用，digester自带一个栈的结构。
> - 3.5.调用digester的parse()方法进行解析。前面几步都是在准备环境，这里才是正真的去解析了。
>
> ### 2.3.内部维护的栈结构
>
> Digester用栈来维护每个正在解析的对象，注意，这个栈里面存放的是正在解析的，即开始解析时就放入栈，解析玩就弹出栈。
>
> 这个栈是Digester自己写的一个，用`ArrayList`来实现的，但是方法都一样，在Digester类中进行维护，代码为：
>
> ```java
> //org.apache.tomcat.util.digester.Digester
> /**
>  * The object stack being constructed.
>  */
> protected ArrayStack<Object> stack = new ArrayStack<>();
> ```
>
> 该栈的主要方法有：
>
> - `clear()`：清楚栈内的所有元素。
> - `peek()`：返回栈顶对象的引用，而不进行删除。
> - `pop()`：弹出栈顶的元素。
> - `push()`：压入一个元素到栈顶。
>
> ## 3.Digester的常用接口
>
> ### 3.1.解析XML节点时的生命周期
>
> 在解析XML节点时，有一整个生命周期在里面，解析一个节点时，会有一下生命周期，1.遇到匹配的节点的开始部分时开始解析，2.解析文本内容，3.遇到匹配的节点的结束部分时结束解析，4.最后处理资源。
>
> - `begin()`:当读取到匹配节点的开始部分时调用，会将该节点的所有属性作为从参数传入。
> - `body()`:当读取到匹配节点的内容时调用，注意指的不是子节点，而是嵌入内容为普通文本。
> - `end()`:当读取到匹配节点的结束部分时调用，如果存在子节点，只有当子节点处理完毕后该方法才会被调用。
> - `finish()`:当整个parse()方法完成时调用，多用于清楚临时数据和缓存数据。
>
> ### 3.2.内置的规则接口
>
> Digester内置了一些规则，可以调用接口直接使用，还有一些其他的API可调用：
>
> - `ObjectCreateRule`: 当begin()方法调用时，该规则会将指定的Java类实例化，并将其放入对象栈。具体的Java类可由该规则在构造方法出啊如，也可以通过当前处理XML节点的某个属性指定，属性名称通过构造方法传入。当end()方法调用时，该规则创建的对象将从栈中取出。
> - `FactoryCreateRule`:ObJectCreateRule规则的一个变体，用于处理Java类无默认构造方法的情况，或者需要在Digester处理该对象之前执行某些操作的情况。
> - `SetPropertiesRule`:当begin()方法调用时，Digester使用标准的Java Bean属性操作方法(setter)将当前XML节点的属性值设置到对象栈顶部的对象中。
> - `SetPropertyRule`:当begin()方法调用时，Digester会设置栈顶部对象指定属性的值，其中属性名和属性值分别通过XML节点的两个属性指定。
> - `SetNextRule`:当end()方法调用时，Digester会找到位于栈顶部对象的下一个对象，并调用其指定的方法，同时将栈顶部对象作为参数传入，用于设置父对象的子对象，以便在栈对象之间建立父子关系，从而形成对象树，方便引用。
> - `SetTopRule`:与setNextRule对象，当end()方法调用时，Digester会找到位于站顶部的对象，调用其指定方法，同时将位于顶部下一个对象作为参数传入，用于设置当前对象的父对象。
> - `CallMethRule`:该规则用于在end()方法调用时执行栈顶对象的某个方法，参数值由CallParamRule获取。
> - `CallParamRule`:该规则与CallMethodRule配合使用，作为其子节点的处理规则创建方法参数，参数值可取自某个特殊属性，也可以取自节点的内容。
> - `NodeCreateRule`:用于将XML文档树的一部分转换为DOM节点，并放入栈。
>
> ### 3.3.自定义规则
>
> 如果需要自定义规则，只需要创建一个类继承Rule接口，并重写里面的生命周期方法，可以选择只重写部分。
>
> 例如:
>
> ```java
> public class ConnectorCreateRule extends Rule {
>  
>   @Override
>     public void begin(String namespace, String name, Attributes attributes){
>       //do something
>     }
>   
>   @Override
>     public void begin(String namespace, String name, Attributes attributes){
>       //do something
>     }
> }
> ```
>
> 最后在使用自定义的规则时，方法如下：
>
> ```java
> digester.addRule("Server/Service/Connector",new ConnectorCreateRule());
> ```
>
> 此时即可。
>
> ### 3.4.addRuleSet方法
>
> 很多时候针对同一个节点，我们会执行很多规则，比如：创建一个对象，设置属性，调用方法。至少三个。每次都写三个是比较麻烦的，那么有没有比较方便的方法呢？
>
> 有。使用`addRuleSet()`方法，传入需要匹配的节点名称，然后对该节点使用多个规则。此方法方便重用。
>
> 案例：
>
> 首先自定义一个规则：
>
> ```java
> public class MyRuleSet
>   extends RuleSetBase {
> 
>   public MyRuleSet()
>   {
>     this("");
>   }
> 
>   public MyRuleSet(String prefix)
>   {
>     super();
>     this.prefix = prefix;
>     this.namespaceURI = "http://www.mycompany.com/MyNamespace";
>   }
> 
>   protected String prefix = null;
> 
>   public void addRuleInstances(Digester digester)
>   {
>     digester.addObjectCreate( prefix + "foo/bar",
>       "com.mycompany.MyFoo" );
>     digester.addSetProperties( prefix + "foo/bar" );
>   }
> 
> }
> ```
>
> 然后可以直接调用：
>
> ```java
>   Digester digester = new Digester();
>   ... configure Digester properties ...
>   digester.addRuleSet( new MyRuleSet( "baz/" ) );
> ```
>
> ## 4.Digester解析Server.xml的案例分析
>
> 上面说了这么多，终于可以开始梳理Tomcat中Digester对server.xml文件的解析了。
>
> ### 4.1.总的步骤
>
> 使用Digester的方法在上面有说过，再展示一下：
>
> ```java
>     //3.用digester解析server.xml文件，把配置文件中的配置解析成java对象
>     //3.1.准备好用来解析server.xml文件需要用的digester。
>     Digester digester = createStartDigester();
>     //3.2.server.xml文件作为一个输入流传入
>     File file = configFile();
>     InputStream inputStream = new FileInputStream(file);
>     //3.3.使用inputStream构造一个sax的inputSource
>     InputSource inputSource = new InputSource(file.toURI().toURL().toString());
>     inputSource.setByteStream(inputStream);
>     //3.4.把当前类压入到digester的栈顶，用来作为digester解析出来的对象的一种引用
>     digester.push(this);
>     //3.5.调用digester的parse()方法进行解析。
>     digester.parse(inputSource);
> ```
>
> 解释一下：
>
> - 3.1.准备好用来解析server.xml文件需要用的digester。
> - 3.2.读取server.xml文件作为一个输入流。
> - 3.3.使用inputStream构造一个sax的inputSource，因为digester底层用的是sax去解析的。
> - 3.4.把当前类压入到digester的栈顶，用来作为digester解析出来的对象的一种引用，digester自带一个栈的结构。
> - 3.5.调用digester的parse()方法进行解析。前面几步都是在准备环境，这里才是正真的去解析了。
>
> 在第1个步骤中，需要先构造一个套规则，存放在digester实例中。构造代码比较多，我拆开说
>
> ### 4.2.节点绑定规则
>
> 以下均为`createStartDigester()`方法的内容
>
> #### 4.2.1.设置giester的参数：
>
> ```java
> // 创建一个digester实例
> Digester digester = new Digester();
> //是否对xml文档进行类似XSD等类型的校验，默认为fasle。
> digester.setValidating(false);
> //是否进行节点设置规则校验，如果xml中相应节点没有设置解析规则会在控制台显示提示信息。
> digester.setRulesValidation(true);
> 
> //将XML节点中的className作为假属性，不必调用默认的setter方法
> //(一般的节点属性在解析时会以属性作为入参调用该节点相应对象的setter方法，而className属性的作用
> // 提示解析器使用该属性的值作来实例化对象)
> Map<Class<?>, List<String>> fakeAttributes = new HashMap<>();
> List<String> objectAttrs = new ArrayList<>();
> objectAttrs.add("className");
> fakeAttributes.put(Object.class, objectAttrs);
> // Ignore attribute added by Eclipse for its internal tracking
> List<String> contextAttrs = new ArrayList<>();
> contextAttrs.add("source");
> fakeAttributes.put(StandardContext.class, contextAttrs);
> digester.setFakeAttributes(fakeAttributes);
> 
> //使用当前线程的上下文类加载器，主要加载FactoryCreateRule和ObjectCreateRule
> digester.setUseContextClassLoader(true);
> ```
>
> #### 4.2.2.Server的解析
>
> **1.创建Server实例**
>
> ```java
> digester.addObjectCreate("Server","org.apache.catalina.core.StandardServer","className");
> digester.addSetProperties("Server");
> digester.addSetNext("Server","setServer","org.apache.catalina.Server");
> ```
>
> 创建对象，设置属性，调用方法添加到Catalina类中。
>
> **2.为Server添加全局J2EE企业命名上下文**
>
> ```java
> digester.addObjectCreate("Server/GlobalNamingResources",
>                          "org.apache.catalina.deploy.NamingResourcesImpl");
> digester.addSetProperties("Server/GlobalNamingResources");
> digester.addSetNext("Server/GlobalNamingResources", 
>                     "setGlobalNamingResources",
>                     "org.apache.catalina.deploy.NamingResourcesImpl");
> ```
>
> 创建对象，设置属性，添加到Server中。
>
> **3.为Server添加生命周期监听器**
>
> ```java
> digester.addObjectCreate("Server/Listener",
>                          null, // MUST be specified in the element
>                          "className");
> digester.addSetProperties("Server/Listener");
> digester.addSetNext("Server/Listener",
>                     "addLifecycleListener",
>                     "org.apache.catalina.LifecycleListener");
> ```
>
> 给Server添加监听器，Server一共添加了5个监听器，xml文件中显示如下：
>
> ```xml
>   <Listener className="org.apache.catalina.startup.VersionLoggerListener" />
>   <!-- Security listener. Documentation at /docs/config/listeners.html
>   <Listener className="org.apache.catalina.security.SecurityListener" />
>   -->
>   <!--APR library loader. Documentation at /docs/apr.html -->
>   <Listener className="org.apache.catalina.core.AprLifecycleListener" SSLEngine="on" />
>   <!-- Prevent memory leaks due to use of particular java/javax APIs-->
>   <Listener className="org.apache.catalina.core.JreMemoryLeakPreventionListener" />
>   <Listener className="org.apache.catalina.mbeans.GlobalResourcesLifecycleListener" />
>   <Listener className="org.apache.catalina.core.ThreadLocalLeakPreventionListener" />
> ```
>
> 这5个监听器的作用分别是：
>
> - `VersionLoggerListener`:在Server初始化之前打印操作系统，JVM以及服务器的版本信息。
> - `AprLifecycleListener`:在Server初始化之前加载APR库，并于Server停止之后销毁。
> - `JreMemoryLeakPreventionListener`:在Server初始化之前调用，以解决单例对象创建导致的JVM内存泄漏问题以及锁文件问题。
> - `GlobalResourcesLifecycleListener`:在Server启动时，将JNDI资源注册为MBean进行管理。
> - `ThreadLocalLeakPreventionListener`:用于在Context停止时重建Exceutor池中的线程，避免导致内存泄漏。
>
> **4.给Server添加Service实例**
>
> ```java
> digester.addObjectCreate("Server/Service",
>                          "org.apache.catalina.core.StandardService",
>                          "className");
> digester.addSetProperties("Server/Service");
> digester.addSetNext("Server/Service",
>                     "addService",
>                     "org.apache.catalina.Service");
> ```
>
> 创建Service对象，设置参数，添加到Server中。
>
> **5.给Service添加生命周期监听器**
>
> ```java
> digester.addObjectCreate("Server/Service/Listener",
>                          null, // MUST be specified in the element
>                          "className");
> digester.addSetProperties("Server/Service/Listener");
> digester.addSetNext("Server/Service/Listener",
>                     "addLifecycleListener",
>                     "org.apache.catalina.LifecycleListener");
> ```
>
> 具体的监听器由className属性指定，默认情况下，Catalina未指定Service监听器。
>
> **6.为Service添加Executor**
>
> ```java
> digester.addObjectCreate("Server/Service/Executor",
>                          "org.apache.catalina.core.StandardThreadExecutor",
>                          "className");
> digester.addSetProperties("Server/Service/Executor");
> digester.addSetNext("Server/Service/Executor",
>                     "addExecutor",
>                     "org.apache.catalina.Executor");
> ```
>
> 通过该配置我们可以知道，Catalina共享Excetor的级别为Service，Catalina默认情况下未配置Executor，即不共享。
>
> **7.为Service添加Connector**
>
> ```java
> digester.addRule("Server/Service/Connector",
>                  new ConnectorCreateRule());
> 
> digester.addRule("Server/Service/Connector",
>                  new SetAllPropertiesRule(new String[]{"executor", 		       "sslImplementationName"}));
> digester.addSetNext("Server/Service/Connector",
>                     "addConnector",
>                     "org.apache.catalina.connector.Connector");
> ```
>
> 设置相关属性时，将executor和sslImplementationName属性排除，因为在Connector创建时，会判断当前是否指定了executor属性，如果是，则从Service中查找该名称的executor并设置到Connector中。同样，Connector创建时，也会判断是否添加了sslIlplementationName属性，如果是，则将属性值设置到使用的协议中，为其指定一个SSL实现。
>
> **8.Connector添加虚拟主机SSL配置**
>
> ```java
> digester.addObjectCreate("Server/Service/Connector/SSLHostConfig",
>                          "org.apache.tomcat.util.net.SSLHostConfig");
> digester.addSetProperties("Server/Service/Connector/SSLHostConfig");
> digester.addSetNext("Server/Service/Connector/SSLHostConfig",
>                     "addSslHostConfig",
>                     "org.apache.tomcat.util.net.SSLHostConfig");
> 
> digester.addRule("Server/Service/Connector/SSLHostConfig/Certificate",
>                  new CertificateCreateRule());
> digester.addRule("Server/Service/Connector/SSLHostConfig/Certificate",
>                  new SetAllPropertiesRule(new String[]{"type"}));
> digester.addSetNext("Server/Service/Connector/SSLHostConfig/Certificate",
>                     "addCertificate",
>                     "org.apache.tomcat.util.net.SSLHostConfigCertificate");
> 
> digester.addObjectCreate("Server/Service/Connector/SSLHostConfig/OpenSSLConf",
>                          "org.apache.tomcat.util.net.openssl.OpenSSLConf");
> digester.addSetProperties("Server/Service/Connector/SSLHostConfig/OpenSSLConf");
> digester.addSetNext("Server/Service/Connector/SSLHostConfig/OpenSSLConf",
>                     "setOpenSslConf",
>                     "org.apache.tomcat.util.net.openssl.OpenSSLConf");
> 
> digester.addObjectCreate("Server/Service/Connector/SSLHostConfig/OpenSSLConf/OpenSSLConfCmd",
>                          "org.apache.tomcat.util.net.openssl.OpenSSLConfCmd");
> digester.addSetProperties("Server/Service/Connector/SSLHostConfig/OpenSSLConf/OpenSSLConfCmd");
> digester.addSetNext("Server/Service/Connector/SSLHostConfig/OpenSSLConf/OpenSSLConfCmd",
>                     "addCmd",
>                     "org.apache.tomcat.util.net.openssl.OpenSSLConfCmd");
> ```
>
> 8.5版本新加内容
>
> **9.为Connector添加生命周期监听器**
>
> ```java
> digester.addObjectCreate("Server/Service/Connector/Listener",
>                          null, // MUST be specified in the element
>                          "className");
> digester.addSetProperties("Server/Service/Connector/Listener");
> digester.addSetNext("Server/Service/Connector/Listener",
>                     "addLifecycleListener",
>                     "org.apache.catalina.LifecycleListener");
> ```
>
> 具体监听器类由className属性指定。默认情况下，Catalina未指定Connector监听器。
>
> **10.为Connector添加升级协议**
>
> ```java
> digester.addObjectCreate("Server/Service/Connector/UpgradeProtocol",
>                          null, // MUST be specified in the element
>                          "className");
> digester.addSetProperties("Server/Service/Connector/UpgradeProtocol");
> digester.addSetNext("Server/Service/Connector/UpgradeProtocol",
>                     "addUpgradeProtocol",
>                     "org.apache.coyote.UpgradeProtocol");
> ```
>
> 用于支持HTTP/2，8.5新增。
>
> **11.添加子元素解析规则**
>
> ```java
> digester.addRuleSet(new NamingRuleSet("Server/GlobalNamingResources/"));
> digester.addRuleSet(new EngineRuleSet("Server/Service/"));
> digester.addRuleSet(new HostRuleSet("Server/Service/Engine/"));
> digester.addRuleSet(new ContextRuleSet("Server/Service/Engine/Host/"));
> addClusterRuleSet(digester, "Server/Service/Engine/Host/Cluster/");
> digester.addRuleSet(new NamingRuleSet("Server/Service/Engine/Host/Context/"));
> 
> // When the 'engine' is found, set the parentClassLoader.
> digester.addRule("Server/Service/Engine",
>                  new SetParentClassLoaderRule(parentClassLoader));
> addClusterRuleSet(digester, "Server/Service/Engine/Cluster/");
> ```
>
> 此部分指定了Servlet容器相关的各级嵌套子节点的解析规则，而且没类嵌套子节点的解析规则封装为一个RuleSet，包括GlobalNamingResources，Engine，Host，Context以及Cluster解析。
>
> #### 4.2.3.Engine的解析
>
> Engine的解析是放在`digester.addRuleSet(new EngineRuleSet("Server/Service/"));`方法里面的。
>
> 具体如下
>
> **1.创建Engine实例**
>
> ```java
> digester.addObjectCreate(prefix + "Engine",
>                          "org.apache.catalina.core.StandardEngine",
>                          "className");
> digester.addSetProperties(prefix + "Engine");
> digester.addRule(prefix + "Engine",
>                  new LifecycleListenerRule
>                          ("org.apache.catalina.startup.EngineConfig",
>                           "engineConfigClass"));
> digester.addSetNext(prefix + "Engine",
>                     "setContainer",
>                     "org.apache.catalina.Engine");
> ```
>
> 创建实例，并通过setContainer添加Service中。同时，还为Engine添加了一个生命周期的监听器，代码里写死，不是通过server.xml配置的。用于打印Engine启动和停止日志。
>
> **2.为Engine添加集群配置**
>
> ```java
> digester.addObjectCreate(prefix + "Engine/Cluster",
>                          null, // MUST be specified in the element
>                          "className");
> digester.addSetProperties(prefix + "Engine/Cluster");
> digester.addSetNext(prefix + "Engine/Cluster",
>                     "setCluster",
>                     "org.apache.catalina.Cluster");
> ```
>
> 具体集群实现类由className属性指定。
>
> **3.为Engine添加生命周期监听器**
>
> ```java
> digester.addObjectCreate(prefix + "Engine/Listener",
>                          null, // MUST be specified in the element
>                          "className");
> digester.addSetProperties(prefix + "Engine/Listener");
> digester.addSetNext(prefix + "Engine/Listener",
>                     "addLifecycleListener",
>                     "org.apache.catalina.LifecycleListener");
> ```
>
> 由server.xml配置，默认情况下，未指定Engine监听器。
>
> **4.为Engine添加安全配置**
>
> ```java
> digester.addObjectCreate(prefix + "Engine/Valve",
>                          null, // MUST be specified in the element
>                          "className");
> digester.addSetProperties(prefix + "Engine/Valve");
> digester.addSetNext(prefix + "Engine/Valve",
>                     "addValve",
>                     "org.apache.catalina.Valve");
> ```
>
> 为Engine添加安全配置以及拦截器Valve，具体的拦截器由className属性指定。
>
> #### 4.2.4.Host的解析
>
> Host的解析位于`HostRuleSet`类，`digester.addRuleSet(new HostRuleSet("Server/Service/Engine/"));`
>
> **1.创建Host实例**
>
> ```java
> digester.addObjectCreate(prefix + "Host",
>                          "org.apache.catalina.core.StandardHost",
>                          "className");
> digester.addSetProperties(prefix + "Host");
> digester.addRule(prefix + "Host",
>                  new CopyParentClassLoaderRule());
> digester.addRule(prefix + "Host",
>                  new LifecycleListenerRule
>                          ("org.apache.catalina.startup.HostConfig",
>                           "hostConfigClass"));
> digester.addSetNext(prefix + "Host",
>                     "addChild",
>                     "org.apache.catalina.Container");
> 
> digester.addCallMethod(prefix + "Host/Alias",
>                        "addAlias", 0);
> ```
>
> 创建Host实例，通过addChild()方法添加到Engine上。同时还为Host添加了一个生命周期监听器HostConfig，同样，该监听器由Catalina默认添加，而不是server.xml配置。
>
> **2.为Host添加集群**
>
> ```java
> digester.addObjectCreate(prefix + "Host/Cluster",
>                          null, // MUST be specified in the element
>                          "className");
> digester.addSetProperties(prefix + "Host/Cluster");
> digester.addSetNext(prefix + "Host/Cluster",
>                     "setCluster",
>                     "org.apache.catalina.Cluster");
> ```
>
> 配置集群，集群的配置即可以在Engine级别，也可以在Host级别。
>
> **3.为Host添加生命周期管理**
>
> ```java
> digester.addObjectCreate(prefix + "Host/Listener",
>                          null, // MUST be specified in the element
>                          "className");
> digester.addSetProperties(prefix + "Host/Listener");
> digester.addSetNext(prefix + "Host/Listener",
>                     "addLifecycleListener",
>                     "org.apache.catalina.LifecycleListener");
> ```
>
> 此部分监听器由server.xml配置。默认情况下，Catalina未指定Host监听器。
>
> **4.为Host添加安全配置**
>
> ```java
> digester.addObjectCreate(prefix + "Host/Valve",
>                          null, // MUST be specified in the element
>                          "className");
> digester.addSetProperties(prefix + "Host/Valve");
> digester.addSetNext(prefix + "Host/Valve",
>                     "addValve",
>                     "org.apache.catalina.Valve");
> ```
>
> 为Host添加安全配置以及拦截器Valve，具体拦截器类由className属性指定。Catalina为Host默认添加的拦截器为AccessLogValve，即用于记录访问日志。
>
> #### 4.2.5.Context的解析
>
> Context的解析，位于`ContextRuleSet`类，`digester.addRuleSet(new ContextRuleSet("Server/Service/Engine/Host/"));`。
>
> 多数情况下，我们并不需要在server.xml中配置Context，而是由HostConfig自动扫描部署目录，以context.xml文件为基础进行解析创建。
>
> **1.创建Context实例**
>
> ```java
> if (create) {
>   digester.addObjectCreate(prefix + "Context",
>                            "org.apache.catalina.core.StandardContext", "className");
>   digester.addSetProperties(prefix + "Context");
> } else {
>   digester.addRule(prefix + "Context", new SetContextPropertiesRule());
> }
> 
> if (create) {
>   digester.addRule(prefix + "Context",
>                    new LifecycleListenerRule
>                    ("org.apache.catalina.startup.ContextConfig",
>                     "configClass"));
>   digester.addSetNext(prefix + "Context",
>                       "addChild",
>                       "org.apache.catalina.Container");
> }
> ```
>
> Context的解析会根据create属性的不同而有所区别，这主要是由于Context来源于多处。通过server.xml配置Context时，create是true，因此需要创建Context实例；而通过HostConfig自动创建Context时，create为false，此时仅需要解析节点即可。
>
> **2.为Context添加生命周期监听器**
>
> ```java
> digester.addObjectCreate(prefix + "Context/Listener",
>                          null, // MUST be specified in the element
>                          "className");
> digester.addSetProperties(prefix + "Context/Listener");
> digester.addSetNext(prefix + "Context/Listener",
>                     "addLifecycleListener",
>                     "org.apache.catalina.LifecycleListener");
> ```
>
> 具体监听器类由属性className指定。
>
> **3.为Context指定类加载器**
>
> ```java
> digester.addObjectCreate(prefix + "Context/Loader",
>                          "org.apache.catalina.loader.WebappLoader",
>                          "className");
> digester.addSetProperties(prefix + "Context/Loader");
> digester.addSetNext(prefix + "Context/Loader",
>                     "setLoader",
>                     "org.apache.catalina.Loader");
> ```
>
> 默认为org.apache.catalina.loader.WebappLoader,通过className属性可以指定自己的实现类。
>
> **4.为Context添加会话管理器**
>
> ```java
> digester.addObjectCreate(prefix + "Context/Manager",
>                          "org.apache.catalina.session.StandardManager",
>                          "className");
> digester.addSetProperties(prefix + "Context/Manager");
> digester.addSetNext(prefix + "Context/Manager",
>                     "setManager",
>                     "org.apache.catalina.Manager");
> 
> digester.addObjectCreate(prefix + "Context/Manager/Store",
>                          null, // MUST be specified in the element
>                          "className");
> digester.addSetProperties(prefix + "Context/Manager/Store");
> digester.addSetNext(prefix + "Context/Manager/Store",
>                     "setStore",
>                     "org.apache.catalina.Store");
> 
> digester.addObjectCreate(prefix + "Context/Manager/SessionIdGenerator",
>                          "org.apache.catalina.util.StandardSessionIdGenerator",
>                          "className");
> digester.addSetProperties(prefix + "Context/Manager/SessionIdGenerator");
> digester.addSetNext(prefix + "Context/Manager/SessionIdGenerator",
>                     "setSessionIdGenerator",
>                     "org.apache.catalina.SessionIdGenerator");
> ```
>
> 默认实现为StandardManager，同时为管理器指定会话存储方式和会话标识生成器。Context提供了多种会话管理方式。
>
> **5.为Context添加初始化参数**
>
> ```java
> digester.addObjectCreate(prefix + "Context/Parameter",
>                          "org.apache.tomcat.util.descriptor.web.ApplicationParameter");
> digester.addSetProperties(prefix + "Context/Parameter");
> digester.addSetNext(prefix + "Context/Parameter",
>                     "addApplicationParameter",
>                     "org.apache.tomcat.util.descriptor.web.ApplicationParameter");
> ```
>
> 通过该配置，为Context添加初始化参数。
>
> **6.为Context添加安全配置以及web资源配置**
>
> ```java
> digester.addRuleSet(new RealmRuleSet(prefix + "Context/"));
> 
> digester.addObjectCreate(prefix + "Context/Resources",
>                          "org.apache.catalina.webresources.StandardRoot",
>                          "className");
> digester.addSetProperties(prefix + "Context/Resources");
> digester.addSetNext(prefix + "Context/Resources",
>                     "setResources",
>                     "org.apache.catalina.WebResourceRoot");
> 
> digester.addObjectCreate(prefix + "Context/Resources/PreResources",
>                          null, // MUST be specified in the element
>                          "className");
> digester.addSetProperties(prefix + "Context/Resources/PreResources");
> digester.addSetNext(prefix + "Context/Resources/PreResources",
>                     "addPreResources",
>                     "org.apache.catalina.WebResourceSet");
> 
> digester.addObjectCreate(prefix + "Context/Resources/JarResources",
>                          null, // MUST be specified in the element
>                          "className");
> digester.addSetProperties(prefix + "Context/Resources/JarResources");
> digester.addSetNext(prefix + "Context/Resources/JarResources",
>                     "addJarResources",
>                     "org.apache.catalina.WebResourceSet");
> 
> digester.addObjectCreate(prefix + "Context/Resources/PostResources",
>                          null, // MUST be specified in the element
>                          "className");
> digester.addSetProperties(prefix + "Context/Resources/PostResources");
> digester.addSetNext(prefix + "Context/Resources/PostResources",
>                     "addPostResources",
>                     "org.apache.catalina.WebResourceSet");
> ```
>
> **7.为Context添加资源连接**
>
> ```java
> digester.addObjectCreate(prefix + "Context/ResourceLink",
>                          "org.apache.tomcat.util.descriptor.web.ContextResourceLink");
> digester.addSetProperties(prefix + "Context/ResourceLink");
> digester.addRule(prefix + "Context/ResourceLink",
>                  new SetNextNamingRule("addResourceLink",
>                                        "org.apache.tomcat.util.descriptor.web.ContextResourceLink"));
> ```
>
> 为Context添加资源链接ContextResourceLink，用于J2EE命名服务。
>
> **8.为Context添加Valve**
>
> ```java
> digester.addObjectCreate(prefix + "Context/Valve",
>                          null, // MUST be specified in the element
>                          "className");
> digester.addSetProperties(prefix + "Context/Valve");
> digester.addSetNext(prefix + "Context/Valve",
>                     "addValve",
>                     "org.apache.catalina.Valve");
> ```
>
> 为Context添加拦截器Valve。
>
> **9.为Context添加守护资源配置**
>
> ```java
> digester.addCallMethod(prefix + "Context/WatchedResource",
>                        "addWatchedResource", 0);
> 
> digester.addCallMethod(prefix + "Context/WrapperLifecycle",
>                        "addWrapperLifecycle", 0);
> 
> digester.addCallMethod(prefix + "Context/WrapperListener",
>                        "addWrapperListener", 0);
> 
> digester.addObjectCreate(prefix + "Context/JarScanner",
>                          "org.apache.tomcat.util.scan.StandardJarScanner",
>                          "className");
> digester.addSetProperties(prefix + "Context/JarScanner");
> digester.addSetNext(prefix + "Context/JarScanner",
>                     "setJarScanner",
>                     "org.apache.tomcat.JarScanner");
> 
> digester.addObjectCreate(prefix + "Context/JarScanner/JarScanFilter",
>                          "org.apache.tomcat.util.scan.StandardJarScanFilter",
>                          "className");
> digester.addSetProperties(prefix + "Context/JarScanner/JarScanFilter");
> digester.addSetNext(prefix + "Context/JarScanner/JarScanFilter",
>                     "setJarScanFilter",
>                     "org.apache.tomcat.JarScanFilter");
> ```
>
> WatchedResource标签用于为Context添加监视资源，当这些资源发生变更时，Web应用将会被重新加载，默认为WEB-INF/web.xml
>
> WrapperLifecycle标签用于为Context添加一个生命周期监听类，此类的实例并非添加到Context上，而是添加到Context包含的Wrapper上。
>
> WrapperListener标签用于为Context添加一个容器监听类，此类同样添加到Wrapper上。
>
> JarScanner标签用于为Context添加一个Jar扫描器。JarScanner扫描Web应用和类加载层级的Jar包。
>
> **10.为Context添加Cookie处理器**
>
> ```java
> digester.addObjectCreate(prefix + "Context/CookieProcessor",
>                          "org.apache.tomcat.util.http.Rfc6265CookieProcessor",
>                          "className");
> digester.addSetProperties(prefix + "Context/CookieProcessor");
> digester.addSetNext(prefix + "Context/CookieProcessor",
>                     "setCookieProcessor",
>                     "org.apache.tomcat.util.http.CookieProcessor");
> ```
>
> ## 5.总结
>
> 本以为Digester就是对server.xml解析，能有多大的问题，不想梳理的，迫于不懂，梳理之后发现它这个设计模式还是挺棒的，用户只需要配置节点名，规则就行。通过栈获取元素。其他的都写好了，只需要用就行。很棒的设计模式，应该用的是策略模式的思想，非常棒。也是有不小收获的。

#### 2.2 Catalina.start

```java
public void start() {
    if (this.getServer() == null) {
        this.load();
    }

    if (this.getServer() == null) {
        log.fatal("Cannot start server. Server instance is not configured.");
    } else {
        // 系统时间
        long t1 = System.nanoTime();

        try {
            // 开启StandardServer(implements Lifecycle)
            this.getServer().start();
        } catch (LifecycleException var7) {
            log.fatal(sm.getString("catalina.serverStartFail"), var7);

            try {
                this.getServer().destroy();
            } catch (LifecycleException var6) {
                log.debug("destroy() failed for failed Server ", var6);
            }

            return;
        }

        long t2 = System.nanoTime();
        if (log.isInfoEnabled()) {
            log.info("Server startup in " + (t2 - t1) / 1000000L + " ms");
        }

        if (this.useShutdownHook) {
            if (this.shutdownHook == null) {
                this.shutdownHook = new Catalina.CatalinaShutdownHook();
            }

            Runtime.getRuntime().addShutdownHook(this.shutdownHook);
            LogManager logManager = LogManager.getLogManager();
            if (logManager instanceof ClassLoaderLogManager) {
                ((ClassLoaderLogManager)logManager).setUseShutdownHook(false);
            }
        }

        if (this.await) {
            this.await();
            this.stop();
        }

    }
}
```

#### 3.1StandardServer



```java
public final synchronized void init() throws LifecycleException {
    if (!this.state.equals(LifecycleState.NEW)) {
        this.invalidTransition("before_init");
    }

    this.setStateInternal(LifecycleState.INITIALIZING, (Object)null, false);

    try {
        //INITIALIZING事件
        this.initInternal();
    } catch (Throwable var2) {
        ExceptionUtils.handleThrowable(var2);
        this.setStateInternal(LifecycleState.FAILED, (Object)null, false);
        throw new LifecycleException(sm.getString("lifecycleBase.initFail", new Object[]{this.toString()}), var2);
    }   
    //初始化成功事件   
    this.setStateInternal(LifecycleState.INITIALIZED, (Object)null, false);
}
```

```java
protected void initInternal() throws LifecycleException {
    super.initInternal();
    this.onameStringCache = this.register(new StringCache(), "type=StringCache");
    MBeanFactory factory = new MBeanFactory();
    factory.setContainer(this);
    this.onameMBeanFactory = this.register(factory, "type=MBeanFactory");
    this.globalNamingResources.init();
    if (this.getCatalina() != null) {
        for(ClassLoader cl = this.getCatalina().getParentClassLoader(); cl != null && cl != ClassLoader.getSystemClassLoader(); cl = cl.getParent()) {
            if (cl instanceof URLClassLoader) {
                URL[] urls = ((URLClassLoader)cl).getURLs();
                URL[] arr$ = urls;
                int len$ = urls.length;

                for(int i$ = 0; i$ < len$; ++i$) {
                    URL url = arr$[i$];
                    if (url.getProtocol().equals("file")) {
                        try {
                            File f = new File(url.toURI());
                            if (f.isFile() && f.getName().endsWith(".jar")) {
                                ExtensionValidator.addSystemResource(f);
                            }
                        } catch (URISyntaxException var9) {
                        } catch (IOException var10) {
                        }
                    }
                }
            }
        }
        //调用StandardService 的init方法。
         for(int i = 0; i < this.services.length; ++i) {
            this.services[i].init();
        }
    }
```

####  3.2 StandardService

而在`StandardService`的`init();`方法中，实质调用和`server`一样，都会调用`initInternal()`  方法，两者都继承与`LifecycleBase`接口，而在`StandardService`中，则是初始化了`engine`和`container`、`executor`，这三个配置都是在`conf/server.xml`中。

```java
protected void initInternal() throws LifecycleException {
    super.initInternal();
    // 初始化container
    if (this.container != null) {
        this.container.init();
    }

    Executor[] arr$ = this.findExecutors();
    int len$ = arr$.length;

    int len$;
    for(len$ = 0; len$ < len$; ++len$) {
        // 初始化executor
        Executor executor = arr$[len$];
        if (executor instanceof LifecycleMBeanBase) {
            ((LifecycleMBeanBase)executor).setDomain(this.getDomain());
        }

        executor.init();
    }

    synchronized(this.connectors) {
        Connector[] arr$ = this.connectors;
        len$ = arr$.length;
		
        //初始化连接器
        for(int i$ = 0; i$ < len$; ++i$) {
            Connector connector = arr$[i$];

            try {
                connector.init();
            } catch (Exception var9) {
                String message = sm.getString("standardService.connector.initFailed", new Object[]{connector});
                log.error(message, var9);
                if (Boolean.getBoolean("org.apache.catalina.startup.EXIT_ON_INIT_FAILURE")) {
                    throw new LifecycleException(message);
                }
            }
        }

    }
}
```

#### 3.2.1 container

Container 是容器的父接口，所有子容器都必须实现这个接口，其左右只是其他组件必须完成的规范。

由ContainerBase负责同统一管理

Container 容器的设计用的是典型的责任链的设计模式，它有四个子容器组件构成，分别是：Engine、Host、Context、Wrapper，这四个组件不是平行的，而是父子关系，Engine 包含 Host,Host 包含 Context，Context 包含 Wrapper。通常一个 Servlet class 对应一个 Wrapper，如果有多个 Servlet 就可以定义多个 Wrapper，如果有多个 Wrapper 就要定义一个更高的 Container 了，如 Context，Context 通常就是对应下面这个配置：

![image-20200902111833088](F:\Typora数据储存\高级语言\JAVA\JavaWeb.assets\image-20200902111833088.png)



#### 3.2.2 connector

而在初始化`connection`的时候，实际是调用`protocolHandler(AjpNio2Protocol)`的`init();`方法，初始化 `endPoint`

```java
protected void initInternal() throws LifecycleException {
    super.initInternal();
    this.adapter = new CoyoteAdapter(this);
    this.protocolHandler.setAdapter(this.adapter);
    if (null == this.parseBodyMethodsSet) {
        this.setParseBodyMethods(this.getParseBodyMethods());
    }

    if (this.protocolHandler.isAprRequired() && !AprLifecycleListener.isAprAvailable()) {
        throw new LifecycleException(sm.getString("coyoteConnector.protocolHandlerNoApr", new Object[]{this.getProtocolHandlerClassName()}));
    } else {
        try {
            this.protocolHandler.init();
        } catch (Exception var2) {
            throw new LifecycleException(sm.getString("coyoteConnector.protocolHandlerInitializationFailed"), var2);
        }
	
        this.mapperListener.init();
    }
}
```

```java
public void init() throws Exception {
    if (this.getLog().isInfoEnabled()) {
        this.getLog().info(sm.getString("abstractProtocolHandler.init", new Object[]{this.getName()}));
    }

    if (this.oname == null) {
        this.oname = this.createObjectName();
        if (this.oname != null) {
            Registry.getRegistry((Object)null, (Object)null).registerComponent(this, this.oname, (String)null);
        }
    }

    if (this.domain != null) {
        try {
            this.tpOname = new ObjectName(this.domain + ":" + "type=ThreadPool,name=" + this.getName());
            Registry.getRegistry((Object)null, (Object)null).registerComponent(this.endpoint, this.tpOname, (String)null);
        } catch (Exception var4) {
            this.getLog().error(sm.getString("abstractProtocolHandler.mbeanRegistrationFailed", new Object[]{this.tpOname, this.getName()}), var4);
        }

        this.rgOname = new ObjectName(this.domain + ":type=GlobalRequestProcessor,name=" + this.getName());
        Registry.getRegistry((Object)null, (Object)null).registerComponent(this.getHandler().getGlobal(), this.rgOname, (String)null);
    }

    String endpointName = this.getName();
    this.endpoint.setName(endpointName.substring(1, endpointName.length() - 1));

    try {
        this.endpoint.init();
    } catch (Exception var3) {
        this.getLog().error(sm.getString("abstractProtocolHandler.initError", new Object[]{this.getName()}), var3);
        throw var3;
    }
}
```

connector组件的功能并不是自己完成对socket的数据流到Request对象的转换，而是借由**Coyote框架**，他是独立于Catalina类的一个实现类，只是借用他进行对tcp/ip，HTTP/AJP的转换，转换成java对象进行操作。

coyote是tomcat的Connector框架的名字，简单说就是coyote来处理底层的socket，并将http请求、响应等字节流层面的东西，包装成Request和Response两个类（这两个类是tomcat定义的，而非servlet中的ServletRequest和ServletResponse），供容器使用；

同时，为了能让我们编写的servlet能够得到ServletRequest，tomcat使用了facade模式，将比较底层、低级的Request包装成为ServletRequest（这一过程通常发生在Wrapper容器一级），这也是为很多人津津乐道的tomcat对设计模式的一个巧妙的运用。



> ## 一、什么是Coyote？
>
>   Coyote是Tomcat链接器框架的名称，是Tomcat服务器提供的供客户端访问的外部接口。客户端通过Coyote与服务器建立链接、发送请求并且接收响应。
>
>   Coyote封装了底层的网络通信（Socket请求以及响应处理），为Catalina容器提供了统一的接口，是Catalina容器与具体的请求协议以及 I/O方式解耦。Coyote将Socket输入转换为Request对象，交由Catalina容器进行处理，处理请求完成之后，Catalina通过Coyote提供的Response对象将结果写入输出流。
>
>   Coyote作为独立的模块，只负责具体协议和I/O的处理，与Servlet规范实现没有直接关系，因此即便是Request和Response对象也并未实现Servlet规范对应的接口，而是在Catalina中将他们进一步封装为ServletRequest何ServletResponse。
>
>   Coyote与Catalina的交互利用通过如下图所示：
> ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190801115128372.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjE0NjM2Ng==,size_16,color_FFFFFF,t_70)
>   在Coyote中，Tomcat支持一下3中传输协议：
>
> - HTTP/1.1协议：这是绝大多数Web应用采用的访问协议，主要用于Tomcat单独运行（不予Web服务器集成）的情况。
> - AJP协议：用于和Web服务器（如Apache HTTP Server）集成，以实现针对静态资源的优化以及集群部署，当前支持AJP/1.3。
> - HTTP/2.0协议：下一代HTTP协议，自Tomcat8.5以及9.0版本开始支持，截止目前，主流的最新版本均已支持HTTP/2.0。
>
>   针对HTTP和AJP协议，Coyote又按照 I/O方式分别提供了不同的选择方案（自8.5/9.0版本起，Tomcat已出了对BIO的支持）。
>
> - NIO：采用Java NIO类库实现。
> - NIO2：采用JDK 7最新的NIO2类库实现。
> - APR：采用APR（Apache可移植运行库）实现。APR是使用C/C++编写的本地库，如果选择该方案，需要单独安装APR库。
>
>   可以采用一种简单的分层视图来描述Tomcat对协议以及 I/O方式的支持，如下图所示：
> ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190801135725752.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjE0NjM2Ng==,size_16,color_FFFFFF,t_70)
>   在8.0之前，Tomcat默认采用的I/O方式为BIO，之后采用NIO。无论NIO、NIO2还是APR，在性能方面均优于以往的BIO。如果采用APR，甚至可以达到接近于 Apache HTTP Server的响应性能。
>
>   在Coyote中，HTTP/2.0的处理方式与HTTP/1.1和AJP不同，采用一种升级协议的方式实现，这也是有HTTP/2.0的传输方案决定的。
>
> ## 二、Web请求处理主要概念
>
>   在Connector中有如下几个核心概念：
>
> - Endpoint：Coyote通信端点，即通信监听的接口，是具体的Socker接收处理类，是对传输层的抽象。Tomcat并没有Endpoint接口，而是提供了一个抽象类AvstractEndpoint。根据I/O方式的不同，提供了NioEndpoint（NIO）、AprEndpoint（APR）以及Nio2Endpoint（NIO2）三个实现。
>
> - Processor：Coyote协议处理接口，负责构造Request和Response对象，并且通过Adapter将其提交到Catalina容器处理，是对应用层的抽象。Processor是单线程的，Tomcat在同一次链接中复用Processor。Tomcat按照协议的不同提供了3个实现类：Http11Processor（HTTP/1.1）、AjpProcessor（AJP）、StreamProcessor（HTTP/2.0）。除此之外，他还提供了两个用于处理内部处理的实现：UpgradeProcessorInternal和UpgradeProcessorExternal，前者用于处理内部支持的升级协议（如HTTP/2.0和WebSocket），后者用于处理外部扩展的升级协议支持。
>
> - ActionHook：本接口代替了Processor接口，成为所有Processor实现类的标准接口。其方法只有一个：public void action( ActionCode actionCode, Object param);ActionCode是一个静态类，说白了是一堆常量（估计以后会改成enum），即对应不同的ActionCode，ActionHook要作出不同的动作，至于param是用于传递一些信息的，通常会把调用者“this”传递进去
>
> - UpgradeProtocol：Tomcat采用UpgradeProtocol接口表示HTTP升级协议，当前只提供了一个实现（Http2Protocol）用于处理HTTP/2.0.他根据请求创建一个用于升级处理的令牌UpgradeToken，该令牌中包含了具体的HTTP升级处理器HttpUpgradeHandler，HTTP/2.0的处理器实现为Http2UpgradeHandler。Tomcat中的WebSocket也是通过UpgradeToken机制实现的。
>
> - Request和Response：Request类实现了对底层http字节流的封装，因为http本质上是从网络过来的一串字节流，并且从逻辑上根据http协议，分成了头和体，其中头部又有很多字段（包括MIME字段）。而Request的作用就是把这些字节封装成对应的字段，并且达到处理效率的最优
>
>   因此，Request里面大部分方法是字段的get方法（set方法不多，因为大部分字段是不可改变的），此外还有提供给容器使用的方法，如recycle、inputbuffer等等。但最关键的是，Request是如何提高处理效率的
>
>   对于底层的、和字节流打交道的DO（data object），性能瓶颈在于对内存的使用上（因为字节都是放在一块块的内存中），如果能有效的使用内存，就能有效地提高DO的性能。
>
>   如果让我们来实现这个Request类，估计大部分人第一反应就是用String来表示每个http头字段，然而String的效率之低下是绝对无法胜任服务器的性能要求的
>
>   Request的注释告诉我们，它的大部分字段是“GC free”的，即很少、甚至不会被垃圾回收。杜绝了java中最大的一个性能瓶颈，Request自然性能得到大幅提升
>
>   此外，其字段的一些耗时操作都会延迟到用户代码一级，也就是说，tomcat内部在使用Request时，都会尽量保证它的字段处于原始的字节状态（而不是图方便到处使用String），直到用户代码（也就是我们写的servlet）需要时才进行转换，如果用不到（其实http请求的大部分字段在我们编程时都用不到），就不作转换。这样又进一步挖掘出更多的性能潜力，其思想和“延迟加载”的设计模式如出一辙。
>
>   当然，tomcat的程序员也是人也喜欢偷懒，谁都不乐意直接操纵字节数组，那样出错的风险也大。因此，tomcat的org.apache.tomcat.util包定义了许多底层的工具类，用于操作和维护字节数组。Request的字段们的类型为MessageBytes就是其中的一种
>
>   而关于Response，原理和Request类似，但是Response简单了很多，最明显的是里面的字段不像Request那样为效率绞尽脑汁，而是直接用了String，也许是Response对整体效率影响不大，亦或者当前版本的tomcat还未对其进行改造
>
> ## 三、请求处理
>
> 来看一下Connector的请求处理的详细过程：
> （1）当Connector启动时，会同时启动其持有的Endpoint实例。Endpoint并允许多个线程（有属性acceptorThreadCount确定），每个线程允许一个AbstractEndpoint.Acceptor实例。在AbstractEndpoint.Acceptor实例中监听端口通信（I/O方式不同，具体的处理方式也不同），而且只要Endpoint处于运行状态，始终循环监听。
>
> （2）当监听到请求时，Acceptor将Socker封装为SocketWrapper实例（此时并未读取数据），并交由一个SocketProcessor对象处理（此过程也由线程池异步处理）。此部分根据I/O方式的不同处理会有所不同，如NIO采用轮询的方式检测SelectionKey是否就绪。如果就绪，则获取一个有效的SocketProcessor对象并且提交线程池处理。
>
> （3）SocketProcessor是一个线程池Worker实例，每一个I/O方式均有自己的实现。他首先判断Socket的状态（如完成SSL握手），然后提交到ConnectionHandler处理。
>
> （4）ConnectionHandler是AbstractProtocol的一个内部类，主要用于链接选择一个合适的Processor实现以进行请求处理。
>
> - 巍峨提升性能，他针对每个有效的理解都会缓存器Processor对象。不仅如此，当前链接关闭时，其Processor对象还会被释放到一个回收队列（升级协议不会回收），这样后续链接可以重置并且重复利用，以减少对象构造。
> - 因此，在处理请求时，他首先会从缓存中获取当前链接的Processor对象。如果不存在，则尝试根据协商协议构造Processor（如HTTP/2.0请求）。如果不存在协商协议（如HTTP/1.1请求），则从回收队列中获取一个已释放的Processor对象使用。如果回收队列中没有可用的对象，那么由具体的协议创建一个Processor使用（同时注册到缓存）。
>
> （5）协议升级时，ConnectionHandler会从当前Processor得到一个UpgradeToken对象（如果没有，则默认为HTTP/2），并构造一个升级Processor实例（如果是Tomcat支持的协议则会是UpgradeProcessorInternal，否则是UpgradeProcessorExternal）替换当前的Processor，并将当前的Processor释放回收。替换后，该链接的后续处理将由升级Process完成。
>
> （6）通过UpgradeToken中HttpUpgradehandler对象的init()方法进行初始化，以便准备开始启用新协议。
>
> ## 四、协议升级
>
>   在8.5版本，Tomcat重构了协议升级的实现方案，以支持HTTP/2.0，而且WebSocket也该由新的升级方案实现。尽管HTTP/2.0与WebSocket底层的升级方案是一致的，但是他们对协议协商的片段机制却是不同的，具体如下所示：
> ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190801145646209.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjE0NjM2Ng==,size_16,color_FFFFFF,t_70)
>   在Servlet规范3.1中，首先通过WebConnection接口表示一个用于升级请求的链接。TOmcat中的UpgradeProcessorExternal和UpgradeProcessorInternal类均实现了该接口。其次，又通过HttpUpgradeHandler接口表示升级协议的处理过程，Tomcat目前只提供了HTTP/2.0和WebSocket两种协议的支持。
>
>   对于升级协议，Tomcat通过一个UpgradeToken对象维护与其相关的信息，如当前Web应用上下文（StandardContexxt）、对象实例管理器（用于实例化对象）以及当前使用的HttpUpgradeHandler实例。无论HTTP/2.0还是WebSocket，均是先构造一个UpgradeTokend对象，然后根据他创建UpgradeProcessorInternal实例，并替换当前的Http11Processor以完成协议升级。当前链接的后续处理均由UpgradeProcessorinternal维护的HttpUpgradeHandler完成。
>
>   HTTP/2.0通过8.5新增的UpgradeProtocol接口创建HttpUpgradeHandler以及UpgradeToken，而WebSocket则是通过过滤器WsFilter片段当前请求是否为WebSocket升级请求，如果是，则调用当前请求的upgrade()方法构造UpgradeToken并传递给Http11Processor。
>
>   也就是说，对于第一次协议协商的过程，HTTP/2.0是由链接器直接处理的，并未提交到Servlet容器，而WebSocket则提交到了Servlet容器。
>
>   由于HTTP/2.0是多路复用的协议，也就是多个HTTP请求通过一个链接完成。因此对于Http2UpgradeHandler，会将每次请求响应交给StreamProcessor处理。而StreamProcessor则会将请求提交到Servlet容器。
>
> ![image-20200902112515055](F:\Typora数据储存\高级语言\JAVA\JavaWeb.assets\image-20200902112515055.png)

#### 3.2.2.1 endPoint

而在`endPoint`（属于tomcat包的，只有Processor是属于coyote包的，adapter）初始化过程中，实则是打开了`ServerSocketChannel`，其open方法会获得一个ServerSocket对象，最后调用了`Selector`的`open()`方法轮询channel。

应为采用的是NIO的I/O控制，因此会有channel、buffer、selector的使用

socket最后调用的方法都是经由SocketImpl调用native方法来获取或绑定socket，应在前端编译阶段就由javac或其他编译器就调用编译成了字节码（应该不会是Jaotc等提前编译来完成吧）

native方法通过JNI接口规范设计的cpp文件实现的，

可下载OpenJDK查询源码，但是到SocketImpl实现类调用的native方法需要的头文件并没有在OpenJDK中，可能未开源，也可能是对C知之甚少。（winsock2.h 和WS2tcpip.h -> https://docs.microsoft.com/en-us/windows/win32/api/     之后便是lib文件了，不开源）

**endpoint会依据socket的状态变化触发protocolHandler调用processor处理socket对象中的http/ajp等应用层信息成一个cotoyo框架提供的request接口，便于我们依据他进行通过xxxadapter将其转化为不同容器的xxxRequest交由容器通过wrapper读取的如web.xml等文件进行映射，最后交由业务层的类进行开发。**

endpoint只负责依据web.xml中的connect设置的socket对象的管理，至于socket的native方法的c实现，暂时不知道如何学习，至少会涉及tcp协议的转换与操作系统的交互。

```java
public final void init() throws Exception {
    this.testServerCipherSuitesOrderSupport();
    if (this.bindOnInit) {
        this.bind();
        this.bindState = AbstractEndpoint.BindState.BOUND_ON_INIT;
    }

}

public void bind() throws Exception {
        this.serverSock = ServerSocketChannel.open();
    	// 为tomcat服务进行socket编程，在channel中注册一个serverSock，保存socket的信息
        this.socketProperties.setProperties(this.serverSock.socket());
        InetSocketAddress addr = this.getAddress() != null ? new InetSocketAddress(this.getAddress(), this.getPort()) : new InetSocketAddress(this.getPort());
    	//  依据初始化的socket的协议族信息校验赋予socket地址与端口等信息
        this.serverSock.socket().bind(addr, this.getBacklog());
        this.serverSock.configureBlocking(true);
        this.serverSock.socket().setSoTimeout(this.getSocketProperties().getSoTimeout());
        if (this.acceptorThreadCount == 0) {
            this.acceptorThreadCount = 1;
        }

        if (this.pollerThreadCount <= 0) {
            this.pollerThreadCount = 1;
        }

        this.stopLatch = new CountDownLatch(this.pollerThreadCount);
    	// SSL加密
        if (this.isSSLEnabled()) {
            SSLUtil sslUtil = this.handler.getSslImplementation().getSSLUtil(this);
            this.sslContext = sslUtil.createSSLContext();
            this.sslContext.init(this.wrap(sslUtil.getKeyManagers()), sslUtil.getTrustManagers(), (SecureRandom)null);
            SSLSessionContext sessionContext = this.sslContext.getServerSessionContext();
            if (sessionContext != null) {
                sslUtil.configureSessionContext(sessionContext);
            }

            this.enabledCiphers = sslUtil.getEnableableCiphers(this.sslContext);
            this.enabledProtocols = sslUtil.getEnableableProtocols(this.sslContext);
        }

        if (this.oomParachute > 0) {
            this.reclaimParachute(true);
        }
		// NIO的方式使用selector进行轮询channel查看是否有对应channel监听的端口收到请求
    	// 减少了一个socket一个线程监听的性能损耗
    	// open方法会从选择器池中选取/创建一个选择器，选择器会传给blockingSelector，blockingSelector中会实现BlockPoller（为thread的子子类，我们通过修改其run方法实现由该轮询），其会依据选择器提供的socketkey进行判断
        this.selectorPool.open();
    }
```



## tomcat相关配置文件

* 即使不细看，也可以知道其中会调用相关的配置文件以启动相关的服务，那么，相关的服务在哪里呢,我们需要知道tomcat服务器的总的存贮目录的功能

> ![img](https://pic2.zhimg.com/80/v2-d8b75829a65958c65d50781155ae80a1_720w.jpg)

* 出现端口相关的问题，肯定在配置文件中，其中配置文件中最重要的便是server.xml和web.xml

> ![preview](https://pic2.zhimg.com/v2-142c11f3c514e299bf8ce7364aa55171_r.jpg)
>

## tomcat整体架构

* 要想理解这两个文件的作用，首先要认识tomcat的整体架构

> ![img](https://img-blog.csdnimg.cn/20181206104130815.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI2MzIzMzIz,size_16,color_FFFFFF,t_70)
>
> Server 表示整个servlet容器，因此Tomcat容器中只有一个Server实例
>
> * Service 表示一个或多个Connector的集合。这些Connector共享同一个Container来处理其他请求。在一个Server中可以包含多个Service，这些Service相互独立
>
> * Connector Tomcat连接器，用于监听并转换为Socket请求，将该请求交由Container处理，支持不同的协议及不同IO方式
>
> * Container 表示能够接收请求并返回响应的一类对象。在Tomcat中存在不同级别的容器：Engine、Host、Context、Wrapper
>
> * Engine 表示整个Servlet引擎，Engine为最高级别的容器。尽量Engine不是直接处理请求的容器却是获得目标容器的入口
>
> * Host 表示Engine中的虚拟机，与一个服务器的网络名有关，如域名等。客户端可以使用这个网络名连接服务器，这个名称必须要在DNS服务器上注册
>
>  * Context 用于表示ServletContext，在Servlet规范中，一个ServletContext表示一个Web应用
>
>  * Wrapper 表示Web应用中定义的Servlet
>
>  * Executor 表示Tomcat组件间可以共享的线程池



## server.xml和web.xml

* 因此我们可以大致猜到Server.xml的作用,**我们出现问题的地方也在这里了，若是我们设置了不同http port，绕过了idea的端口检测，且启动同一个catalina.bat，我们将读取同一个Server.xml文件，那我们的server 端口将会重复，出现：InstanceNotFoundException: Catalina:type=Server（即catalina在server创建阶段出现错误，无法找到相应实例），其中server的识别是依据相应的端口号来识别的，而相同的catalina.bat的启动导致读取的server.xml仍是相同的server端口号，导致异常**
* 解决方案便是：启动时两个服务器各自应用两个独立的tomcat服务器文件，或者修改catalina.bat（并创建另外的server文件），使其第二次启动时跳过已被占用了的端口文件，读取其他的配置。所以我选择了前者。

> server.xml的作用：
>
> ![preview](https://pic2.zhimg.com/v2-fb9aa5d024fa131bbec7fb9fe9d99739_r.jpg)

* 另外还有一个要注意的时这个web.xml,我们在idea或者eclipse中创建的web项目中也包含了web.xml，两者有什么关系呢
* 这是他们的父类，它里面的配置，如果动态web工程没有覆盖，就会被“继承”下来。我们会发现，conf/web.xml里配置了默认的DefaultServlet（org.apache.catalina.servlets.DefaultServlet,以及一些cgi等）和filter （很多类org.apache.catalina.filters. *）

> web.xml的作用：
>
> ![preview](https://pic1.zhimg.com/v2-70c094a65cffcfcdb0c74f199c01dbc8_r.jpg)
>

## 联想到SpringMVC



* 这便是web项目中web.xml最原始的模样，springmvc中的dispatcherServlet也是如此，将匹配到的/路径下的拿到dispatcherServlet中去转发或重定位

![image-20200726140749677](F:\Typora数据储存\高级语言\JAVA\servlet.assets\image-20200726140749677.png)

## Web.xml对servlet和Litsener等的读取方式

* **而若想知道为什么单凭一个链接便可以接入到相应的servlet**，你便需要了解整个流程
* 我们通过catalina.bat开始创建服务器，将server.xml环境配置完后，tomcat是通过生成一个StandardServer将其与catalina实例绑定，进行注册等操作后，便会开始部署项目，创建StandardContext
* 部署项目时一个StandardContext便是一个web项目，其会读取web.xml，最终解析是在`ContextConfig`中的`webConfig` 方法解析完生成一个WebXml 对象

> web.xml加载过程（其中节点的加载顺序context-param -> listener -> filter -> servlet ）
> 1.全局默认配置文件：tomcat目录下conf/web.xml解析为WebXml对象。
> 2.具体应用程序下的web.xml解析：例 /examples/WEB-INF/web.xml
> 3.扫描应用程序examples下的所有Jar，查找`META-INF/web-fragment.xml`文件并解析，保存到变量fragments中，格式<String,WebXml>。
> 4.已经检索到的WebXml配置进行排序。
> 5.基于SPI机制查找ServletContainerInitializer的实现。
> 6.扫描/WEB-INF/classes下面的类，处理Annotation：@ WebServlet,@ WebFilter,@ WebListener
> 7、扫描jar包中的Annotation：web-fragment.xml所在的jar包。
> 8、合并配置：所有web-fragment.xml中的配置，合并到应用程序examples中的配置web.xml。
> 9、合并配置：全局默认配置defaults 合并到应用程序的web.xml。

* 接着用这个对象去创建ServletContext（单例共享）

> 10、将JSP转换为Servlet。
> 11、将合并后的最终配置web.xml对象webXml应用到servlet容器(ServletContext)，装配servlet上下文;
> 12、将配置信息保存容器中，供其他组件访问，使得其他组件不需要再次重复上面的步骤去获取配置信息了。
> 13、查找jar包中的静态资源。
> 14、将ServletContainerInitializer应用到上下文。

* 同时StandardContext在读取web.xml中过的**<listener>创建<listener>中的类实例，创建监听器。 ****
* **有了监听器，我们便可以监听有到达我们的端口的消息（url-pattern）；有了ServletContext，我们就可以依据相应的相应的servlet-class去执行（或者再经过disparcherServlet去转发），若是找不到相应的servlet，则会用defaultServlet（连出错处理的servlet都没有的情况下）去处理**

>  * 有这种servlet则加入到ServletContext的Map中

    <servlet>
        <servlet-name>HelloServlet</servlet-name>
        <servlet-class>HelloServlet</servlet-class>
    </servlet>
    
    <servlet-mapping>
        <servlet-name>HelloServlet</servlet-name>
        <url-pattern>/hello</url-pattern>
    </servlet-mapping>

* springmvc将我们配置servlet的步骤省略了，都靠DisparcherServlet接收，并由他进行后续的处理，而我们只需要依照他的应用规则相应的注释。
* 需要相应实体类便由spring用工厂模式加载到的容器中去调用
* Spring与myBatis的集成便需要细细述说了，但就是Spring的事务管理的基础上，改换一下sqlsession的创建流程，换一个不同种类的代理类(与.getmapper相似)，并用专门的SpringManageTransaction用以获取保存的connection。

## Tomcat 调优

1. tomcat内存调优：启动时通过bat文件的JAVA_OPTS选项调优
2.  Tomcat并发优化：server.xml
3. Tomcat运行模式优化：