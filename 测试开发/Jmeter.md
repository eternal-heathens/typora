# Authrization头部与HTTPS的区别

https是做加密的，Authrization头部是做认证的，这两者没有必然联系。但是https握手的时候，会用到公私钥，有点认证的概念，但是认证服务方的公钥是否是被CA签名的。是客户端认证服务端的一个行为。

# 基本认证

把“用户名+冒号+密码”用BASE64加密后，放到Authrization Header中发送给服务器，每次请求都得加上Authorization Header。

# HTTP OAuth认证

放在Authrization Header中的不是用户名密码，而是一个token。**返回给前端的一般会附上tokenHead，以便前端使用，前后端会商量Token的存放方式，如直接RequestHeader的子选项中放置或者放置在Authrization等。**

# 使用Jmeter

https://www.cnblogs.com/star91/p/5059222.html

**1.启动jmeter：在bin下以管理员身份运行jmeter.bat，启动jmeter**

 **2. 创建测试计划：**

默认启动jmeter时会加载一个测试技术模板，保存测试计划：修改名称为Apitest,点击保存，选择保存路径,后面的步骤，每次添加或修改了了一些选项，软件并不会自动保存到jmx文件中，所以进行测试后，如果需要保存本次测试选项，要手动到“文件”菜单中保存一下。

**3. 添加线程组**

右键左边树中的测试计划“Apitest”节点，“添 加”→”Threads”→”线程组”

**4. 添加http****默认请求**：（用来配置公共参数,不是http请求）

右键线程组，选择“添加”→ “配置元件”→“HTTP请求默认值”，点击“HTTP请求默认值”后

添加成功后，线程组”节点下多了“HTTP请求默认值”节点

这里可以设置主机地址等一下公共参数，比如我们的例子中请求路径前面都是主机地址+index.php,就可以统一在"http请求默认值"里设置

填写默认请求名、服务器、默认请求路径，保存测试计划。

**5.添加http请求信息头**（若是session作为保存的可以设置cookie管理器：其默认会为后面的请求添加cookie）

这一项并不是必须的，只不过我们的例子中使用了Userid和Token放在HTTP请求头中用作用户验证（可以自定义存放token的标签的位置如Authorization）

右键“Apitest”，选择“添加”→ “配置元件”→“HTTP信息头管理器”

**6. 添加http请求**

右键“Apitest”，选择“添加”→ “Sampler”→“HTTP请求”

添加成功后，出现新的节点“HTTP请求”，就可以填写具体的请求参数了。

**这里可以添加如用户账号密码等接口相应的形参的信息**

**7.** **添加监听器**：

右键线程组，选择“添加”→“监听器”→“XXXXXXXXX”

可以添加的监听器有很多种，可以添加多个监听器，这里我们添加几个常用的“图形结果”、**==“察看结果树”==**、“聚合报告”

**8. 试运行**

点击执行

> 聚合报告：
>
> Label：每个 JMeter 的 element（例如 HTTP Request）都有一个 Name 属性，这里显示的就是 Name 属性的值
>
> \#Samples：表示你这次测试中一共发出了多少个请求，如果模拟10个用户，每个用户迭代10次，那么这里显示100
>
> Average：平均响应时间——默认情况下是单个 Request 的平均响应时间，当使用了 Transaction Controller 时，也可以以Transaction 为单位显示平均响应时间
>
> Median：中位数，也就是 50％ 用户的响应时间
>
> 90% Line：90％ 用户的响应时间
>
> Note：关于 50％ 和 90％ 并发用户数的含义，请参考下文
>
> http://www.cnblogs.com/jackei/archive/2006/11/11/557972.html
>
> Min：最小响应时间
>
> Max：最大响应时间
>
> Error%：本次测试中出现错误的请求的数量/请求的总数
>
> Throughput：吞吐量——默认情况下表示每秒完成的请求数（Request per Second），当使用了 Transaction Controller 时，也可以表示类似 LoadRunner 的 Transaction per Second 数
>
> KB/Sec：每秒从服务器端接收到的数据量，相当于LoadRunner中的Throughput/Sec
>
> “图形结果”监听器：
>
> 样本数目：总共发送到服务器的请求数.
> 最新样本：代表时间的数字,是服务器响应最后一个请求的时间.
> 吞吐量：服务器每分钟处理的请求数.
> 平均值：总运行时间除以发送到服务器的请求数.
> 中间值：时间的数字,有一半的服务器响应时间低于该值而另一半高于该值.
> 偏离：服务器响应时间变化、离散程度测量值的大小,或者,换句话说,就是数据的分布.



**9. 修改线程组的线程数等参数，用于压力测试**

点击左侧树形导航中的“线程组”



# 对于加密



  在接口测试的工作中我们一般首先面对的时登录操作，由于部分系统出于对安全性的考虑，登录做的都比较复杂如：1.参数加密传输；2.需要输入验证码；3.需要进行ToKen等。面对这里都是让我们接口测试时比较头疼的，那我们就先从易到难说下去。

​      1.常规登录：
​       首先我们要建一个HTTP 请求默认值，将公共用到的协议，服务器或ip，端口进行录入如：

![img](https://img-blog.csdn.net/20181011100824754?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzNjY4MDEx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

​    然后新建一个线程组，在线程组中我们建一个cookie管理器（不需要任何设置），这个的作用就是将下面登录的获得cookie共享给整个线程（如需共享给整个计划，将cookie管理器放置到线程组同级即可）

​    最后我们就可以做接口请求了：新建一个HTTP请求这里需要填入：方法 post；路径 /api/login/grade；参数：user , pwd ,为了可以看到接口的效果我们可以在接口中新增一个擦看结果树
![img](https://img-blog.csdn.net/20181011102130380?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzNjY4MDEx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

 完成之后就可以点击运行进行测试了，如果出现错误我们可以通过查看结果树中的响应数据进行分析，找到失败原因
![img](https://img-blog.csdn.net/20181011102319837?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzNjY4MDEx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

如果登录成功我们就可以做接下来的的接口了。

   2.参数加密传输：
  如果传输的参数是先进行了加密后进行传输的。最简单的解决方法就是进行抓包，获得加密后的密文，已参数的形式传输密文
![img](https://img-blog.csdn.net/20181011102652966?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzNjY4MDEx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
或者找到研发获得加密方式手动加密，或者通过调用第三方的脚本（java,python···）进行自动加密（后续文章中会进行说明）

   3.需要输入验证码：
   这个一般使用的是图片验证码，建议直接让研发做一个万能验证码（上线前去掉）；或者手动添加cookie绕过登录（在cookie中手动添加，但一般的cookie有效时间比较短无法满足绕过登录要求）

   4.需要进行ToKen
  需要进行ToKen，这个需要知道token获取的方式或者生成token的方式。通过接口或手动计算，调用第三方脚本（java,python···）进行获取（后续文章中会进行说明）

​      其实复杂的登录解决的思路都是一样的，主要是了解整个登录的相关流程，争对个流程进行分析，通过手动，调用接口，脚本的方式去解决这些流程。